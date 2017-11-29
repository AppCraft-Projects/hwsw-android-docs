# ViewModel

## Itt tartunk most

<img src="https://raw.githubusercontent.com/AppCraft-Projects/bgg-demo-app/docs/docs/img/architecture-components-full-viewmodel.png" width="420">

## Néhány bevezető gondolat

Van még itt nekünk egy fogós problémánk. Miért van az, hogy a legtöbb Android fejlesztő azzal kezd egy új appot, hogy letiltja az alkalmazás elforgathatóságát? Nos, a válasz a konfiguráció változás. Ahogy forgatjuk az appot az *Activity*-k létrejönnek, élnek, meghalnak. Valahogy így...

<img src="https://raw.githubusercontent.com/AppCraft-Projects/bgg-demo-app/docs/docs/img/lifecycle.png" width="420">

Nos, ez az adatbázis kezelés szempontjából nem kifejezetten optimális működés, sok pazarláshoz vezethet. Konkrétan számos költséges lekérdezés megismétléséhez. Avagy minden egyes alkalommal, amikor elforgatja a felhasználó a telefont.

<img src="https://raw.githubusercontent.com/AppCraft-Projects/bgg-demo-app/docs/docs/img/lifecycle-database.png" width="420">

Ezt lehet kicselezni a *ViewModel*-eket, bevezetünk egy újabb réteget, amelynek az életciklusa különáll az *Activity*-től. Túléli az elforgatások hatását, avagy az *Activity*-k létrejöttét és megsemmisítését.

<img src="https://raw.githubusercontent.com/AppCraft-Projects/bgg-demo-app/docs/docs/img/lifecycle-viewmodel.png" width="420">

## Mi a ViewModel?

> Tehát, röviden összefoglalva a *ViewModel* egy olyan objektum, amely adatot szolgáltat a *UI komponenseknek*, és túléli a konfiguráció változásokat.

## Három új fogalmat érdemes megjegyezni

Ismét három új osztályt / fogalmat kell memorizálni:

* *ViewModel*,
* *AndroidViewModel*,
* és *ViewModelProvider*.

A *ViewModel* és az *AndroidViewModel* közötött annyi a különbség, hogy átadásra kerül az *Application* objektum vagy sem. Az utóbbi ennyivel jobban kötődik az *Android* keretrendszerhez, ami a tesztelésnél lehet hátrány.

## Lássuk a példát

Először is örökölni kell a *UserListViewModelből*:

```java
public class UserListViewModel extends AndroidViewModel { 
  private AppDatabase mDatabase; 
  private LiveData<List<User>> users;

  public UserListViewModel(Application application) { 
    super(application);
    mDatabase = AppDatabase.getDb(getApplication());
    users = mDatabase.userModel().findAllUsers();
  }

  // Getters, setters ...

}
```

Az *AppDatabase* egy *Room* adatbázis singleton, onnan tudod elkérni az alkalmazáshoz tartozó adatbázist.

Abból lehet lekérni a *UserModel*-t, amin keresztül a *DAO* parancsok elérhetővé válnak.

## Használat *Activity*-ből

A *UserListActivity*-ben a használata nagyon egyszerű, mindössze ez a pár sor:

```java
userListViewModel = ViewModelProviders.of(this).get(UserListViewModel.class);

userListViewModel.getUsers().observe(this, users -> { 
  mUserRecyclerAdapter.replaceItems(users);
  mUserRecyclerAdapter.notifyDataSetChanged(); 
});
```

Lássuk a lépéseket sorban:

* A *ViewModelProviders.of(this)* használatával elkérjük a *ViewModelProivoder* példányt.
* Ebből a *.get(UserListViewModel.class)* metódus használatávan egy újabb példányt teszünk magunkévá.
* A *userListViewModel* változó keresztül elérhetővé tesszük az *Activity*-ben.
* A *userListViewModel.getUsers()* használatával megszerezzük a *LiveData*-t.
* *.observe(this, users -> { ... }* metódussal feliratkozunk az adatváltozás eseményre.
* Végül utolsó lépésként azon belül frissítjük a *UI*-t.

Összefoglalva ezt csináltuk most: ￼

<img src="https://raw.githubusercontent.com/AppCraft-Projects/bgg-demo-app/docs/docs/img/lifecycle-viewmodel-explanation.png" width="420">

Okos, igaz?

## A LiveData és az lifecycle

Egy fontos részletet azonban nem tárgyaltunk korábban, ez pedig a teljes életciklus témakör. Három fogalommal érdemes itt tisztában lenni:

* **Lifecycle**: Objektum, ami definiálja az *Android* életciklust.
* **LifecycleOwner**: Interface egy objektumnak, ami rendelkezik életciklussal.
* **LifecycleObserver**: interface egy *LifecycleOwner* megfigyeléséhez (**observation**).

**A *Support Library 26.1*-től kezdődően az *Activity* és *Fragment* osztályok implementálják a *LifecycleOwner*-t.**

Így néz ez ki: ￼
<img src="https://raw.githubusercontent.com/AppCraft-Projects/bgg-demo-app/docs/docs/img/livedata-observes-notifies.png" width="420">

Az ide tartozó kódot pedig ismeritek a korábbi példából:

```java
userListViewModel = ViewModelProviders.of(this).get(UserListViewModel.class);

userListViewModel.getUsers().observe(this, users -> { 
  mUserRecyclerAdapter.replaceItems(users);
  mUserRecyclerAdapter.notifyDataSetChanged(); 
});
```

Itt az *Activity*-ben az az *observe* hívást pont ezt a monitorozást váltja ki.

**FONTOS**, hogy amikor egy *Activity* megsemmisül, akkor ez a monitorozás is végetér, nem lesz semmi csintalan leak, neked pedig ehhez semmit sem kell tenned.

<img src="https://raw.githubusercontent.com/AppCraft-Projects/bgg-demo-app/docs/docs/img/livedata-unsubscibe.png" width="420">

## Összeállt, amit mára terveztünk

Itt tartunk tehát most a nagy képből, ennyit kínál az *Android Architecture Components*, de ki fogjuk egészíteni a még szükséges komponensekkel. ￼

<img src="https://raw.githubusercontent.com/AppCraft-Projects/bgg-demo-app/docs/docs/img/architecture-components-without-networking.png" width="420">