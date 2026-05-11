# QR Code ControlNet SD 1.5 Proje Akisi

Bu proje, Stable Diffusion 1.5 tabanli bir ControlNet modeli ile QR kodu sanatsal bir gorselin icine yerlestirmek icin kullanilir. Amac, okunabilir QR kod yapisini korurken prompt ile istenen stile uygun bir gorsel uretmektir. Bu ciktilar Fiverr, Upwork, Etsy, sosyal medya kampanyalari, restoran menuleri, etkinlik davetleri, kartvizitler ve marka promosyonlari icin satilabilir "custom artistic QR code" hizmetine donusturulebilir.

Repo, `DionTimmer/controlnet_qrcode-control_v1p_sd15` modelinin yerel kopyasi gibi duruyor. Ancak mevcut klasordeki `.safetensors` ve `.bin` dosyalari gercek model agirligi degil, Git LFS pointer dosyalari. Bu yuzden Google Colab'da modeli yerel dosyadan degil, Hugging Face uzerinden `from_pretrained` ile indirmek daha dogru ve sorunsuzdur.

## Proje Ne Yapar?

1. Kullanicidan bir hedef link/metin alinir.
2. Bu hedef icin yuksek hata duzeltmeli bir QR kod uretilir.
3. QR kod, ControlNet'e kosul gorseli olarak verilir.
4. Stable Diffusion 1.5, bir baslangic gorseli ve metin prompt'u ile yeni bir sanatsal gorsel uretir.
5. ControlNet, uretilen gorselin icinde QR kod geometrisini korumaya calisir.
6. Cikti QR okuyucu ile test edilir.
7. Okunabilirlik zayifsa parametreler ayarlanir ve tekrar uretilir.
8. En iyi sonuc PNG/JPG olarak musteriye teslim edilir.

## Girdiler

Ana girdiler:

- `qr_data`: QR kodun acacagi link veya metin. Ornek: `https://example.com/menu`
- `prompt`: Gorselin nasil gorunmesini istedigimiz. Ornek: `luxury coffee shop poster, warm cinematic lighting, premium branding`
- `negative_prompt`: Istenmeyen kalite problemleri. Ornek: `ugly, blurry, low quality, distorted text, nsfw`
- `init_image`: Img2Img icin baslangic gorseli. Bu bir renk dokusu, fotograf, poster veya bos canvas olabilir.
- `condition_image`: QR kod gorseli. ControlNet bunu takip eder.
- `seed`: Ayni sonucu tekrar uretebilmek icin rastgelelik degeri.

Degistirilebilir ana parametreler:

- `resolution`: Genelde `768`. SD 1.5 ve bu model icin tavsiye edilen boyut.
- `num_inference_steps`: Kalite/hesaplama dengesi. Baslangic icin `40-80`, en iyi islerde `100-150`.
- `guidance_scale`: Prompt'a ne kadar uyulacagi. Baslangic icin `9-15`; README orneginde `20`.
- `controlnet_conditioning_scale`: QR yapisinin ne kadar baskin olacagi. Baslangic icin `1.2-1.8`.
- `strength`: Img2Img baslangic gorselinin ne kadar degisecegi. Baslangic icin `0.75-0.95`.

## Ciktilar

- Sanatsal QR kod gorseli: `output.png`
- QR test sonucu: okunabilir/okunamaz
- Parametre kaydi: prompt, seed, scale, strength, step sayisi
- Musteriye teslim edilecek final dosya

## Neler Degistirilebilir?

- QR kodun icerdigi link/metin
- QR kod rengi ve arka plan rengi
- Prompt ile tema: luks, cyberpunk, restoran, dugun, muzik festivali, emlak, kahve dukkuani, oyun, moda vb.
- Init image ile kompozisyon
- ControlNet gucu ile QR okunabilirligi
- Inference step sayisi ile kalite
- Seed ile varyasyon
- Negative prompt ile bozulmalarin azaltilmasi

## Okunabilir QR Icin Kritik Notlar

- QR kod uretilirken hata duzeltme seviyesi `H` olmali. Bu yaklasik %30 hata toleransi verir.
- QR kodun etrafinda yeterli beyaz bosluk, yani quiet zone, korunmali.
- Cok yogun prompt'lar QR yapisini bozabilir.
- `controlnet_conditioning_scale` artarsa QR daha okunabilir olur, fakat sanatsal stil zayiflayabilir.
- `strength` cok yuksek olursa gorsel guzellesebilir ama QR bozulabilir.
- Her final cikti mutlaka telefon kamerasiyla veya Python QR okuyucu ile test edilmeli.

## Google Colab A100 Uyumlu IPYNB Akisi

