# Képkezelés refaktorálása Picassoval

## Itt tartunk most

<img src="https://raw.githubusercontent.com/AppCraft-Projects/bgg-demo-app/docs/docs/img/architecture-components-full-networking.png" width="420">

## Pár gondolat előzetesen

Még egy puzzle darab azért hiányzik nekünk. Szükségünk lesz képek kezelésére is, de ez nem egyszerű történet, le kell fedni számos use-caset:

* Rendkívül költséges az *ImageView*-t újra és újra létrehozni és mindig megsemmisíteni, például egy *RecyclerView* esetében, jó lenne valahogy újrafelhasználni.
* Letöltés megszakítását. Például, ha az *Activity* megsemmisül.
* Különböző kép-transzformációk alacsony memória használattal.
* Automatikus kép cachelés memóriába és háttértárra.

## A korábbi megoldás

Mindezt hagyományos módon piszkosul melós lenne lekódolni. Jó lenne, ha ezt valami frankón elfedné előlünk. Na mindezt hozza a *Picasso*.

```java
public class ImageCache {

    private static final ImageCache ourInstance = new ImageCache();

    public static ImageCache getInstance() {
        return ourInstance;
    }

    private ImageCache() {
    }

    private Map<String,Bitmap> cache = new HashMap<>();

    public void put(String url, Bitmap bitmap) {
        if (!cache.containsKey(url)) {
            cache.put(url, bitmap);
        }
    }

    public Bitmap get(String url) {
        if (cache.containsKey(url)) {
            return cache.get(url);
        }
        return null;
    }

    public boolean contains(String url) {
        return cache.containsKey(url);
    }
}
```

A használatánál is oda kellett azért figyelni, nem iazán rejtetette el a részleteket. Ráadásul azért maradtak is ki részletek.
```java
if (!ImageCache.getInstance().contains(recommendation.getImageURL())) {
    new DownloadImageTask(viewHolder.foodImageView).execute(recommendation.getImageURL());
} else {
    viewHolder.foodImageView.setImageBitmap(ImageCache.getInstance().get(recommendation.getImageURL()));
}
```

## Az új út Picassoval

Picassora átállni azonban kvázi fájdalommentes, és a kimaradt use caseket is szépen lefedi:

```kotlin
Picasso.with(getContext())
  .load(recommendation.getImageURL())
  .placeholder(R.drawable.food)
  .error(R.drawable.food)
  .into(viewHolder.foodImageView, new Callback() {
      @Override
      public void onSuccess() {
      }

      @Override
      public void onError() {
          Log.e(TAG, "Error downloading image from " + recommendation.getImageURL());
      }
});
```

Nem kifejezetten bonyolult a kód, viszont nem fed le mindent, amit elvártunk tőle, ezen még azért dolgozni kellene.

Helyette inkább bevezetjük a Picassot.

Látványosan egyszerűbb így, ugye.