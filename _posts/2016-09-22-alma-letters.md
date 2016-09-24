---
layout: post
title: "Alma letters & slips under version control"
author: dmheggo
date: 2016-09-22 17:02:00
categories: alma python
comments: True
---

We've published [our Alma letters & slips](https://github.com/scriptotek/alma-letters-ubo)
and [a script for synchronizing them](https://github.com/scriptotek/alma-slipsomat)
with Alma at GitHub. This post provides some background on how we're working with
the files at the University of Oslo Library.<!-- more -->

## What letters?

Alma comes with a growing number of XSLT templates (currently 87) called
“letters”, that cover basically everything to be printed or sent by email or
sms from the system, including all communication with users and all slips that
follow documents during processes such as inter-library loans.

Each time a letter is needed, Alma produces an XML file with the necessary data,
which it then couples it with the corresponding XSLT template using some
XSLT processor to produce HTML that is sent by email either directly to an end
user *or* to an email address connected in some way to a printer.

HTML [certainly isn't](http://ideas.exlibrisgroup.com/forums/308173-alma/suggestions/11095275-alma-letters-as-pdf-or-postscript) the best format for
print, but it's possible to get good results with some effort. We're
currently converting the HTML to PostScript using
[html2ps](http://user.it.uu.se/~jan/html2ps.html), which is dead easy to install
and support, but it's a bit limiting that it doesn't support CSS.

## Starting from the default templates

When did you last see an email written like a formal letter, starting with the
full postal address of the sender and recipient?

Here's an example of a letter produced using one of the default templates
to inform a user that a document he or she has shown interest for is now
available:

![Default interested in letter](/assets/interested-in-letter-before.png)

Great, huh? Apart from the overly formal (and weird) style and the awful layout,
note that there's also no call-to-action button to, say, reserve the item in
Primo or even look it up the there! It's like this letter is really supposed to
be printed and sent by post, not emailed.

When it came to the slips, they often lacked essential information. Requests
for a specific journal article would in some cases only show the name of the
journal, not any information about the article, sending slips did not include
the library number for the recipient, self-service pick up slips did not
include pick-up numbers, etc.

## Adding version control

Realizing that we would have to do quite a lot of modifications to the defaults,
we started thinking about how we could keep track of the changes and also be able
to merge in upstream changes from Ex Libris. We needed version control!

So earlier this year we set up [a Git repo](https://github.com/scriptotek/alma-letters-ubo)
for the letters. To be able to track upstream changes from Ex Libris, we also
included in it a folder for all the default letters.

## Syncing local templates with Alma using Selenium

Having a Git repo is fine, but Alma doesn't provide Git or GitHub integration.
Worse though, it doesn't even provide a file transfer protocol for the letters –
there's no (s)ftp, webdav, rest-api, what have you.

While we're [waiting for this](http://ideas.exlibrisgroup.com/forums/308173-alma/suggestions/12471084-synchronizing-xsl-templates-with-external-systems),
the only way to edit the XSLT templates seems to be through the form within the
Alma interface. Which by the way doesn't provide an embedded code editor like
[Ace](https://ace.c9.io/), so to remain sane, you need to copy the content into
your favourite text editor, do the editing, before you copy it back.

After doing this for a while, you end up writing a script which does the copying
back and forth for you. So we wrote [slipsomat](https://github.com/scriptotek/alma-slipsomat),
which is a command line script to pull in changes from Alma and push local changes
back. Since there's no file transfer protocol we can use, we use Selenium
browser automation, which is kinda crazy and prone to break on updates, it works
fine most of the time.

Then, in order to avoid overwriting the work of others, the script also stores
checksums for all the letters in a status file. If someone changes a letter
directly in Alma, its checksum will no longer match the one in the status file,
and the script will warn me when I try to push changes to that letter. If not,
it will happily upload the letter or letters that have been modified and update
the status file with new checksums:

<iframe width="560" height="315" src="https://www.youtube.com/embed/ObbkCwWqQkg?VQ=HD720" frameborder="0" allowfullscreen></iframe>

Some more information on our workflow is available [in the readme](https://github.com/scriptotek/alma-slipsomat).

And in case you wonder how the example letter above looks with our latest
templates, here it is:

![Interested in letter](/assets/interested-in-letter-after.png)

As you see, we still haven't found a way to introduce a call-for-action button.
<!-- The XML doesn't include any info on the connected Primo instance, so
we would have to hard code that, but then the XML doesn't include the document
identifier used by Primo, so we would have to use something like the barcode,
which nevertheless isn't available for e-resources. The only general identifiers
seem to be the title and the POL-ID, which isn't searchable in Primo.
There's just not much data in the XML to support one. It's quite
surprising that Alma and Primo, two products from the same company, aren't
better integrated.-->
Also, there are some odd data quality issues in this example that cannot be fixed
by a template, like why is the isbn part of the title field..? Oh well, just
another day with Alma.
