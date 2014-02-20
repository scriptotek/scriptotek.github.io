---
layout: post
title:  "Ex Libris and JSON – not BFF"
author: dmheggo
date:   2015-11-17 17:36:00
categories: primo alma api
comments: True
---

I'm exploring the growing number of APIs at [Ex Libris Developer Network](https://developers.exlibrisgroup.com/). While JSON is supported, as you would expect from any modern API, it seems like it's threated a second class citizen to the extent that you *might* not want to use it – at least not with the Bib API… it's possible that the situation is better for other APIs.

While working on [a PHP client library](https://github.com/scriptotek/php-alma-client) for editing Alma Bib records in August, I found that I just could not save records when using JSON… but when switching to XML it worked. To my relief, someone else had already reported the issue on [the Developer Network forum](https://developers.exlibrisgroup.com/discussions#!/forum/posts/list/200.page) a few days before. After two months of waiting, Ex Libris replies that they will actually not fix this, but rather update the documentation to state that JSON is not supported for updating records. End of story.

<!--

Partial support for a format like JSON is almost worse than no support, at least when it's not clearly stated in the documentation that the support is not complete. You start writing your application using JSON, and then at some point later on discovers that you cannot use it for everything. At this point you might be tempted to use another format (like XML) just for these few calls, and your application quickly becomes a mess.
-->

Moving on, we're now working on getting catalogue search results for our [subject search](http://app.uio.no/ub/emnesok) and the [library app](http://app.uio.no/ub/bdi/realfagsbiblioteket). We should be able to use the Primo brief search API for that, right? Before getting to the central point, it's impossible not to mention the crazy data structure ([Here's what it looks like](https://gist.github.com/danmichaelo/6d5611592e396945b7a2)). A lot of the information is repeated in different places, but coded in slightly different ways. And the JSON is sprinkled with objects coded as strings split by MARC subfield codes that aren't documented anywhere I'm aware of, stuff like this:

    "delcategory" : [
	    "Alma-E$$IUBO",
	    "Alma-P$$IBIBSYS",
	    "Alma-P$$IUBO"
	  ]

Note that there's no initial subfield codes. In other strings there are:

	"availlibrary" : [
         "$$INTNU_UB$$LNTNU_UB1160133$$1DORA mag B$$2115524$$Savailable$$X47BIBSYS_NTNU_UB$$Y1160133$$Za00096$$P1",
         "$$INTNU_UB$$LNTNU_UB1160103$$1REAL$$2541.49 Qua$$Savailable$$X47BIBSYS_NTNU_UB$$Y1160103$$Za00330$$P2",
         "$$INB$$LNB0183334$$1Fjellmagasinet$$Savailable$$X47BIBSYS_NB$$Y0183334$$Zg00001$$P1",
         "$$INB$$LNB0183300$$1Fjernlånseksemplar$$Savailable$$X47BIBSYS_NB$$Y0183300$$Zg00030$$P2",
         "$$INB$$LNB0030100$$1NB Oslo$$2NA/A 1996:7626$$Savailable$$X47BIBSYS_NB$$Y0030100$$Zg00005$$P3"
    ]

Some subfields can easily be guessed: `$$I` is for institution, `$$L` for library, etc. (From Alma we know that "Library" is the same as "sublocation" in MAR21 Holdings), others not.

	"frbr" : {
      "k3" : "$$Kluminescence of lanthanide ions in coordination compounds and nanomaterials$$AT",
      "k1" : "$$Kbettencourt dias ana de$$AA",
      "t" : "1"
    }

Luckily there's [a nice php library](https://github.com/BCLibraries/primo-services) from Boston Collegy Libraries that takes away some of the pain of handling the data structure. (I also came across [a Ruby library](https://github.com/scotdalton/exlibris-primo) that I haven't tested.) If something is missing, the code is on GitHub, so you can send in a pull request (I've already sent a few). Great!


But why is the Primo API so slow? I'm not querying Primo Central index, just the library catalogue, which *should* be fast. For testing, I did a few requests on the command line using curl, and didn't bother to ask for JSON. To my surprise, it seemed to be processed much faster. Could it actually be that the API is significantly slower at returning JSON than XML?
Seemed really crazy, so I picked a sample of 1000 simple, random search terms (subjects from Realfagstermer). If the same request is made twice, the response time seems to more or less the same, but to be safe I did all the requests using JSON and XML, but alternated which request I did first. What I got was the following:

<iframe width="100%" height="500" frameborder="0" scrolling="no" src="https://plot.ly/~danmichaelo/370.embed"></iframe>

<!--
  Query: any,contains,Nydanning
    xml: 0.555942058563 seconds
    json: 1.86256408691 seconds
  Query: any,contains,Inverse problemer
    json: 2.38481616974 seconds
    xml: 0.65318608284 seconds
  Query: any,contains,Kulturlandskap
    xml: 2.06030988693 seconds
    json: 5.34008288383 seconds
  Query: any,contains,Land
    json: 5.14769411087 seconds
    xml: 1.56396198273 seconds
-->

The script I used to collect the data can be [found here](https://gist.github.com/danmichaelo/31a4095ee4156298676d).
