# UKDA — Geliştirme Yol Haritası ve Yapılacaklar Listesi

**Proje:** Ulusal Kaynak Dayanıklılık Analizi (UKDA)
**Belge türü:** Dahili geliştirme takip belgesi
**Amaç:** Adım adım kodlama sürecinde ilerlemeyi takip etmek
**İlişkili belgeler:** Yazılım Kapsam Belgesi (teknik detay) · Sunum Notları (anlatı/demo)

> Bu belge geliştirme sürecinde referans olarak kullanılır. Kullanıcı "başla" ya da belirli bir faz için komut verdiğinde, ilgili maddeler tamamlandıkça işaretlenir ve **İlerleme Durumu** bölümü güncellenir.

---

## 1. Genel Yol Haritası (4 Haftalık Çerçeve)

| Hafta | Odak |
|---|---|
| 1 | Veri modeli + JSON + "Örnek Kent" senaryosu |
| 2 | Çekirdek simülasyon motoru + doğrulama |
| 3 | Kritik düğüm analizi + Zayıf Nokta + Görselleştirme |
| 4 | Arayüz (Streamlit) + entegrasyon + sunum hazırlığı |

---

## 2. Detaylı Yapılacaklar Listesi

### Faz 0 — Kurulum
- [ ] Proje klasör yapısını oluştur
- [ ] Git reposu aç, ilk commit
- [ ] Python sanal ortam + gerekli kütüphaneler (networkx, streamlit, matplotlib)
- [ ] `requirements.txt` oluştur

### Faz 1 — Veri Modeli
- [ ] JSON şemasını tanımla (düğümler, kenarlar, özellikler)
- [ ] "Örnek Kent" varsayılan senaryosunu JSON olarak yaz (6 düğüm, katsayı tablosu)
- [ ] JSON okuma/yazma fonksiyonlarını yaz
- [ ] Veri doğrulama ekle (sınır kontrolleri: `bagimlilik_orani` 0–1, `dayaniklilik` 0–100)

### Faz 2 — Çekirdek Simülasyon Motoru
- [ ] NetworkX ile graf nesnesini JSON'dan oluşturma
- [ ] Yayılma algoritmasını yaz (çöküş → komşu etkisi → eşik kontrolü)
- [ ] Döngü koruması ekle (maksimum iterasyon sınırı, ziyaret edilen düğüm takibi)
- [ ] Adım kaydı (zaman çizelgesi) mekanizmasını ekle
- [ ] Konsol üzerinden manuel test (arayüzsüz)

### Faz 3 — Doğrulama
- [ ] 1-2 senaryoyu elle hesapla (beklenen sonuçlar)
- [ ] Yazılım çıktısıyla karşılaştır, sapmaları düzelt
- [ ] Doğrulama sonuçlarını not al (rapor için kanıt olacak)

### Faz 4 — Analiz Çıktıları
- [ ] Etki raporu fonksiyonu (hangi düğüm ne kadar etkilendi)
- [ ] Dayanıklılık süresi tahmini formülü
- [ ] Kritik düğüm analizi (her düğümü sırayla çöktürüp karşılaştırma)
- [ ] "⚠️ Zayıf Nokta" otomatik etiketleme mantığı

### Faz 5 — Görselleştirme
- [ ] NetworkX + Matplotlib ile graf çizimi
- [ ] Renk kodlaması (🟢/🟡/🔴)
- [ ] Zayıf Nokta görsel vurgusu (çerçeve/ikon)
- [ ] Adım adım gezinme (adım 1, 2, 3...)

### Faz 6 — Arayüz (Streamlit)
- [ ] Ana ekran düzeni (graf + yan panel)
- [ ] Düğüm seçme + "Çöküşü Simüle Et" butonu
- [ ] "Veri Düzenle" paneli (katsayı/dayanıklılık güncelleme)
- [ ] "Sıfırla" butonu
- [ ] `st.session_state` ile durum yönetimi

### Faz 7 — Entegrasyon & Test
- [ ] Uçtan uca test (arayüzden veri girme → simülasyon → sonuç)
- [ ] "Örnek Kent" demo senaryosunu arayüzde tekrar test et
- [ ] Kenar durumları kontrol et (boş graf, tüm düğümler çökmüş vb.)

### Faz 8 — Sunum Hazırlığı
- [ ] Demo akışını canlı ortamda prova et
- [ ] Sunum belgesindeki SSS'yi gözden geçir
- [ ] Rapor için ekran görüntüsü/kayıt al

---

## 3. Sıralama / Bağımlılık Notları

- **Faz 2 tamamlanmadan Faz 4-5'e geçilmemeli** — çekirdek motor önce sağlamlaştırılmalı.
- **Faz 3 (doğrulama), Faz 2 biter bitmez yapılmalı** — arayüz eklenmeden önce mantık hatası varsa erken yakalanmalı.
- **Faz 6 (arayüz) en son** — mantık zaten test edilmiş olacağından hata ayıklama kolaylaşır.

---

## 4. İlerleme Durumu

**Genel durum:** Henüz başlanmadı — Faz 0 bekleniyor
**Son güncelleme:** —
**Sıradaki adım:** —

---

## Güncelleme Günlüğü

- İlk sürüm oluşturuldu (4 haftalık yol haritası + 9 fazlık detaylı liste)