Asagidaki hucreler Colab'da notebook olarak sirayla calistirilabilir. A100 GPU VRAM icin `float16`, `xformers`, `enable_model_cpu_offload` ve 768px varsayilanlari uygundur. A100 40GB/80GB VRAM icin bu is akisi rahat calisir.

### Hucre 1 - GPU Kontrolu

```python
!nvidia-smi
```

### Hucre 2 - Kurulum

```python
!apt-get -qq update
!apt-get -qq install -y libzbar0
!pip -q install --upgrade \
  "Pillow<12" \
  diffusers \
  transformers \
  accelerate \
  safetensors \
  xformers \
  "qrcode[pil]" \
  opencv-python \
  pyzbar
```

Not: Colab'da `cannot import name '_Ink' from 'PIL._typing'` hatasi alirsan bunun sebebi Pillow surum uyumsuzlugudur. Yukaridaki kurulumda `Pillow<12` sabitlendi. Bu hucreyi calistirdiktan sonra `Runtime > Restart runtime` yap ve notebook'a Hucre 3'ten devam et.

Kurulumdan sonra surum kontrolu:

```python
import PIL
print(PIL.__version__)
```

### Hucre 3 - Importlar

```python
import torch
import qrcode
from PIL import Image, ImageFilter
from diffusers import StableDiffusionControlNetImg2ImgPipeline, ControlNetModel, DDIMScheduler
from diffusers.utils import load_image
from pyzbar.pyzbar import decode
from IPython.display import display
```

### Hucre 4 - QR Kod Uretimi

```python
qr_data = "https://example.com" #@param {type:"string"}
qr_fill_color = "black" #@param {type:"string"}
qr_back_color = "white" #@param {type:"string"}
qr_box_size = 16 #@param {type:"slider", min:8, max:32, step:1}
qr_border = 4 #@param {type:"slider", min:2, max:8, step:1}
make_soft_condition = True #@param {type:"boolean"}
soft_condition_blur = 0.0 #@param {type:"slider", min:0.0, max:2.0, step:0.1}

qr = qrcode.QRCode(
    version=None,
    error_correction=qrcode.constants.ERROR_CORRECT_H,
    box_size=qr_box_size,
    border=qr_border,
)
qr.add_data(qr_data)
qr.make(fit=True)

qr_img = qr.make_image(fill_color=qr_fill_color, back_color=qr_back_color).convert("RGB")
qr_img.save("qr_condition.png")

condition_source = qr_img
if make_soft_condition and soft_condition_blur > 0:
    condition_source = condition_source.filter(ImageFilter.GaussianBlur(radius=soft_condition_blur))

condition_source.save("qr_condition_controlnet.png")
display(condition_source)
```

### Hucre 5 - Yardimci Fonksiyonlar

```python
def resize_for_condition_image(input_image: Image.Image, resolution: int = 768):
    input_image = input_image.convert("RGB")
    width, height = input_image.size
    scale = float(resolution) / min(height, width)
    height = int(round((height * scale) / 64.0)) * 64
    width = int(round((width * scale) / 64.0)) * 64
    return input_image.resize((width, height), resample=Image.LANCZOS)

def test_qr(image_path: str):
    img = Image.open(image_path).convert("RGB")
    result = decode(img)
    if not result:
        print("QR okunamadi. ControlNet scale artir, prompt'u sadelestir veya strength dusur.")
        return None
    decoded = [item.data.decode("utf-8") for item in result]
    print("QR okundu:", decoded)
    return decoded
```

### Hucre 6 - Model Yukleme

```python
controlnet = ControlNetModel.from_pretrained(
    "DionTimmer/controlnet_qrcode-control_v1p_sd15",
    torch_dtype=torch.float16,
)

pipe = StableDiffusionControlNetImg2ImgPipeline.from_pretrained(
    "runwayml/stable-diffusion-v1-5",
    controlnet=controlnet,
    safety_checker=None,
    torch_dtype=torch.float16,
)

pipe.scheduler = DDIMScheduler.from_config(pipe.scheduler.config)
pipe.enable_xformers_memory_efficient_attention()
pipe.enable_model_cpu_offload()
```

### Hucre 7 - Init Image Hazirlama

Baslangic gorseli olarak kendi gorselini yukleyebilir veya internetten bir gorsel kullanabilirsin. Hizli test icin sade bir canvas yeterlidir.

```python
resolution = 768 #@param {type:"slider", min:512, max:1024, step:64}
init_red = 235 #@param {type:"slider", min:0, max:255, step:1}
init_green = 230 #@param {type:"slider", min:0, max:255, step:1}
init_blue = 220 #@param {type:"slider", min:0, max:255, step:1}

condition_image = resize_for_condition_image(Image.open("qr_condition_controlnet.png"), resolution)

init_image = Image.new("RGB", condition_image.size, (init_red, init_green, init_blue))
init_image.save("init_image.png")

display(condition_image)
display(init_image)
```

