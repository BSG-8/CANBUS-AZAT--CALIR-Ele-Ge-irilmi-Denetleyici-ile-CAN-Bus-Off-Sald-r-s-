PROJE RAPORU: ANOMALİ SENARYOSU (HİZMET ENGELLEME)
Proje Adı: Elektrikli Araç Şarj Altyapısı Güvenliği için Yapay Zekâ Tabanlı Anomali Tespit Sistemi
Rapor Tarihi: 3 Kasım 2025
Hazırlayan: Azat Öcalır

1. SENARYO DETAYI
Ele Geçirilmiş Denetleyici ile CAN Bus-Off Saldırısı

Özet (Problem Tanımı)
Bu senaryo, bir Elektrikli Araç Şarj Noktasının (CP - Charge Point) iç operasyonel ağına (OT)
yönelik gelişmiş bir siber-fiziksel hizmet reddi (Denial of Service - DoS) saldırısını
detaylandırmaktadır.
Saldırgan, öncelikle şarj noktasının ana denetleyicisini (MCU/SoC) bir ağ zafiyeti (örneğin,
OCPP üzerinden komut enjeksiyonu) veya kötü amaçlı bellenim güncellemesi yoluyla ele
geçirir. Ardından, bu ele geçirilmiş "köprü" bileşenini kullanarak, şarj cihazının dahili CAN-bus
ağına kasıtlı olarak yüksek frekanslı, yüksek öncelikli veya bozuk CAN çerçeveleri enjekte eder.
Bu eylem, CAN protokolünün yerleşik bir özelliği olan "hata yönetimi" mekanizmasını tetikler.
Güç elektroniği gibi kritik dahili bileşenler, bu anormal trafiği bir ağ hatası olarak algılar ve
kendilerini korumak için "Bus-Off" durumuna geçerler. Bu durumda, ilgili bileşenler ağdan
kopar, iletişimi tamamen durdurur ve sonuç olarak şarj işlemi fiziksel olarak imkansız hale gelir.

Senaryonun Amacı ve Kapsamı
● Amaç (IT-OT Pivot Gösterimi): Bir dış ağ protokolü (OCPP) üzerindeki bir zafiyetin,
fiziksel donanımı (güç elektroniği) çalışamaz hale getiren bir iç ağ (CAN-bus) saldırısına
nasıl dönüştürülebileceğini (pivot yapılabileceğini) göstermek.
● Amaç (Protokol Zafiyeti İstismarı): CAN-bus protokolünün güvenlik için değil,
güvenilirlik (reliability) için tasarlandığını kanıtlamak. Saldırının, protokolün "hata toleransı"
özelliğini bir silaha dönüştürmesini analiz etmek.
● Kapsam: Saldırı, CP'nin dış IT ağı (OCPP) ile iç OT ağı (CAN-bus) arasındaki "köprü"
bileşeni olan ana denetleyiciyi (MCU) hedefler. Fiziksel hedef, güç elektroniği
denetleyicisidir.
STRIDE Tehdit Sınıflandırması
● Denial of Service (Hizmet Reddi): Bu, en saf hizmet reddi senaryolarından biridir; zira
hedef veri çalmak değil, hizmeti (şarj etme) tamamen durdurmaktır.
● Tehdit Modeli: Saldırgan (Orta-yüksek düzeyde teknik bilgiye sahip), gömülü sistemler ve
CAN-bus bilgisiyle, ele geçirdiği ana denetleyici üzerinden iç ağa saldırır.
Gerekli Koşullar ve İstismar Edilen Zafiyetler
Saldırının başarılı olması için Aşama 1'in (İlk Erişim) gerçekleşmiş olması gerekir:
● Kötü Amaçlı Bellenim Güncellemesi: Saldırganın, içinde "CAN-bus saldırı yükü"
gizlenmiş sahte bir bellenimi CP'ye göndermiş olması.
● Komut Enjeksiyonu: Saldırganın, CP'nin OCPP mesajlarını işleyen yazılımındaki bir girdi
doğrulama eksiğini (örn. CVE-2024-23971) istismar ederek ana denetleyicide 'root' erişimi
kazanmış olması.
● Zafiyet: CAN-bus protokolünün, hata toleransı özelliğinin (Bus-Off mekanizması) istismar
edilmesi.

