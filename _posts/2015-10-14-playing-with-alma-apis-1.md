---
layout: post
title:  "Playing with the Alma APIs"
author: dmheggo
date:   2015-10-14 13:07:00
categories: api alma
comments: True
---

Hoot hoot – the Alma train is coming, so we should jump on and get familiar with the APIs it bring. Our ticket is an API key to [Developers Network](https://developers.exlibrisgroup.com/dashboard/application), and to get it you should send an email to Bibsys (or just ask me if you're at UBO).
<!-- more -->

Open up a terminal and store the key in a variable. At the same time, let's also create a variable to hold the MMS id of a bibliographic record we're interested in (the MMS id is the identifier that identifies a bibliographic record in Alma):

```
ALMA_API_KEY="my secret key goes here"
MMS_ID="991400201794702204"
```

Now, for the first oddity: if you don't know the MMS id, there's no search API easily to be found. Alma does support SRU though, so the thinking is probably that you should use SRU for search. I might get back to SRU in a later post.

Let's see if we can get the holdings belonging to this record.
To make the result more readable, we'll also pipe it through [prettyjson](https://github.com/rafeca/prettyjson):

<pre>
curl -X GET \
       --header "Authorization: apikey ${ALMA_API_KEY}" \
       --header "Accept: application/json" \
       "https://api-na.hosted.exlibrisgroup.com/almaws/v1/bibs/${MMS_ID}/holdings" | prettyjson
</pre>

to get:

<pre>
<span style="color:green;">holding: </span><span style="color:black;">
  </span><span style="color:green;">- </span><span style="color:black;">
    </span><span style="color:green;">library: </span><span style="color:black;">
      </span><span style="color:green;">value: </span><span style="color:black;">1030310
      </span><span style="color:green;">desc: </span><span style="color:black;"> UiO Realfagsbiblioteket
    </span><span style="color:green;">location: </span><span style="color:black;">
      </span><span style="color:green;">value: </span><span style="color:black;">k00475
      </span><span style="color:green;">desc: </span><span style="color:black;"> UREAL Samling 42
    </span><span style="color:green;">link: </span><span style="color:black;">       https://api-na.hosted.exlibrisgroup.com/almaws/v1/bibs/991400201794702204/holdings/22148809160002204
    </span><span style="color:green;">holding_id: </span><span style="color:black;"> 22148809160002204
    </span><span style="color:green;">call_number: </span><span style="color:black;">FA 4606
</span><span style="color:green;">bib_data: </span><span style="color:black;">
  </span><span style="color:green;">title: </span><span style="color:black;">               The science of cheese
  </span><span style="color:green;">author: </span><span style="color:black;">              Tunick, Michael H..
  </span><span style="color:green;">issn: </span><span style="color:black;">                null
  </span><span style="color:green;">isbn: </span><span style="color:black;">                9780199922307
  </span><span style="color:green;">publisher: </span><span style="color:black;">           Oxford University Press
  </span><span style="color:green;">link: </span><span style="color:black;">                https://api-na.hosted.exlibrisgroup.com/almaws/v1/bibs/991400201794702204
  </span><span style="color:green;">mms_id: </span><span style="color:black;">              </span><span style="color:blue;">991400201794702200</span><span style="color:black;">
  </span><span style="color:green;">place_of_publication: </span><span style="color:black;">Oxford
  </span><span style="color:green;">network_number: </span><span style="color:black;">
    </span><span style="color:green;">- </span><span style="color:black;">(NO-TrBIB)140020179

    </span><span style="color:green;">- </span><span style="color:black;">140020179-47bibsys_network
    </span><span style="color:green;">- </span><span style="color:black;">(EXLNZ-47BIBSYS_NETWORK)991400201794702201
</span><span style="color:green;">total_record_count: </span><span style="color:black;"></span><span style="color:blue;">1</span>
</pre>

Great! We can get information about the items too:

<pre>
HOLDING_ID="22148809160002204"
curl -X GET \
     --header "Authorization: apikey ${ALMA_API_KEY}" \
     --header "Accept: application/json" \
     "https://api-na.hosted.exlibrisgroup.com/almaws/v1/bibs/${MMS_ID}/holdings/${HOLDING_ID}/items" | prettyjson
</pre>

gives us:

<pre>
<span style="color:green;">item: </span><span style="color:black;">
  </span><span style="color:green;">- </span><span style="color:black;">
    </span><span style="color:green;">link: </span><span style="color:black;">        https://api-na.hosted.exlibrisgroup.com/almaws/v1/bibs/991400201794702204/holdings/22148809160002204/items/23148809150002204
    </span><span style="color:green;">bib_data: </span><span style="color:black;">
      </span><span style="color:green;">title: </span><span style="color:black;">               The science of cheese
      </span><span style="color:green;">author: </span><span style="color:black;">              Tunick, Michael H..
      </span><span style="color:green;">issn: </span><span style="color:black;">                null
      </span><span style="color:green;">isbn: </span><span style="color:black;">                9780199922307
      </span><span style="color:green;">link: </span><span style="color:black;">                https://api-na.hosted.exlibrisgroup.com/almaws/v1/bibs/991400201794702204
      </span><span style="color:green;">mms_id: </span><span style="color:black;">              </span><span style="color:blue;">991400201794702200</span><span style="color:black;">
      </span><span style="color:green;">place_of_publication: </span><span style="color:black;">Oxford
      </span><span style="color:green;">publisher_const: </span><span style="color:black;">     Oxford University Press
      </span><span style="color:green;">network_number: </span><span style="color:black;">
        </span><span style="color:green;">- </span><span style="color:black;">(NO-TrBIB)140020179
        </span><span style="color:green;">- </span><span style="color:black;">140020179-47bibsys_network
        </span><span style="color:green;">- </span><span style="color:black;">(EXLNZ-47BIBSYS_NETWORK)991400201794702201
    </span><span style="color:green;">holding_data: </span><span style="color:black;">
      </span><span style="color:green;">link: </span><span style="color:black;">                 https://api-na.hosted.exlibrisgroup.com/almaws/v1/bibs/991400201794702204/holdings/22148809160002204
      </span><span style="color:green;">holding_id: </span><span style="color:black;">           22148809160002204
      </span><span style="color:green;">call_number_type: </span><span style="color:black;">
        </span><span style="color:green;">value: </span><span style="color:black;">8
        </span><span style="color:green;">desc: </span><span style="color:black;"> Other scheme
      </span><span style="color:green;">call_number: </span><span style="color:black;">          FA 4606
      </span><span style="color:green;">accession_number: </span><span style="color:black;">     
      </span><span style="color:green;">copy_id: </span><span style="color:black;">              
      </span><span style="color:green;">in_temp_location: </span><span style="color:black;">     </span><span style="color:red;">false</span><span style="color:black;">
      </span><span style="color:green;">temp_library: </span><span style="color:black;">
        </span><span style="color:green;">value: </span><span style="color:black;">null
        </span><span style="color:green;">desc: </span><span style="color:black;"> null
      </span><span style="color:green;">temp_location: </span><span style="color:black;">
        </span><span style="color:green;">value: </span><span style="color:black;">null
        </span><span style="color:green;">desc: </span><span style="color:black;"> null
      </span><span style="color:green;">temp_call_number_type: </span><span style="color:black;">
        </span><span style="color:green;">value: </span><span style="color:black;">
        </span><span style="color:green;">desc: </span><span style="color:black;"> null
      </span><span style="color:green;">temp_call_number: </span><span style="color:black;">     
      </span><span style="color:green;">temp_policy: </span><span style="color:black;">
        </span><span style="color:green;">value: </span><span style="color:black;">
        </span><span style="color:green;">desc: </span><span style="color:black;"> null
    </span><span style="color:green;">item_data: </span><span style="color:black;">
      </span><span style="color:green;">pid: </span><span style="color:black;">                         23148809150002204
      </span><span style="color:green;">barcode: </span><span style="color:black;">                     053225na0
      </span><span style="color:green;">policy: </span><span style="color:black;">
        </span><span style="color:green;">value: </span><span style="color:black;">
        </span><span style="color:green;">desc: </span><span style="color:black;"> null
      </span><span style="color:green;">provenance: </span><span style="color:black;">
        </span><span style="color:green;">value: </span><span style="color:black;">
        </span><span style="color:green;">desc: </span><span style="color:black;"> null
      </span><span style="color:green;">description: </span><span style="color:black;">                 
      </span><span style="color:green;">library: </span><span style="color:black;">
        </span><span style="color:green;">value: </span><span style="color:black;">1030310
        </span><span style="color:green;">desc: </span><span style="color:black;"> UiO Realfagsbiblioteket
      </span><span style="color:green;">location: </span><span style="color:black;">
        </span><span style="color:green;">value: </span><span style="color:black;">k00475
        </span><span style="color:green;">desc: </span><span style="color:black;"> UREAL Samling 42
      </span><span style="color:green;">pages: </span><span style="color:black;">                       
      </span><span style="color:green;">pieces: </span><span style="color:black;">                      
      </span><span style="color:green;">requested: </span><span style="color:black;">                   </span><span style="color:red;">false</span><span style="color:black;">
      </span><span style="color:green;">edition: </span><span style="color:black;">                     null
      </span><span style="color:green;">imprint: </span><span style="color:black;">                     null
      </span><span style="color:green;">language: </span><span style="color:black;">                    null
      </span><span style="color:green;">creation_date: </span><span style="color:black;">               2015-11-05Z
      </span><span style="color:green;">modification_date: </span><span style="color:black;">           2015-11-23Z
      </span><span style="color:green;">base_status: </span><span style="color:black;">
        </span><span style="color:green;">value: </span><span style="color:black;">1
        </span><span style="color:green;">desc: </span><span style="color:black;"> Item in place
      </span><span style="color:green;">physical_material_type: </span><span style="color:black;">
        </span><span style="color:green;">value: </span><span style="color:black;">BOOK
        </span><span style="color:green;">desc: </span><span style="color:black;"> Book
      </span><span style="color:green;">po_line: </span><span style="color:black;">                     
      </span><span style="color:green;">is_magnetic: </span><span style="color:black;">                 </span><span style="color:red;">false</span><span style="color:black;">
      </span><span style="color:green;">arrival_date: </span><span style="color:black;">                2014-05-14Z
      </span><span style="color:green;">year_of_issue: </span><span style="color:black;">               
      </span><span style="color:green;">enumeration_a: </span><span style="color:black;">               
      </span><span style="color:green;">chronology_i: </span><span style="color:black;">                
      </span><span style="color:green;">receiving_operator: </span><span style="color:black;">          import
      </span><span style="color:green;">process_type: </span><span style="color:black;">
        </span><span style="color:green;">value: </span><span style="color:black;">
        </span><span style="color:green;">desc: </span><span style="color:black;"> null
      </span><span style="color:green;">alternative_call_number: </span><span style="color:black;">     
      </span><span style="color:green;">alternative_call_number_type: </span><span style="color:black;">
        </span><span style="color:green;">value: </span><span style="color:black;">
        </span><span style="color:green;">desc: </span><span style="color:black;"> null
      </span><span style="color:green;">storage_location_id: </span><span style="color:black;">         
      </span><span style="color:green;">public_note: </span><span style="color:black;">                 
      </span><span style="color:green;">fulfillment_note: </span><span style="color:black;">            
      </span><span style="color:green;">internal_note_1: </span><span style="color:black;">             Status: kat - kat
      </span><span style="color:green;">internal_note_2: </span><span style="color:black;">             TIME FOR CIRC STATUS: 2015-11-02 | CIRC STATUS: 0
      </span><span style="color:green;">internal_note_3: </span><span style="color:black;">             
      </span><span style="color:green;">statistics_note_1: </span><span style="color:black;">           
      </span><span style="color:green;">statistics_note_2: </span><span style="color:black;">           
      </span><span style="color:green;">statistics_note_3: </span><span style="color:black;">           
</span><span style="color:green;">total_record_count: </span><span style="color:black;"></span><span style="color:blue;">1</span>
</pre>

Note that the response doesn't just include item data, but also a selection of holdings and bibliographic data. At least it's well separated, so we can just ignore what we don't need.

Having the item identifier (named 'pid') we could try checking out the item `23148809150002204`:

<!-- If we don't know the user ID, we should be able to look it up from the [Alma Users API](https://developers.exlibrisgroup.com/alma/apis/users/GET/gwPcGly021r0XQMGAttqcNyT3YiaSYVA/0aa8d36f-53d6-48ff-8996-485b90b103e4). Trying to find myself, I had to replace the "ø" in my name with "o" to get it to work though. I also couldn't figure out how to search for two first names. According to the docs, you
should be able to escape space as underscore. So `first_name~Dan_Michael` should work, but it didn't. Querying using `AND` worked though, so
I could query for `first_name~Dan%20AND%20last_name~Heggo` to find myself. Nevertheless, let's finally try to checkout an item to me:
-->

```
ITEM_ID=23148809150002204
USER_ID=<YOUR USER ID>
curl -X POST \
     --header "Authorization: apikey ${ALMA_API_KEY}" \
     "https://api-na.hosted.exlibrisgroup.com/almaws/v1/bibs/${MMS_ID}/holdings/${HOLDING_ID}/items/${ITEM_ID}/loans?user_id=${USER_ID}"
```

Well, it didn't work, I just got a `BAD_REQUEST` error. Not really sure why.
