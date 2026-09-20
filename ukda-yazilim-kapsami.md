# UKDA — Prototip Yazılım Kapsam Belgesi

**Sürüm:** v1.0
**Proje:** Ulusal Kaynak Dayanıklılık Analizi (UKDA)
**Belge türü:** Yazılım kapsam tanımı
**Aşama:** Tasarım → Geliştirme
**Hedef süre:** 1 ay
**Durum:** Prototip kapsamı (v1)

---

## 1. Veri Modeli (Ağ Yapısı)

Sistem, bir şehri **yönlü ve ağırlıklı graf** olarak temsil eder.

**Düğümler (6 adet, sabit):**
Elektrik · Su · İletişim · Sağlık · Lojistik · Güvenlik

**Her düğümün özellikleri:**

| Özellik | Açıklama |
|---|---|
| `durum` | sağlam / etkilenmiş / çökmüş |
| `dayaniklilik` | 0–100 arası puan (kaynağın kendi başına direnme kapasitesi) |

**Kenarlar:**
A → B yönünde tanımlanır ve `bagimlilik_orani` (0–1) taşır.
*Örnek:* Su → Elektrik: 0.7 (Su, elektriğe %70 bağımlı)

**Veri saklama:**
Tüm ağ tanımı ayrı bir **JSON** dosyasında tutulur; böylece veri katmanı, kod katmanından tamamen bağımsızdır. Bu dosya sadece elle değil, **programın arayüzü üzerinden de** görüntülenip düzenlenebilir (düğüm ekleme/çıkarma, dayanıklılık puanı ve bağımlılık oranı güncelleme). Böylece kod değiştirmeden hem farklı senaryolar denenebilir hem de veri güncellemesi teknik bilgi gerektirmeden yapılabilir — bu da sistemin **maksimum modülerliğini** sağlar.

---

## 2. Çöküş Simülasyonu (Çekirdek Motor)

Kullanıcı bir düğümü çöktürür; sistem zincirleme etkiyi hesaplar.

**Yayılma mantığı:**

1. Çöken düğüme bağlı her komşunun dayanıklılığı, `bagimlilik_orani` kadar düşürülür.
2. Dayanıklılığı belirlenen eşiğin (örn. **30**) altına inen düğüm de "çökmüş" sayılır.
3. Etki bir sonraki halkaya yayılır.
4. Yeni çöken düğüm kalmayana kadar adım adım tekrarlanır.

**Adım kaydı:**
Her adım ayrı ayrı kaydedilir ve bir zaman çizelgesi üretilir.

> *Örnek çıktı:* 1. adımda Elektrik çöktü → 2. adımda Su ve İletişim etkilendi → 3. adımda Sağlık çöktü.

---

## 3. Analiz Çıktıları

Simülasyon sonunda üç bilgi üretilir:

**3.1 Etki Raporu**
Hangi düğüm ne kadar etkilendi, hangileri tamamen çöktü.

**3.2 Dayanıklılık Süresi (Tahmini)**
Her düğüm için kalan dayanıklılık puanından türetilen basit süre tahmini.
*Formül:* `kalan_puan × sabit_katsayı = tahmini saat`

**3.3 Kritik Düğüm Analizi**
Her düğüm tek tek çöktürülür, toplam hasar karşılaştırılır. En çok zincirleme hasara yol açan düğüm(ler) **"en kritik kaynak"** olarak raporlanır ve otomatik olarak **"⚠️ Zayıf Nokta"** etiketiyle işaretlenir (eşik: en yüksek hasarın üstündeki ilk 1-2 düğüm).

> **Not:** Bu etiket ayrı bir girdi parametresi (örn. yeni bir "zayıflık katsayısı") olarak eklenmemiştir. Zaten hesaplanan kritik düğüm analizi sonucunun üzerine görsel bir filtre olarak uygulanır — yani sistemin kendi dinamiklerinden türeyen bir sonuçtur, keyfi bir varsayım değil. Bu, hem kapsamı büyütmez hem de modele tutarsızlık riski taşıyan yeni bir alan eklemez.

**3.4 Doğrulama Yöntemi**
Sistemin doğru çalıştığını göstermek için 1-2 basit senaryo elle hesaplanır (örn. "Elektrik çökerse Su'nun dayanıklılığı ~%70 oranında düşmeli") ve yazılımın ürettiği sonuçla karşılaştırılır. Bu karşılaştırma hem geliştirme sırasında hata ayıklamayı kolaylaştırır hem de raporun "test/doğrulama" bölümü için somut kanıt sağlar.

---

## 4. Görselleştirme

- Ağ, düğüm ve oklardan oluşan bir graf olarak çizilir.
- Oklar üzerinde bağımlılık oranı yazılır.
- Simülasyon adımları sırayla gezilebilir (adım 1, 2, 3...).

**Renk kodu:**

| Renk | Anlam |
|---|---|
| 🟢 Yeşil | Sağlam |
| 🟡 Sarı | Etkilenmiş |
| 🔴 Kırmızı | Çökmüş |

