# qrcode_logo_merge.ipynb Analiz ve Ayar Rehberi

Bu dokuman, `qrcode_logo_merge.ipynb` notebook'unun kod analizini, tum input/parametre aciklamalarini ve en iyi cikti icin onerilen ayarlari icerir.

Notebook, yuklenen bir gorseli QR kodun siyah/beyaz modul yapisiyla parlaklik uzerinden birlestirir. Sonra farkli guc degerleriyle adaylar uretir ve `pyzbar` ile OpenCV kullanarak gercekten okunup okunmadigini test eder.

## Genel Akis

1. Gerekli paketler kurulur.
2. Kullanici bir gorsel yukler.
3. QR verisi ve gorsel/okunabilirlik ayarlari girilir.
4. Gorsel kare formata hazirlanir.
5. Temiz QR uretilir.
6. QR maskeleri cikarilir.
7. Gorselin parlakligi QR modullerine gore degistirilerek sanat/QR birlesimi yapilir.
8. Birden fazla aday denenir.
9. Ilk okunabilir aday final secilir.
10. Final, temiz QR ve hazirlanmis gorsel zip olarak indirilir.

## Hucre 0: Kurulum

```python
!apt-get -qq update
!apt-get -qq install -y libzbar0
!pip -q install --upgrade "Pillow<12" opencv-python pyzbar segno numpy
```

Bu hucre Colab ortamini hazirlar.

- `libzbar0`: `pyzbar` QR okuyucusunun sistem bagimliligidir.
- `Pillow`: gorsel isleme icin kullanilir.
- `opencv-python`: ikinci QR test motoru ve renk uzayi islemleri icin kullanilir.
- `pyzbar`: QR okuma testi icin kullanilir.
- `segno`: QR kod uretir.
- `numpy`: piksel/mask islemleri icin kullanilir.

## Hucre 1: Importlar

```python
import os
import cv2
import math
import zipfile
import numpy as np
import segno
from PIL import Image, ImageEnhance, ImageFilter
from pyzbar.pyzbar import decode as zbar_decode
from IPython.display import display
from google.colab import files
```

Burada tum kutuphaneler yuklenir. Kod Colab odaklidir cunku `google.colab.files` kullanir.

## Hucre 2: Gorsel Yukleme

```python
uploaded = files.upload()
```

Kullanicidan gorsel alir. Ilk yuklenen dosyayi `artwork_path` olarak secer.

```python
artwork_preview = Image.open(artwork_path).convert("RGB")
display(artwork_preview)
```

Gorsel RGB'ye cevrilir ve onizlenir.

Dikkat edilmesi gereken nokta: Birden fazla dosya yuklenirse sadece ilk dosya kullanilir.

## Hucre 3: Tum Inputlar / Parametreler

### qr_data

```python
qr_data = "https://example.com"
```

QR icine yazilacak veri. URL, metin, urun linki veya kampanya linki olabilir.

En iyi secim: kisa URL kullanmak.

Cunku veri uzadikca QR modul sayisi artar, desen siklasir ve sanatla birlesince okunabilirlik zorlasir.

Oneri:

```python
qr_data = "https://kisa.link/abc"
```

### output_size

```python
output_size = 3072
```

Cikti kare piksel boyutu.

Aralik: `1024 - 4096`

En iyi secim:

```python
output_size = 3072
```

Baski veya cok net cikti icin:

```python
output_size = 4096
```

Web/sosyal medya icin `2048` yeterli olabilir. En kaliteli ve dengeli calisma icin `3072` iyi bir baslangictir.

### error_level

```python
error_level = "h"
```

QR hata duzeltme seviyesi.

Secenekler:

- `l`: dusuk
- `m`: orta
- `q`: yuksek
- `h`: en yuksek

En iyi secim:

```python
error_level = "h"
```

Bu tarz gorsel birlestirmede en mantikli secim `h` seviyesidir.

### quiet_zone

```python
quiet_zone = 4
```

QR'nin dis beyaz bosluk alanidir.

