# ArtQR Fusion

ArtQR Fusion, Fiverr/Upwork isleri icin hazirlanmis Google Colab tabanli logolu, sanatsal ve okunabilir QR teslim pipeline'idir. Adobe/Illustrator gerekmez. Musteri logosunu verir veya sen disarida hazirladigin logoyu yuklersin; notebook QR'i uretir, logo ile birlestirir, okunabilirligi test eder, finali gosterir ve ZIP teslim paketi olusturur.

AI logo uretimi bilerek yoktur. Bu projenin isi logo uretmek degil, gelen logoyu profesyonel QR teslimine donusturmektir.

## Ana Akis

1. **Blok 1 - Ortam Kurulumu:** Eksik kutuphaneler kurulur.
2. **Blok 2 - Kutuphaneler:** Pillow, OpenCV, Segno, pyzbar, zxing-cpp ve Colab araclari yuklenir.
3. **Blok 3 - Gorsel ve QR Giris Formu:** Logo/artwork yuklenir. Istenirse hazir QR gorseli de yuklenebilir.
4. **Blok 4 - Ana QR, Kalite Profili ve Fusion Ayarlari:** QR icine yazilacak bilgi, kalite ve okunabilirlik ayarlari secilir.
5. **Blok 5 - Musteri Brief, Ticari Stil ve Teslim Paketi:** Stil, logo, renk, halftone, baski ve teslim dosyalari secilir.
6. **Blok 8-12 - Uretim:** Temiz QR, adaylar, final, ticari varyasyonlar ve raporlar uretilir.
7. **Blok 13 - Final Galeri ve ZIP:** Final ve varyasyonlar ekranda gorulur, `qr_fusion_delivery.zip` indirilir.

## Blok 3 - Yukleme Ayarlari

| Ayar | Secenek / Aralik | Ne yapar |
|---|---|---|
| `upload_mode` | `generate_qr_from_data`, `use_uploaded_qr_image` | Normalde QR'i form bilgilerinden uretir. Hazir QR varsa ikinci secenek kullanilir. |
| `upload_artwork_image` | `True/False` | `True`: Colab dosya yukleme acar. `False`: dosya yolu elle yazilir. |
| `upload_qr_image_when_needed` | `True/False` | Hazir QR modunda QR gorseli yukletir. |
| `artwork_path` | metin | Colab disinda calisirken logo dosya yolu. |
| `uploaded_qr_path` | metin | Hazir QR dosya yolu. |

## Blok 4 - QR Icerik Ayarlari

| Ayar | Secenek / Aralik | Ne yapar |
|---|---|---|
| `service_tier` | `basic`, `standard`, `premium` | Paket seviyesini belirler. Premium en kaliteli ve en fazla cikti verir. |
| `apply_service_tier_to_quality` | `True/False` | Paket secimini kalite ayarlarina otomatik uygular. |
| `qr_content_type` | `url`, `plain_text`, `email`, `phone`, `sms`, `whatsapp`, `wifi`, `geo_location`, `vcard`, `calendar`, `social_profile`, `payment_link`, `app_store`, `online_menu`, `google_review`, `youtube`, `raw` | QR okutulunca ne acilacagini belirler. Social/payment/app/menu/review/youtube pratikte URL olarak islenir. |
| `qr_data` | metin | Ana link veya metin. URL, payment link, menu, YouTube, Google review gibi islerde buraya link yazilir. |
| `expected_qr_data` | metin | Hazir QR kullaniyorsan testte beklenen sonucu yazarsin. Bos kalirsa `qr_data` kullanilir. |
| `contact_name/company/title/phone/email/website/address` | metin | vCard, email, phone, SMS, WhatsApp gibi tiplerde kullanilir. |
| `message_subject`, `message_body` | metin | Email, SMS, WhatsApp mesaj metni. |
| `wifi_ssid`, `wifi_password` | metin | WiFi QR bilgileri. |
| `wifi_security` | `WPA`, `WEP`, `nopass` | WiFi sifreleme tipi. |
| `wifi_hidden` | `True/False` | Gizli WiFi agi ise `True`. |
| `geo_latitude`, `geo_longitude` | metin | Konum QR'i icin enlem/boylam. |
| `event_title/start/end/location` | metin | Takvim etkinligi QR'i. |

## Blok 4 - Kalite ve Okunabilirlik

