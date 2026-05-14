# ArtQR Fusion Kullanım Rehberi

Bu proje, müşteri logosunu veya görselini okunabilir QR kodla birleştirir. Sevdiğin rakip çıktısının adı bu kodda `competitor_logo_halftone` olarak geçer. Tekniğin gerçek adı: **logo-aware halftone QR**. Mantığı şudur: logo arkada net durur, QR modülleri logo üstüne nokta/plus/piksel gibi basılır, üç köşedeki finder şekilleri yuvarlak ve güçlü kalır. Böylece logo görünür, QR de okunur.

## Günlük İş Akışı

1. Blok 3'te müşterinin logo/artwork dosyasını yükle.
2. Blok 4'te QR içine girecek bilgiyi seç.
3. Blok 5'te `customer_id = "murat"` altında müşteri işini takip et.
4. Rakip tarzı çıktı için `master_style_choice = "competitor_logo_halftone"` kullan.
5. Blok 8-13 arası çalıştır. Final dosya `qr_fusion_outputs/final_readable_art_qr.png`, teslim paketi `qr_fusion_delivery.zip` olur.
6. Teslimden önce final PNG'yi telefonla mutlaka okut.

## Müşteriden Alacağın Bilgiler

| Bilgi | Koda Yazılacak Yer | Ne İşe Yarar |
|---|---|---|
| Müşteri adı | `client_name` | Rapor ve teslim notunda görünür. Varsayılan: Murat. |
| İş kimliği | `customer_id` | Bütün müşteri isteklerini bu ad altında takip edersin. Varsayılan: `murat`. |
| Sipariş no / not | `order_reference` | Fiverr/Upwork siparişini ayırmak için. |
| QR linki/metni | `qr_data` | QR okutulunca açılacak asıl bilgi. |
| QR türü | `qr_content_type` | URL, WhatsApp, WiFi, vCard, telefon, email gibi türü belirler. |
| Logo/artwork | Blok 3 upload | QR içine işlenecek görsel. |
| Renk isteği | `customer_brand_hex`, `customer_dark_hex`, `customer_light_hex` | Marka rengi ve kontrast. |
| Stil isteği | `requested_style_note` | Müşterinin “rakip gibi”, “minimal”, “renkli” gibi notu. |
| Kullanım yeri | `usage_place` | Web, baskı, menü, kartvizit, sticker gibi kullanım. |
| Revizyon notu | `revision_note` | Değişiklik istekleri. |

## Murat Ayarları ve Müşteri Ayarları

Müşteriden gelen bilgiler `customer_id`, `client_name`, `qr_data`, `qr_content_type`, renk ve stil alanlarına yazılır. Bunlar müşteri isteğidir.

Murat ayarları senin kontrolünde kalır: `quality_mode`, `output_size`, `error_level`, `quiet_zone`, `module_contrast`, `finder_boost`, `halftone_density`, `halftone_logo_strength`, `min_decoder_passes`. Müşteriye sormadan kaliteyi ve okunabilirliği bunlarla yönetirsin.

## En Önemli Stil Seçimi

| Ayar | Ne Seçersen Ne Olur |
|---|---|
| `competitor_logo_halftone` | Rakip örneğe en yakın sonuç: logo arkada, plus/nokta halftone üstte, yuvarlak finder. |
| `full_output_explorer` | Tüm varyasyonları üretir; deneme ve portföy için iyi. |
| `tumunu_uret` | Premium teslim paketi ve çoklu okunabilir çıktı üretir. |
| `pixel_dot_matrix` | Logo QR hücrelerine nokta/piksel/plus gibi yedirilir. |
| `basic_logosuz_duz` | En güvenli, sade, logosuz QR. |
| `design_from_logo` | Logodan renk alır, markalı tasarım üretir. |
| `line_variations` | Dikey/yatay çizgili modern varyasyonlar üretir. |
| `qrbtf_style_pack` | QRBTF benzeri deneysel şekiller üretir. |
| `custom` | Her şeyi elle ayarlarsın. |

## Blok 4: QR İçerik Ayarları

