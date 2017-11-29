# Repository

## Itt tartunk most

<img src="https://raw.githubusercontent.com/AppCraft-Projects/bgg-demo-app/docs/docs/img/architecture-components-full-repository.png" width="420">

## Rövid összefoglalás

Következő lépésben szeretnénk, ha az adatok kezelése nem az *Activity*-ben foglalna helyet. Létrehozunk egy Repositority-t, ahova a hálózat kezelést kiszervezzük és később az adatbázis kezelést is ide fogjuk tenni. Egyébként egy rendkívül egyszerű komponensről van szó, tényleg nincs benne semmi nagy csavar, mindössze egy pici logika.

Így fog kinézni, amikor teljesen összeáll:

* Használja a lokálisan perzisztált adatot, ha még adott időkereten belül van.
* Egyébként próbáljon meg frissebbet letölteni. Arról már az *OkHttp* és a szerver együttesen képesek gondoskodni, ha nem áll rendekezésre a cacheltnél frissebb adat.