| Ayar | Secenek / Aralik | Ne yapar |
|---|---|---|
| `quality_mode` | `en_yuksek_kalite`, `dengeli`, `hizli_test`, `manual` | Genel kalite profili. En iyi islerde `en_yuksek_kalite`. |
| `apply_quality_mode` | `True/False` | Secilen kalite profilini slider ayarlarina uygular. |
| `output_size` | 1024-4096, adim 512 | Final piksel boyutu. 4096 baski ve premium teslim icin en iyisi. |
| `error_level` | `h`, `q`, `m`, `l` | QR hata duzeltme. `h` en guvenli ve logolu tasarim icin onerilen seviye. |
| `quiet_zone` | 4-10 | QR etrafindaki bos alan. Arttikca okuma guvenligi artar, tasarim alani azalir. |
| `logo_aware_qr_mask_optimize` | `True/False` | QR maskesini logoya daha uyumlu secmeye calisir. Premiumda acik kalsin. |
| `logo_aware_mask_trials` | 1-8 | Daha cok deneme daha iyi sonuc, daha yavas sure. Premium icin 8. |
| `fit_mode` | `cover_crop`, `contain_pad` | `cover_crop`: kareye kirpar. `contain_pad`: logoyu kirpmadan boslukla oturtur. |
| `pad_red/green/blue` | 0-255 | `contain_pad` modunda zemin rengi. Beyaz icin 255/255/255. |
| `dark_strength_start` | 0.05-0.80 | Fusion taramasinin baslangic gucu. Dusuk deger daha yumuşak gorsel. |
| `dark_strength_end` | 0.10-0.95 | Fusion taramasinin en guclu adayi. Yuksek deger daha belirgin QR ve daha okunabilir final. |
| `dark_strength_step` | 0.01-0.10 | Adaylar arasi adim. 0.01-0.02 daha cok aday ve daha iyi secim, daha yavas. |
| `light_strength_ratio` | 0.20-1.00 | Acik alanlarin fusion etkisi. Yuksek deger gorseli daha fazla hissettirir. |
| `module_contrast` | 0.00-1.00 | QR modul kontrasti. 0.75-0.90 premium icin guvenli. |
| `center_bias` | 0.00-1.00 | Modul merkezlerini guclendirir. Yuksek deger okuma guvenligini artirir. |
| `finder_boost` | 1.00-2.50 | Kosedeki QR bulucu kareleri guclendirir. Premium icin 2.10-2.50. |
| `quiet_zone_boost` | 0.00-1.00 | Sessiz bolgeyi temiz tutar. Yuksek deger taramayi iyilestirir. |
| `contrast_after` | 1.00-1.80 | Final kontrast cilasi. Cok yuksek deger logoyu sertlestirebilir. |
| `sharpness_after` | 1.00-2.50 | Keskinlik. 1.40-1.60 genelde iyi. |
| `min_decoder_passes` | 1-3 | Kac okuyucu motorunun dogru okumasini istedigin. Premiumda 2, cok kritik islerde 3. |
| `prefer_best_score_not_first_pass` | `True/False` | Ilk okunan aday yerine en iyi skor alan adayi secer. Premiumda `True`. |
| `save_all_candidates` | `True/False` | Tum adaylari kaydeder. Kalite kontrol icin acik kalabilir. |
| `show_candidate_previews` | `True/False` | Adaylari ekranda gosterir. Hiz icin kapali, inceleme icin acik. |
| `download_final` | `True/False` | Son ZIP'i Colab'dan indirir. |

## Blok 5 - Teslim ve Stil Ayarlari

| Ayar | Secenek / Aralik | Ne yapar |
|---|---|---|
| `delivery_quality_mode` | `en_yuksek_kalite`, `dengeli`, `hizli_test`, `manual` | Teslim dosyalari icin kalite profili. |
| `apply_delivery_quality_mode` | `True/False` | Teslim kalite profilini uygular. |
| `apply_service_tier_to_delivery` | `True/False` | Basic/Standard/Premium paketini teslim kapsamına uygular. |
| `commercial_pack_enabled` | `True/False` | Ticari ZIP, rapor, varyasyon ve ekstra dosyalari acar/kapatir. |
| `delivery_variant_mode` | `single_best`, `all_premium_variants` | Tek final veya tum premium varyasyonlar. Fiverr icin `all_premium_variants`. |
| `include_unreadable_visual_variants` | `True/False` | Okunmayan ama guzel varyasyonlari ZIP'e alma. Musteriye teslim icin `False` kalmali. |
| `master_style_choice` | `custom`, `pixel_dot_matrix`, `basic_logosuz_duz`, `design_from_logo`, `line_variations`, `qrbtf_style_pack`, `tumunu_uret` | Ana stil secimi. En kolay premium satis icin `tumunu_uret`. |

