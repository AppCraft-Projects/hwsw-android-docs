# Room

## Itt tartunk most

<img src="https://raw.githubusercontent.com/AppCraft-Projects/bgg-demo-app/docs/docs/img/architecture-components-full-room.png" width="420">

## Mi a Room?

Kezdjük a bal alsó sarokban a *Room*-nál, ez pedig egy *SQL-Java* mappelő könyvtár. Mindössze ennyit csinálunk majd vele.

<img src="https://raw.githubusercontent.com/AppCraft-Projects/bgg-demo-app/docs/docs/img/appui_room.png" width="420">

## Két új fogalom

Mindössze két “új” fogalmat kell majd itt memorizálnotok:

* *POJO*,
* és *DAO*.

### POJO

Nézzük is ezeket. Egyszerű *POJO*-kat (*Plain Old Java Object*) képez le az SQLite adatbázisnak megfelelő formátumra és vissza.

Ilyen egyszerű, már százszor írtatok hasonlót:
```java
public class User {

  public String id; 
  public String firstName; 
  public String lastName; 
  public String jobTitle; 
  public int age; 

}
```

Ezt egészítjük ki néhány egyszerű annotációval, és már jó is lesz a *Room*-nak:

```java
@Entity 
public class User { 

  @PrimaryKey
  @ColumnInfo(name = “user_id”) 
  public String id; 

  public String firstName; 
  public String lastName; 
  public String jobTitle; 
  public int age; 

}
```

Amennyiben az egy tábla vagy oszlop neve eltérne az osztály vagy egy tulajdonság nevétől, az lehet jelölni:

* **@Entity(tableName = “users”)**: Összeköti a *User* osztályt a *users* táblával.
* **@ColumnInfo(name = “user_id”)**: Összeköti az *id* tulajdonságot a *user_id* osztloppal.

### DAO

Mindegyik *POJO*-hoz tartozik egy *DAO (Database Access Object)* is, ahol minden egyes funckió egy SQLite parancsnak felel meg.

Mutatok egy példát az összes alap *CRUD (Create, Read, Update, Delete)* műveletre:

```java
@Dao 
public interface UserDao {

    @Insert(onConflict = IGNORE)
    void insertUser(User user);

    @Query(“SELECT *  FROM User”)
    public List<User> findAllUsers();

    @Update(onConflict = REPLACE)
    void updateUser(User user);

    @Query(“DELETE FROM User”)
    void deleteAllUsers();

}
```

Figyeld meg az első három funkciót, mindháromnál megjelenik a User objektum, ezt fogja átalakítani oda és vissza a *POJO* és az *SQL* adatbázis között.

Valamint *@Query* után szereplő parancsok folyamatosan ellenőrizve vannak fordítási időben, egész érthető hibaüzeneteket ad az *Android Studio*, ha valami nem stimmel.

Tegyük fel, hogy egy sima elgépelés történt: ￼

<img src="https://raw.githubusercontent.com/AppCraft-Projects/bgg-demo-app/docs/docs/img/room-error.png" width="420">
