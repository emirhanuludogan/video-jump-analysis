# Video Tabanlı Sıçrama Analizi

Standart kamera kayıtlarından sıçrama performansını incelemek için geliştirilen
Python tabanlı staj projesi. Kişi tespiti ve MediaPipe poz tahmini kullanılarak
kalça yer değiştirmesi, uçuş süresi ve yerle temas süresi hesaplanır.

Çalışmanın odağı, farklı kişi dedektörlerinin aynı analiz zincirindeki etkisini
karşılaştırmak; kalibrasyon, hareket evreleri ve kamera kaynaklı ölçüm hatalarını
incelemektir. Proje deneysel bir analiz uygulamasıdır.

**Durum:** Deneysel geliştirme. CMJ, Squat Jump, Abalakov, tek bacak ve Drop Jump
için test seçenekleri bulunur; her seçenek için doğrulanmış ölçüm başarımı gösterilmemiştir.

## Nasıl çalışıyor?

Video → kişi dedektörü → kişi bölgesinde MediaPipe Pose → görüntü koordinatlarına
dönüşüm → kalça yer değiştirmesi, uçuş süresi ve Drop Jump temas süresi → rapor.

Dedektör seçenekleri MediaPipe, YOLOv8 Nano, Ultralytics RT-DETR-L, RT-DETR v1/v2,
D-FINE ve DEIM'dir. Dedektör değişse de sonraki poz tahmini MediaPipe ile yapılır.
Bu nedenle dedektör karşılaştırması, farklı poz modellerinin karşılaştırılması değildir.

## Hesaplanan değerler

| Değer | Uygulama | Dikkat edilmesi gereken |
| --- | --- | --- |
| Kalça yer değiştirmesi | Sol/sağ kalçanın ortalaması, başlangıca göre en büyük yükselme | Bütün vücut kütle merkezi değildir |
| Santimetre karşılığı | Piksel farkı × cm/piksel ölçeği | Varsayılan `0.19230` her video için geçerli değildir |
| Uçuş süresi | Temas eşiğine göre havada sayılan kareler / FPS | Ayak noktaları ve eşik seçimine bağlıdır |
| Uçuştan yükseklik | `g*t²/8` | Kalkış/iniş yüksekliği varsayımına bağlı ayrı bir tahmindir |
| GCT | Temas kareleri / FPS; Drop Jump seçeneğinde | Bekleme ve kutu üstündeki temas ayrıca ayrıştırılmalıdır |
| İşlem performansı | Tespit oranı, kare başına süre ve işleme FPS'i | İşleme FPS'i videonun kayıt FPS'i değildir |

Kodda “CoM” adı geçen kalça sinyali ile uçuştan hesaplanan yükseklik farklı
ölçümlerdir; raporda birbirlerinin yerine kullanılmamalıdır.

## Kurulum ve ilk çalıştırma

Mevcut yerel ortam Python 3.10.11 kullanır. Bağımlılık listesi ve temiz kurulum
henüz standartlaştırılmadı. [Kurulum notu](docs/SETUP.md) mevcut durumu açıklar.

Mevcut `jump_env` ortamınız hazırsa PowerShell'de repo kökünden:

```powershell
.\jump_env\Scripts\python.exe jump_demo.py
```

Masaüstü arayüzünde video ve test türünü seçin. İlk incelemede MediaPipe
seçeneğiyle başlayabilirsiniz; diğer dedektörler ek kaynak kod ve ağırlık ister.
Örnek video adları yerel dosyalara işaret eder; kendi videonuzu dosya seçiciden açabilirsiniz.

Kalça ve segment hesaplarını aynı poz kaydı üzerinden karşılaştıran yerel arayüz:

```powershell
.\jump_env\Scripts\python.exe jump_comparison_ui.py
```

Bu karşılaştırma Python'a ek olarak Node.js ister. Bu arayüzler mevcut yerel
geliştirmelerdir; aynı dosyalar GitHub ana dalında henüz bulunmayabilir.

## Dosya rehberi

| Dosya | Görevi |
| --- | --- |
| `jump_analyzer.py` | Dedektör yönlendirmesi ve ana analiz zinciri |
| `com_analyzer.py` | Kalça yer değiştirmesi ve cm/piksel ölçeği |
| `flight_time_analyzer.py` | Uçuş ve sabit duruş takibi |
| `gct_analyzer.py` | Temas süresi hesabı |
| `report_generator.py` | Metin raporu; aynı hedef dosyanın üzerine yazar |
| `kalibrasyon.py` | İlk karede iki noktayla manuel ölçek denemesi |
| `jump_demo.py` | Yerel video analizi için masaüstü arayüzü |
| `jump_comparison.py`, `jump_comparison_ui.py` | Aynı poz verisiyle hesap karşılaştırması ve CSV |
| `jump_comparison.mjs`, `jump_com_web.mjs` | Karşılaştırmanın JavaScript hesapları |
| `docs/SETUP.md` | Ortam, model kaynakları ve veri düzeni |
| `docs/ROADMAP.md` | Düzenleme ve doğrulama sırası |

## Sonuçlar ve sınırlılıklar

Önceki GitHub açıklaması ve sonuç tablosu
[arşivde](docs/onceki-github-readme.md) korunur. O tablodaki sonuçlar bu düzenlemede
yeniden üretilmedi; yinelenen model etiketleri ve ölçüm tanımlarının açıklığa
kavuşturulması gerekir. Güncel doğruluk veya model sıralaması olarak sunulmaz.

Sabit piksel eşikleri, ilk 15 kareden alınan başlangıç konumu, kamera perspektifi,
örtüşme ve yanlış zemin seçimi ölçümleri etkileyebilir. Drop Jump başlangıç
beklemesinin GCT'ye karışması ayrıca incelenmelidir.

## İlgili proje ve lisans

[CMJ karşılaştırma reposu](https://github.com/emirhanuludogan/cmj-camera-comparison)
ön/yan kamera kayıtlarını ve manuel Kinovea referanslarını ayrı bir çalışma olarak ele alır.

Depoda mevcut [LICENSE](LICENSE) dosyası korunur. Üçüncü taraf kod, model
ağırlıkları ve veriler için ilgili kaynakların kendi lisansları ayrıca incelenmelidir.