### `master_style_choice` Ne Secilmeli?

| Secim | Sonuc |
|---|---|
| `pixel_dot_matrix` | Logo QR hucrelerine nokta/piksel/arti gibi yedirilir. Fiverr tarzi sanatsal isler icin iyi. |
| `basic_logosuz_duz` | Sade, logosuz, en guvenli QR. Hizli ve risksiz isler icin. |
| `design_from_logo` | Logo renklerini kullanan markali tasarim. Sosyal medya ve marka islerinde iyi. |
| `line_variations` | Dikey/yatay/cizgisel QR varyasyonlari. Modern/tech tasarim isteyenlere. |
| `qrbtf_style_pack` | QRBTF benzeri planet finder, interlock, cross, radial, diagonal line stilleri. |
| `tumunu_uret` | Premium: tum iyi varyasyonlari, raporlari ve teslim paketini uretir. |
| `custom` | Tum ayarlari elle kontrol etmek icin. |

## Blok 5 - Musteri Brief Alanlari

| Ayar | Secenek / Aralik | Ne yapar |
|---|---|---|
| `client_name` | metin | Musteri adi, raporlara yazilir. |
| `order_reference` | metin | Siparis numarasi veya not. |
| `requested_style_note` | metin | Musterinin istedigi stil. |
| `revision_note` | metin | Revizyon notu. |
| `usage_place` | `web_only`, `print`, `web_and_print`, `menu`, `business_card`, `sticker`, `product_label`, `event_ticket`, `social_media` | Kullanim yerini rapora ve presetlere yansitir. |
| `qr_static_or_dynamic` | `static_lifetime`, `dynamic_link_provided_by_client` | Notebook link yonetimi yapmaz; dynamic istenirse musteri dynamic link vermeli. |

## Blok 5 - Frame ve Baski

| Ayar | Secenek / Aralik | Ne yapar |
|---|---|---|
| `make_scan_me_frame` | `True/False` | Final QR icin Scan Me cercevesi uretir. |
| `scan_me_text` | metin | Cercevede yazacak metin. |
| `scan_frame_style` | `rounded_brand`, `minimal`, `bold_label` | Cerceve stili. |
| `print_size_preset` | `2x2_inch`, `3x3_inch`, `4x4_inch`, `custom` | Baski olcusu. Kucuk baskida temiz QR daha guvenli. |
| `custom_print_width_in`, `custom_print_height_in` | sayi | Custom baski olcusu inch. |
| `print_dpi` | 300, 600 | 300 yeterli, 600 premium/baski icin daha detayli. |

## Blok 5 - Presetler

| Ayar | Secenekler | Ne yapar |
|---|---|---|
| `portfolio_example` | `custom`, `restaurant_qr`, `instagram_linktree_qr`, `business_card_qr`, `product_label_qr`, `luxury_brand_qr`, `event_ticket_qr`, `colorful_logo_qr`, `black_white_clean_qr`, `halftone_logo_qr`, `social_media_preview_qr` | Musteri is tipine gore hazir paket secimi. |
| `style_preset` | `custom`, `business_clean`, `luxury`, `colorful`, `restaurant`, `event`, `product_label`, `social_media`, `black_white_clean`, `halftone_logo` | Renk, sekil, zemin ve stil ailesini ayarlar. |

## Blok 5 - Logo Ayarlari