Aralik: `4 - 10`

En iyi secim:

```python
quiet_zone = 4
```

Cogu standart QR icin minimum guvenli degerdir. Okunabilirlik problemi varsa:

```python
quiet_zone = 6
```

Cok estetik kaygi yoksa `6` daha guvenlidir.

### fit_mode

```python
fit_mode = "cover_crop"
```

Gorseli kareye sigdirma yontemidir.

Secenekler:

- `cover_crop`: Gorsel tum kareyi doldurur, kenarlardan kirpabilir.
- `contain_pad`: Gorsel tamamen gorunur, bos kalan yerler pad rengiyle doldurulur.

Fotograf, illustrasyon veya arka plan icin:

```python
fit_mode = "cover_crop"
```

Logo, ambalaj veya tam gorunmesi gereken gorsel icin:

```python
fit_mode = "contain_pad"
```

### pad_red, pad_green, pad_blue

```python
pad_red = 255
pad_green = 255
pad_blue = 255
```

`contain_pad` modunda bos alan rengidir.

Varsayilan beyaz:

```python
pad_red = 255
pad_green = 255
pad_blue = 255
```

QR okunabilirligi icin beyaz ya da cok acik renk en iyidir. Koyu pad rengi tavsiye edilmez.

### dark_strength_start, dark_strength_end, dark_strength_step

```python
dark_strength_start = 0.25
dark_strength_end = 0.85
dark_strength_step = 0.04
```

Kodun en kritik arama araligidir. QR'nin siyah modullerini gorsel uzerinde ne kadar karartacagini dener.

- `dark_strength_start`: ilk deneme gucu
- `dark_strength_end`: son deneme gucu
- `dark_strength_step`: denemeler arasi artis

Mevcut iyi genel ayar:

```python
dark_strength_start = 0.25
dark_strength_end = 0.85
dark_strength_step = 0.04
```

Daha kaliteli gorsel aramak icin:

```python
dark_strength_start = 0.20
dark_strength_end = 0.75
dark_strength_step = 0.02
```

Daha okunabilir sonuc icin:

```python
dark_strength_start = 0.35
dark_strength_end = 0.90
dark_strength_step = 0.03
```

Net tavsiye:

```python
dark_strength_start = 0.22
dark_strength_end = 0.82
dark_strength_step = 0.02
```

Bu daha fazla aday uretir ama daha iyi final bulma ihtimalini artirir.

### light_strength_ratio

```python
light_strength_ratio = 0.8
```

QR'nin beyaz modullerini ne kadar aydinlatacagini belirler.

En iyi genel secim:

```python
light_strength_ratio = 0.8
```

Gorsel cok koyuysa:

```python
light_strength_ratio = 0.9
```

Gorsel cok soluklasiyorsa:

```python
light_strength_ratio = 0.65
```

### module_contrast

```python
module_contrast = 0.7
```

Siyah ve beyaz moduller arasinda minimum kontrast baskisini belirler.

En kritik okunabilirlik parametrelerinden biridir.

Mevcut deger iyi:

```python
module_contrast = 0.7
```

Daha estetik/sanat baskin:

```python
module_contrast = 0.55
```

Daha okunabilir/QR baskin:

```python
module_contrast = 0.8
```

Net tavsiye:

```python
module_contrast = 0.70
```

Okunmazsa `0.80` denenmelidir.

### center_bias

```python
center_bias = 0.8
```

Her QR modulunun merkezine daha fazla etki verir, kenarlarda daha yumusak gecis birakir.

Bu estetik icin iyi bir tekniktir. QR okuyucular modul merkezlerinden daha cok faydalanir.

En iyi secim:

```python
center_bias = 0.8
```

Daha sert/okunabilir QR icin:

```python
center_bias = 0.6
```

Daha organik goruntu icin:

```python
center_bias = 0.85
```

### finder_boost

```python
finder_boost = 2
```

QR'nin uc kosesindeki buyuk konum bulucu kareleri guclendirir.

