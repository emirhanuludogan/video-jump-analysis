# Ortam ve model kurulumu

## Mevcut durum

Yerel geliştirme ortamı `jump_env`, Python 3.10.11 kullanır.
Aşağıdaki sürümler kurulu paket metadata bilgilerinden okunmuştur; temiz kurulum
ve tüm modellerin çalışması bu listeyle doğrulanmış değildir:

| Paket | Yerel sürüm |
| --- | --- |
| mediapipe | 0.10.14 |
| numpy | 2.2.6 |
| opencv-python | 5.0.0.93 |
| torch | 2.5.1+cu121 |
| torchvision | 0.20.1+cu121 |
| ultralytics | 8.4.182 |
| Pillow | 12.3.0 |

`jump_demo.py` ve karşılaştırma arayüzü Tkinter kullanır.
`jump_analyzer.py` PyTorch ve Torchvision'ı başlangıçta içe aktarır;
MediaPipe seçiliyken de bu paketler gerekir. Karşılaştırma ayrıca
`PATH` üzerinde `node` komutuna ihtiyaç duyar.

## Ek dedektör kaynakları

Mevcut kod aşağıdaki klasör adlarını bekler:

| Yerel klasör | Kaynak | Git'te kayıtlı revizyon |
| --- | --- | --- |
| `PeterandeD-FINE/` | [D-FINE](https://github.com/Peterande/D-FINE) | `956d1709314c2c6a4df6f34de232054578a7449f` |
| `lyuwenyuRT-DETR/` | [RT-DETR](https://github.com/lyuwenyu/RT-DETR) | `df2a96c8c3a098567a1149bf0248da6efe6d495d` |
| `yzd-vDEIM/` | [DEIM](https://github.com/Intellindust-AI-Lab/DEIM) | `09d35d53d39ee3145a1e61e3a989b28b9468d1dd` |

Bu klasörler Git indeksinde gömülü depo bağlantılarıdır; ana repoda
`.gitmodules` bulunmadığından standart bir recursive clone kurulumu yeterli değildir.
Şimdilik kaynakları ayrıca edinmek gerekir. Sonraki adımda submodule veya
kurulum betiğiyle bu bağımlılıklar açıkça yönetilmelidir.

## Beklenen ağırlık dosyaları

Aşağıdaki adlar repo kökünde aranır. Dosyalar kaynak kodla birlikte dağıtılmaz;
mevcut yerel kopyalar korunur.

| Seçenek | Dosya |
| --- | --- |
| YOLOv8 Nano | `yolov8n.pt` |
| Ultralytics RT-DETR-L | `rtdetr-l.pt` |
| RT-DETR v1 | `rtdetr_r18vd_dec3_6x_coco_from_paddle.pth` |
| RT-DETR v2 | `rtdetrv2_r18vd_120e_coco_rerun_48.1.pth` |
| D-FINE-S | `dfine_s_coco.pth` |
| DEIM D-FINE-S | `deim_dfine_hgnetv2_s_coco_120e.pth` |

İndirilen ağırlığın kaynağı, revizyonu ve dosya özeti henüz bir manifestte
kayıtlı değildir. Bu kayıt tamamlanmadan bütün modeller için tekrarlanabilir
kurulum sağlandığı söylenemez.

## Video, ölçek ve çıktılar

Videolar yerel tutulur; dosya seçiciden kendi kaydınızı seçebilirsiniz.
`jump_demo.py` içindeki hazır örnekler ayrıca komşu
`cmj-reliability-test/data/raw_videos/` klasörüne başvurabilir.

`kalibrasyon.py` sabit olarak `60 fps 1 .mov` dosyasının ilk karesini açar
ve seçilen iki nokta arasında **50 cm** olduğunu varsayar. Gerçek referans
uzunluğu farklıysa bu hesap kullanılamaz. Ekranda önerdiği `cm_carpani` adı
güncel kodla aynı değildir; güncel sınıf `cm_per_pixel` kullanır.
Ölçeğin yeni video ve kamera geometrisi için ayrıca belirlenmesi gerekir.

`test_sonuclari.txt` üretilen rapordur ve yeniden çalıştırmada üzerine yazılabilir.
Önemli bir deney çıktısını deney kimliğiyle ayrı bir dosyaya kopyalayın.
