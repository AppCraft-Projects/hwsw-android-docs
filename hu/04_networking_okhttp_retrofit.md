# A hálózati komponens refaktorálása Retrofittel

## Itt tartunk most

<img src="https://raw.githubusercontent.com/AppCraft-Projects/bgg-demo-app/docs/docs/img/architecture-components-full-networking.png" width="420">

## Ahonnan indulunk....

Érdemes egy kicsit ízlelgetni a kódot, amit a legacy változat esetében látunk.

```java
public class GetRecomendationTask extends AsyncTask<Void,Void,List<RecommendationModel>> {

  private ProgressDialog progressDialog;

  public GetRecomendationTask(AppCompatActivity appCompatActivity) {
    progressDialog = new ProgressDialog(appCompatActivity);
    progressDialog.setIndeterminate(true);
  }

  @Override
  protected void onPreExecute() {
    super.onPreExecute();
    if (!progressDialog.isShowing()) {
      progressDialog.show();
    }
  }

  @Override
  protected void onPostExecute(List<RecommendationModel> recommendationModels) {
    super.onPostExecute(recommendationModels);
    if (progressDialog.isShowing()) {
      progressDialog.dismiss();
    }
    NearbyActivity.this.renderItems();
  }

  @Override
  protected List<RecommendationModel> doInBackground(Void... voids) {
    List<RecommendationModel> result = new ArrayList<>();
    String resultString;
    String inputLine;
    try {
      HttpURLConnection connection = (HttpURLConnection) new URL("http://192.168.1.225:8080/restaurants").openConnection();
      connection.setRequestMethod("GET");
      connection.setReadTimeout(15000);
      connection.setConnectTimeout(15000);

      connection.connect();
      //Create a new InputStreamReader
      InputStreamReader streamReader = new InputStreamReader(connection.getInputStream());
      //Create a new buffered reader and String Builder
      BufferedReader reader = new BufferedReader(streamReader);
      StringBuilder stringBuilder = new StringBuilder();
      //Check if the line we are reading is not null
      while((inputLine = reader.readLine()) != null){
        stringBuilder.append(inputLine);
      }

      //Close our InputStream and Buffered reader
      reader.close();
      streamReader.close();
      
      //Set our result equal to our stringBuilder
      resultString = stringBuilder.toString();

      JSONArray resultArray = new JSONArray(resultString);
      for (int i = 0; i < resultArray.length(); i++) {
        RecommendationModel model = new RecommendationModel(resultArray.getJSONObject(i));
        databaseHelper.addRecommendation(model);
      }
    } catch (IOException | JSONException e) {
      Log.e(TAG, e.getMessage(), e);
    }
    return result;
  }
}
```

Mennyire hosszú kódot és hány osztályt használunk ahhoz, hogy letöltsünk egy sima *JSON* formátumú adatot egy szerverről, majd átalakítsuk azt a megfelelő formátumra? *AsnycTask*, *HTTPURLConnection*, *JSONArray*, és még számos más osztályt alkalmazunk itt.

Alapvetően nem nehéz, de kifejezetten repetatív feladatot végzünk, és sok a boilerplate. Felteszem ezt többségében ismeritek.

## Lehet ezt jobban is

Ezt nem mi vettük észre először, a *Square* fejlesztő csapatát is nyomasztotta ez az egész, ezért hozták létre az *OKHttp*, *Retrofit* és *Picasso* nevezetű könyvtárakat. Használták mellé a *Google* féle *JSON-to-POJO* mapping könyvtárát, és kész is van.

Mind sokat bizonyított, maximálisan érett könyvtárak, és együtt mostanra de-facto szabvánnyá váltak. Teljes nyugalommal tudom azt mondani, hogy az esetek 90%-ban ez a csomag hibátlanul fog működni, és nem kell sokat dolgozni rajt.

## OkHttp

<img src="https://raw.githubusercontent.com/AppCraft-Projects/bgg-demo-app/docs/docs/img/okhttp.png" width="420">

Kifejezetten alacsony szintű könyvtárról van szó, az *Android* alap *HTTP* stackét cseréli ki egy modernebbre. A következő néhány előnnyel jár:

* Teljes *HTTP/2* támogatás, illetve connection pooling, ha az nem támogatott.
* *GZip* tömörítés, hogy összenyomja a csomagok méretét.
* Cacheli a szerverek válaszát, hogy csökkentse az ismételt hívások számát.
* Kezeli az *IPv4* és *IPv6* váltásokat.
* Elfed számos különböző hálózati problémát és csendben feláll azokból.

Olyan szinten elterjedt könyvtárról van szó, hogy az Android framework csapat is gyakran kifejezetten ennek a használatát javasolja.

Az alacsony szinthez viszonyítva kifejezetten egyszerű a használata, de előlünk a mélyebb részeket el fogja rejteni a *Retrofit*.

Mindössze egy OkHttpClientet kell konfigurálnunk:

```kotlin
OkHttpClient okHttpClient = new OkHttpClient.Builder()
  .writeTimeout(60L, TimeUnit.SECONDS)
  .readTimeout(60L, TimeUnit.SECONDS)
  .connectTimeout(60L, TimeUnit.SECONDS)
  .build();
```

## Retrofit

Térjünk ki erre részletesebben, ez a könyvtár nagyságrendekkel egyszerűbbé teszi a *REST API*-k kezelését. A kód maga sokkal rövidebb és könnyebben értelmezhető lesz.

Két dolgot kell majd magadnak csinálni, definiálni kell a *Retrofit*tel egy sima interfacet, deklaratív módon leírni a kommunikációt a *REST API*-val. A *Retrofit.Builder*rel legyárthatóak lesznek a hívások, majd mehetnek a megfelelő hívások.

Szépen be tudod kötni ebbe *HTTP* kliensként az *OkHttp*-t, illetve számos konvertert is, így számos formátumból mappelhető lesz az adat.

[TODO] mutasd meg a saját REST API-t

Így néz ki a REST API lefedve az OkHttp-vel:
```kotlin
interface BGGApiDefinition {
    @GET("restaurants")
    fun getRecommendations() : Call<List<RecommendationEntity>>

    @POST("restaurants")
    fun addRestaurant(@Body recommendation : RecommendationEntity) : Call<RecommendationEntity>
}
```

A beállítása is ugyanennyire egyszerű, az előzőekben előkészített OkHttpClient mellett kell egy Gson objektum, és úgy már minden sinen lesz:
```kotlin
Gson gson = new GsonBuilder().create();

apiDefinition = new Retrofit.Builder()
  .addConverterFactory(GsonConverterFactory.create(gson))
  .client(okHttpClient)
  .baseUrl(BuildConfig.API_BASE_URL)
  .build()
  .create(BGGApiDefinition.class);
```

Végül pedig használható: 
```kotlin
apiService.getRecommendations().enqueue(object: Callback<List<RecommendationEntity>> {
    override fun onFailure(call: Call<List<RecommendationEntity>>?, t: Throwable?) {
        Log.e(TAG.toString(), "Cannot fetch recommendations from network.", t)
    }

    override fun onResponse(call: Call<List<RecommendationEntity>>?, 
                            response: Response<List<RecommendationEntity>>?) {
        val items = response?.body()
        // ...
    }
})
```

Fontos lekezelni a sikeres (onResponse) és sikertelen (onFailure) eseteket egyaránt, ugye mindkét esetben másképp kell eljárni.

Még egy apóság, ez az inferface egyébként kifejezetten egyszerűen generálható, mondjuk egy *Swagger* doksiból.