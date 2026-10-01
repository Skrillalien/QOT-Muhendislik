# QOT Muhasebe

QOT Muhasebe, QOT bünyesindeki ortak kasa, harcama, gelir/gider, filament stok ve finansal kayıtların takip edilmesi için geliştirilmiş basit ve Firebase tabanlı bir web uygulamasıdır.

Uygulama, ortak kullanım için tasarlanmıştır ve verileri gerçek zamanlı olarak Firebase Firestore üzerinde saklar.

## Özellikler

### 🔐 Google ile Giriş

Uygulama Google Authentication kullanır.

- Google hesabı ile giriş
- Oturum durumunun otomatik kontrolü
- Çıkış yapabilme
- Kullanıcı adı/e-posta bilgisinin arayüzde gösterilmesi

Firebase Authentication ve Firestore kullanımı uygulamanın temel altyapısını oluşturur.

---

## 💰 Kasa

Kasa ekranı şirketin mevcut nakit durumunu takip etmek için kullanılır.

### Desteklenen işlemler

- Mevcut kasa bakiyesini görüntüleme
- Ortak tarafından kasaya para yatırma
- Kasa hareketlerini görüntüleme
- Kasa giriş/çıkışlarını tarih bazında takip etme
- Ortaklardan beklenen ödemeleri görüntüleme
- Beklenen ödemeler geldiğinde oluşacak tahmini bakiyeyi görüntüleme
- Manuel bakiye düzeltmesi yapma

Kasa bakiyesi, Firestore'daki kasa hareketleri üzerinden hesaplanır.

```text
Kasa Bakiyesi =
Toplam Girişler - Toplam Çıkışlar
```

---

## 🧾 Harcamalar

Harcamalar bölümü şirket adına yapılan giderlerin ve ortakların bu giderlerdeki paylarının takip edilmesini sağlar.

Bir harcama oluşturulduğunda:

1. Harcamanın toplam tutarı belirlenir.
2. Kullanılabilir kasa miktarı hesaplanır.
3. Kasanın karşılayabildiği tutar belirlenir.
4. Kasanın karşılayamadığı tutar üç ortağa eşit olarak bölünür.
5. Her ortağın ödeme durumu takip edilir.

Mevcut ortaklar:

- Berk
- Melisa
- Dolunay

### Ortak ödeme takibi

Her ortak için:

- Bekliyor
- Ödendi

durumu tutulur.

Ortak ödeme yaptığında ödeme ayrıca kasa hareketi olarak kaydedilir.

Ödeme geri alınmak istendiğinde ilgili kasa hareketi silinerek ödeme durumu tekrar `Bekliyor` yapılabilir.

---

## 📊 Aylık Gelir / Gider

Aylık finansal kayıtların tutulduğu bölümdür.

Desteklenen kayıt türleri:

- Gelir
- Gider

Her kayıt için:

- Tarih
- Kategori
- Tutar
- Not

bilgileri tutulabilir.

Seçilen ay için:

```text
Toplam Gelir
Toplam Gider
Net
```

değerleri hesaplanır.

> Not: Mevcut sürümde genel gelir/gider kayıtları ile ortak harcamaları ayrı veri koleksiyonlarında tutulmaktadır. Gelecekte bunların kullanıcı arayüzünde tek bir finans ekranında birleştirilmesi planlanabilir.

---

## 🖨️ Filament Stok Takibi

Filament bölümü 3D yazıcı filamentlerinin stok takibi için kullanılır.

### Desteklenen filament türleri

- PLA
- PETG
- ABS
- ASA
- TPU
- Diğer

Her filament için:

- Renk
- Malzeme
- Marka
- Kalan gram
- Kullanım geçmişi

bilgileri tutulur.

### Filament kullanımı

Stoktan filament kullanıldığında kullanılan gram miktarı girilir ve mevcut stok otomatik olarak azaltılır.

Örneğin:

```text
1000 g → 150 g kullanım → 850 g
```

Filamente yeni miktar da eklenebilir.

Stok `0 g` veya altına düştüğünde filament `Bitti` olarak gösterilir.

---

## 🎨 Renk Kütüphanesi

Uygulamada hazır filament renklerinin yanında özel renkler de eklenebilir.

Hazır renkler arasında:

- Siyah
- Beyaz
- Gri
- Kırmızı
- Turuncu
- Sarı
- Yeşil
- Mavi
- Lacivert
- Mor
- Pembe
- Kahverengi
- Şeffaf
- Altın

