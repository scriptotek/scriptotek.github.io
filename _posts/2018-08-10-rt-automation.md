---
layout: post
title: "Automatic sorting of RT tickets"
author: dmheggo
date: 2018-08-13 16:30:00
categories: alma rt python
comments: True
---

This blog post is a walkthrough of [auto-sorty.py](https://github.com/scriptotek/rt-bot/blob/master/auto-sort.py),
a small Python 3 script that uses the [Request Tracker (RT) API](https://rt-wiki.bestpractical.com/wiki/REST)
to automatically sort RT tickets into different queues based on information in the email text
as well as context information from the [Alma API](https://developers.exlibrisgroup.com/alma/apis).
The script barely touches upon the possibilities of automating RT processing,
but demonstrates how we can automate a small, yet tedious real-world task.<!-- more -->

The case: The University of Oslo Library uses a single sender address for most emails from Alma,
and all replies to that address goes to the same Request Tracker (RT) queue.
Most emails cannot be handled centrally though, as they refer to specific physical documents,
so they are delegated to the RT queues of one the four library branches.
Sometimes the email itself contains enough information to make a decision on which library branch the ticket should be passed on to,
but in the less fortunate cases it's also necessary to open Alma to lookup additional information about the document(s) or sender.

The auto-sort script is based on defining a few *rules*, in the form of functions that can generate suggestions when given a message.
Once all rules have been tested, a decision is made on which queue to move the ticket to based on the suggestions.

## Getting data from RT

First we need to get some data from RT.
There are at least two Python client libraries ([python-rt](https://github.com/CZ-NIC/python-rt) and [python-rtkit](https://github.com/z4r/python-rtkit)) that can make it easier to work with the RT API.
Unfortunately, both are a bit old and have a few glitches with Python 3.
I decided to go for python-rt, since it was updated more recently and has a responsive maintainer, so there's a chance of having issues fixed.
So, using python-rt, here's how we can find new messages in an RT queue and get the message content of those messages:

```python
import rt

tracker = rt.Rt(RT_URL, RT_USER, RT_PASSWORD)
tracker.login()

# Loop over all the tickets that has queue="ub-alma" and status="new":
for ticket in tracker.search(Queue='ub-alma', Status='new'):

    # Find the ticket id
    # (The need for split here is a glitch with the library, see https://github.com/CZ-NIC/python-rt/issues/24)
    ticket_id = ticket['id'].split('/')[1]

    # Every message in RT is really an *attachment* to the ticket,
    # so to find the message text we loop over all attachments.
    for att_info in tracker.get_attachments(ticket_id):

        # At this point we only have a reference to an attachment.
        # To get the actual content, another request is needed:
        att = tracker.get_attachment(ticket_id, att_info[0])

        # The attachment can be a message, but it can also be an image or a PDF,
        # so we will filter by content type. Note that while many email clients send
        # messages as HTML, they also (if behaving well) include a plain text version.
        # The HTML and plain text versions end up as two different attachments in RT.
        # We will use the plain text version, since it's easier to process.
        if att['ContentType'] == 'text/plain':
            content = att['Content'].decode('utf-8')  # (The decoding should ideally be done by the library)
            process_ticket(ticket_id, content)
            break  # Don't process the same ticket twice if it contains multiple messages


def process_ticket(ticket_id, content):
    # Do something based on the ticket content.
    # ...

```

Now that we have the message content we are ready for the fun stuff: finding out which queue to move the ticket to.


## The first suggestion rule: Text matching

We start with the simplest rule. Some of the emails actually include the library branch name. If the email contains the string "Arkeologisk bibliotek", we will suggest the "ub-humsam-biblioteket" queue, if it contains "Medisinsk bibliotek", we will suggest the "ub-umed" queue. We can create a map of all the text patterns we want to check for as keys (left side) and queue suggestion as values (right side):

```python
pattern_map = {
    'Arkeologisk bibliotek': 'ub-humsam-biblioteket',
    'Etnografisk bibliotek': 'ub-humsam-biblioteket',
    'HumSam-biblioteket': 'ub-humsam-biblioteket',
    'Ibsensenteret': 'ub-ujur',
    'Informatikkbiblioteket': 'ub-realfagsbiblioteket-ifi',
    'Juridisk bibliotek': 'ub-ujur',
    'Kriminologibiblioteket': 'ub-ujur',
    'Læringssenteret DN': 'ub-ujur',
    'Medisinsk bibliotek': 'ub-umed',
    'Menneskerettighetsbiblioteket': 'ub-ujur',
    'NSSF Selvmordsforskning og forebygging': 'ub-ujur',
    'Naturhistorisk museum biblioteket': 'ub-realfagsbiblioteket',
    'Offentligrettsbiblioteket': 'ub-ujur',
    'Petroleums- og EU-rettsbiblioteket': 'ub-ujur',
    'Privatrettsbiblioteket': 'ub-ujur',
    'Realfagsbiblioteket': 'ub-realfagsbiblioteket',
    'Rettshistorisk samling': 'ub-ujur',
    'Rettsinformatikkbiblioteket': 'ub-ujur',
    'Sjørettsbiblioteket': 'ub-ujur',
    'Sophus Bugge': 'ub-humsam-biblioteket',
    'Teologisk bibliotek': 'ub-humsam-biblioteket',
}
```

We can then loop over the map and yield queue suggestions for any text pattern matched.
In addition to the queue itself, we also include a descriptive comment that explains the choice.

```python
import re

# For each of the items in the map,
for pattern, queue in pattern_map.items():
    # check if we find the text pattern in the email content.
    # We've only used simple strings so far, but could in principle
    # use regular expressions for more advanced matching.
    if re.search(pattern, content):
        # If we do, yield a suggestion
        yield {
            'queue': queue,
            'comment': '- Meldingen inneholder teksten "%s"' % pattern,
        }
```

## Second rule: Looking up document IDs in Alma

When refering to a document, most emails include the document ID (also called barcode) of the document.
Can we extract them from the email text?
What pattern do they follow?
They take a few different forms, but they're all 9 characters long, and they include more digits than you would find in other words.
Our naïve approach is to extract all 9-character words that include at least a few consecutive integers, let's say four.
We can always make the test more stringent later if needed.

```python
barcodes = re.findall(r'\b[0-9a-zA-Z]{9}\b', content)
barcodes = set([x for x in barcodes if re.search(r'[0-9]{4}', x)])
```

where `\b` is the word boundary metacharacter.
I've split the test into two parts for readability: first we extract all 9-character words, then we narrow down the set to only those that contain at least four consecutive integers.
The result will surely include some false matches, like barcodes from other libraries, but this is not a huge problem since we will validate them with Alma in a moment.
Just one more thing; we also have items loaned from libraries abroad that get a temporary barcode in the form of a looong identifier (it's probably fixed-length, but I've never checked) starting with "RS-47BIBSYSUBO", so we will add those to our set:

```python
for barcode in re.findall(r'\bRS-47BIBSYSUBO[0-9]+\b', content):
    barcodes.add(barcode)
```

Now we have a set of *possible* document barcodes that we can validate with Alma using the `/almaws/v1/items?item_barcode={item_barcode}` endpoint (see [Retrieve Item and label printing information](https://developers.exlibrisgroup.com/alma/apis/bibs/GET/gwPcGly021om4RTvtjbPleCklCGxeYAfEqJOcQOaLEvNcHQT0/ozqu3DGTurs/Xx+GZLELMQamEGJL0f6Mjkdw==/af2fb69d-64f4-42bc-bb05-d8a0ae56936e)). To check them, we first initialize a session using the `requests` library:

```python
import requests

ALMA_KEY = 'secret'

session = requests.Session()
session.headers = {
    'Accept': 'application/json',
    'Authorization': 'apikey %s' % ALMA_KEY,
}
```

where `ALMA_KEY` is an Alma API key with read access to Bibs and Users.
Then we will loop over the barcodes we found and see which ones can be looked up.
As with the previous rule, we will return not only the queue name, but also an explanatory comment.

```python
ALMA_URL = 'https://api-eu.hosted.exlibrisgroup.com/almaws/v1'

for barcode in barcodes:
    response = session.get(
        '%s/items' % ALMA_URL,
        params={'item_barcode': barcode}
    ).json()

    # If the response includes 'item_date', the barcode is valid
    item_data = response.get('item_data')
    if item_data is not None:
        # The library branch code and name:
        libcode = item_data['library']['value']
        libname = item_data['library']['desc']
        # The physical location / collection name:
        locname = item_data['location']['desc']

        if libcode in libcode_map:
            # Yield queue suggestion
            yield {
                'queue': libcode_map[libcode],
                'comment': '- %s hører til %s %s.' % (barcode, libname, locname),
            }
```

Here, `libcode_map` is a map that connects library codes to RT queues:

```python
libcode_map = {
    '1030011': 'ub-humsam-biblioteket',
    '1030010': 'ub-humsam-biblioteket',
    '1030012': 'ub-humsam-biblioteket',
    '1030317': 'ub-realfagsbiblioteket-ifi',
    '1030000': 'ub-ujur',
    '1030002': 'ub-ujur',
    '1030307': 'ub-umed',
    '1032300': 'ub-umed',
    '1030500': 'ub-realfagsbiblioteket',
    '1030303': 'ub-humsam-biblioteket',
    ...
}
```

If the email contains multiple documents from different libraries, this rule will yield multiple suggestions. The decision-making process could then involve something like selecting the library with the most documents. For now, we just choose the first one though.

## Last resort rule: Check the RS library of the user

Users do not really belong to a given library branch, but users who have access to interlibrary loans have a so called resource sharing (RS) library, which is the library branch that handles interlibrary loans for them.
In practice, this is often the "home branch" of the user, so we can try looking up the user in Alma by their email address and find their RS library:

```python
search_results = session.get(
    '%s/users' % ALMA_URL,
    params={
        'q': 'email~%s' % email,
        'limit': 10,
        'offset': 0
    }
).json()

if search_results['total_record_count'] != 0:
    primary_id = search_results['user'][0]['primary_id']
    user_data = session.get('%s/users/%s' % (ALMA_URL, quote(primary_id))).json()
    user_group = user_data['user_group']['desc']
    user_group_code = int(user_data['user_group']['value'])

    libcode = user_data['rs_library'][0]['code']['value']
    libname = user_data['rs_library'][0]['code']['desc']
    log.info('[#%s] Sender email %s belongs to Alma user %s with resource sharing library: %s', ticket_id, email, primary_id, libname)

    queue = libcode_map.get(libcode)
    if queue is not None:
        yield {
            'queue': queue,
            'comment': '- Avsender (%s) er i brukergruppen «%s» og har %s som resource sharing library.' % (
                primary_id,
                user_group,
                libname
            ),
        }
```

where `libcode_map` is the same map that we used in the second rule.

## Making a decision
Now we have a script that generates zero or more suggestions for each RT ticket,
and we're free to use any decision making process we want (the two processes are decoupled).
So far, ours is extremely simple:
We just define an order of preference for the three rules and choose the first suggestion of the "best" type:

1. If we have at least one suggestion from the document id rule, use the first of those suggestions.
2. Otherwise, if we have a suggestion from the text pattern match rule, use that suggestion.
3. Otherwise, if we have a suggestion from the rs_library rule, use that suggestion
4. Otherwise, give up.

If the decision process turns out to be too simplistic, we can always make it complex later.

## Updating the RT ticket

Once we have decided which queue to move the ticket to, actually moving the ticket is just a simple call to the `edit_ticket()` method:

```python
tracker.edit_ticket(ticket_id, Queue=decision['queue'])
```

Finally, since we have gathered some context information about the sender (his or her resource sharing library)
and about the documents mentioned in the message (what library branch and collection name they belong to),
it might be helpful to make that information available to whoever is going to process the ticket.
This also makes the automatic sorting process more transparent.
Luckily, it's also very simple to add a comment to an RT ticket:

```python
tracker.comment(ticket_id, text=comment_body)
```

where `comment_body` is generated from the suggestion comments.

That concludes this walkthrough of the auto-sort script. There's a few bits and bolts I haven't covered,
but you can find the full script [at GitHub](https://github.com/scriptotek/rt-bot).

