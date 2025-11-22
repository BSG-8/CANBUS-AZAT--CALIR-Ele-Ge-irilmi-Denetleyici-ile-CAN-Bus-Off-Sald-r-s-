PROJE RAPORU: SWOT ANALİZİ (CAN BUS-OFF SALDIRISI)
Proje Adı: Elektrikli Araç Şarj Altyapısı Güvenliği için Yapay Zekâ Tabanlı Anomali Tespit Sistemi
Rapor Tarihi: 3 Kasım 2025
Hazırlayan: Azat Öcalır

1. ANALİZ KONUSU
Bu SWOT analizi, "Ele Geçirilmiş Denetleyici ile CAN Bus-Off Saldırısı (Senaryo D-02)"
özelinde, geliştirilmekte olan "Yapay Zekâ Tabanlı Anomali Tespit Sistemi" projesinin güçlü ve
zayıf yönlerini, fırsatlarını ve tehditlerini değerlendirmektedir.

2. GÜÇLÜ YÖNLER (STRENGTHS)
(Projemizin bu saldırıyı ele almadaki iç avantajları)

● Yüksek Tespit Edilebilirlik (Belirgin Semptomlar):
Bu saldırı, doğası gereği "gürültülü" bir saldırıdır. AI sisteminin izlediği ana veri kaynakları
olan Heartbeat (yaşam sinyali) ve MeterValues (sayaç verileri) akışını anında keser. Bu ani
ve tam kesinti, AI modelinin "normal operasyon" temel çizgisinden (baseline) keskin bir
sapma olarak kolayca tespit edebileceği güçlü bir anomalidir.
● Çok Katmanlı Korelasyon Yeteneği:
Projemiz sadece tek bir veriye bakmaz. AI modeli, Heartbeat kaybını, MeterValues
kesintisini ve istasyondan gelmesi muhtemel bir "Faulted" (Arızalı) durumunu saniyeler
içinde birbiriyle ilişkilendirebilir. Bu "ani ölüm" imzasını normal bir ağ kesintisinden ayırt
etme potansiyeline sahiptir.
● Eğitilebilirlik:
Senaryo (D-02), AI modelini eğitmek için net bir "saldırı imzası" (attack signature) sunar.
Bu senaryo test ortamında (testbed) yeniden oluşturularak, modelin bu spesifik saldırı
türünü %100'e yakın bir doğrulukla tanıması sağlanabilir.
3. ZAYIF YÖNLER (WEAKNESSES)
(Projemizin bu saldırıyı ele almadaki iç kısıtlamaları)

● CAN-bus Görünürlüğünün Eksikliği (IT vs. OT):
Projemiz, CSMS (Merkezi Yönetim Sistemi) tarafında, yani IT katmanında çalışmaktadır.
Saldırının kök nedeni ise OT katmanında (iç CAN-bus ağı) gerçekleşir. AI sistemimiz, CAN
veri yolu yükünün %100'e fırladığını veya hata çerçevelerini doğrudan göremez. Sadece
saldırının sonucunu (iletişim kaybı) görebilir.
● Tepkisellik (Reaktif) vs. Önleme (Proaktif):
AI sistemimiz bu saldırıyı önleyemez. Saldırıyı ancak gerçekleştikten saniyeler sonra, yani
istasyon "Bus-Off" durumuna geçip çevrimdışı olduktan sonra tespit edebilir. Raporun
"Önlemler" bölümündeki (Katman 1-2) "Güvenli Bellenim" veya "Gateway Filtrelemesi"
gibi gerçek önleyici tedbirler, projemizin değil, donanım üreticisinin sorumluluğundadır.
● Donanımsal Arıza ile Karışma Riski:
Gerçek bir donanım arızası (örn. arızalı bir CAN alıcı-vericisi veya güç elektroniği kartı
hatası) da istasyonun "Bus-Off" durumuna geçmesine ve Heartbeat göndermeyi
durdurmasına neden olabilir. AI modelimizin, kötü amaçlı bir saldırı ile normal bir donanım
arızasını %100 kesinlikle ayırt etmesi, sadece OCPP verilerine bakarak çok zor olabilir.
4. FIRSATLAR (OPPORTUNITIES)
(Projemizin bu tehdidi kullanarak elde edebileceği dış avantajlar)

● IT-OT Güvenlik Açığını Kapatma Değeri:
Bu senaryo, IT (OCPP) ve OT (CAN-bus) dünyaları arasındaki tehlikeli "köprü" zafiyetini
mükemmel bir şekilde göstermektedir. Projemizi, "OT katmanında neler olduğunu IT
verilerinden tahmin edebilen" kritik bir güvenlik köprüsü olarak pazarlamak için büyük bir
fırsattır.
● Üretici İş Birliği İçin Veri Sağlama:
AI sistemimiz bu "ani ölüm" anomalisini tespit ettiğinde, bu veriyi doğrudan donanım
üreticisine (CP üreticisi) iletebiliriz. Üreticinin kendi iç logları (eğer varsa) ile bizim
tespitimizi birleştirmek, sorunun "saldırı" mı yoksa "arıza" mı olduğunu doğrulamak için
(bkz. Zayıf Yön W3) bir fırsat sunar.
● Gelişmiş Kurtarma Prosedürleri Tetikleme:
AI, bu saldırıyı normal bir ağ kesintisinden farklı olarak "Kritik Donanım Felci" olarak
sınıflandırabilir. Bu tespit, standart bir "yeniden başlat" komutu yerine, doğrudan bir
"teknisyen gönder" veya "fiziksel müdahale gerektiren acil durum" uyarısı oluşturarak
operasyonel maliyeti (OPEX) düşürmek için bir fırsat sunar.
5. TEHDİTLER (THREATS)
(Projemizin başarısını engelleyebilecek dış riskler)

● Yanlış Pozitif Riski (Alarm Yorgunluğu):
En büyük tehdit, Zayıf Yönler (W3) bölümünde belirtilen donanım arızalarıdır. Eğer şarj
istasyonları sık sık donanımsal olarak arızalanıp "Bus-Off" oluyorsa, AI sistemimiz sürekli
"saldırı olabilir" uyarısı verir. Bu durum, operatörlerde alarm yorgunluğuna (alarm fatigue)
yol açar ve gerçek saldırıların göz ardı edilmesine neden olabilir.
● Saldırı Evrimi (Düşük ve Yavaş Saldırılar):
Bu senaryo "ani ve gürültülü" bir saldırıyı tanımlar. Ancak saldırganlar, AI'dan kaçınmak
için saldırılarını değiştirebilir. Örneğin, CAN-bus'ı %100 kilitlemek yerine, sadece %50 yük
bindirerek veya günde sadece birkaç kez hata çerçevesi göndererek istasyonu
"yavaşlatabilirler". Bu "düşük ve yavaş" (low-and-slow) saldırıyı normal bir ağ
gecikmesinden ayırmak çok daha zordur.
● İlk Erişimin Gizliliği:
Saldırı, "Aşama 1: İlk Erişim"in (örn. Kötü Amaçlı Bellenim) başarılı olduğunu varsayar. Eğer
bu ilk erişim adımı, AI sistemimizin tespit edemeyeceği kadar gizli bir şekilde (anomali
yaratmadan) gerçekleştirilirse, saldırının kendisi başlayana kadar hiçbir şey göremeyiz.