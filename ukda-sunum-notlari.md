# UKDA — Sunum ve Proje Anlatım Notları

**Sürüm:** v1.0
**Proje:** Ulusal Kaynak Dayanıklılık Analizi (UKDA)
**Belge türü:** Sunuma yönelik proje açıklaması
**Eşlik ettiği belge:** UKDA — Prototip Yazılım Kapsam Belgesi (teknik detaylar için)
**Kullanım amacı:** Jüri sunumu, proje tanıtımı, rapor metni taslağı

> Bu belge, teknik kapsam belgesiyle birlikte güncellenir. Biri güncellendiğinde diğerine eklemeye değer bir şey varsa, o belgenin kendi diline uygun şekilde işlenir. Burada teknik detaydan çok **anlatı ve sunum** önceliklidir.

---

## 1. Tek Cümlelik Özet (Asansör Konuşması)

> UKDA, bir şehirdeki elektrik, su, iletişim gibi birbirine bağlı kaynaklardan biri çöktüğünde diğerlerinin ne kadar etkileneceğini önceden hesaplayan, kriz yönetimine karar desteği sunan bir simülasyon yazılımıdır.

---

## 2. Problem: Neden Bu Proje?

Modern şehirlerde elektrik, su, iletişim gibi temel kaynaklar birbirine sıkı sıkıya bağlıdır. Bir kaynak çöktüğünde bu etki, devrilen domino taşları gibi diğer kaynaklara da yayılır.

Bunun ne kadar gerçek bir tehdit olduğunu 2023'teki Kahramanmaraş depremlerinde gördük: elektrik kesintileri ve kopan hatlar nedeniyle bölgedeki baz istasyonlarının yaklaşık %30'u ilk saatlerde tamamen devre dışı kaldı — tam da arama-kurtarma çalışmalarının en kritik olduğu ilk 72 saatte.

**UKDA, böyle bir zincirleme etkiyi krizden önce görebilmek için var.**

---

## 3. Çözümümüz Ne Yapıyor? (Teknik Olmayan Anlatım)

- Şehri, görünmez bağlarla birbirine bağlı bir ağ gibi düşünün.
- Bir kaynak (örn. elektrik) çöktüğünde, yazılım bu çöküşün diğer kaynaklara ne kadar yayılacağını hesaplar.
- En "kritik" kaynağı — yani çökerse en çok zincirleme hasara yol açacak olanı — otomatik olarak bulur ve işaretler.
- Tüm bunlar, kod bilmeyen birinin bile kullanabileceği sade bir ekran üzerinden yapılır.

---

## 4. Bizi Farklı Kılan (Özgünlük)

- Mevcut sistemler kaynakları genelde ayrı ayrı izler; UKDA bunları **tek bir bağlı ağ** olarak birleştirir.
- Sadece "ne bozuldu" demekle kalmaz, "bundan sonra ne olacak" diye zincirleme etkiyi önceden tahmin eder.
- En kritik kaynağı otomatik bulur — kriz yöneticisi bunu tahmin etmek zorunda kalmaz.

---

## 5. Demo Senaryosu: "Örnek Kent"

Canlı sunumda izlenecek hikâye:

**Sahne:** Örnek Kent'te bir gece, ana elektrik santralinde teknik arıza çıkar.

1. **Simülasyonu başlat:** "Elektrik" düğümü seçilir → **"Çöküşü Simüle Et"** butonuna basılır
2. **Adım 1:** Elektrik çöker (🔴 kırmızı)
3. **Adım 2:** Su ve İletişim etkilenir (🟡 sarı) — çünkü ikisi de elektriğe yüksek oranda bağımlı
4. **Adım 3:** İletişime bağımlı Güvenlik ve Lojistik, Su'ya bağımlı Sağlık da etkilenmeye başlar
5. **Sonuç ekranı:** Sistem, Elektrik'i otomatik olarak **"⚠️ Zayıf Nokta"** olarak işaretler — çünkü en fazla zincirleme hasara o yol açmıştır

**Anlatılacak mesaj:**
> "Kriz anında hangi kaynağın önceliklendirileceğini tahmin etmek genelde tecrübeye ve şansa kalır. UKDA bu kararı veriye dayandırıyor — kriz yöneticisi, elektriğin en kritik nokta olduğunu çöküş gerçekleşmeden önce bilebilir."

---

## 6. Sık Sorulabilecek Sorular (Jüri Hazırlığı)

**"Bu gerçek şehir verisi mi?"**
Hayır. Prototipte kurgusal **"Örnek Kent"** senaryosu kullanıyoruz. Bağımlılık oranlarını ilgili literatürden (Rinaldi, Peerenboom & Kelly, 2001) ve gerçek afet raporlarından (2023 Kahramanmaraş depremi gibi) yola çıkarak belirledik. Gerçek şehir verisiyle kalibrasyon, ilgili kurumlarla (belediye, AFAD) işbirliği gerektiren bir sonraki adım.

**"Sisteminizin doğru çalıştığını nasıl biliyorsunuz?"**
Elle hesapladığımız basit senaryoları (örn. Elektrik çökerse Su'nun ne kadar etkileneceği) yazılımın ürettiği sonuçla karşılaştırarak doğruladık.

**"Katsayılar kesin mi?"**
Hayır, ve bunu bilinçli olarak belirtiyoruz. Belirsizliği görmezden gelmek yerine, her katsayıyı ±%20 değiştirip sonucun ne kadar değiştiğini test ediyoruz (duyarlılık analizi). Sonuç çok değişiyorsa o bağlantıyı ayrıca "belirsiz/kritik" olarak işaretliyoruz.

**"Gelecekte neler eklenecek?"**
Derin öğrenme tabanlı tahmin (CNN/LSTM), gerçek zamanlı veri entegrasyonu, gerçek şehir verisiyle doğrulama ve coğrafi harita katmanı — bunların hepsi bilinçli olarak sonraki sürüme bırakıldı; prototipte hız ve sağlamlığa odaklandık.

---

## 7. Sunum Akışı Önerisi

| Süre | Bölüm |
|---|---|
| 30 sn | Açılış — çarpıcı problem örneği (Kahramanmaraş rakamı) |
| 30 sn | Çözüm — tek cümlelik özet |
| 2-3 dk | Canlı demo — Örnek Kent senaryosu |
| 30 sn | Özgünlük — bizi farklı kılan |
| 30 sn | Gelecek vizyonu |
| 15 sn | Kapanış — etki cümlesi |

---

## 8. Gelecek Vizyonu (Kısa)

- Derin öğrenme (CNN/LSTM) ile daha kesin tahminler
- Gerçek şehir verisiyle pilot uygulama (belediye/AFAD işbirliği)
- Coğrafi harita katmanı ile bölgesel görselleştirme

---

## Güncelleme Günlüğü

*Bu bölüm, belgeye yapılan büyük eklemeleri kısaca listeler.*

- İlk sürüm oluşturuldu (proje özeti, demo senaryosu, SSS, sunum akışı)
