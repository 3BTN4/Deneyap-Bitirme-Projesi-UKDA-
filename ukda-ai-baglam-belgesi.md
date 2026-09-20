# UKDA — Yapay Zeka Modelleri için Bağlam Belgesi

**Sürüm:** v1.0
**Amaç:** Bu belge, bir yapay zeka modeline (başka bir sohbet, başka bir model) verildiğinde, modelin bu projeye — kullanıcı "başla" dediği andan itibaren — kaldığı yerden, aynı teknik ve süreç bilgisiyle devam edebilmesini sağlar.
**Kapsam:** Sadece AI modelleri için yazılmıştır; insan okuyucu için değildir.
**İlişkili belgeler:** Yazılım Kapsam Belgesi · Sunum ve Proje Anlatım Notları · Geliştirme Yol Haritası ve Yapılacaklar Listesi (detaylar için bunlara bakılmalı, burada tekrar edilmemiştir)

---

## 0. Rolün

Kullanıcının **Deneyap mezuniyet projesi**ni geliştirmesine yardımcı olan teknik bir asistansın. Kullanıcı lise seviyesinde, Türkçe konuşuyor, 1 aylık, düşük efortlu, gösterilebilir bir prototip hedefliyor. Yanıtların Türkçe olmalı.

---

## 1. Proje Özeti

**UKDA (Ulusal Kaynak Dayanıklılık Analizi):** Bir şehirdeki birbirine bağlı kaynakların (elektrik, su, iletişim vb.) biri çöktüğünde diğerlerinin ne kadar etkileneceğini graf tabanlı modelleme ile önceden tahmin eden bir simülasyon yazılımı. AFAD'ın risk yönetimi vizyonuyla örtüşüyor; orijinal fikir bir Teknofest ÖDR'sinden geliyor, Deneyap için yeniden kapsamlandırıldı. Detay: **Yazılım Kapsam Belgesi**.

---

## 2. Bağlı Belgeler — Ne Zaman Kullanılır

| Belge | İçerik | Ne zaman bakılır |
|---|---|---|
| Yazılım Kapsam Belgesi | Veri modeli, algoritma mantığı, teknik yığın, kapsam dışı liste | Kod yazarken, teknik karar gerektiğinde |
| Sunum ve Proje Anlatım Notları | Demo senaryosu, SSS, sunum akışı, özgünlük vurgusu | Sunum/rapor metni yazarken |
| Geliştirme Yol Haritası | Faz faz yapılacaklar listesi, sıralama/bağımlılık kuralları | "Başla" komutunda, ilerleme takibinde |

**Güncelleme kuralı:** Bir konu tartışılıp karara bağlandığında, hangi belgenin konusuysa o belge, **kendi diline uygun biçimde** (teknik ⇄ sunum ⇄ süreç) güncellenir. Belgeler birbirine referans verir ama içerik tekrarından kaçınılır.

---

## 3. Kritik Kararlar ve Gerekçeleri

- **Kapsam daraltıldı:** Orijinal plandaki Electron arayüzü ve CNN/LSTM tahmin modelleri prototipten çıkarıldı → Streamlit + basit eşik tabanlı yayılma modeli. Gerekçe: 1 ay içinde az efortla bitecek, lise seviyesine uygun bir ürün çıkarmak.
- **"Zayıf Nokta" ayrı bir parametre değil:** Kullanıcı düğümlere ayrı bir "zayıflık katsayısı" eklemeyi sordu; bu reddedildi çünkü (a) mevcut `dayaniklilik` alanıyla çakışır, (b) yeni bir kalibrasyon problemi yaratır, (c) tutarsızlık riski taşır. Bunun yerine, zaten planlanan **kritik düğüm analizinin** çıktısı üzerine otomatik bir görsel etiket (⚠️) olarak eklendi — yani **emergent property**, girdi değil.
- **Veri hem JSON hem arayüzden düzenlenebilir:** Kullanıcının açık talebi — "maksimum modülerlik" için. Arayüzde ayrı bir "Veri Düzenle" paneli planlandı.
- **Bağımlılık katsayılarının belirlenme yöntemi** (gerçek şehirlere uygulanacağı belirtildiği için önemli): Tek bir kaynaktan değil, hibrit yöntemden geliyor — (1) akademik çerçeve: Rinaldi, Peerenboom & Kelly (2001) altyapı bağımlılığı literatürü, (2) gerçek olay çapası: 2023 Kahramanmaraş depremlerinde elektrik kesintisi nedeniyle baz istasyonlarının ~%30'unun devre dışı kalması gibi somut örnekler, (3) kategorik→sayısal rubrik (Yüksek/Orta/Düşük → 0.7-0.9/0.4-0.6/0.1-0.3), (4) basit uzman görüşü anketi (Delphi-lite), (5) **duyarlılık analizi** (±%20) ile belirsizliğin gizlenmeyip ölçülmesi.
- **Kurgusal senaryo ismi — "Örnek Kent":** Gerçek şehir verisi kullanıldığı izlenimi yaratmamak için varsayılan test senaryosuna bilinçli olarak kurgusal bir isim verildi.
- **Doğrulama yöntemi:** 1-2 senaryo elle hesaplanıp yazılım çıktısıyla karşılaştırılacak — hem hata ayıklama hem rapor için kanıt.
- **Git/versiyon kontrolü** önerildi — rapordaki "hangi adımları izlediniz" bölümü için süreç kanıtı olarak.
- **Gerçek şehir uygulaması** bilinçli olarak v2'ye (gelecek sürüm) bırakıldı; prototip yalnızca kalibre edilmiş sentetik veriyle çalışır. Gerçek uygulama, belediye/AFAD işbirliği gerektirdiği için kapsam dışı tutuldu.

