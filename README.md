# ArtQR Fusion

ArtQR Fusion, logo veya artwork gorselini okunabilir QR kod ile birlestiren Google Colab tabanli bir ticari teslim pipeline'idir. Projenin amaci, Fiverr, Upwork, Bionluk ve benzeri freelancer platformlarinda satilabilecek profesyonel "branded / artistic QR code" hizmetleri icin hizli, test edilebilir ve paketlenmis cikti uretmektir.

Bu proje sadece logoyu QR kodun ortasina yapistirmaz. Notebook, logo maskesini analiz eder, QR modul cizimini logo ve kontrast alanlarina gore uyarlar, farkli adaylar uretir, otomatik QR okuma testleri yapar ve musteriye teslim edilebilecek ZIP paketi hazirlar.

## English Summary

ArtQR Fusion is a Google Colab workflow for creating readable, logo-aware, artistic QR codes for freelance delivery. It combines a client logo or artwork with a scannable QR code, generates multiple visual candidates, validates readability with QR decoders, and exports a commercial delivery package.

It is designed for sellers who want to offer branded QR codes for restaurants, business cards, product labels, social media profiles, events, packaging, luxury brands, and marketing campaigns.

## Projenin Amaci

Bu repo, freelancer olarak sunulabilecek su hizmeti destekler:

> Markaniza ozel, logolu, okunabilir, baskiya ve dijital kullanima hazir sanatsal QR kod tasarimi.

Kullanim senaryolari:

- Restoran menu QR kodlari
- Instagram, Linktree, web sitesi veya WhatsApp QR kodlari
- Kartvizit ve kurumsal kimlik QR kodlari
- Urun etiketi ve ambalaj QR kodlari
- Etkinlik bileti, davetiye ve kampanya QR kodlari
- Luks marka, sosyal medya ve portfolyo QR gorselleri
- Wi-Fi, vCard, e-posta, telefon, SMS, konum ve takvim QR kodlari

## What This Project Offers

- Logo-aware artistic QR generation
- Clean QR and fused/artistic QR outputs
- Multiple style presets for commercial use
- QR readability checks with decoder testing
- High-resolution PNG output up to 4096 px
- Print-ready PNG/PDF options
- Transparent PNG and JPG export options
- Clean SVG/EPS vector QR export for base QR
- Social preview image generation
- Client delivery brief, quality summary, test report, and ZIP package

## Notebook Ozeti

Ana dosya: `qrcode_logo_merge.ipynb`

Notebook akis olarak su isi yapar:

1. Colab ortaminda gerekli kutuphaneleri kurar.
2. Musteri logosu veya artwork dosyasini yukler.
3. QR icine yazilacak veriyi olusturur veya hazir QR gorselini kullanir.
4. Kalite profilini ve servis paketini secer.
5. Logo/artwork gorselini hazirlar.
6. Logo-aware QR maskesi ve temiz QR uretir.
7. Farkli fusion guclerinde aday gorseller olusturur.
8. Adaylari QR okuyucularla test eder.
9. En iyi okunabilir final gorseli secer.
10. Ticari teslim dosyalarini ve `qr_fusion_delivery.zip` paketini hazirlar.

## Desteklenen QR Icerikleri

Notebook su QR veri tiplerini destekler:

- URL
- Duz metin
- E-posta
- Telefon
- SMS
- WhatsApp
- Wi-Fi
- Konum
- vCard
- Takvim etkinligi
- Raw/custom QR payload

## Stil ve Portfolyo Presetleri

Hazir portfolyo ornekleri:

- `restaurant_qr`
- `instagram_linktree_qr`
- `business_card_qr`
- `product_label_qr`
- `luxury_brand_qr`
- `event_ticket_qr`
- `colorful_logo_qr`
- `black_white_clean_qr`
- `halftone_logo_qr`
- `social_media_preview_qr`

Hazir stil presetleri:

- `business_clean`
- `luxury`
- `colorful`
- `restaurant`
- `event`
- `product_label`
- `social_media`
- `black_white_clean`
- `halftone_logo`

## Freelancer Paketleri

### Basic

Basit ve temiz logolu QR teslimi.