### Hucre 8 - Gorsel Uretimi

```python
prompt = "premium coffee shop poster, elegant product photography, warm cinematic lighting, natural paper texture, modern branding, high detail, professional advertisement" #@param {type:"string"}
negative_prompt = "ugly, blurry, low quality, low resolution, distorted, unreadable, bad anatomy, extra text, watermark, logo, nsfw" #@param {type:"string"}

seed = 123121231 #@param {type:"integer"}
guidance_scale = 10 #@param {type:"slider", min:1, max:30, step:1}
controlnet_conditioning_scale = 1.75 #@param {type:"slider", min:0.5, max:3.0, step:0.05}
strength = 0.80 #@param {type:"slider", min:0.1, max:1.0, step:0.05}
num_inference_steps = 80 #@param {type:"slider", min:20, max:150, step:10}

generator = torch.Generator(device="cuda").manual_seed(seed)

result = pipe(
    prompt=prompt,
    negative_prompt=negative_prompt,
    image=init_image,
    control_image=condition_image,
    width=condition_image.size[0],
    height=condition_image.size[1],
    guidance_scale=guidance_scale,
    controlnet_conditioning_scale=controlnet_conditioning_scale,
    strength=strength,
    num_inference_steps=num_inference_steps,
    generator=generator,
)

output = result.images[0]
output.save("output.png")
display(output)
```

### Hucre 9 - QR Okunabilirlik Testi

```python
test_qr("output.png")
```

### Hucre 10 - QR Okunabilirlik Kurtarma

Eger `output.png` guzel gorunuyor ama QR okunmuyorsa once bu hucreyi overlay kapali halde calistir. Bu islem modeli tekrar calistirmadan cikti gorselini buyutur, keskinlestirir ve kontrastini artirir. `use_qr_overlay` son caredir; QR'i okutabilir ama logo/gorsel kalitesini bozabilir.

```python
from PIL import ImageEnhance, ImageFilter

postprocess_input = "output.png" #@param {type:"string"}
postprocess_output = "output_readable.png" #@param {type:"string"}
upscale_factor = 2 #@param {type:"slider", min:1, max:4, step:1}
sharpness = 1.8 #@param {type:"slider", min:1.0, max:4.0, step:0.1}
contrast = 1.25 #@param {type:"slider", min:1.0, max:2.5, step:0.05}
qr_overlay_alpha = 0.18 #@param {type:"slider", min:0.0, max:0.6, step:0.02}
use_qr_overlay = False #@param {type:"boolean"}

img = Image.open(postprocess_input).convert("RGB")

if upscale_factor > 1:
    new_size = (img.width * upscale_factor, img.height * upscale_factor)
    img = img.resize(new_size, Image.Resampling.LANCZOS)

img = ImageEnhance.Contrast(img).enhance(contrast)
img = ImageEnhance.Sharpness(img).enhance(sharpness)
img = img.filter(ImageFilter.UnsharpMask(radius=1.2, percent=140, threshold=3))

if use_qr_overlay and qr_overlay_alpha > 0:
    qr_layer = Image.open("qr_condition_controlnet.png").convert("RGB")
    qr_layer = qr_layer.resize(img.size, Image.Resampling.NEAREST)
    img = Image.blend(img, qr_layer, qr_overlay_alpha)

img.save(postprocess_output, quality=95)
display(img)
test_qr(postprocess_output)
```

Okunmazsa su sirayla dene:

- `contrast`: `1.25` -> `1.5`
- `sharpness`: `1.8` -> `2.5`
- `upscale_factor`: `2` -> `3`
- Son care olarak `use_qr_overlay=True`, `qr_overlay_alpha`: `0.08` -> `0.12` -> `0.18`

### Hucre 11 - Varyasyon Denemeleri

Profesyonel sonuc icin tek resme abanmak yerine 6-12 aday uretmek daha iyi calisir. Bu hucre farkli seed ve denge ayarlariyla adaylar uretir, okunabilenleri bildirir. En iyi kalite genelde QR'i sonradan bindirmeden, burada okunabilen adaylardan secilir.