Saldırı Yöntemleri ve Adım Adım Akış
Saldırı, kurban şarj cihazı üzerinde tam bir felce yol açmak için dört ana aşamada gerçekleşir.
Aşama 1: İlk Erişim (IT Katmanı İhlali)
(Bu senaryo, bu erişimin zaten sağlandığını varsayar.) Saldırgan, kötü amaçlı bellenim veya
komut enjeksiyonu yoluyla ana denetleyici (MCU) üzerinde kod çalıştırma yetkisi kazanır.
Aşama 2: Yürütme ve "Köprü" Kullanımı
Ana denetleyiciye yerleşen kötü amaçlı kod (payload) etkinleştirilir. Saldırgan, hem dış OCPP
ağına hem de iç CAN-bus ağına bağlı olan bu "köprü" bileşenini OT dünyasına saldırmak için
kullanır.
Aşama 3: CAN-Bus Taşkını (OT Katmanı Saldırısı)
Kötü amaçlı kod, dahili CAN-bus üzerinde bir "gürültü fırtınası" başlatır:
● Teknik A (Öncelik Taşkını): Kod, CAN-bus üzerinde mümkün olan en yüksek öncelikli
mesaj olan ID 0x000 ile saniyede binlerce çerçeve (frame) yayınlamaya başlar. Düşük
öncelikli meşru mesajlar (durum raporları vb.) veriyoluna asla erişemez ve ağ kilitlenir.
● Teknik B (Hata Çerçevesi Enjeksiyonu): Kod, kasıtlı olarak bozuk çerçeveler (yanlış
CRC, format hatası) yayınlar. Bu durum, ağdaki diğer tüm meşru cihazların sürekli "Hata
Çerçevesi" yayınlamasına neden olur ve ağı bir hata döngüsüne sokar.
Aşama 4: "Bus-Off" Durumu ve Fiziksel Sonuç

Güç elektroniği denetleyicisi, Aşama 3'teki taşkın nedeniyle kendi "İletim Hata Sayacını"
(TEC) hızla artırır.
Sayaç kritik eşiği (genellikle 255) aştığında, protokolün "hatalı düğümü izole etme" kuralı
gereği, güç elektroniği denetleyicisi otomatik olarak Bus-Off durumuna geçer.
Sonuç: Güç elektroniği denetleyicisi kendini ağdan fiziksel olarak ayırır. Artık hiçbir CAN
mesajı gönderemez veya alamaz. Ana denetleyiciden gelen "şarjı başlat" komutlarına
yanıt veremez.
Nihai Etki: Şarj işlemi durur ve cihaz donanımsal bir yeniden başlatma (hard reboot)
yapılana kadar bir daha başlatılamaz.
Tespit Yöntemleri ve Anomali Göstergeleri
Bu saldırı, hem ağ (OCPP) hem de donanım (CAN) katmanında çok belirgin izler bırakır:
● OCPP (Ağ) Katmanı Gözlemleri:
○ Anomali: CP, CSMS'e Heartbeat (yaşam sinyali) göndermeyi durdurur.
○ Tespit: CSMS, CP'nin "Çevrimdışı" (Offline) olduğunu algılar.
○ Anomali: MeterValues (Sayaç Değerleri) akışı aniden kesilir.
○ Tespit: CSMS, devam eden bir şarj oturumu varsa veri kaybını algılar.
● CAN (Veri Yolu) Katmanı Gözlemleri (CAN IDS ile):
○ Anomali (Veri Yolu Yükü): Normalde %20-40 olan yük, aniden %90-100 seviyesine
fırlar.
○ Tespit: CAN IDS, veri yolu yükü metriğini izleyerek bu ani sıçramayı anında
yakalayabilir.
○ Anomali (Mesaj Frekansı): ID 0x000 (veya başka bir sahte ID) frekansı saniyede
binlere fırlarken, diğer tüm meşru ID'lerin (örn. 0x300 - Metre) frekansı sıfıra düşer.
○ Tespit: CAN IDS, mesaj frekanslarını ve entropiyi izleyerek temel çizgiden (baseline)
bu aşırı sapmayı tespit eder.
○ Anomali (ECU Durumu): Güç elektroniği ECU'su aniden "sessizleşir" ve ağdan
kaybolur.
○ Tespit: IDS, kritik ECU'ların "yaşam sinyallerini" (heartbeat) kaybettiğini algılar.
○ Anomali (Hata Çerçeveleri): (Teknik B kullanılırsa) CAN hata çerçevesi sayacında
logaritmik bir artış gözlemlenir.
○ Tespit: IDS veya denetleyici logları, hata sayacındaki anormal artışı işaretler.