- Clean/branded QR
- Final PNG/JPG
- Hazir okunabilir QR
- Temel test raporu
- Dijital kullanim icin uygun teslim

### Standard

Web, sosyal medya ve baski icin daha guclu teslim paketi.

- Basic paket icerigi
- Ek stil varyasyonlari
- Print-ready PNG/PDF
- Sosyal medya onizleme gorseli
- Clean QR SVG
- Musteri teslim notu

### Premium

En iyi gorsel kalite ve ticari teslim icin onerilen paket.

- Logo-aware halftone final QR
- Yuksek cozunurluklu 4096 px cikti
- Transparent PNG
- Layout pack
- Print-ready dosyalar
- Test raporu
- Client quality summary
- Gig readiness checklist
- Teslime hazir ZIP paketi

## Fiverr / Upwork / Bionluk Icin Ilan Taslagi

### Turkce Baslik

Markaniza Ozel Okunabilir Logolu ve Sanatsal QR Kod Tasarlayacagim

### English Title

I will design a scannable custom branded QR code with your logo

### Turkce Aciklama

Markaniz, restoraniniz, kartvizitiniz, urun etiketiniz veya sosyal medya hesabiniz icin profesyonel, okunabilir ve logolu QR kod tasarimi hazirliyorum.

Standart QR kodlar genellikle sade ve markadan kopuk gorunur. Bu hizmette logonuzu veya artwork dosyanizi QR kod ile gorsel olarak birlestiriyorum. QR kodunuz hem markali gorunur hem de okuma testi yapilarak teslim edilir.

Teslim edebilecegim dosyalar:

- Yuksek cozunurluklu PNG
- JPG
- Transparent PNG
- Print-ready PNG/PDF
- Clean QR SVG
- Sosyal medya onizleme gorseli
- QR test raporu
- Teslim ZIP paketi

Lutfen siparis vermeden once logonuzu, QR icine yazilacak link/metin bilgisini ve kullanacaginiz alani gonderin. Ornek: menu, kartvizit, urun etiketi, web sitesi, Instagram, WhatsApp, etkinlik bileti.

### English Description

I will create a custom branded QR code for your business, restaurant, product label, business card, event, social media profile, or marketing campaign.

Instead of placing a logo on top of a basic QR code, I use a logo-aware workflow that blends your logo or artwork with a scannable QR design. Each final delivery is tested for readability before delivery.

Possible delivery files:

- High-resolution PNG
- JPG
- Transparent PNG
- Print-ready PNG/PDF
- Clean QR SVG
- Social media preview
- QR test report
- Delivery ZIP package

Please send your logo/artwork, the link or data for the QR code, and where you plan to use it, such as menu, business card, product label, website, Instagram, WhatsApp, or event ticket.

## Musteriden Istenilecek Bilgiler

- Logo veya artwork dosyasi
- QR icine yazilacak link, metin veya iletisim bilgisi
- Kullanim alani: baski, web, sosyal medya, ambalaj, menu vb.
- Tercih edilen renk veya marka rengi
- Istenen dosya formatlari
- Baskiya gidecekse olcu ve DPI bilgisi

## Okunabilirlik ve Kalite Notu

Notebook uretilen QR dosyalarini mevcut decoder motorlariyla test eder:

- `pyzbar` / ZBar
- OpenCV QR detector
- ZXing C++ uygun oldugunda

Freelancer ilaninda kullanilabilecek guvenli ifade:

> Final QR kodu teslimden once okuma testinden geciriyorum ve test raporu ekleyebiliyorum. Baskili kullanimlarda kagit, murekkep, boyut, isik ve yuzey taramayi etkileyebilecegi icin toplu baski oncesi bir deneme baskisi okutmanizi oneririm.

En iyi sonuc icin kisa URL, temiz logo, yuksek kontrast ve QR hata duzeltme seviyesi `H` onerilir. Cok uzun linkler QR kodu daha yogun yapar ve sanatsal tasarim alanini azaltir.

## Teslim Dosyalari

`qr_fusion_delivery.zip` paketi ayarlara gore su dosyalari icerebilir:

- `final_readable_art_qr.png`
- `clean_qr.png`
- `prepared_artwork.png`
- `commercial/halftone_logo_qr.png`
- `commercial/styled_clean_qr.png`
- `commercial/art_qr_soft.png`
- `commercial/art_qr_balanced.png`
- `commercial/art_qr_strong.png`
- `commercial/print_ready_...dpi.png`
- `commercial/print_ready_...dpi.pdf`
- `commercial/final_readable_art_qr.jpg`
- `commercial/clean_qr_vector.svg`
- `commercial/clean_qr_vector.eps`
- `commercial/social_preview_1080.png`
- `commercial/qr_test_report.txt`
- `commercial/customer_delivery_brief.txt`
- `commercial/client_quality_summary.txt`
- `commercial/gig_readiness_checklist.txt`
- `delivery_manifest.txt`

Onemli vektor notu: Sanatsal logo-fused final genellikle yuksek cozunurluklu raster PNG/JPG/PDF olarak teslim edilir. Clean base QR SVG/EPS olarak verilebilir. Sanatsal halftone final, manuel vektorlestirme ve tekrar test yapilmadan "tam editable vector" olarak satilmamalidir.

## Colab Gereksinimleri

Notebook Google Colab odaklidir ve kurulum hucreleri su paketleri hazirlar:

```bash
apt-get install -y libzbar0
pip install "Pillow<12" opencv-python pyzbar segno numpy zxing-cpp
```

Hedef akis Colab oldugu icin ayri bir lokal kurulum zorunlu degildir.

## Revizyon Politikasi Onerisi

- Basic: 1 revizyon
- Standard: 2 revizyon
- Premium: 3 revizyon

Link degisikligi, logo degisikligi ve tamamen yeni stil istegi ayni revizyon gibi degerlendirilmemelidir. Bu ayrimi ilanda net yazmak, freelancer platformlarinda is kapsamini korur.

## Rakiplere Gore Konumlandirma

Fiverr, Upwork ve Bionluk gibi platformlarda cok sayida satici basit "logo in the center" QR kod hizmeti sunar. Bu proje o tarz hizmetlerden farkli olarak su noktalara odaklanir:

- Logo/artwork ile QR arasinda daha dogal gorsel birlesim
- Halftone ve module-level stil uretimi
- Farkli adaylar arasindan okunabilir final secimi
- Otomatik QR test raporu
- Tek dosya yerine ticari teslim paketi
- Dijital, sosyal medya ve baski kullanimi icin ayri ciktilar
- Portfolyo ornegi uretmeye uygun preset sistemi

Eksik veya gelistirilebilecek taraflar:

- Web arayuzu henuz yok; su an Colab notebook olarak calisiyor.
- Musteriye gosterilecek hazir demo galeri repo icinde yok.
- Gercek cihazlarla manuel test yine teslim oncesi yapilmali.
- Artistic final raster ciktidir; tamamen vektor sanatsal final icin ek gelistirme gerekir.
- Freelancer ilaninda daha guclu gorunmek icin 10-20 adet ornek portfolyo gorseli uretilmeli.
- Siparis surecini hizlandirmak icin hazir brief formu ve fiyat tablosu eklenebilir.

## Onerilen Sonraki Eklemler

Freelancer platformlarinda daha kolay is almak icin projeye su eklemeler yapilabilir:

- `examples/` klasorune restoran, kartvizit, urun etiketi ve sosyal medya ornekleri
- Before/after gorselleri
- Fiverr gig kapak gorseli
- Bionluk hizmet aciklamasi ve paket fiyat metni
- Musteri brief formu
- Otomatik demo galeri ureten ek notebook hucreleri
- Tek komutla portfolyo paketi ureten script

## Kullanim Notlari

En iyi sonuc icin:

- Temiz, yuksek cozunurluklu ve kontrastli logo kullanin.
- Cok uzun URL yerine kisa URL tercih edin.
- Baskiya gitmeden once final QR kodu telefonda manuel okutun.
- Premium teslimlerde `service_tier = premium` ve `quality_mode = en_yuksek_kalite` kullanin.

## Lisans

Lisans bilgisi icin `LICENSE` dosyasina bakin.
