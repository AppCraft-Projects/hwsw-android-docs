# LiveData

## Itt tartunk most

<img src="https://raw.githubusercontent.com/AppCraft-Projects/bgg-demo-app/docs/docs/img/architecture-components-full-livedata.png" width="420">

## Mi a LiveData?
Lépjünk egyet tovább, tegyük fel, hogy egy tisztességes reaktív alkalmazást akarunk építeni, ez esetben jól fog jönni a *LiveData*, a második architektúra komponensünk. Ezzel monitorozhatóak a adatváltozások az adatbázisban.

> A *LiveData* tömören egy megfigyelhető (*observable*) adat tároló. Értesíti a megfigyelőket (observers), amikor valami adatváltozás történik. Így azok frissíthetik az alkalmazás UI-át.

Így illeszkedik ez a komponenens a mi kis egyszerű alkalmazásunkba...

<img src="https://raw.githubusercontent.com/AppCraft-Projects/bgg-demo-app/docs/docs/img/livedata-appui-room-twoway.png" width="420">

## Két új fogalom

A használatához mindössze két egyszerű új osztályt kell memorizálnotok:

* **MutableLiveData**: írható, *setValue()* és *postValue()* metódusokkal változtatható az értéke.
* **LiveData**: csak olvasható, immutable, nincs se *setValue()*, se *postValue()*.

Azt javaslom, hogy legfeljebb a *ViewModel*-en belül használd a *MutableLiveData* osztályt, azon kívül az immutable *LiveData*-t ajánld ki, csomó bugnak az elejébe lehet így menni.

## Használata

Nagyon egyszerű használni:

```java
MutableLivedData<String> dayOfWeek = new MutableLiveData<>(); 

dayOfWeek.observe(this, data -> {
  mTextView.setText(dayOfWeek.getValue() + “ is a good day.”); 
});
```

Tehát...

1. deklarálod valamilyen típusra,
2. megfigyelem azt,
3. frissíted a UI-t.

Valahogy így lehet ezt vizualizálni szépen: ￼

<img src="https://raw.githubusercontent.com/AppCraft-Projects/bgg-demo-app/docs/docs/img/livedata-thursday.png" width="420">

## Egyszerű megváltoztatni az értékét

Ha meg akarod változtatni az értékét, mindössze ennyi egy metódust kell hívnod:

```java
// UI szálról
dayOfWeek.setValue(“Friday”);

// Háttér szálról
dayOfWeek.postValue(“Friday”);
```

￼Több módja is van egy *LiveData* értékének a módosítására:

* **setValue()**: ha UI szálról történik,
* **postValue()**: ha háttér szálról.


Ez is vizualizálva: ￼

<img src="https://raw.githubusercontent.com/AppCraft-Projects/bgg-demo-app/docs/docs/img/livedata-friday.png" width="420">

## Room támogatás

Ami igazán király, hogy a *Room* teljes mértékben támogatja a *LiveData* komponenst.:

* Automatikusan értesít adatbázis frissítésnél,
* Az adatokat automatikusan betölti egy háttérszálon.

Rendkívül egyszerű rávenni a *Dao*-t, hogy *LiveData*-t adjon vissza, mindössze a *DAO* objektum visszatérési típusát kell finomhangolni.

```java
@Dao 
public interface UserDao {

  @Insert(onConflict = IGNORE) 
  void insertUser(User user);

  @Query(“SELECT \* FROM User”) 
  public LiveData<List<User>> findAllUsers();

  @Update(onConflict = REPLACE) 
  void updateUser(User user);

  @Query(“DELETE FROM User”) 
  void deleteAllUsers();

}
```

Tehát a második parancsnál a visszatérési érték korábban *List<User>* volt, most pedig wrappeltük azt, *LiveData<List<User>>*.

## Használat az *Activity*-ből

Ezután tudsz mondjuk egy *RecyclerView*-t ennyivel frissíteni az *UserListActivity*-ben:

```java
userLiveData.observe(this, users -> { 
  mUserRecyclerAdapter.replaceItems(users);
  mUserRecyclerAdapter.notifyDataSetChanged(); 
});
```

Összefoglalva annyi történik, hogy...

1. megváltozik az adat a *DB*-ben,
2. frissül a *LiveData*,
3. a *UI* is értesítve lesz az eseményről, így az szintén frissülhet.

<img src="https://raw.githubusercontent.com/AppCraft-Projects/bgg-demo-app/docs/docs/img/livedata-appui-room-oneway.png" width="420">

## Transzformációk

A későbbiekben előfordulhat az, hogy manipulálni / transzformálni szeretnéd a *LiveData*-t, erre három funckiót kínál jelenleg a rendszer:

* **map()**: egy funkcióval a *LiveData* kimenetét lehet megváltoztatni a használatával.
* **switchMap()** - mint a *map()*, de teljesen új *LiveData* objektumot ad vissza.
* **MediatorLiveData** - több *LiveData* összefuttatásához. Pl adatbázisból és hálózatról.