**Zayıf Nokta vurgusu:**
Kritik düğüm analizinden gelen "⚠️ Zayıf Nokta" etiketi, ayrı bir renk değil, ek bir görsel işaretle gösterilir (örn. kalın çerçeve + ⚠️ ikonu). Böylece durum rengi (yeşil/sarı/kırmızı) ile kritiklik bilgisi karışmaz, ikisi bağımsız okunabilir.

---

## 5. Arayüz

Tek ekranlık, sade bir panel:

- Düğüm seçme listesi + **"Çöküşü Simüle Et"** butonu
- Graf görseli (ortada)
- Yan panel: etki raporu, dayanıklılık süreleri, adım geçmişi
- **"Veri Düzenle"** paneli: dayanıklılık puanlarını ve bağımlılık oranlarını arayüzden güncelleme, düğüm ekleme/çıkarma — JSON dosyasını elle açmaya gerek kalmadan
- **"Sıfırla"** butonu

---

## 6. Teknik Yığın

| Katman | Teknoloji | Gerekçe |
|---|---|---|
| Çekirdek mantık | **Python** | Ekibin bildiği dil, hızlı geliştirme |
| Graf yapısı | **NetworkX** | Hazır analiz fonksiyonları, sıfırdan algoritma yazmaya gerek yok |
| Arayüz | **Streamlit** | Tek dosyayla tarayıcıda çalışır, kurulum derdi yok |
| Çizim | **Matplotlib** | NetworkX ile doğrudan uyumlu |
| Veri | **JSON** | Kod değiştirmeden, arayüz üzerinden senaryo düzenleme |

---

## 7. Kapsam Dışı (Gelecek Sürüm)

Aşağıdakiler bilinçli olarak prototip dışında bırakılmıştır ve raporda "gelecek planı" olarak sunulacaktır:

- Derin öğrenme tabanlı tahmin (CNN / LSTM)
- Gerçek zamanlı veri ve API entegrasyonu
- Electron masaüstü uygulaması
- Veritabanı ve kullanıcı yönetimi
- Gerçek şehir verisiyle doğrulama
- Coğrafi harita katmanı

---

## 8. Geliştirme Öncesi Teknik Notlar

Kodlamaya başlamadan önce dikkat edilecek noktalar:

**Simülasyon motoru**
- Yayılma algoritmasında her düğümün en fazla bir kez "etkilenmiş" sayılması garanti edilmeli; aksi halde döngüsel bağımlılıklar (örn. A → B → C → A) sonsuz döngüye yol açabilir.
- Bir güvenlik sınırı (maksimum iterasyon sayısı) baştan tanımlanmalı — algoritmanın her koşulda duracağından emin olunmalı.

**Veri doğrulama**
- Arayüzden girilen değerler için sınır kontrolü şart: `bagimlilik_orani` 0–1, `dayaniklilik` 0–100 aralığı dışına çıkarsa otomatik sınırlanmalı (clip). Aksi halde tek bir yanlış girilen değer tüm simülasyonu anlamsız sonuçlara götürebilir.

**Streamlit durum yönetimi**
- Streamlit her etkileşimde script'i baştan çalıştırır; adım geçmişinin ve simülasyon sonucunun kaybolmaması için `st.session_state` kullanılmalı.

**Önerilen geliştirme sırası**
1. Veri modeli + JSON okuma/yazma
2. Çekirdek simülasyon motoru (arayüzsüz, sabit bir test senaryosuyla elle doğrulanmış)
3. Kritik düğüm analizi + Zayıf Nokta etiketleme
4. Görselleştirme
5. Arayüz (en son — mantık zaten test edilmiş olacağından hata ayıklama kolaylaşır)

**Kapsam disiplini**
- Bölüm 7'de kapsam dışı bırakılan özellikler, kodun hiçbir yerine "ileride lazım olur" mantığıyla sızdırılmamalı; gereksiz genellik/esneklik geliştirme süresini uzatır.

**Versiyon kontrolü**
- Ekip GitHub (veya benzeri) üzerinden ortak bir repo kullanmalı; düzenli commit geçmişi hem geliştirme sürecini kolaylaştırır hem de raporun "6.2 Hangi adımları izlediniz?" bölümü için somut bir kanıt oluşturur.

**Varsayılan test senaryosu**
- Önceki istişarede belirlenen örnek katsayı tablosu, kurgusal **"Örnek Kent"** senaryosu altında geliştirmenin ilk gününden itibaren varsayılan JSON verisi olarak kullanılmalı; tüm testler ve doğrulama (bkz. 3.4) bu senaryo üzerinden yürütülmeli. "Örnek Kent" isminin kurgusal olduğu hem kodda hem raporda açıkça belirtilmeli — gerçek veri kullanıldığı izlenimi yaratılmamalı.

---

## Kapsam Onayı

Bu belge, 1 aylık prototip sürecinin sınırlarını tanımlar. Buradaki maddeler dışına çıkan her talep, **v2 kapsamına** aktarılır.