```python
experiments = [
    {"seed": 101, "guidance_scale": 10, "controlnet_conditioning_scale": 1.70, "strength": 0.76},
    {"seed": 102, "guidance_scale": 11, "controlnet_conditioning_scale": 1.60, "strength": 0.80},
    {"seed": 103, "guidance_scale": 12, "controlnet_conditioning_scale": 1.55, "strength": 0.82},
    {"seed": 104, "guidance_scale": 13, "controlnet_conditioning_scale": 1.45, "strength": 0.84},
    {"seed": 105, "guidance_scale": 14, "controlnet_conditioning_scale": 1.35, "strength": 0.86},
    {"seed": 106, "guidance_scale": 12, "controlnet_conditioning_scale": 1.75, "strength": 0.78},
]

readable_outputs = []

for i, params in enumerate(experiments, start=1):
    generator = torch.Generator(device="cuda").manual_seed(params["seed"])
    result = pipe(
        prompt=prompt,
        negative_prompt=negative_prompt,
        image=init_image,
        control_image=condition_image,
        width=condition_image.size[0],
        height=condition_image.size[1],
        guidance_scale=params["guidance_scale"],
        controlnet_conditioning_scale=params["controlnet_conditioning_scale"],
        strength=params["strength"],
        num_inference_steps=80,
        generator=generator,
    )
    path = f"output_{i}.png"
    result.images[0].save(path)
    print(path, params)
    display(result.images[0])
    decoded = test_qr(path)
    if decoded:
        readable_outputs.append(path)

print("Okunabilen adaylar:", readable_outputs)
```

### Kalite Iyi Ama QR Okumuyorsa

Eger gorsel kalitesi iyiyse ve sadece QR okunmuyorsa `Hucre 8` icinde once su ayarlari dene:

```python
guidance_scale = 10
controlnet_conditioning_scale = 1.75
strength = 0.80
num_inference_steps = 80
```

Hala okunmazsa gorseli fazla bozmadan sirayla sadece sunlari degistir:

```python
controlnet_conditioning_scale = 1.85
strength = 0.78
```

Sonraki deneme:

```python
controlnet_conditioning_scale = 1.95
strength = 0.76
```

Prompt'u da biraz sadelestir. Ozellikle cok fazla detay, logo, texture, tiny details, complex background gibi ifadeler QR modullerini bozabilir. Daha okunur prompt ornegi:

```python
prompt = "premium coffee shop poster, clean composition, warm lighting, elegant branding, high detail"
```

## Fiverr/Upwork Icin Hizmet Fikri

Hizmet basligi:

`I will create a custom scannable AI QR code artwork for your brand`

Sunulacak paketler:

- Basic: 1 QR tasarim, 1 tema, 1 revizyon
- Standard: 3 varyasyon, 2 revizyon, sosyal medya uyumlu PNG
- Premium: 5-10 varyasyon, marka renkleri, poster/banner oranlari, ticari kullanim teslimi

Musteriden istenecek bilgiler:

- QR kodun acacagi link
- Marka/urun/etkinlik adi
- Istenen tema veya referans gorseller
- Renk tercihleri
- Kullanim alani: menu, kartvizit, afis, sosyal medya, ambalaj vb.
- Dosya orani: kare, dikey hikaye, yatay banner

Teslim edilecekler:

- Final PNG/JPG
- Okunabilirlik testinden gecmis QR
- Istege bagli 2-5 varyasyon
- Kullanilan prompt ve seed bilgisi

## Onerilen Uretim Stratejisi

1. Musteri linkini al.
2. QR kodu `ERROR_CORRECT_H` ile uret.
3. Ilk olarak sade ve okunabilir bir kompozisyon dene.
4. QR okunuyorsa stil kalitesini artir.
5. QR okunmuyorsa:
   - `controlnet_conditioning_scale` degerini artir.
   - `strength` degerini biraz dusur.
   - Prompt'u sadelestir.
   - Arka plani daha kontrastli yap.
6. En az 3 varyasyon uret.
7. Telefon kamerasi ile final kontrol yap.
8. Musteriye en iyi sonucu ve alternatifleri teslim et.

## Pratik Parametre Rehberi

| Hedef | guidance_scale | controlnet_conditioning_scale | strength | steps |
| --- | ---: | ---: | ---: | ---: |
| En okunabilir QR | 10-13 | 1.7-2.0 | 0.70-0.82 | 60-100 |
| Dengeli stil + okunabilirlik | 12-16 | 1.4-1.7 | 0.82-0.90 | 70-120 |
| Daha sanatsal cikti | 14-20 | 1.1-1.5 | 0.88-0.95 | 80-150 |

## Sonuc

Bu proje dogru parametrelerle calistirildiginda, verilen bir linki okunabilir ve sanatsal QR kod gorseline donusturur. En onemli basari kriteri sadece gorselin guzel olmasi degil, QR kodun gercek cihazlarla okunmasidir. Colab A100 ortaminda 768px cozumleme, fp16 model yukleme ve Diffusers pipeline ile ticari isler icin yeterli hiz ve kalite elde edilebilir.