| Ayar | Secenek / Aralik | Ne yapar |
|---|---|---|
| `logo_source_mode` | `uploaded_artwork`, `none` | Logo kullan veya logosuz QR uret. |
| `logo_mode` | `none`, `center_badge`, `center_transparent`, `background_logo`, `corner_small`, `logo_under_qr`, `qr_over_logo_soft` | Logonun QR icindeki yerlesimi. En guvenli: `center_badge`. En sanatsal: `qr_over_logo_soft` veya halftone final. |
| `logo_size_ratio` | 0.08-0.26 | Logo boyutu. Buyudukce marka gorunur, okuma riski artar. |
| `logo_badge_shape` | `rounded_square`, `circle`, `square` | Logo arkasindaki rozet sekli. |
| `logo_badge_opacity` | 0.40-1.00 | Rozet opakligi. Yuksek deger daha okunur ve temiz. |
| `logo_badge_padding` | 0.05-0.45 | Logo etrafindaki rozet boslugu. Arttikca okuma guvenligi artar. |
| `logo_visual_strength` | 0.20-1.00 | Logo renk/kontrast etkisi. |
| `logo_auto_readability_guard` | `True/False` | Logo yerlesimini okunabilirlik testine gore korur. Teslimde acik kalsin. |
| `brand_text` | metin | Layout dosyalarinda marka basligi. |
| `logo_mode_from_preset` | `True/False` | Preset logo modunu zorlasin mi? Genelde `False`. |

## Blok 5 - Logo Hazirlama

| Ayar | Secenek / Aralik | Ne yapar |
|---|---|---|
| `logo_prep_enabled` | `True/False` | Logoyu buyutme, temizleme ve keskinlestirme hazirligini acar. |
| `logo_upscale_engine` | `none`, `opencv_lanczos` | Dusuk cozunurluklu logoyu daha temiz buyutur. |
| `logo_upscale_factor` | 1-4 | Logo buyutme katsayisi. Premium icin 4. |
| `logo_denoise` | `none`, `light`, `medium` | Gurultu temizleme. Cok detayli logoda fazla artirma. |
| `logo_sharpen` | `none`, `light`, `medium`, `strong` | Logoyu keskinlestirir. Premium icin `strong` genelde iyi. |

## Blok 5 - QR Stil Ayarlari

| Ayar | Secenek / Aralik | Ne yapar |
|---|---|---|
| `module_shape` | `square`, `rounded`, `dot`, `diamond`, `hexagon`, `vertical_line`, `horizontal_line`, `diagonal_tl_br`, `diagonal_tr_bl`, `cross_line`, `interlock_line`, `radial_line`, `soft_blob` | QR veri modullerinin sekli. `square` en guvenli, `dot/rounded` dengeli, line sekilleri daha sanatsal. |
| `finder_style` | `classic`, `rounded`, `circle`, `planet` | Kose bulucu stilleri. `classic` en guvenli, `planet` QRBTF tarzi gorsel etki verir. |
| `color_mode` | `black_white`, `brand_color`, `two_color`, `gradient`, `image_based` | QR renk mantigi. En guvenli `black_white`, markali is icin `brand_color`, sanatsal is icin `image_based/gradient`. |
| `dark_hex`, `brand_hex`, `gradient_start_hex`, `gradient_end_hex`, `light_hex` | hex renk | QR koyu, marka, gradient ve zemin renkleri. Koyu/acik kontrast yuksek olmali. |
| `background_style` | `white`, `brand_tint`, `transparent`, `artwork` | Zemin. En okunur `white`, en sanatsal `artwork`, teslim icin transparent ek dosya olarak iyi. |
| `brand_tint_strength` | 0.00-0.35 | Marka renkli zemin yogunlugu. Fazla artarsa okuma riski artabilir. |

## Blok 5 - Halftone Logo Ayarlari

| Ayar | Secenek / Aralik | Ne yapar |
|---|---|---|
| `make_halftone_logo_qr` | `True/False` | Logo-aware halftone QR uretir. Premium islerde acik. |
| `use_halftone_as_final` | `True/False` | Okunabilir halftone bulunursa final olarak secer. |
| `halftone_mark_shape` | `dot`, `square`, `plus`, `diamond`, `star` | Logo/QR hucrelerinin isaret sekli. `plus` Fiverr tarzi icin guclu. |
| `halftone_density` | 0.40-1.00 | Nokta/sekil dolulugu. Yuksek deger daha koyu ve okunur, cok yuksek deger logoyu sertlestirir. |
| `halftone_logo_strength` | 0.20-1.00 | Logo etkisi. Yuksek deger logo daha belirgin. |
| `halftone_logo_forward` | `True/False` | Logoyu one cikarir. |
| `halftone_light_logo_alpha` | 0.20-1.00 | Acik logo katmani gucu. |
| `halftone_logo_cell_threshold` | 0.02-0.30 | Logo hucre esigi. Dusuk deger daha fazla logo detayi, yuksek deger daha sade. |
| `halftone_background_dots` | `True/False` | Arka plan noktalarini da cizer. |
| `halftone_designer_finders` | `True/False` | Kose finder alanlarini tasarimla guclendirir. |
| `halftone_logo_aware_mode` | `True/False` | Logo kontur/ic dolgu/halo analizini kullanir. Premiumda acik. |
| `halftone_outline_boost` | 0.00-1.00 | Logo konturunu guclendirir. |
| `halftone_edge_weight` | 0.00-1.50 | Kenar algisi agirligi. Yuksek deger logo sinirlarini belirgin yapar. |
| `halftone_negative_cutout` | 0.00-1.00 | Logo etrafinda negatif bosluk etkisi. Fazlasi QR'i zayiflatabilir. |
| `halftone_safe_background_density` | 0.30-0.95 | Arka plan QR guvenlik yogunlugu. Yuksek deger daha okunur. |