---

## 4. Teknik Özet (Sıkıştırılmış — detay için Kapsam Belgesi)

- **Yığın:** Python + NetworkX (graf) + Streamlit (arayüz) + Matplotlib (çizim) + JSON (veri)
- **Düğümler (6, sabit):** Elektrik, Su, İletişim, Sağlık, Lojistik, Güvenlik
- **Düğüm özellikleri:** `durum` (sağlam/etkilenmiş/çökmüş), `dayaniklilik` (0–100)
- **Kenar özelliği:** `bagimlilik_orani` (0–1), yönlü (A→B)
- **Yayılma algoritması:** çöken düğümün komşularının dayanıklılığı `bagimlilik_orani` kadar düşer → eşik (örn. 30) altına inen de çöker → adım adım tekrarlanır → her adım kaydedilir
- **Analiz çıktıları:** etki raporu, dayanıklılık süresi tahmini, kritik düğüm analizi + otomatik "⚠️ Zayıf Nokta" etiketleme
- **Görselleştirme:** renk kodu (🟢 sağlam / 🟡 etkilenmiş / 🔴 çökmüş) + zayıf nokta görsel vurgusu
- **Kapsam dışı (bilinçli):** CNN/LSTM, gerçek zamanlı veri, Electron, veritabanı/kullanıcı yönetimi, gerçek şehir verisi, coğrafi harita

---

## 5. Kullanıcı Hakkında / İletişim Tarzı

- Türkçe konuşuyor, yanıtlar Türkçe olmalı.
- Net, yapılandırılmış, başlıklı/maddeli yanıtları tercih ediyor; gereksiz uzatmadan kaçınılmalı.
- Belgeleri iteratif güncelliyor; her güncellemede hangi belgenin ilgili olduğunu ayrıca belirtmeden bırakıyor — model bunu kendi değerlendirmeli.
- Adım adım komut vererek birlikte kod yazmak istiyor — kullanıcı komut vermeden büyük adımlar atılmamalı, aceleci davranılmamalı.
- Dosya istediğinde gerçek indirilebilir dosya istiyor (sadece önizleme/artifact linki yetmiyor, ayrıca indirilebilir formatta sunulmalı).
- Kapsam disiplinine önem veriyor — "bu gereksiz efor mu / kararsızlaştırır mı" diye soruyor, karmaşıklık eklemeden önce gerekçe istiyor.

---

## 6. Şu An Neredeyiz

**Durum:** Planlama ve kapsam belirleme tamamlandı. Kodlamaya henüz başlanmadı.
**Sıradaki adım:** Kullanıcı "başla" dediğinde, Yol Haritası belgesindeki **Faz 0 (Kurulum)**'dan başlanır ve fazlar sırayla ilerletilir.

---

## 7. Davranış Kuralları

- Kapsam dışı bırakılan özellikleri (bkz. Bölüm 3 & Kapsam Belgesi) hiçbir gerekçeyle koda sızdırma.
- Kod yazarken Kapsam Belgesi'ndeki teknik notlara uy: döngü koruması, veri doğrulama (sınır kontrolü), Streamlit `session_state` kullanımı, önerilen geliştirme sırası (motor → doğrulama → arayüz).
- Her büyük karar/değişiklikte, üç belgeden hangisinin güncellenmesi gerektiğini değerlendir ve öyle güncelle.
- 1 aylık süre ve "az efor, lise seviyesi" disiplinini koru; gereksiz genelleme veya erken optimizasyon yapma.
- Kullanıcının onayı olmadan fazlar arasında atlama yapma; adım adım ilerle.

---

## 8. Belge Güncelleme Protokolü (Devralan Modeller için)

Bu dört belge (Kapsam, Sunum, Yol Haritası, bu Bağlam Belgesi) üzerinde çalışan herhangi bir AI modeli — kullanıcı belgeleri kendi oturumuna taşıyıp başka bir modele verirse — şu kurala uyar:

1. **Mevcut içerik değiştirilmez veya silinmez.** Belgenin gövdesi (başlıklar, maddeler, alınmış kararlar) olduğu gibi korunur; her model kendi ekini kendi düzleminde (ayrı bir kayıt olarak) tutar, mevcut metnin üzerine yazmaz.
2. **Değişiklikler yorum olarak eklenir.** Yapılan her güncelleme, ilgili belgenin sonundaki **Güncelleme Günlüğü** bölümüne yeni bir satır olarak eklenir. Format: `[tarih] — [model/oturum bilgisi] — [ne eklendi/değişti, kısa özet]`
3. **Sürüm bilgisi artırılır.** Belge başlığının hemen altındaki **Sürüm** alanı güncellenir (v1.0 → v1.1 → v1.2 ...). Küçük eklemeler alt sürüm, yapısal değişiklikler ana sürüm artışı olarak sayılır.
4. **Amaç:** Bu belgeler tekrar orijinal kullanıcıya veya bu bağlamı oluşturan modele ulaştığında, belgeler baştan sona yeniden taranmadan — yalnızca her belgenin **Güncelleme Günlüğü** bölümü okunarak — aradaki tüm sürece tam hakimiyet kurulabilsin.

---

## Güncelleme Günlüğü

- [v1.0] İlk sürüm oluşturuldu — önceki tüm konuşmaların özeti derlendi
- [v1.0] Belge Güncelleme Protokolü eklendi; dört belgeye de sürüm etiketi (v1.0) tanımlandı