## Colab Ticari Luminance Fusion Notebook

Bu kisim ControlNet kullanmaz. Hazir logo/gorsel dosyani Colab'a yuklersin, sistem temiz QR kodu kendisi uretir, sonra logo/gorsel ile QR kodu parlaklik kanalinda birlestirir. GPU gerekmez; CPU ile calisir. Fiverr/Upwork icin pratik ve teslim edilebilir final uretim akisi budur.

### Fusion Hucre 1 - Kurulum

```python
!apt-get -qq update
!apt-get -qq install -y libzbar0
!pip -q install --upgrade "Pillow<12" opencv-python pyzbar segno numpy
```

Kurulumdan sonra hata alirsan `Runtime > Restart runtime` yap ve sonraki hucreden devam et.

### Fusion Hucre 2 - Importlar

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

### Fusion Hucre 3 - Logo/Gorsel Yukle

Bu hucrede kendi urettigin kaliteli logo, poster veya gorseli yukle. Dosya adi otomatik alinacak.

```python
uploaded = files.upload()

if not uploaded:
    raise ValueError("Dosya yuklenmedi.")

artwork_path = list(uploaded.keys())[0]
print("Yuklenen gorsel:", artwork_path)

artwork_preview = Image.open(artwork_path).convert("RGB")
display(artwork_preview)
```

### Fusion Hucre 3B - Gorsel Uygunluk Kontrolu

Bu hucre yuklenen gorselin QR fusion icin uygun olup olmadigini hizli kontrol eder. En onemli kural: Yuklenen gorselin icinde zaten QR kod veya QR'a benzeyen kareli desen olmamali. Sistem gercek QR'i kendisi uretecek.

```python
img = Image.open(artwork_path).convert("RGB")
gray = np.array(img.convert("L"))

print("Dosya:", artwork_path)
print("Boyut:", img.size)
print("Parlaklik min/max/ortalama/std:", int(gray.min()), int(gray.max()), float(gray.mean()), float(gray.std()))

dark_pct = float((gray < 40).mean() * 100)
light_pct = float((gray > 235).mean() * 100)
mid_pct = 100.0 - dark_pct - light_pct

print("Cok koyu alan %:", round(dark_pct, 2))
print("Cok acik alan %:", round(light_pct, 2))
print("Orta ton alan %:", round(mid_pct, 2))

if dark_pct > 15 and light_pct > 45:
    print("UYARI: Gorsel cok siyah-beyaz/sert kontrastli. Zaten QR, yazi veya keskin logo iceriyorsa fusion zorlasir.")

if gray.std() < 35:
    print("UYARI: Gorsel dusuk kontrastli. QR okunabilirligi icin daha guclu fusion gerekebilir.")

print("Not: En iyi sonuc icin temiz logo/illustrasyon kullan; gorselin icinde hazir QR kod olmasin.")
display(img)
```

### Fusion Hucre 4 - Ayarlar

Bu hucre Colab Form olarak gorunur. Buradaki alanlari degistirerek musteri isine gore final uretirsin.

```python
qr_data = "https://example.com" #@param {type:"string"}
output_size = 2048 #@param {type:"slider", min:1024, max:4096, step:512}
error_level = "h" #@param ["h", "q", "m", "l"]
quiet_zone = 4 #@param {type:"slider", min:4, max:10, step:1}

fit_mode = "cover_crop" #@param ["cover_crop", "contain_pad"]
pad_red = 255 #@param {type:"slider", min:0, max:255, step:1}
pad_green = 255 #@param {type:"slider", min:0, max:255, step:1}
pad_blue = 255 #@param {type:"slider", min:0, max:255, step:1}

dark_strength_start = 0.18 #@param {type:"slider", min:0.05, max:0.80, step:0.01}
dark_strength_end = 0.55 #@param {type:"slider", min:0.10, max:0.95, step:0.01}
dark_strength_step = 0.04 #@param {type:"slider", min:0.01, max:0.10, step:0.01}
light_strength_ratio = 0.70 #@param {type:"slider", min:0.20, max:1.00, step:0.05}
module_contrast = 0.35 #@param {type:"slider", min:0.00, max:1.00, step:0.05}
center_bias = 0.65 #@param {type:"slider", min:0.00, max:1.00, step:0.05}

finder_boost = 1.45 #@param {type:"slider", min:1.00, max:2.50, step:0.05}
quiet_zone_boost = 0.35 #@param {type:"slider", min:0.00, max:1.00, step:0.05}
contrast_after = 1.08 #@param {type:"slider", min:1.00, max:1.80, step:0.02}
sharpness_after = 1.25 #@param {type:"slider", min:1.00, max:2.50, step:0.05}

save_all_candidates = True #@param {type:"boolean"}
show_candidate_previews = True #@param {type:"boolean"}
download_final = True #@param {type:"boolean"}
```

