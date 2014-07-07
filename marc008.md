## Snodigheter i 008

008 skal være et felt med fast lengde 40 tegn,

Ref: http://www.loc.gov/marc/bibliographic/bd008.html

### Test 1: [131381679](http://oai.bibsys.no/oai/repository?verb=GetRecord&metadataPrefix=marcxchange&identifier=oai:bibsys.no:biblio:131381679), a printed book

008: `130916s2011 000 u|eng d` (23 tegn)

```
00-05    : 130916 [Date entered on file]
06       : s [Type of date/Publication status: ???]
07-10    : 2011 [Date 1]
11-14    :  000 [Date 2]
15-17    :  u| [???] 
35-38(?) : eng [Language: English]
39(?)    : d [Cataloging source : Other]
```

* Lengden er bare 24 tegn. 
  Hvor er tegn 18-34 (Material specific coded elements)?
  Det ser også litt ut som tegn 15-17 viser til noe annet enn "Place of publication, production, or execution".
* 008/06 : "s" finnes ikke i listen over "type of date/publication status"[1](http://www.loc.gov/marc/bibliographic/bd008a.html)


### Test 2: [080880762](http://oai.bibsys.no/oai/repository?verb=GetRecord&metadataPrefix=marcxchange&identifier=oai:bibsys.no:biblio:080880762), an electronic journal

008: `110404uuuuuuuuu p o | 0eng d` (28 tegn)
```
00-05    : 110404 [Date entered on file]
06       : u [Type of date/Publication status: Unknown]
07-10    : uuuu [Date 1]
11-14    : uuuu [Date 2]
19       : Kan dette være 21: periodical ?
21       : Kan dette være 23: online ?
35-38(?) : eng [Language: English]
39(?)    : d [Cataloging source : Other]
```

* Overraskende er lengden på her 29 tegn, altså 5 tegn lenger enn i forrige eksempel...
* Det kan virke som man har inkludert 008/21 for å skille mellom tidsskrifter ("p"), aviser ("n"), flerbindsverk ("m") o.l., men hvorfor i posisjon 19??

### Test 3: [101482086](http://oai.bibsys.no/oai/repository?verb=GetRecord&metadataPrefix=marcxchange&identifier=oai:bibsys.no:biblio:101482086), a CD

008 `140613s2010 eng` (15 tegn)
```
00-05    : 140613 [Date entered on file]
06       : s [Type of date/Publication status: ???]
07-10    : 2010 [Date 1]
...
35-38(?) : eng [Language: English]
```