Olası Etkiler
● Finansal Etki: Birincil etki, şarj hizmetinin tamamen ve anında durdurulmasıdır. O anda
şarj olan bir aracın şarjı kesilir ve yeni bir şarj işlemi başlatılamaz. (Risk Puanı Hasar:
10/10).
● Operasyonel Etki: Kurtarma için fiziksel müdahale (manuel yeniden başlatma veya
teknisyen ziyareti) gerektirebilir, bu da yüksek operasyonel maliyete (OPEX) yol açar.
● İtibar Kaybı: İstasyonların güvenilmez hale gelmesi.
Önlemler ve Azaltma Stratejileri
Bu siber-fiziksel saldırıyı önlemek için çok katmanlı bir savunma stratejisi şarttır:
● Katman 1: Önleme (İlk Erişimi Engelleme - IT Katmanı):
○ Güvenli Bellenim Yönetimi: Tüm bellenim güncellemeleri kriptografik olarak
imzalanmalı ve CP tarafından indirilip kurulmadan önce bu imza doğrulanmalıdır.
○ Katı Girdi Doğrulama: OCPP mesajlarından gelen tüm girdiler sisteme geçirilmeden
önce sıkı bir şekilde doğrulanmalı ve temizlenmelidir.
○ En Az Ayrıcalık Prensibi: OCPP aracısı gibi dışa dönük servisler, 'root' (en yetkili)
kullanıcı olarak değil, sınırlı yetkilere sahip bir kullanıcı hesabıyla çalıştırılmalıdır.
● Katman 2: İzolasyon (Köprüyü Güçlendirme - Siber-Fiziksel Katman):
○ Zorunluluk (Hız Sınırlaması): MCU'nun CAN veriyoluna saniyede gönderebileceği

toplam mesaj sayısı veya belirli bir ID için mesaj sayısı katı bir şekilde
sınırlandırılmalıdır. Bu, taşkın (flooding) saldırılarını anında durdurur.
● Zorunluluk (Beyaz Liste Filtrelemesi): MCU'dan güç elektroniği veriyoluna yalnızca
önceden tanımlanmış, meşru CAN ID'lerinin (örn. 0x201, 0x210) geçişine izin verilmelidir.
ID 0x000 veya rastgele ID'ler gibi bilinmeyen tüm çerçeveler engellenmelidir.
● Katman 3: Tespit ve Kurtarma (OT Katmanı):
○ CAN Saldırı Tespit Sistemi (IDS): "Gözlemlenebilir Anomaliler" bölümünde listelenen
metrikleri (veri yolu yükü, mesaj frekansı, hata sayaçları) sürekli izleyen hafif bir IDS
yazılımı çalıştırılmalıdır.
○ Hızlı Kurtarma Mekanizması: Bir "Bus-Off" durumu tespit edildiğinde, sistemin
otomatik olarak (ve güvenli bir şekilde) kendini sıfırlayıp 30 saniye içinde tekrar
çevrimiçi olmasını sağlayan bir "watchdog timer" veya yazılımsal bir kurtarma
prosedürü uygulanmalıdır.