### Fusion Hucre 5 - Yardimci Fonksiyonlar

```python
def prepare_artwork(path, size, fit_mode="cover_crop", pad_color=(255, 255, 255)):
    img = Image.open(path).convert("RGB")
    w, h = img.size

    if fit_mode == "cover_crop":
        scale = size / min(w, h)
        nw, nh = int(round(w * scale)), int(round(h * scale))
        img = img.resize((nw, nh), Image.Resampling.LANCZOS)
        left = (nw - size) // 2
        top = (nh - size) // 2
        img = img.crop((left, top, left + size, top + size))
        return img

    scale = size / max(w, h)
    nw, nh = int(round(w * scale)), int(round(h * scale))
    img = img.resize((nw, nh), Image.Resampling.LANCZOS)
    canvas = Image.new("RGB", (size, size), pad_color)
    canvas.paste(img, ((size - nw) // 2, (size - nh) // 2))
    return canvas

def make_qr_png(data, path, size, error="h", border=4):
    qr = segno.make(data, error=error, boost_error=True)
    modules_with_border = qr.symbol_size(scale=1, border=border)[0]
    scale = max(1, size // modules_with_border)
    render_size = modules_with_border * scale
    qr.save(path, kind="png", scale=scale, border=border, dark="black", light="white")
    img = Image.open(path).convert("L")
    if img.size != (size, size):
        img = img.resize((size, size), Image.Resampling.NEAREST)
        img.save(path)
    return qr, img

def get_qr_masks(qr_img):
    arr = np.array(qr_img.convert("L"))
    dark_mask = arr < 128
    light_mask = ~dark_mask
    return dark_mask, light_mask

def build_finder_mask(size, qr, border):
    modules_total = qr.symbol_size(scale=1, border=border)[0]
    module_px = size / modules_total
    qr_size_no_border = modules_total - 2 * border

    finder_mask = np.zeros((size, size), dtype=bool)

    # Finder pattern: QR okuyucunun once buldugu 3 buyuk kose karesi.
    # 7 modul ana kare + 1 modul separator bolgesi dahil edilerek guclendirilir.
    zones = [
        (border - 1, border - 1),
        (border + qr_size_no_border - 8, border - 1),
        (border - 1, border + qr_size_no_border - 8),
    ]

    for mx, my in zones:
        x1 = max(0, int(math.floor(mx * module_px)))
        y1 = max(0, int(math.floor(my * module_px)))
        x2 = min(size, int(math.ceil((mx + 9) * module_px)))
        y2 = min(size, int(math.ceil((my + 9) * module_px)))
        finder_mask[y1:y2, x1:x2] = True

    return finder_mask

def build_quiet_zone_mask(size, qr, border):
    modules_total = qr.symbol_size(scale=1, border=border)[0]
    module_px = size / modules_total
    b = int(round(border * module_px))
    mask = np.zeros((size, size), dtype=bool)
    mask[:b, :] = True
    mask[-b:, :] = True
    mask[:, :b] = True
    mask[:, -b:] = True
    return mask

def build_module_center_weight(size, qr, border, center_bias):
    if center_bias <= 0:
        return np.ones((size, size), dtype=np.float32)

    modules_total = qr.symbol_size(scale=1, border=border)[0]
    module_px = size / modules_total

    yy, xx = np.indices((size, size))
    mx = (xx / module_px) % 1.0
    my = (yy / module_px) % 1.0

    dx = np.abs(mx - 0.5) * 2.0
    dy = np.abs(my - 0.5) * 2.0
    dist = np.maximum(dx, dy)
    center = np.clip(1.0 - dist, 0.0, 1.0)
    return (1.0 - center_bias) + center_bias * center

def luminance_fusion(
    art_img,
    qr_img,
    qr,
    border,
    dark_strength,
    light_ratio,
    finder_boost,
    quiet_boost,
    module_contrast=0.35,
    center_bias=0.65,
):
    art = np.array(art_img.convert("RGB"))
    dark_mask, light_mask = get_qr_masks(qr_img)
    finder_mask = build_finder_mask(art.shape[0], qr, border)
    quiet_mask = build_quiet_zone_mask(art.shape[0], qr, border)
    center_weight = build_module_center_weight(art.shape[0], qr, border, center_bias)

    lab = cv2.cvtColor(art, cv2.COLOR_RGB2LAB).astype(np.float32)
    l = lab[:, :, 0]

    strength_map = np.full(l.shape, dark_strength, dtype=np.float32)
    strength_map *= center_weight
    strength_map[finder_mask] *= finder_boost
    strength_map = np.clip(strength_map, 0.0, 0.95)

    dark_floor = 255.0 * (1.0 - module_contrast)
    light_floor = 255.0 * module_contrast

    dark_target = np.minimum(l * (1.0 - strength_map), dark_floor)
    light_strength = np.clip(strength_map * light_ratio, 0.0, 0.90)
    light_target = np.maximum(l + (255.0 - l) * light_strength, light_floor)

    l2 = l.copy()
    l2[dark_mask] = dark_target[dark_mask]
    l2[light_mask] = light_target[light_mask]

    if quiet_boost > 0:
        l2[quiet_mask] = l2[quiet_mask] + (255.0 - l2[quiet_mask]) * quiet_boost

    lab[:, :, 0] = np.clip(l2, 0, 255)
    fused = cv2.cvtColor(lab.astype(np.uint8), cv2.COLOR_LAB2RGB)
    return Image.fromarray(fused)

def polish_image(img, contrast=1.08, sharpness=1.25):
    img = ImageEnhance.Contrast(img).enhance(contrast)
    img = ImageEnhance.Sharpness(img).enhance(sharpness)
    img = img.filter(ImageFilter.UnsharpMask(radius=1.0, percent=90, threshold=3))
    return img

def test_qr_all(path, expected_data=None):
    pil_img = Image.open(path).convert("RGB")

    zbar_results = zbar_decode(pil_img)
    zbar_texts = [r.data.decode("utf-8") for r in zbar_results]

    cv_img = cv2.cvtColor(np.array(pil_img), cv2.COLOR_RGB2BGR)
    detector = cv2.QRCodeDetector()
    cv_text, points, _ = detector.detectAndDecode(cv_img)
    cv_texts = [cv_text] if cv_text else []

    all_texts = list(dict.fromkeys(zbar_texts + cv_texts))
    ok = expected_data in all_texts if expected_data else bool(all_texts)

    return {
        "ok": ok,
        "texts": all_texts,
        "zbar": zbar_texts,
        "opencv": cv_texts,
    }
```