bulunur.

Ayarlar bölümünden özel renk adı ve HEX değeri ile yeni renkler eklenebilir.

---

## ⚙️ Ayarlar

Ayarlar bölümünde:

### Kasa bakiyesi düzeltme

Mevcut kasa bakiyesi ile gerçek kasa bakiyesi arasında fark varsa yeni gerçek bakiye girilebilir.

Uygulama farkı otomatik olarak `Manuel düzeltme` adıyla kasa hareketine işler.

### Filament renkleri

Özel filament renkleri eklenebilir ve daha sonra silinebilir.

---

## 💾 Yedekleme

Uygulamadaki veriler JSON formatında dışarı aktarılabilir.

Yedek dosyası:

```text
qot-yedek-YYYY-MM-DD.json
```

formatında oluşturulur.

Yedek içerisinde:

- expenses
- movements
- txns
- filaments
- colors

koleksiyonlarındaki kayıtlar bulunur.

### Geri yükleme

Daha önce oluşturulmuş `qot-v1` formatındaki JSON dosyası tekrar yüklenebilir.

Mevcut kaydın ID'si aynıysa ilgili kayıt üzerine yazılır.

Yeni kayıtlar eklenir; JSON'da bulunmayan mevcut kayıtlar otomatik olarak silinmez.

---

# 🏗️ Teknik Yapı

Uygulama şu teknolojileri kullanır:

- HTML5
- CSS3
- JavaScript (ES Modules)
- Firebase Authentication
- Firebase Firestore

Firebase modülleri Google'ın CDN'i üzerinden kullanılmaktadır.

## Firebase

Uygulama aşağıdaki Firestore koleksiyonlarını kullanır:

```text
expenses
movements
txns
filaments
colors
```

Uygulama başlatıldığında bu koleksiyonlar gerçek zamanlı `onSnapshot` listener'ları ile dinlenir. Böylece Firestore'daki değişiklikler uygulamaya otomatik olarak yansır.

---

# 📁 Proje Yapısı

Mevcut sürüm tek HTML dosyası üzerinden çalışmaktadır:

```text
QOT-Muhasebe/
│
└── index.html
```

`index.html` içerisinde:

- HTML arayüzü
- CSS stilleri
- Firebase bağlantısı
- Authentication
- Firestore işlemleri
- Uygulama state'i
- Sayfa render fonksiyonları
- CRUD işlemleri
- JSON yedekleme/geri yükleme

bulunmaktadır.

---

# 🔄 Veri Akışı

Genel yapı:

```text
Google Authentication
        │
        ▼
Firebase Authentication
        │
        ▼
QOT Muhasebe
        │
        ├── Kasa
        │     └── movements
        │
        ├── Harcamalar
        │     └── expenses
        │
        ├── Gelir / Gider
        │     └── txns
        │
        ├── Filament
        │     └── filaments
        │
        └── Renkler
              └── colors
```

---

# 🔒 Güvenlik

Uygulama Google Authentication ile kullanıcı doğrulaması yapar.

Firestore erişimi Firebase Security Rules tarafından kontrol edilmelidir.

Uygulamada kullanıcı giriş yaptıktan sonra Firestore koleksiyonlarına erişim sağlanır. Firestore erişiminde yetki problemi olduğunda uygulama kullanıcıya ilgili hata bilgisini gösterir.

> Firebase API key'in frontend uygulamalarında bulunması tek başına gizli anahtar olarak değerlendirilmemelidir. Asıl veri güvenliği Firebase Authentication ve Firestore Security Rules ile sağlanmalıdır.

---

# 🚀 Kurulum

## 1. Projeyi indirin

```bash
git clone <repository-url>
cd QOT-Muhasebe
```

## 2. Firebase projesini oluşturun

Firebase üzerinde:

- Authentication
- Google Sign-In
- Firestore Database

özelliklerini etkinleştirin.

## 3. Firebase yapılandırmasını ekleyin

`index.html` içerisindeki Firebase configuration bölümünü kendi Firebase projenizin bilgileriyle değiştirin.

```js
const app = initializeApp({
  apiKey: "...",
  authDomain: "...",
  projectId: "...",
  storageBucket: "...",
  messagingSenderId: "...",
  appId: "..."
});
```

## 4. Firestore Security Rules

Firestore Security Rules içerisinde uygulamayı kullanmasına izin verilen kullanıcıların yetkilendirilmesi gerekir.

Uygulamanın kendisi, Firestore'dan veri okun
