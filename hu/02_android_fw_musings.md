# Gondolatok az Android keretrenszer kapcsán

## Az Android fejlesztés nehéz

Az *Android* fejlesztés nehéz, ugye? Próbáltatok összerakni egyszerűbb appokat mondjuk *Angular*ral, *React*tal vagy *Vue*val, az valahogy sokkal egyszerűbben indul egész.

Feladat | Web | Android m
------- | --- | -------                      
Hello World | Egyszerű | Egyszerű 
Adat letöltés és megjelenítés | Egyszerű | Trükkös 
Komplex app | Nehéz | Nehéz és trükkös

Mindemelett kevesebb fogalmat, kevesebb osztályt és metódust kell megtanulni és fejben tartani. És mennyivel kevesebb a boilerplate!

Évek óta morgok ezen, hogy a Google kényelmesen úgy gondoltak, hogy csak ad egy keretrendszert, és ezzel az Ő dolgának vége.

## A Google nem foglalt állást...

Szerintem sok baj forrása, nagyon sokáig nem akartak állást foglalni számos fontos kérdésben, egyszerűen a fejlesztőkre hagyták, magas szintről nézve ilyen volt az architektúra is.

Ez pedig nem kevés káoszt szült, rengeteg különböző verziót hoztak össze az évek során:

* **MVC**,
* **MVP**,
* **MVVM**,
* **Clean Architecture / VIPER / Uber RIBS**,
* stb.

Beszéljünk át ezekből az első hármat részletesebben is, hogy megadjunk egy alaphangot az elkövetkezőknek.

Kicsi, jól átlátható, könnyen elképzelhető, így pedig én könnyen tudom majd szemléltetni felétek vele az alap
koncepciókat.

### MVC

<img src="https://raw.githubusercontent.com/AppCraft-Projects/bgg-demo-app/docs/docs/img/mvc.png" width="300">

A 80-as években a *SmallTalk*kal terjedt el igazán, majd sokan átvették az ötletet. Az *Objective-C* és a *Cocoa* keretrenszer fejlesztői különösen sokat inspirálódtak belőle. Így szivárgott át később a *CocoaTouch*-ba, avagy az *iOS* fejlesztők eszköztárába is. Ezt követően persze sok más terülekre is átszivárgott.

Az architektúra egyik alapvetése, hogy az információ egy irányba halad / folyik. Avagy a *View* figyel az inputra, majd azt átadja a *Controller*-nek, az elvégez néhány műveletet, mondjuk némi input validációt, majd némi transzformáció után továbbadja a domain Model irányába.

Talán a lényeges szempont, amiben ténylegesen eltér a többi megoldástól, hogy a *View* és a *Model* között direkt függőség van.

Ez pedig kínálja a lehetőséget egy megfigyelő (*observer*) minta valamilyenféle megvalósítására. Avagy ha változik bármi az alsó domain adat Modelben, az visszaszinkronizálható a *View*-ra.

Ilyesfajta állapot szinkronizálásához szépen passzolhat egy *Data Binding* is, erre az *Android* kínál is egy normális megoldást, ami az *XML* leíróból is elérhető.

**Megjegyzés**: Ez az iOS változat volt, a macOS féle változat esetében még direktebb a binding.

### MVP

<img src="https://raw.githubusercontent.com/AppCraft-Projects/bgg-demo-app/docs/docs/img/mvp.png" width="300">

A fő cél, hogy a logika minél nagyobb részét kivigyük a viewból.

Három fontos különbséget szeretnék kiemelni az *MVC*-vel szemben:

1. Nincs direkt függőség a *View* és a *Domain* model között, a *Presenter* közvetít közöttük.
2. Ennek következtében az adat-áramlás kétirányú.
3. A View könnyedén mockolható.

Ahogy a képről látjátok a *Presenter* nincs direkt függőségben a *View*-val, ha valamit meg akar változtatni, akkor egy *Interface*-en keresztül kommunikál azzal. Ez erősen különbözik a korábbitól, nincs szó *observation*-ről, nincs szó *data binding*ról, ez egy konkrét parancs, “Hé View, jelenítsd meg a felhasználót!”.

Ez pedig a tesztelésnél válik igazán hasznossá, nem kell a valós Androidos view objektumokkal dolgozni. Kicserélhetővé válik mindez egy tesztkörnyezetben.

### MVVM

<img src="https://raw.githubusercontent.com/AppCraft-Projects/bgg-demo-app/docs/docs/img/mvvm.png" width="300">

A harmadik és egyúttal utolsó az *MVVM*, ez is egy klasszikus minta. Valahol az előző kettő közötti megoldásról van szó.

Abból a szempontból hasonlít az *MVP*-re, hogy nincs direkt függőség a *View* és a domain *Model* között, a *ViewModel* közvetít közöttük.

A *ViewModel* magába zárja a logika jelentős részét, validációt, hasonlókat. Viszont fennáll annak a veszélye, hogy túlzottan nagyra nő.

A *View* két kapcsolatot tart fenn a *ViewModel*-el:

* Továbbítja az *input*okat.
* Megfigyeli (*observe*) azt, ha változik az állapot, akkor frissíti önmagát.

### Még néhány gondolat...

Mindez cégről-cégre, projektről-projektre változik, sokszor elég szabadosan is értelmezik. De persze aláírom, hogy projekt mérettől és igényektől függően szükség lehet más megközelítésekre, de akkor is.

Ez a helyzet bizonyosan nem kevés fejfájást is okoz a fejlesztőknek. Megnehezítik, ha egyik projektről a másikra váltanak.

## Véleményt kellett alkotniuk

Szóval úgy röpke 10 évvel az első Android megjelenése után a Googlenél is ráéreztek, hogy ez nem teljesen frankó így.

Összehívtak egy méretes bizottságot, számos cég fejlesztőjével, oktatókkal, szabadúszúkkal. Miután elkezdték körbejárni az általános problémákat, bizony számos fájdalmas terület kijött:

* Adat perzisztálás,
* Életciklus menedzselés,
* Alkalmazás modularitás,
* Adat szinkronizálás,
* Szálkezelés,
* Sok boilerplate.

És volt egy emlékezetes tanulság. Nem akarnak a technikai részletekkel vesződni, egyszerűen csak szállítani akarják a működő terméket, építeni az üzletet.

Elsőre durván hangzik, de gondoljatok csak bele, mert a maga szempontjából igaza van. Ehhez pedig az alapokat rendbe kell szedni.

A céljaik a következők voltak:

* Az egyszerű dolgok tényleg legyenek egyszerűek.
* A bonyolultak legyenek lehetségesek.
* Ne találják fel újra a melegvizet.

Külön kiemelném az utolsót. Nem akarták újragondolni az egész mindenséget, szépen játszanak a meglévő és bevált megoldásokkal, mint *Retrofit*, *Dagger*, vagy *RxJava*.

Bevezettek tehát néhány architektúrális komponenst / fogalmat:

* **Room**
* **LiveData**
* **ViewModel**
* **Lifecycle Observer**

Így néz ki az architektúra ajánlása egészében:

<img src="https://raw.githubusercontent.com/AppCraft-Projects/bgg-demo-app/docs/docs/img/architecture-components-full.png" width="420">

Külön-külön is használhatóak ezek a komponensek, de együtt lesz igazán érdekes. Ezt egyelőre csak felületesen veszem át, de rövidesen részeltesebben is ránézek az egyes részekre.

Ezekből szeretném egyelőre átvenni veletek az *Android Architecture Components* által kínált első hármat, a negyedikkel pedig majd később fogok foglalkozni.
