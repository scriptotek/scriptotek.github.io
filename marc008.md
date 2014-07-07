## Snodigheter i 008

Test 1: [131381679](http://oai.bibsys.no/oai/repository?verb=GetRecord&metadataPrefix=marcxchange&identifier=oai:bibsys.no:biblio:131381679), a printed book

```
008 : 130916s2011 000 u|eng d
008/00-05 : 130916 [Date entered on file]
008/06 : s [Type of date/Publication status: ???]
008/07-10 : 2011 [Date 1]
008/11-14 :  000 [Date 2]
008/15-17 :  u| [???] 
008/35-38 : eng [Language: English]
008/39 : d [Cataloging source : Other]
```

* 008 skal være et felt med fast lengde 40 tegn, men har bare 24 tegn. 
  Hvor er tegn 18-34 (Material specific coded elements)?
  Det ser også litt ut som tegn 15-17 viser til noe annet enn "Place of publication, production, or execution".
  Ref: http://www.loc.gov/marc/bibliographic/bd008.html

* 008/06 : "s" finnes ikke i listen over "type of date/publication status"[1](http://www.loc.gov/marc/bibliographic/bd008a.html)


Test 2: [080880762](http://oai.bibsys.no/oai/repository?verb=GetRecord&metadataPrefix=marcxchange&identifier=oai:bibsys.no:biblio:080880762), an electronic journal

Overraskende er lengden på 008 her 29 tegn, altså 5 tegn lenger.

```
008 : 110404uuuuuuuuu p o | 0eng d
00-17 : dates 
19 : Kan dette være 21: periodical ?
21 : Kan dette være 23: online ?
35-39 : language, cataloging source
```

* Her er lengden på 29 tegn. Det kan virke som man har inkludert 008/21 for å skille mellom tidsskrifter ("p"), aviser ("n"), flerbindsverk ("m") o.l., men hvorfor i posisjon 19??
