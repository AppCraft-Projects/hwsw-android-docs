# A legacy kód megismerése

Ezen a ponton szeretnénk nektek 5 percet adni, hogy átnézzétek. Azt követően Tibi fogja végig mutagatni nektek ezeket a pontokat részletesen, itt csak néhány részletet szeretnék kiemelni. 

## Activity

**Hol vagyunk?** *NearbyActivity*

Rengeteg dolog van egyben az *Activity*-ben, nem szeparáljuk a felelősségi köröket:

* *View* és kód összekötése
* User inputok kezelése
* UI renderelés
* Activity lifecycle kezelés
* *Networking* és *AsyncTask* bele a kellős közepébe
* *Adatbázis* kezelés

## .onCreate()

**Hol vagyunk?** *NearbyActivity.onCreate()*

A *findViewById()* jelen helyzeztben nem vészes, lehetne durvább is, láttam osztályokat ahol 15-20-30 elemet is kötöttek be így. 

Ami ellenben már zavaróbb, hogy az onCreate()-ben számos más felelősség kör is megjelenik, ezzel mindenképp kezdeni kell majd valamit.

## AsyncTask:

**Hol vagyunk?** *NearbyActivity > GetRecomendationTask*

Egy *AsyncTask* az *Activity* kellős közepében, ezzel számos probléma lehet.

* Alapvetően nem komplikált konstrukció, de könnyen be lehet nézni dolgokat, trükkös.
* Számos memory leak bújhat meg itt, például hivatkozások az Activity-be.
* Konfiguráció váltás, az *Activity* lifecycle változás számos problémát okozhat.
* Túl sok AsyncTask indítása esetében megtelik a queue, ez pedig crash-ez vezet.

## HttpURLConnection

**Hol vagyunk?** *NearbyActivity > GetRecomendationTask > doInBackground()*

JSON-t akarok letölteni egy REST API-ról, de ez nagyjából 40 sor kódot igényel. Csúnya, nehezen átlátható, és számos apró csapdát rejt.

## Database handling

**Hol vagyunk?** *RecommendationDatabaseHelper*

Ismét egy AsyncTask, erről már írtam feljebb. 

Egészen pici SQL queryket írunk, de ehhez képest rendkívül hosszú az eredmény. A sok string konkatencáció miatt nem tud segíteni az IDE, és az elgépelések csak futási időben jönnek ki. Számos apró hiba rejtőzhet ebben, amit nehéz kiszúrni.