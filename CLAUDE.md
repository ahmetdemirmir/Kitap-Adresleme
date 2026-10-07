# CLAUDE.md — Kitap Adresleme (Demir Kırtasiye Raf Adresleme)

## Ben kimim
- Ahmet Demir, **Demir Kırtasiye** zincirinin sahibi (Ataevler, Fethiye ve diğer şubeler + merkez depo).
  Ayrıca Kartepe'de **dk Concept** mağazası var (ayrı unvan ve logo, Demir Kırtasiye ile karıştırma).
- ERP: **Akınsoft Wolvox** (MSSQL). Diğer iç uygulamalarımız Windows sunucuda çalışıyor (Flask + SQLite, PWA).
- Teknik bilgim iyi ama geliştirici değilim. İş sistemini uçtan uca ben biliyorum; kodu sana bırakıyorum, iş kararları bende.

## İletişim kuralları (ZORUNLU)
1. **Her cevap, ara not, plan ve özet TÜRKÇE.** Tek bir İngilizce ara cümle bile yazma.
   Sadece kod tanımlayıcıları (değişken, fonksiyon, kolon adları) İngilizce kalabilir. Arayüz metinleri Türkçe.
2. **İş akışı belirsizse kodu kazıyarak tahmin etme, bana DİREKT sor.** Soruyu iş diliyle sor
   ("bu Excel'i sonra nereye yüklüyorsun?" gibi), teknik terimle değil.
3. Kısa ve net yaz. Yapabileceğin işi (kod, düzeltme, analiz) bana devretme, kendin yap.
4. Commit mesajları Türkçe, ne değiştiğini net anlatsın.

## Bu proje ne?
Tek dosyalık web uygulaması (`index.html`, sunucusuz). Kitapların (ve ürünlerin) **raf adresini** okutarak atamaya yarıyor.
- **Veri kaynağı:** Yayınlanmış Google Sheets CSV'si (`SHEETS_URL`) ya da elle yüklenen Excel.
  Sütunlar otomatik eşleniyor: Barkod, Stok Adı, Mağaza (fiyat), Fiyat Değişiklik Tarihi, **Modeli = raf kodu**.
  Otomatik eşleşme olmazsa elle sütun eşleme penceresi açılıyor.
- **Okutma:** Kamera (BarcodeDetector, EAN-13 / UPC-A) ya da elle/el terminaliyle barkod girişi. İkisi de aynı `handleScan` fonksiyonuna gidiyor.
  12 haneli barkod başına 0 eklenerek 13 haneye tamamlanıyor (`pad13`).
- **Raf kodu biçimi:** `HARF-NUMARA`, harf A–H, numara 1–50 (örn. `C-17`). Son kullanılan 5 raf hatırlanıyor.
- **Toplu Adresle:** birden çok barkod okutulup hepsine tek raf atanabiliyor.
- **Çıktı:** "Excel'e Kaydet" (tüm liste) ve "Değişiklikler.xlsx" (eski raf → yeni raf, kim, ne zaman).
  "Kim Adresledi" alanı her değişikliğe yazılıyor.
- **Kalıcılık:** Sadece tarayıcının `localStorage`'ı var, sunucu ya da veritabanı yok.

## Dokunurken dikkat
- Raf kodu Wolvox'taki **MODELI** alanına karşılık geliyor. Excel sütun adlarını ve sırasını değiştirme;
  sonradan içe aktarma yapılıyorsa format bozulur. Değişiklik gerekiyorsa önce bana sor.
- Barkod eşlemesi 13 haneye tamamlanmış hâliyle yapılıyor; bu mantığı bozma.
- Aynı barkod listede birden fazla satırda olabilir (`byBarcode` ilkini tutuyor). Bunu değiştirmeden önce bana sor.
- Kamera sadece HTTPS'te ya da localhost'ta çalışıyor.
- **El terminali önceliklidir** (Honeywell, klavye gibi yazıyor). Elle barkod alanı her zaman çalışır kalmalı ve okutmadan sonra temizlenmeli.
  Kamera yedek yol. Yeni kamera kodu yazılacaksa standardımız `html5-qrcode`.
- Okutma geri bildirimi (ses + titreşim, başarılı/hatalı) sahada önemli, kaldırma.

## Tasarım dili
Mevcut sayfa kırmızı-mavi gradyan ve Tailwind kullanıyor; kurumsal dilimize uymuyor. Yeni ekran yazarken ya da sayfayı yenilerken şu kurallar geçerli:
- Renk tokenları: `--navy:#2b2a80 --navy-dark:#222170 --navy-soft:#eef0fb --red:#e2231a --red-soft:#fdeceb --bg:#f3f4f8
  --surface:#fff --ink:#1a1a2e --muted:#6b7280 --line:#e5e7eb --green:#16a34a --amber:#d97706`
- Font **Inter**. Beyaz sticky üst bar + Demir Kırtasiye yatay logosu (logo dosyası depoda yoksa bana söyle, yükleyeyim).
  Kartlar radius 14–20, `1px var(--line)` çerçeve, birincil buton navy, aktif sekme altında kırmızı çizgi.
- Karanlık mod yok, "jenerik AI tasarımı" yok, emoji ağırlıklı tema yok. Mobil (telefon) öncelikli, büyük dokunma alanları.

## Bulut oturumu notu
Bulutta Wolvox'a, şirket ağına (Tailscale) ve sunucudaki `C:\` klasörlerine erişimin yok. Canlı veri gerekiyorsa bunu açıkça söyle; veri uydurma.
Test için gerekirse örnek bir Excel üret ama gerçek veri gibi sunma.
