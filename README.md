# WebToApk Converter (Customized Edition)

Ubah situs web apa pun menjadi aplikasi Android mandiri dengan cepat menggunakan [Sketchware Pro](https://github.com/Sketchware-Pro/Sketchware-Pro/releases) atau pembuat aplikasi berbasis Java. Repositori ini berisi kode implementasi WebView yang dioptimalkan untuk menangani pemutar video pihak ketiga, penyimpanan cookie, dan navigasi yang mulus.

## Fitur Utama

- **Fullscreen Video Support** — Menangani mode layar penuh (`CustomView`) secara otomatis untuk pemutar media web.
- **Pull-to-Refresh** — Usap ke bawah dari posisi paling atas untuk memuat ulang halaman.
- **Smart Back Navigation** — Tombol kembali perangkat menavigasi riwayat halaman web terlebih dahulu sebelum menutup aplikasi.
- **Optimized WebViewClient** — Aset, CDN, dan pemutar video pihak ketiga dapat dimuat dengan lancar tanpa menutup aplikasi.
- **Cookie & JS Enabled** — JavaScript, penyimpanan DOM, dan cookie pihak ketiga aktif (penting untuk sesi dan pemutaran media).

---

## Panduan Implementasi (Sketchware Pro)

### 1. Permission Manifest/manager

Tambahkan di **App Manifest**:

```xml
<uses-permission android:name="android.permission.INTERNET" />
```

### 2. Blok `onCreate`

Event **onCreate** → *Add Source Directly* → tempel kode berikut:

```java
final String URL = "https://xhamster2.com/";
final android.webkit.WebView w = new android.webkit.WebView(this);
setContentView(w);

// Pengaturan WebView
w.getSettings().setJavaScriptEnabled(true);
w.getSettings().setDomStorageEnabled(true);
w.getSettings().setSupportZoom(true);
w.getSettings().setBuiltInZoomControls(true);
w.getSettings().setDisplayZoomControls(false);
w.getSettings().setLoadsImagesAutomatically(true);
w.getSettings().setMediaPlaybackRequiresUserGesture(false);
android.webkit.CookieManager.getInstance().setAcceptCookie(true);
android.webkit.CookieManager.getInstance().setAcceptThirdPartyCookies(w, true);

// Pull-to-Refresh
w.setOnTouchListener(new android.view.View.OnTouchListener(){
    float y;
    public boolean onTouch(android.view.View v, android.view.MotionEvent e){
        if(e.getAction() == 0) y = e.getY();
        if(e.getAction() == 1 && e.getY() - y > 500 && w.getScrollY() == 0) w.reload();
        return false;
    }
});

// Navigasi tetap di dalam aplikasi
w.setWebViewClient(new android.webkit.WebViewClient(){
    public boolean shouldOverrideUrlLoading(android.webkit.WebView v, String u){
        v.loadUrl(u);
        return true;
    }
});

// Fullscreen video
w.setWebChromeClient(new android.webkit.WebChromeClient(){
    android.view.View v;
    CustomViewCallback c;
    public void onShowCustomView(android.view.View i, CustomViewCallback k){
        v = i; c = k;
        getWindow().getDecorView().setSystemUiVisibility(5894);
        setRequestedOrientation(4);
        ((android.view.ViewGroup)getWindow().getDecorView()).addView(v);
    }
    public void onHideCustomView(){
        ((android.view.ViewGroup)getWindow().getDecorView()).removeView(v);
        getWindow().getDecorView().setSystemUiVisibility(0);
        setRequestedOrientation(2);
        if(c != null) c.onCustomViewHidden();
    }
});

w.loadUrl(URL);
```

### 3. Blok `onBackPressed`

Event **onBackPressed** → *Add Source Directly* → tempel kode berikut:

```java
android.webkit.WebView webview1 = (android.webkit.WebView)getWindow().getDecorView().getRootView().findFocus();
if(webview1 != null && webview1.canGoBack()){
    webview1.goBack();
} else {
    finish();
}
```

---

## Catatan

- Ubah nilai `URL` di baris pertama `onCreate` sesuai situs yang ingin dikonversi.
- Jangan menambahkan WebView dari palet Sketchware — kode sudah membuat WebView sendiri dengan `setContentView(w)`.
- Untuk situs video/streaming, **jangan** memblokir domain lain (`return !u.startsWith(URL)`), karena CDN dan player pihak ketiga biasanya beda domain.

---

## Lisensi

MIT License — bebas digunakan, dimodifikasi, dan didistribusikan.
