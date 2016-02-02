---
layout: post
title:  "Alma batch scan-in with Python"
author: dmheggo
date:   2016-02-02 17:02:00
categories: alma api
comments: True
---

Some way we ended up in a situation with a few thousand items stuck with status ‘in transit’ in Alma. Manually having to scan-in all the items was out of question. Scan-in API to the rescue! <!-- more --> The API is documented quite well both in the [API documentation](https://developers.exlibrisgroup.com/alma/apis/bibs/POST/gwPcGly021om4RTvtjbPleCklCGxeYAfEqJOcQOaLEvNcHQT0/ozqu3DGTurs/Xx+GZLELMQamEGJL0f6Mjkdw==/af2fb69d-64f4-42bc-bb05-d8a0ae56936e) and in an [Ex Libris blog post](https://developers.exlibrisgroup.com/blog/Scan-in-and-Next-Step-APIs).

First we prepared a plain text file with the barcodes for all the items, one barcode per line. Then we just needed a few lines of Python to read in the barcodes and do the API calls. Since the scan-in API itself requires item IDs, not barcodes, we also had to lookup the item ID for each barcode.

We start the script by importing the modules we need and defining a few variables: the [Developer Network API key](https://developers.exlibrisgroup.com/alma/apis), and the parameters for the scan-in API according to the API documentation.

```python
import json
from requests import Session

api_key = 'SECRET'  # Replace with your API key for prod read/write
scan_in_params = {
    'op': 'scan',
    'library': '1030310',  # Replace with your library code
    'circ_desk': 'DEFAULT_CIRC_DESK'  # Replace with your circ desk
}
```

We also define the URLs we need, using [named placeholders](https://pyformat.info/#named_placeholders):

```python
base_url = 'https://api-na.hosted.exlibrisgroup.com/almaws/v1'
barcode_api = base_url + '/items?item_barcode={barcode}'
item_api = base_url + '/bibs/{mms_id}/holdings/{holding_id}/items/{item_id}'
```

Whenever you need to send some headers with every request, it's quite handy
to create a `Session` object and specify them there. In our case, we'll need
a header for the API key and a header to indicate the we want JSON as
response format, so we add that to our session:

```python
session = Session()
session.headers.update({
    'accept': 'application/json',
    'authorization': 'apikey {}'.format(api_key)
})
```

Then we're ready for the fun part: reading the barcode list file,
looping over all the lines and doing the actual API requests:

```python
with open('barcodes.txt', 'r') as fp:

    # For each line in the file
    for line in fp:

        # Strip away any extra whitespace
        barcode = line.strip()

        # Make a barcode lookup request and parse the response as JSON
        res = session.get(barcode_api.format(barcode=barcode))
        doc = json.loads(res.text)

        # Extract MMS id, holding id and item id from the response
        mms_id = doc['bib_data']['mms_id']
        holding_id = doc['holding_data']['holding_id']
        item_id = doc['item_data']['pid']

        # Do the scan-in POST request:
        session.post(item_api.format(mms_id=mms_id,
                                     holding_id=holding_id,
                                     item_id=item_id), params=scan_in_params)
```

We now have a working script, but we should also test if each request was successful
or not, and output the status to screen.
The complete code is [available as a Gist](https://gist.github.com/danmichaelo/fea2a222efd00fc47531).
