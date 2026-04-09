# Görev Takip Uygulaması (To-Do MVP)

## Proje Özeti

Firebase Firestore destekli, tek sayfalık (vanilla HTML/CSS/JS) bir görev takip uygulaması yaz. Framework kullanma — saf HTML dosyası olsun, `index.html` tek dosyada çalışsın.

---

## Teknik Yığın

- **Frontend:** Vanilla HTML + CSS + JavaScript (framework yok)
- **Veritabanı:** Firebase Firestore (compat SDK v10, CDN üzerinden)
- **Çalışma şekli:** Tek `index.html` dosyası — backend yok, build adımı yok

---

## Özellikler (MVP Kapsamı)

### Görev Yönetimi
- Görev ekleme: başlık (zorunlu), öncelik seviyesi, son tarih (due date), not/açıklama
- Görevi tamamlandı olarak işaretleme (toggle)
- Görevi silme

### Öncelik Sistemi
Görevler üç gruba ayrılır ve ekranda bu gruplara göre listelenir:
- 🔴 **Yüksek** öncelik
- 🟡 **Orta** öncelik
- 🟢 **Düşük** öncelik

### Son Tarih (Due Date)
- Tarih seçici ile girilir
- Tarihi geçmiş ve tamamlanmamış görevler "gecikmiş" olarak kırmızıyla işaretlenir

### Filtreleme
Header'da tab butonları: **Tümü / Aktif / Tamamlanan**

### İstatistik Çubuğu
Liste üstünde: toplam görev sayısı — tamamlanan — bekleyen

### Firebase Yapılandırma Ekranı
Uygulama ilk açıldığında kullanıcıdan şu bilgileri isteyen bir yapılandırma formu göster:
- `apiKey`
- `authDomain`
- `projectId`
- `appId`

Kullanıcı "Bağlan" butonuna tıklayınca Firebase'i başlat ve uygulamaya geç. Config bilgilerini `localStorage`'a kaydet — sonraki ziyarette otomatik bağlanılsın.

### Gerçek Zamanlı Sync
`onSnapshot` ile Firestore'u dinle — değişiklikler sayfayı yenilemeden anında yansısın.

---

## Firestore Koleksiyon Yapısı

**Koleksiyon adı:** `tasks`

Her belge şu alanları içersin:

```
title       : string   (görev başlığı)
priority    : string   ("high" | "med" | "low")
due         : string   (ISO tarih "YYYY-MM-DD", opsiyonel)
note        : string   (açıklama, opsiyonel)
done        : boolean  (varsayılan: false)
createdAt   : timestamp (serverTimestamp)
```

---

## UI / Tasarım Gereksinimleri

### Genel Atmosfer
- Sıcak, krem/bej tonlarında arka plan (`#f5f2eb`)
- Beyaz/açık yüzey kartlar
- Serif display font (başlık için) + sans-serif (gövde) kombinasyonu
- Google Fonts'tan yüklensin (DM Serif Display + DM Sans önerilir)

### Görev Kartı Anatomisi
```
[ ○ ]  Görev başlığı
       [ÖNCELİK BADGE] [📅 Tarih]
       *not metni italik*        [✕ sil]
```
- Kart hover'da hafif gölge alır
- Tamamlanan görevde başlık üstü çizili, kart soluk

### Öncelik Renkleri
- Yüksek: `#c84b2f` (kırmızımsı)
- Orta: `#d4873a` (amber)
- Düşük: `#5a8a5e` (yeşil)

### Animasyon
- Yeni eklenen görev yukarıdan kayarak gelir (`slideIn` keyframe, 200ms)
- Toast bildirimi (sağ alt köşe): görev eklendi / silindi mesajları

### Responsive
Mobil uyumlu olsun — 600px altında padding küçülsün, filter tablar gizlensin.

---

## Dosya Çıktısı

Tek bir `index.html` dosyası. İçinde:
1. `<style>` bloğu — tüm CSS
2. HTML yapısı — config ekranı + ana uygulama
3. Firebase SDK `<script>` tagları (CDN, compat v10)
4. `<script>` bloğu — tüm JS mantığı

---

## Kullanıcı Akışı

```
Sayfa açılır
    → Config formu gösterilir
    → Kullanıcı Firebase bilgilerini girer
    → "Bağlan" tıklanır
    → Firebase init edilir, Firestore dinlenmeye başlar
    → Ana uygulama görünür
        → Görevler öncelik gruplarına göre listelenir
        → Kullanıcı görev ekler / tamamlar / siler
        → Değişiklikler Firestore'a yazılır, onSnapshot ile UI güncellenir
```

---

## Geliştirici Notları

- Firestore güvenlik kuralları başlangıçta "test modunda" açık bırakılabilir
- `firebase.apps.length` kontrolü ile çift init önlenir
- XSS'e karşı kullanıcı girdileri `escHtml()` ile temizlenir
- Tarih karşılaştırmaları ISO string ile yapılır (`"YYYY-MM-DD"`)