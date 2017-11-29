# Bevezető

## Néhány gondolat az elejére

Szeretek kezdésként anekdótázni egy kicsit. A Kotlin workshophoz hasonlatosan a mostani is egy régi gondolat volt. Szerettünk volna bemutatni az Android fejlesztést egy új szemszögből. Mi is sokat tapasztaltuk, de ahogy számos ismerőssel beszélgetve ráéreztünk egy közös problémára.  

Jellemzően nem akkor van a gond, amikor egy új Android projekben kell építeni bevált és kényelmesebb megoldásokkal, az jellemzően egészen simán megy. Hanem amikor van egy app, ami már 3-4, vagy akár 5 éve épül, és a legkörültekintőbb munka ellenére is elkezd rozsdásodni. És ez még a polkorrekt változat. 

Éreztük, hogy ez egy nagyobb téma, és amikor konferencia szervezőivel beszélgettünk erről, mindenképp úgy voltunk vele, hogy ez egy olyan téma, amibe igazán mélységeiben bele akarunk menni, nem lesz elég egy átlagos előadásnyi idő, a workshop lesz ennek a jó formátum.

Majd amikor elkezdtünk alaposan kidolgozni abszolút érezhető volt, rájöttünk, hogy simán van két napnyi anyagunk. Szóval ezen a ponton már ott találtuk magunkat, hogy erősen vágni kell. Sokat gondolkodtunk és úgy döntöttük, hogy megcsináljuk ezt a három órásat, pörgős lesz, fókuszál, kimarad sok-sok téma, de ennek ellenére magában is kerek lesz. Tehát a teljesség igénye nélkül szemléljétek mindezt.

Ezek alapján biztos érzitek, hogy zúzós dologra vállalkoztok a következő órákban, és bizony feszes lesz a tempó. Ne számítsatok egy hagyományos elmélyülős-mentorálás workshopra. Inkább tekintsetek erre úgy, mint egy interaktív előadásra, ami azért egészen mélyen tud a témában elmerülni.

Gyorsan meg is mutatom az alkalmazást, először, hogy miből indultunk ki, majd ahol most tartunk.

Tehát nagy anyagról van szó, ezért arra törekedtünk, hogy napi munkában leggyakrabban felmerülő részeket vegyük most előre és mutassuk be. 

## Tartalom

A következő témákról lesz tehát szó a következő órákban:

- [A legacy kód áttekintése](https://github.com/AppCraft-Projects/bgg-demo-app/blob/docs/docs/hu/01_legacy_code.md)
- [Android keretrendszer, régen és most](https://github.com/AppCraft-Projects/bgg-demo-app/blob/docs/docs/hu/02_android_fw_musings.md)
- [Kotlin gyorstalpaló](https://github.com/AppCraft-Projects/bgg-demo-app/blob/docs/docs/hu/03_kotlin_basics.md)
- [Networking komponens átalakítása OkHttp-vel és Retrofit-tel](https://github.com/AppCraft-Projects/bgg-demo-app/blob/docs/docs/hu/04_networking_okhttp_retrofit.md)
- [Képkezelő komponens átalakítása Picasso-val](https://github.com/AppCraft-Projects/bgg-demo-app/blob/docs/docs/hu/05_image_handling_picasso.md)
- [Repository komponens rétebevezetése](https://github.com/AppCraft-Projects/bgg-demo-app/blob/docs/docs/hu/06_repository.md)
- [Adatbáziskezelő komponens átalakítása Room-al]https://github.com/AppCraft-Projects/bgg-demo-app/blob/docs/docs/hu/07_room.md)
- [LiveData komponens bevezetése](https://github.com/AppCraft-Projects/bgg-demo-app/blob/docs/docs/hu/08_livedata.md)
- [Activity életciklus javítása ViewModel-el](https://github.com/AppCraft-Projects/bgg-demo-app/blob/docs/docs/hu/09_viewmodel.md)
- [Linkgyűjtemény](https://github.com/AppCraft-Projects/bgg-demo-app/blob/docs/docs/hu/10_links.md)
- [Ami kimaradt...](https://github.com/AppCraft-Projects/bgg-demo-app/blob/docs/docs/hu/11_missing_topics.md)
- Q&A

Egyébként olyan lesz az egész, mintha egy repülőgépet menet közben építenénk át, apránként cseréljük ki a részeket, és mindezt úgy, hogy az alkalmazás mindig működőképes maradjon.

## Anyagok

Számos módon próbálunk nektek segíteni, hogy a nagy tempó ellenére jól tudjatok minket követni. Több repó is elő lett készítve: 

- Backend: [http://bit.ly/hwsw-android-workshop-backend](http://bit.ly/hwsw-android-backend),
- Android app: [http://bit.ly/hwsw-android-workshop-app](http://bit.ly/hwsw-android-app),
- Jegyzetek: [http://bit.ly/hwsw-android-workshop-notes](http://bit.ly/hwsw-android-notes),
- Prezi: [http://bit.ly/hwsw-android-workshop-presentation](http://bit.ly/hwsw-android-presentation).

## Mit találtok itt?
- A backendet úgy döntöttünk legyen offline is elérhető, így időnként könnyebb dolgozni vele.
- Az Android appon fogunk mi dolgozni, itt tartalom szerint láthatjátok a commitokat sorban. Igazából mindegyikhez két commit lesz, egyrészt megtaláljátok majd a régi verziót kommentelve, majd a következőben a változtatásokat.
- Mindegyik lépéshez a jegyzeteket. 
- És a prezit is, amit mutatok.

Kérlek ezt húzzátok le magatokhoz, és egyelőre mindből a legutolsó commitot checkoutoljátok ki. 

Mindenek előtt a backendet setupoljátok fel a repóban leírtak szerint. Ha nincs Node.js telepítve, akkor itt található segítség: [http://bit.ly/node-install](node-install).

Ezeket használva nem kell a jegyzeteléssel időt tölteni, mert szinte minden le van írva. Valamint akkor sem lesz baj, ha valahol lemaradtok, mindent lehet később otthonról pótolni.

Kérdés esetén minket a következő email címeken értek el:
gabor (pötty) orosz (kukac) appcraft (pötty) hu
motibi89 (kukac) gmail (pötty) com

Még utolsó gondolat, ha kérdés van, akkor nyugodtan kezet fel, adjatok füstjelet, kukorékoljatok, ami jól esik. Közbe is nyugodtan, de a végén lesz 20-30 perc, amit külön a Q&A-re dedikálunk.

Ennyit a bevezetésről, csapjunk bele!