### Fusion Hucre 6 - Temiz QR ve Gorsel Hazirla

```python
os.makedirs("qr_fusion_outputs", exist_ok=True)

pad_color = (pad_red, pad_green, pad_blue)
art_img = prepare_artwork(artwork_path, output_size, fit_mode=fit_mode, pad_color=pad_color)
qr, qr_img = make_qr_png(
    qr_data,
    "qr_fusion_outputs/clean_qr.png",
    output_size,
    error=error_level,
    border=quiet_zone,
)

art_img.save("qr_fusion_outputs/prepared_artwork.png", quality=95)

print("QR version/designator:", qr.designator)
print("Artwork size:", art_img.size)
display(art_img)
display(qr_img)

clean_test = test_qr_all("qr_fusion_outputs/clean_qr.png", expected_data=qr_data)
print("Temiz QR test:", clean_test)

if not clean_test["ok"]:
    raise ValueError("Temiz QR bile okunmuyor. qr_data, pyzbar/opencv kurulumu veya QR uretim ayarlarini kontrol et.")
```

### Fusion Hucre 7 - Otomatik Fusion ve QR Test

Bu hucre farkli strength degerleri dener. Ilk okunan sonucu `final_readable_art_qr.png` olarak kaydeder. Daha guzel adaylar icin tum adaylari da kaydedebilir.

```python
candidate_paths = []
passed_candidates = []

strength_values = np.arange(
    dark_strength_start,
    dark_strength_end + 0.0001,
    dark_strength_step,
)

for idx, strength in enumerate(strength_values, start=1):
    fused = luminance_fusion(
        art_img=art_img,
        qr_img=qr_img,
        qr=qr,
        border=quiet_zone,
        dark_strength=float(strength),
        light_ratio=light_strength_ratio,
        finder_boost=finder_boost,
        quiet_boost=quiet_zone_boost,
        module_contrast=module_contrast,
        center_bias=center_bias,
    )
    fused = polish_image(fused, contrast=contrast_after, sharpness=sharpness_after)

    path = f"qr_fusion_outputs/candidate_{idx:02d}_strength_{strength:.2f}.png"
    fused.save(path, quality=95)
    candidate_paths.append(path)

    result = test_qr_all(path, expected_data=qr_data)
    print(path, "OK" if result["ok"] else "NO", result["texts"])

    if show_candidate_previews:
        display(fused.resize((384, 384), Image.Resampling.LANCZOS))

    if result["ok"]:
        passed_candidates.append((path, float(strength), result))
        if not save_all_candidates:
            break

if not passed_candidates:
    print("Henuz okunabilir sonuc yok. Hucre 4'te dark_strength_end, finder_boost, module_contrast veya quiet_zone_boost artir.")
else:
    final_path = "qr_fusion_outputs/final_readable_art_qr.png"
    best_path, best_strength, best_result = passed_candidates[0]
    Image.open(best_path).save(final_path, quality=95)
    print("Final secildi:", final_path)
    print("Strength:", best_strength)
    print("Okunan veri:", best_result["texts"])
    display(Image.open(final_path))
```