| Ayar | Seçenek / Aralık | Açıklama |
|---|---|---|
| `service_tier` | `basic`, `standard`, `premium` | Paket seviyesi. Premium en iyi kalite ve en fazla dosya. |
| `qr_content_type` | `url`, `plain_text`, `email`, `phone`, `sms`, `whatsapp`, `wifi`, `geo_location`, `vcard`, `calendar`, `social_profile`, `payment_link`, `app_store`, `online_menu`, `google_review`, `youtube`, `raw` | QR okutulunca ne açılacağını belirler. |
| `qr_data` | metin/link | Ana link veya metin. |
| `expected_qr_data` | metin | Hazır QR kullanırsan testte beklenen sonucu yazarsın. Boşsa `qr_data` kullanılır. |
| `contact_*` | metin | vCard, email, telefon, WhatsApp işlerinde kullanılır. |
| `wifi_*` | metin/seçenek | WiFi QR üretmek için SSID, şifre, güvenlik tipi. |
| `geo_latitude`, `geo_longitude` | metin | Konum QR için enlem/boylam. |
| `event_*` | metin | Takvim etkinliği QR için başlık, başlangıç, bitiş, konum. |

## Blok 4: Kalite ve Okunabilirlik

| Ayar | Aralık / Seçenek | Ne Seçersen Ne Olur |
|---|---|---|
| `quality_mode` | `en_yuksek_kalite`, `dengeli`, `hizli_test`, `manual` | En yüksek kalite daha yavaş ama daha iyi sonuç arar. |
| `output_size` | 1024-4096 | Büyük değer daha temiz baskı ve detay verir. Premium için 4096. |
| `error_level` | `h`, `q`, `m`, `l` | Hata düzeltme. Logolu QR için `h` en güvenli. |
| `quiet_zone` | 4-10 | QR etrafındaki boş alan. Artarsa okuma kolaylaşır, tasarım alanı azalır. |
| `dark_strength_start/end` | 0.05-0.95 | QR etkisinin zayıftan güçlüye taranacağı aralık. End yükselirse okuma artar ama logo sertleşebilir. |
| `dark_strength_step` | 0.01-0.10 | Küçük adım daha çok aday ve daha iyi seçim, ama daha yavaş. |
| `module_contrast` | 0.00-1.00 | QR modül kontrastı. 0.75-0.90 güvenli. |
| `center_bias` | 0.00-1.00 | Modül merkezlerini güçlendirir. Okumayı artırır. |
| `finder_boost` | 1.00-2.50 | Üç köşedeki finder alanlarını güçlendirir. Premium için 2.10+. |
| `quiet_zone_boost` | 0.00-1.00 | Dış boşluğu temiz tutar. Okumayı artırır. |
| `contrast_after` | 1.00-1.80 | Final kontrast cilası. Fazlası logoyu sertleştirir. |
| `sharpness_after` | 1.00-2.50 | Keskinlik. 1.40-1.60 genelde iyi. |
| `min_decoder_passes` | 1-3 | Kaç okuyucu motoru doğru okumalı. Teslim için 2 iyi, çok kritik işte 3. |

## Blok 5: Rakip Tarzı Halftone Ayarları

| Ayar | Aralık / Seçenek | Ne Seçersen Ne Olur |
|---|---|---|
| `make_halftone_logo_qr` | `True/False` | Logo-aware halftone üretimini açar. Rakip tarzı için açık olmalı. |
| `use_halftone_as_final` | `True/False` | Okunabilir halftone bulunursa final yapar. |
| `halftone_force_competitor_layout` | `True/False` | Rakip düzene öncelik verir: logo altta, halftone QR üstte. |
| `halftone_mark_shape` | `dot`, `square`, `plus`, `diamond`, `star` | Üstteki QR işaret şekli. Rakip hissi için `plus` iyi. |
| `halftone_density` | 0.40-1.00 | İşaretlerin doluluk oranı. Artarsa QR okunabilirliği artar, logo daha yoğun görünür. |
| `halftone_logo_strength` | 0.20-1.00 | Logo etkisi. Artarsa logo daha net görünür. |
| `halftone_logo_underlay_alpha` | 0.00-0.55 | Arkadaki büyük logo görünürlüğü. 0.30-0.38 rakip tarzı için iyi. |
| `halftone_light_logo_alpha` | 0.20-1.00 | Açık alanlarda logo detayını taşıma gücü. |
| `halftone_logo_cell_threshold` | 0.02-0.30 | Logo hücre eşiği. Düşük değer daha fazla logo detayı, yüksek değer daha sade çıktı. |
| `halftone_background_dots` | `True/False` | Logo dışındaki QR noktalarını da çizer. Okunabilirlik için genelde açık. |
| `halftone_designer_finders` | `True/False` | Üç köşeyi yuvarlak/tasarım finder olarak çizer. |
| `halftone_outline_boost` | 0.00-1.00 | Logo konturunu güçlendirir. |
| `halftone_edge_weight` | 0.00-1.50 | Logo kenarlarını daha belirgin yapar. |
| `halftone_negative_cutout` | 0.00-1.00 | Logodaki beyaz/negatif boşlukları korur. Fazlası QR'i zayıflatabilir. |
| `halftone_safe_background_density` | 0.30-0.95 | Logo dışındaki QR güvenlik yoğunluğu. Artarsa okuma artar. |