## Blok 5 - Teslim Dosyalari

| Ayar | Secenek / Aralik | Ne yapar |
|---|---|---|
| `make_soft_balanced_strong` | `True/False` | Soft, balanced, strong sanat varyasyonlari uretir. |
| `make_styled_clean_qr` | `True/False` | Stilize ama temiz QR uretir. |
| `make_social_preview` | `True/False` | 1080x1080 sosyal medya onizleme. |
| `make_print_png` | `True/False` | DPI bilgili print PNG. |
| `make_print_pdf` | `True/False` | Print PDF. |
| `make_transparent_png` | `True/False` | Transparent QR dosyasi. |
| `make_jpg` | `True/False` | JPG kopya. |
| `make_svg` | `True/False` | Clean QR SVG. Sanatsal halftone final rasterdir. |
| `make_eps` | `True/False` | Clean QR EPS. |
| `make_layout_pack` | `True/False` | Sticker, business card, label layout dosyalari. |
| `make_test_report` | `True/False` | QR test raporu. |

## Blok 10 ve 11 - Inceleme / Manuel Secim

| Ayar | Ne yapar |
|---|---|
| `inspect_candidate_path` | Belirli aday dosyasini ekranda temiz QR ve artwork ile yan yana gosterir. |
| `manual_final_path` | Otomatik final yerine okunabilir bir adayi manuel final yapar. Okunmuyorsa kabul etmez. |

## En Kaliteli Premium Sonuc Icin Net Ayarlar

En iyi Fiverr teslimi icin su ayarlarla basla:

| Ayar | Deger |
|---|---|
| `service_tier` | `premium` |
| `quality_mode` | `en_yuksek_kalite` |
| `delivery_quality_mode` | `en_yuksek_kalite` |
| `master_style_choice` | `tumunu_uret` |
| `delivery_variant_mode` | `all_premium_variants` |
| `output_size` | `4096` |
| `error_level` | `h` |
| `quiet_zone` | `5` veya `6` |
| `logo_aware_qr_mask_optimize` | `True` |
| `logo_aware_mask_trials` | `8` |
| `dark_strength_start` | `0.22` |
| `dark_strength_end` | `0.94` |
| `dark_strength_step` | `0.02` |
| `module_contrast` | `0.85` |
| `center_bias` | `0.90` |
| `finder_boost` | `2.35` |
| `quiet_zone_boost` | `0.90` |
| `contrast_after` | `1.18` |
| `sharpness_after` | `1.55` |
| `min_decoder_passes` | `2` |
| `prefer_best_score_not_first_pass` | `True` |
| `logo_prep_enabled` | `True` |
| `logo_upscale_factor` | `4` |
| `logo_denoise` | `medium` |
| `logo_sharpen` | `strong` |
| `make_halftone_logo_qr` | `True` |
| `use_halftone_as_final` | `True` |
| `halftone_mark_shape` | `plus` |
| `halftone_density` | `0.94`-`0.96` |
| `halftone_logo_strength` | `0.94`-`0.96` |
| `halftone_logo_aware_mode` | `True` |
| `make_print_png/pdf/social_preview/transparent_png/jpg/svg/layout_pack/test_report` | `True` |

Pratik kural: Temiz ve yuksek cozunurluklu logo yukle, cok uzun URL yerine kisa link kullan, final ZIP olussa bile `final_readable_art_qr.png` dosyasini telefondan manuel okut. Musteriye sadece testten gecen dosyalari teslim et; `include_unreadable_visual_variants` kapali kalsin.
