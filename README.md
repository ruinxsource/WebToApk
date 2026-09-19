# Converter WebToApk

Ubah situs web apa pun menjadi aplikasi Android mandiri dengan cepat menggunakan [Sketchware Pro](https://github.com/Sketchware-Pro/Sketchware-Pro/releases) atau pembuat aplikasi berbasis Java. Repositori ini berisi kode WebView yang dioptimalkan untuk pemutar video pihak ketiga, cookie, dan navigasi yang mulus.

## Fitur Utama

- **Fullscreen Video Support** — Mode layar penuh (`CustomView`) otomatis untuk pemutar media web.
- **Pull-to-Refresh** — Usap ke bawah dari atas halaman untuk reload.
- **Smart Back Navigation** — Tombol kembali menavigasi riwayat web dulu, baru menutup aplikasi.
- **Optimized WebViewClient** — Aset, CDN, dan player pihak ketiga tetap bisa dimuat.
- **Cookie & JS Enabled** — JavaScript, DOM storage, dan cookie pihak ketiga aktif.

---

## Implementasi (Sketchware Pro)

### 1. Permission Manifest
Tambahkan di **App Manifest**:
```xml
<uses-permission android:name="android.permission.INTERNET" />
```

### 2. Blok `onCreate`

Event **onCreate** → *Add Source Directly* → tempel:

```java
final String URL="https://nekopoi.care";final android.webkit.WebView w=new android.webkit.WebView(this);setContentView(w);w.getSettings().setJavaScriptEnabled(true);w.getSettings().setDomStorageEnabled(true);w.getSettings().setSupportZoom(true);w.getSettings().setBuiltInZoomControls(true);w.getSettings().setDisplayZoomControls(false);w.getSettings().setLoadsImagesAutomatically(true);w.getSettings().setMediaPlaybackRequiresUserGesture(false);android.webkit.CookieManager.getInstance().setAcceptCookie(true);android.webkit.CookieManager.getInstance().setAcceptThirdPartyCookies(w,true);w.setOnTouchListener(new android.view.View.OnTouchListener(){float y;public boolean onTouch(android.view.View v,android.view.MotionEvent e){if(e.getAction()==0)y=e.getY();if(e.getAction()==1&&e.getY()-y>500&&w.getScrollY()==0)w.reload();return false;}});w.setWebViewClient(new android.webkit.WebViewClient(){public boolean shouldOverrideUrlLoading(android.webkit.WebView v,String u){v.loadUrl(u);return true;}});w.setWebChromeClient(new android.webkit.WebChromeClient(){android.view.View v;CustomViewCallback c;public void onShowCustomView(android.view.View i,CustomViewCallback k){v=i;c=k;getWindow().getDecorView().setSystemUiVisibility(5894);setRequestedOrientation(4);((android.view.ViewGroup)getWindow().getDecorView()).addView(v);}public void onHideCustomView(){((android.view.ViewGroup)getWindow().getDecorView()).removeView(v);getWindow().getDecorView().setSystemUiVisibility(0);setRequestedOrientation(2);if(c!=null)c.onCustomViewHidden();}});w.loadUrl(URL);
```

### 3. Blok `onBackPressed`

Event **onBackPressed** → *Add Source Directly* → tempel:

```java
android.webkit.WebView webview1=(android.webkit.WebView)getWindow().getDecorView().getRootView().findFocus();if(webview1!=null&&webview1.canGoBack()){webview1.goBack();}else{finish();}
```

---

## Catatan

- Ganti nilai `URL` sesuai situs yang ingin dikonversi.
- Jangan menambah WebView dari palet Sketchware — kode sudah membuatnya sendiri.
- Untuk situs video/streaming, jangan blokir domain lain (`return !u.startsWith(URL)`), agar CDN dan player eksternal tetap jalan.

## Lisensi

MIT License