Okunabilirlik icin cok onemlidir.

En iyi secim:

```python
finder_boost = 2.0
```

Okunmazsa:

```python
finder_boost = 2.3
```

Estetik bozuluyorsa:

```python
finder_boost = 1.6
```

### quiet_zone_boost

```python
quiet_zone_boost = 0.7
```

QR cevresindeki bos alani beyaza yaklastirir.

En iyi genel secim:

```python
quiet_zone_boost = 0.7
```

Okunabilirlik sorunu varsa:

```python
quiet_zone_boost = 0.9
```

Sanatsal kenar istenirse ama risk artar:

```python
quiet_zone_boost = 0.5
```

### contrast_after

```python
contrast_after = 1.12
```

Birlesim sonrasi genel kontrasttir.

Iyi deger:

```python
contrast_after = 1.12
```

Cok soluk cikti icin:

```python
contrast_after = 1.20
```

Fazla sertlesirse:

```python
contrast_after = 1.05
```

### sharpness_after

```python
sharpness_after = 1.35
```

Birlesim sonrasi keskinliktir.

Iyi deger:

```python
sharpness_after = 1.35
```

Baski/QR netligi icin:

```python
sharpness_after = 1.5
```

Fazla yapay gorunurse:

```python
sharpness_after = 1.15
```

### save_all_candidates

```python
save_all_candidates = True
```

Tum adaylari kaydeder.

En iyi secim:

```python
save_all_candidates = True
```

Cunku en guzel aday ilk okunan aday olmayabilir.

### show_candidate_previews

```python
show_candidate_previews = True
```

Adaylari ekranda gosterir.

Kalite secimi yapilacaksa:

```python
show_candidate_previews = True
```

Hiz istenirse:

```python
show_candidate_previews = False
```

Cok fazla adayda Colab yavaslayabilir.

### download_final

```python
download_final = True
```

Final zip dosyasini indirir.

Colab icin:

```python
download_final = True
```

Yerel calismada bu satir sorun cikarabilir cunku `files.download` Colab'a ozeldir.

## Hucre 4: Ana Fonksiyonlar

### prepare_artwork

Gorseli kare hale getirir.

`cover_crop` secilirse gorsel kareyi doldurur, tasan yerleri kirpar.

`contain_pad` secilirse gorsel kirpilmaz, bosluklar renk ile doldurulur.

### make_qr_png

```python
qr = segno.make(data, error=error, boost_error=True)
```

QR kodu uretir. `boost_error=True`, mumkunse hata duzeltmeyi daha guvenli seviyeye tasimaya calisir.

```python
qr.save(... dark="black", light="white")
```

Temiz siyah/beyaz QR uretir.

Dikkat: `render_size` degiskeni hesaplanmis ama kullanilmiyor. Zararli degildir, sadece gereksizdir.

### get_qr_masks

QR gorselini siyah/beyaz maske olarak ayirir.

```python
dark_mask = arr < 128
light_mask = ~dark_mask
```

Siyah alanlara karartma, beyaz alanlara aydinlatma uygulanir.

### build_finder_mask

QR'nin uc kosesindeki buyuk kare alanlarini bulur.

Bu alanlar QR okuyucunun ilk baktigi yerlerdir. Kod burada bu bolgeleri daha guclu yapar.

### build_quiet_zone_mask

QR cevresindeki sessiz alani maskeler.

Bu alanin temiz/acik kalmasi QR okunabilirligi icin sarttir.

### build_module_center_weight

Her QR modulunun merkezinde etkiyi artirir, kenarlarda azaltir.

Bu iyi bir secimdir: QR okunabilirligi korunurken gorsel daha az blok blok gorunur.

### luminance_fusion

Notebook'un ana fonksiyonudur.

Gorsel RGB'den LAB renk uzayina cevrilir:

```python
lab = cv2.cvtColor(art, cv2.COLOR_RGB2LAB)
l = lab[:, :, 0]
```

Sadece parlaklik kanali degistirilir. Renkler buyuk olcude korunur.