## Renk, Şekil ve Finder Ayarları

| Ayar | Seçenek | Açıklama |
|---|---|---|
| `customer_color_mode` / `color_mode` | `black_white`, `brand_color`, `two_color`, `gradient`, `image_based` | Siyah-beyaz en güvenli, marka rengi dengeli, gradient ve image_based daha sanatsal. |
| `customer_module_shape` / `module_shape` | `square`, `rounded`, `dot`, `diamond`, `hexagon`, çizgi tipleri, `soft_blob` | Kare en güvenli, rounded/dot dengeli, çizgili şekiller görsel varyasyon için. |
| `customer_finder_style` / `finder_style` | `classic`, `rounded`, `circle`, `planet` | Rakip tarzı için `circle`. En güvenli klasik. |
| `background_style` | `white`, `brand_tint`, `transparent`, `artwork` | Beyaz en okunur. Artwork daha sanatsal ama riskli. |
| `brand_tint_strength` | 0.00-0.35 | Zemin marka tonu. Fazlası okumayı azaltabilir. |

## Teslim Dosyaları

| Ayar | Ne Üretir |
|---|---|
| `commercial_pack_enabled` | Teslim ZIP, rapor ve ekstra dosyaları açar. |
| `delivery_variant_mode = single_best` | Tek en iyi final üretir. |
| `delivery_variant_mode = all_premium_variants` | Çoklu premium varyasyon üretir. |
| `make_print_png` | Baskı PNG. |
| `make_print_pdf` | Baskı PDF. |
| `make_transparent_png` | Şeffaf PNG. |
| `make_jpg` | JPG kopya. |
| `make_svg` | Temiz QR SVG. Sanatsal halftone raster PNG'dir. |
| `make_layout_pack` | Kartvizit, sticker, label gibi layout dosyaları. |
| `make_test_report` | QR okuma test raporu. |

## Rakip Çıktısına En Yakın Başlangıç

| Ayar | Değer |
|---|---|
| `customer_id` | `murat` |
| `client_name` | `Murat` |
| `service_tier` | `premium` |
| `quality_mode` | `en_yuksek_kalite` |
| `master_style_choice` | `competitor_logo_halftone` |
| `output_size` | `4096` |
| `error_level` | `h` |
| `quiet_zone` | `5` veya `6` |
| `finder_style` | `circle` |
| `make_halftone_logo_qr` | `True` |
| `use_halftone_as_final` | `True` |
| `halftone_force_competitor_layout` | `True` |
| `halftone_mark_shape` | `plus` |
| `halftone_density` | `0.94`-`0.96` |
| `halftone_logo_strength` | `0.96`-`0.98` |
| `halftone_logo_underlay_alpha` | `0.30`-`0.38` |
| `halftone_logo_cell_threshold` | `0.035`-`0.055` |
| `halftone_designer_finders` | `True` |
| `min_decoder_passes` | `2` |

## Sorun Çıkarsa

| Sorun | Ne Yap |
|---|---|
| QR okunmuyor | `finder_boost`, `module_contrast`, `quiet_zone_boost`, `halftone_safe_background_density` artır. |
| Logo çok silik | `halftone_logo_strength`, `halftone_logo_underlay_alpha`, `halftone_light_logo_alpha` artır. |
| Logo çok karışık | `halftone_logo_cell_threshold` artır, `halftone_density` biraz düşür. |
| Köşeler zayıf | `finder_style = "circle"`, `halftone_designer_finders = True`, `finder_boost` artır. |
| Çıktı çok sert | `contrast_after`, `sharpness_after`, `halftone_density` biraz düşür. |
| Müşteri baskı istiyor | `output_size = 4096`, `print_dpi = 300` veya `600`, `error_level = "h"`. |