### Fusion Hucre 8 - Adaylari Incele

QR okunmasa bile uretilen gorselleri yan yana incelemek icin bu hucreyi calistir. Sol: hazirlanan gorsel, orta: temiz QR, sag: sectigin aday.

```python
inspect_candidate_path = "" #@param {type:"string"}

if not inspect_candidate_path.strip():
    if candidate_paths:
        inspect_candidate_path = candidate_paths[-1]
    else:
        raise ValueError("Aday bulunamadi. Once Hucre 7'yi calistir.")

prepared = Image.open("qr_fusion_outputs/prepared_artwork.png").convert("RGB").resize((384, 384), Image.Resampling.LANCZOS)
clean = Image.open("qr_fusion_outputs/clean_qr.png").convert("RGB").resize((384, 384), Image.Resampling.NEAREST)
candidate = Image.open(inspect_candidate_path).convert("RGB").resize((384, 384), Image.Resampling.LANCZOS)

canvas = Image.new("RGB", (384 * 3, 384), "white")
canvas.paste(prepared, (0, 0))
canvas.paste(clean, (384, 0))
canvas.paste(candidate, (384 * 2, 0))

print("Incelenen aday:", inspect_candidate_path)
print(test_qr_all(inspect_candidate_path, expected_data=qr_data))
display(canvas)
```

### Fusion Hucre 9 - Daha Estetik Aday Secimi

Eger birden fazla aday okunduysa, genelde en dusuk strength en dogal gorunur. Ama ekranda adaylara bakip daha guzel olan dosya adini buraya yazabilirsin.

```python
manual_final_path = "" #@param {type:"string"}

if manual_final_path.strip():
    final_path = "qr_fusion_outputs/final_readable_art_qr.png"
    result = test_qr_all(manual_final_path, expected_data=qr_data)
    if not result["ok"]:
        raise ValueError(f"Secilen aday QR testten gecmedi: {result}")
    Image.open(manual_final_path).save(final_path, quality=95)
    print("Manuel final secildi:", final_path)
    print("Okunan veri:", result["texts"])
    display(Image.open(final_path))
else:
    print("Manuel secim yapilmadi. Hucre 7'nin sectigi final kullanilacak.")
```

### Fusion Hucre 10 - Final Test ve Indirme

```python
final_path = "qr_fusion_outputs/final_readable_art_qr.png"

if not os.path.exists(final_path):
    raise FileNotFoundError("Final dosya bulunamadi. Once Hucre 7'yi calistir.")

result = test_qr_all(final_path, expected_data=qr_data)
print("Final QR test:", result)

if not result["ok"]:
    raise ValueError("Final QR okunamadi. Ayarlari artirip tekrar dene.")

zip_path = "qr_fusion_delivery.zip"
with zipfile.ZipFile(zip_path, "w", zipfile.ZIP_DEFLATED) as z:
    z.write(final_path, arcname="final_readable_art_qr.png")
    z.write("qr_fusion_outputs/clean_qr.png", arcname="clean_qr.png")
    z.write("qr_fusion_outputs/prepared_artwork.png", arcname="prepared_artwork.png")

print("Teslim paketi hazir:", zip_path)

if download_final:
    files.download(zip_path)
```

### Fusion Ayar Rehberi

QR okunmuyorsa:

- `dark_strength_end` degerini `0.65` veya `0.75` yap.
- `finder_boost` degerini `1.70` yap.
- `quiet_zone_boost` degerini `0.50` yap.
- `output_size` degerini `2048` yerine `3072` yap.

Gorsel cok QR gibi gorunuyorsa:

- `dark_strength_start` degerini `0.12` yap.
- `dark_strength_end` degerini `0.40` yap.
- `light_strength_ratio` degerini `0.50` yap.
- `finder_boost` degerini `1.20` yap.

Ticari teslim icin ideal kabul:

- `final_readable_art_qr.png` hem `pyzbar` hem `OpenCV` tarafindan okunmali.
- Telefon kamerasi ile ayrica test edilmeli.
- Musteriye final PNG ve temiz QR yedegi verilmeli.