Siyah QR modulleri:

```python
dark_target = np.minimum(l * (1.0 - strength_map), dark_floor)
```

Yani siyah alanlar karartilir.

Beyaz QR modulleri:

```python
light_target = np.maximum(l + (255.0 - l) * light_strength, light_floor)
```

Yani beyaz alanlar aydinlatilir.

Finder alanlari ayrica guclendirilir:

```python
strength_map[finder_mask] *= finder_boost
```

Sessiz alan beyaza yaklastirilir:

```python
l2[quiet_mask] = l2[quiet_mask] + (255.0 - l2[quiet_mask]) * quiet_boost
```

Sonra tekrar RGB'ye donulur.

### polish_image

Son gorsele kontrast, keskinlik ve Unsharp Mask uygular.

```python
ImageEnhance.Contrast
ImageEnhance.Sharpness
ImageFilter.UnsharpMask
```

Bu QR kenarlarini daha okunur yapar.

### test_qr_all

Final veya aday QR'yi iki motorla test eder:

1. `pyzbar`
2. OpenCV `QRCodeDetector`

Ikisinden biri dogru veriyi okursa sonuc basarilidir.

Bu iyi bir guvenlik kontroludur.

## Hucre 5: Hazirlik ve Temiz QR Testi

Bu hucre cikti klasorunu olusturur:

```python
os.makedirs("qr_fusion_outputs", exist_ok=True)
```

Gorsel hazirlanir:

```python
art_img = prepare_artwork(...)
```

Temiz QR uretilir:

```python
qr, qr_img = make_qr_png(...)
```

Sonra temiz QR test edilir.

Eger temiz QR bile okunmuyorsa islem durur. Bu dogru bir kontroldur.

Kucuk hata: Sonraki hata mesajlarinda "Hucre 7" deniyor ama aday uretimi aslinda notebook indeksine gore Hucre 6. Kullanici acisindan kucuk numara karisikligi var.

## Hucre 6: Aday Uretimi

```python
strength_values = np.arange(...)
```

Belirlenen `dark_strength_start`, `dark_strength_end`, `dark_strength_step` araliginda cok sayida aday uretir.

Her aday icin:

1. Gorsel ve QR birlestirilir.
2. Kontrast/keskinlik uygulanir.
3. Dosya kaydedilir.
4. QR okunabilirlik testi yapilir.
5. Basariliysa listeye eklenir.

Final secim mantigi:

```python
best_path, best_strength, best_result = passed_candidates[0]
```

Ilk okunan aday final secilir.

Bu okunabilirlik acisindan guvenli, ama estetik acisindan her zaman en iyi olmayabilir. Cunku daha sonraki adaylar daha belirgin ama belki daha guvenli olabilir.

En iyi pratik: `save_all_candidates=True` birakip gorsel olarak en iyi okunan adayi manuel secmek.

## Hucre 7: Aday Inceleme

Secili adayi, hazirlanmis gorseli ve temiz QR'yi yan yana gosterir.

```python
inspect_candidate_path = ""
```

Bos birakilirsa son aday incelenir.

Belirli aday incelemek icin ornek:

```python
inspect_candidate_path = "qr_fusion_outputs/candidate_08_strength_0.39.png"
```

## Hucre 8: Manuel Final Secimi

```python
manual_final_path = ""
```

Kullanici begendigi adayi manuel final yapabilir.

Ornek:

```python
manual_final_path = "qr_fusion_outputs/candidate_10_strength_0.43.png"
```

Kod once QR test eder. Okunmuyorsa final yapmaz. Bu iyi bir kontroldur.

## Hucre 9: Zip Teslim

Final tekrar test edilir.

Sonra su dosyalar ziplenir:

```text
final_readable_art_qr.png
clean_qr.png
prepared_artwork.png
```

Son olarak Colab'da indirme baslatilir.

## En Iyi Genel Ayar Seti

Kalite + okunabilirlik dengesi icin net onerilen ayar:

```python
qr_data = "https://kisa-url-kullan"
output_size = 3072
error_level = "h"
quiet_zone = 4

fit_mode = "cover_crop"
pad_red = 255
pad_green = 255
pad_blue = 255

dark_strength_start = 0.22
dark_strength_end = 0.82
dark_strength_step = 0.02
light_strength_ratio = 0.8
module_contrast = 0.7
center_bias = 0.8

finder_boost = 2.0
quiet_zone_boost = 0.7
contrast_after = 1.12
sharpness_after = 1.35

save_all_candidates = True
show_candidate_previews = True
download_final = True
```

## Maksimum Okunabilirlik Ayari

QR kesin okunsun, sanat biraz daha QR'ye feda edilebilir:

```python
output_size = 3072
error_level = "h"
quiet_zone = 6

dark_strength_start = 0.35
dark_strength_end = 0.90
dark_strength_step = 0.03
light_strength_ratio = 0.9
module_contrast = 0.8
center_bias = 0.65

finder_boost = 2.3
quiet_zone_boost = 0.9
contrast_after = 1.18
sharpness_after = 1.5
```

## Maksimum Estetik Ayari

Gorsel daha baskin olsun, ama okunabilirlik riski artar:

```python
output_size = 3072
error_level = "h"
quiet_zone = 4

dark_strength_start = 0.15
dark_strength_end = 0.65
dark_strength_step = 0.02
light_strength_ratio = 0.65
module_contrast = 0.55
center_bias = 0.85

finder_boost = 1.7
quiet_zone_boost = 0.6
contrast_after = 1.08
sharpness_after = 1.2
```

## Logo Icin En Dogru Secimler

Eger yuklenen gorsel bir logo ise:

```python
fit_mode = "contain_pad"
pad_red = 255
pad_green = 255
pad_blue = 255
```

Logo kirpilmamalidir. Bu yuzden `contain_pad` daha dogrudur.

Ayrica logo icin okunabilirlik adina:

```python
quiet_zone = 6
module_contrast = 0.75
finder_boost = 2.2
quiet_zone_boost = 0.85
```

Logo cok acik renkliyse `dark_strength_start` biraz artirilmalidir:

```python
dark_strength_start = 0.30
```

Logo cok koyuysa `light_strength_ratio` artirilmalidir:

```python
light_strength_ratio = 0.9
```

## Kodun Guclu Yanlari

- QR'yi iki farkli okuyucuyla test eder.
- Sadece parlaklik kanalini degistirerek renkleri korur.
- Finder pattern bolgelerini ayrica guclendirir.
- Quiet zone alanini korur.
- Birden fazla aday uretip otomatik test eder.
- Manuel final secimine izin verir.

## Eksikler / Iyilestirme Alanlari

- Ilk okunan aday otomatik final seciliyor; estetik olarak en iyi aday olmayabilir.
- `render_size` degiskeni kullanilmiyor.
- Hata mesajlarinda hucre numaralari biraz karisik.
- Colab disi ortamda `files.upload()` ve `files.download()` calismaz.
- Cok uzun `qr_data` kullanilirsa QR karmasiklasir ve sanatla birlesim zorlasir.
- Aday skorlamasi sadece okunabilir/okunamaz seklinde; kalite skoru yok.

## En Onemli Pratik Kural

En iyi sonuc icin su sirayi izle:

1. `qr_data` kisa olsun.
2. `error_level = "h"` kullan.
3. Logo ise `contain_pad`, fotograf/illustrasyon ise `cover_crop`.
4. `save_all_candidates = True` kalsin.
5. Ilk finali otomatik kabul etme; adaylari gorsel olarak incele.
6. Manuel final secimini kullan.
7. Finali mutlaka telefon kamerasi ile de test et.

Net en dengeli baslangic ayari:

```python
output_size = 3072
error_level = "h"
quiet_zone = 4
dark_strength_start = 0.22
dark_strength_end = 0.82
dark_strength_step = 0.02
module_contrast = 0.7
finder_boost = 2.0
```
