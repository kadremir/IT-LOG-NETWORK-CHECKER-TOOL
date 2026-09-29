# CorporateFlowSuite 1.3 → 2.0
## Ürün Yönetimi Değerlendirmesi ve Yol Haritası Geliştirme Promptu

**Hazırlayan:** Ürün ve Proje Departmanı Başkanlığı
**Değerlendirilen ürün:** CorporateFlowSuite 1.3.0 (Eylül 2026) — Netveri · Logo İş Ortağı
**Değerlendirme dayanağı:** Tanıtım broşürü (19 sayfa) ve içindeki ekran görüntüleri. Kod incelenmemiştir; ürün, müşteriye sunulan hâliyle "bitmiş ürün" olarak ele alınmıştır.
**Hedef pazar:** Logo Tiger / Go / Wings kullanan 50–2.000 çalışanlı KOBİ ve orta ölçekli kurumlar; holding yapıları; Logo İş Ortağı kanalı üzerinden satış.

Bu belge iki bölümdür:

- **Bölüm A** — Yönetici değerlendirmesi: ürünün pazardaki yeri, korunacak farklar, satışı engelleyen boşluklar, UI/UX bulguları ve öncelik matrisi.
- **Bölüm B** — Geliştirme ekibine (veya bir yapay zekâ geliştirme ajanına) doğrudan verilecek **yol haritası geliştirme promptu**. Kopyalanıp olduğu gibi kullanılabilir.

---

# BÖLÜM A — YÖNETİCİ DEĞERLENDİRMESİ

## A1. Ürün konumu, tek paragrafta

CorporateFlowSuite, Logo ERP çevresinde üç operasyonel boşluğu (masraf, izin, tahsilat takibi) tek pakette kapatan, tamamen şirket içi çalışan bir "Logo çevresi" ürünü. Ürünün gerçek farkı, Logo'ya **gerçekten yazması** (dışa aktarım dosyası değil, kesen kişinin kimliğiyle Alınan Hizmet Faturası), Logo'yu **doğru okuması** (vade planı, çek çift sayımı, vadesi tanımsız bakiye), fatura okuma ve özetlemede **verinin şirket dışına çıkmaması** ve 4857 sayılı İş Kanunu'nun **kurulumla gelmesi**dir. Bu dört fark pazarda gerçekten ayırt edici ve korunmalıdır. Ancak ürün, bugünkü hâliyle **"KOBİ'nin bilgi işlemcisi tek başına kurabilir"** düzeyinde tasarlanmış; kurumsal satın alma komitesinin (BT güvenlik, İK direktörü, CFO, satın alma) sorduğu soruların önemli bir kısmına henüz cevabı yok.

## A2. Korunacak farklar (dokunulmayacaklar)

| Fark | Neden değerli | Yol haritasındaki karşılığı |
|---|---|---|
| Logo'ya gerçek fatura yazımı, mükerrer koruma (MSR izleme kodu), yarıda kalan aktarımın güvenli tekrarı | Rakiplerin çoğu dosya ihracı yapar; muhasebe bunu tekrar elle işler | Korunur, genişletilir (personel cari / avans kapatma) |
| Satır bazlı onay ve ret gerekçesi | Kurumsal masraf politikalarının %80'i kalem bazlı istisnadır | Korunur |
| Yerel OCR (PP-OCRv5) + yerel dil modeli, sıfır bulut bağımlılığı | KVKK envanterinde "yurt dışına aktarım" satırı boş; kamu ve finans müşterisi için kapı açıcı | Korunur; GPU/hız seçeneği eklenir |
| 4857 md. 53/56/59 ve Yönetmelik md. 13/20 kurulumla gelir; izin defteri ekranda | Yerli rakiplerin çoğu bunu "yapılandırma" olarak bırakır | Korunur, diğer yasal izinlerle genişletilir |
| SHA-256 zincirli, silinemez denetim kaydı | Denetim ve iç kontrol için güçlü satış argümanı | Korunur; SIEM'e akıtılır |
| Görsel akış tasarımcısı (koşul, grup, vekâlet, Logo düğümü) | Kodsuz onay zinciri kurumsal talebin merkezi | Korunur; SLA/eskalasyon eklenir |
| Tek kurulum paketi, sürüm el sıkışması, "Bağlantıyı sına" | Kurulum maliyetini düşürür, kanal partnerine kolaylık | Korunur |

## A3. Kurumsal satışı engelleyen boşluklar

Aşağıdaki maddeler, bir kurumsal satın alma sürecinde **"bu yoksa ilerlemeyiz"** denecek düzeydedir. Sıra, kayıp satış riskine göredir.

### 1. Kimlik ve erişim: SSO yok, üç ayrı giriş kapısı var
- Active Directory / Entra ID (Azure AD) / LDAP / SAML 2.0 / OpenID Connect desteği yok. Kurumsal BT'nin ilk sorusu budur.
- Üç modül **ayrı giriş kapısı** ile açılıyor ("ayrı giriş kapıları" broşürde özellik gibi sunulmuş; kurumsal kullanıcı için bu bir kusurdur). Kullanıcı bir kez kimliğini kanıtlar, rolüne göre modülleri görür.
- Rol/yetki modeli broşürde görünmüyor: alan bazlı görünürlük (maaş, tutar), departman bazlı veri sınırı, görev ayrılığı (fatura kesen ≠ onaylayan) tanımlı değil.

### 2. Platform kapsamı: iOS yok, push bildirim yok, yalnızca Windows sunucu
- Yönetici kademesinde iPhone oranı çok yüksektir; "iOS yok, web portalı kullanılır" cümlesi onay akışının en kritik kullanıcısını dışarıda bırakır.
- Bildirim kanalı yalnızca e-posta ve SMS. **Mobil push bildirim yok.** Onay deneyiminin bel kemiği eksik.
- Sunucu tarafı yalnızca Windows servisi. .NET 10 zaten çapraz platform; Linux/container dağıtımı ve yüksek erişilebilirlik (HA) seçeneği kurumsal BT için gereklidir.
- Cloudflare tüneli KOBİ için pratik, kurumsal BT için kabul edilemez bir yol olarak algılanır; "kurumsal profil"de gizlenmeli/kapatılabilmelidir.

### 3. Entegrasyon yüzeyi: açık API yok, webhook yok
- Belgelenmiş, sürümlü, kimlik doğrulamalı bir **REST API** ve **webhook** kataloğu yok. Kurumsal müşteri kendi intranetine, BI aracına, PDKS'sine bağlamak ister.
- Bordro aktarımı yalnızca CSV; **Logo Bordro / Logo J-HR / Netsis** ile canlı entegrasyon beklenir.
- **PDKS** (giriş-çıkış) entegrasyonu yok: izin ve puantaj tutarlılığı kanıtlanamaz.
- **Gelen e-Fatura eşleştirmesi** yok: Türkiye'de B2B fişlerin çoğu e-Fatura/e-Arşiv'dir. Masraf talebi, Logo'daki gelen e-Fatura ile VKN + fatura no üzerinden eşleşmeli; aksi hâlde aynı belge hem masraf hem tedarikçi faturası olarak iki kez muhasebeleşir.

### 4. Masraf modülünün kapsamı KOBİ düzeyinde
Eksikler: **avans** talebi ve avans kapatma, **harcırah** (yurt içi/yurt dışı gündelik, vergiden istisna sınırlar), **kilometre** bazlı araç masrafı, **döviz** ve kur, **masraf merkezi / proje kodu** (Logo'daki masraf merkezi ve proje kartlarıyla eşleşme), **politika limitleri** (günlük yemek tavanı, hafta sonu masrafı, konaklama şehir tavanı) ve ihlal uyarısı, **kurumsal kart ekstresi** eşleştirme, **personel geri ödeme** akışı (personel carisine borç kaydı → ödeme emri). Broşür yalnızca "Alınan Hizmet Faturası" yazıyor; oysa masrafın parası çalışana geri ödenecekse bu akış yarım kalır.

### 5. Tahsilat modülü okur ama kapatmaz
Eksikler: **cari mutabakat** (BA/BS ve cari ekstre mutabakatı; Türkiye'de finansın en büyük aylık yükü), hatırlatma e-postasında **ödeme linki** (sanal POS), **banka hesap hareketi** ile tahsilat eşleştirme, **DBS** (Doğrudan Borçlandırma Sistemi) görünürlüğü, **çok para birimi**, **tedarikçi ödeme planı ve ödeme emri onayı** (modül adı "Ödeme Tahsilat" ama ödeme tarafı zayıf), **müşteri risk skoru** ve kredi limiti uyarısı, 8 haftadan uzun **senaryolu nakit akış tahmini**.

### 6. İzin modülü yalnızca yıllık izin mevzuatını taşıyor
Eksikler: **mazeret izinleri** (4857 Ek md. 2: evlilik, doğum/babalık, ölüm, engelli çocuk tedavisi), **analık izni** (md. 74), süt izni, ücretsiz izin, raporlu gün (SGK iş göremezlik) gibi türlerin **yasal süre ve belge kuralı ile** kurulumla gelmesi; **e-imza / mobil imza** ile izin formu; **ekip takvimi** dışa aktarımı (Outlook/Google Calendar ICS); **toplu izin** (bayram tatili ekleme); **ayrılış kıdem/izin ücreti** hesabında bordro ile çift yönlü kontrol.

### 7. Kurumsal operasyon: holding, çok dil, gözlemlenebilirlik
- **Çok şirketli/holding yapısı**: "firma ve dönem talep başına seçilir" var ama tek kurulumdan çok şirketi tek organizasyon ağacında yönetme (grup şirketleri, ortak İK, ayrı muhasebe) tanımlı değil.
- **Yalnızca Türkçe**. Yabancı ortaklı ve yurt dışı iştirakli kurumlar İngilizce ister; ürün mimarisi çok dile hazırlanmalı.
- **Gözlemlenebilirlik**: sağlık uç noktası, metrikler, yapılandırılmış log, denetim kaydının **SIEM'e** (syslog/CEF) akıtılması yok.
- **Yedekleme/geri dönüş ve felaket kurtarma** kılavuzu; sürüm geri alma prosedürü.
- **Lisans yönetimi**: kurulum lisansı + kullanıcı tavanı. Kurumsal müşteri modül bazlı, adlandırılmış/eşzamanlı kullanıcı, abonelik + bakım, çevrimdışı aktivasyon ve lisans portalı ister.

### 8. Raporlama ve analitik
Sabit raporlar + tasarlanabilir PDF var. Eksikler: **Excel** çıktısı her listede, **zamanlanmış rapor** (her Pazartesi CFO'ya), **özelleştirilebilir rapor tasarımcısı** (sürükle bırak alan/filtre), **BI konektörü** (OData / doğrudan görünüm), **karşılaştırmalı dönem** analizi, **AI Özet**'in "paragraf" olmaktan çıkıp anomali (mükerrer fiş, hafta sonu masrafı, politika ihlali, ödeme alışkanlığı bozulan müşteri) yakalamaya evrilmesi.

## A4. UI/UX bulguları (ekran görüntülerinden)

**Genel izlenim:** Temiz, tutarlı bir yönetim paneli dili var (koyu kenar çubuğu, kart tabanlı KPI'lar, açık/koyu tema, WCAG AA iddiası). Bu iyi bir zemin. Ancak ürün, "üç ayrı uygulamanın yan yana konması" gibi hissettiriyor; kurumsal kullanıcı **tek ürün** hissi bekler.

| # | Bulgu | Etki | Yön |
|---|---|---|---|
| U1 | Açılış ekranı "Nereden devam etmek istersiniz?" ile dört kart; her modül kendi **Giriş Yap** düğmesiyle ayrı oturum açtırıyor | Günde 3 kez parola; rol karmaşası; kurumsal algıyı düşürür | Tek oturum, rol tabanlı ana sayfa; modüller üst gezinmede sekme |
| U2 | Modül renkleri (mavi/yeşil/turuncu) marka değil **ayrılık** anlatıyor | Bütünlük hissi zayıf | Tek marka birincil rengi; modül rengi yalnızca ikincil vurgu |
| U3 | Panolarda 5–9 KPI kutusu aynı ağırlıkta; hiyerarşi yok ("Net pozisyon 2,08 M" ile "8 müşteri" aynı boyutta) | Karar odağı dağılır | KPI hiyerarşisi: 1 birincil, 3 ikincil, gerisi katlanır; her KPI'da trend ve hedef |
| U4 | Yeni masraf formu çalışana **tedarikçi fatura no, VKN, ünvan** soruyor | Saha çalışanı için gereksiz yük; broşürdeki "~45 sn" vaadi ile çelişir | OCR'dan otomatik dolsun, çalışan yalnızca doğrulasın; alanlar "muhasebe alanı" olarak katlanır |
| U5 | Masraf satırında **Miktar, Birim fiyat, KDV %** görünür | Fiş girişini fatura girişine çevirir | Varsayılan görünüm: Açıklama, Tutar, Tür, Belge; ayrıntı isteğe bağlı |
| U6 | Kenar çubuğu menüleri uzun ve düz (İzin modülünde 14+ madde) | Bulma süresi artar | Gruplama + arama; sık kullanılan üstte; rol bazlı gizleme |
| U7 | URL adlandırması karışık: `/hatirlatici/...`, `/talepler/yeni`, `/masraf/panom`, `/akislar/1` | İç ad ("hatırlatıcı") sızıyor; paylaşılan link anlamı belirsiz | Tutarlı, modül önekli, İngilizce ya da Türkçe tek dil URL şeması |
| U8 | "Masraf Panom", "İzin Panom" gibi samimi adlar | Kurumsal dilde tutarsız | "Pano" / "Genel Bakış" |
| U9 | Bildirim merkezi, genel arama, komut paleti yok | Onaycı işi bulmak için modül modül gezer | Üst çubukta bildirim zili + Ctrl/⌘+K genel arama |
| U10 | Boş durum (empty state), ilk kullanım rehberi, ipucu turu görünmüyor | İlk hafta terk riski | Her liste için boş durum + "ilk adım" çağrısı; rehberli tur |
| U11 | Mobil ekranlar broşürde yok; Android'de biyometrik giriş, push, çevrimdışı onay yazılmıyor | Onay deneyiminin ana cihazı kanıtsız | Mobil-önce onay akışı; Face/parmak izi; push |
| U12 | Web portalı telefon tarayıcısında (iPhone) nasıl göründüğü kanıtlanmamış | iOS kullanıcısı tek seçeneği web | Responsive + PWA (ana ekrana ekle, push) |
| U13 | Tablolar: toplu işlem, sütun seçimi, kayıtlı filtre, satır yoğunluğu ayarı görünmüyor (tahsilat listesinde CSV ve toplu işlem var, diğerlerinde yok) | Muhasebe ekranları listede yaşar | Tüm listelerde ortak "veri tablosu" bileşeni |
| U14 | Akış tasarımcısı iyi; ancak **simülasyon** ("bu talep nereden geçer?"), **sürüm** ve **SLA süresi** düğümü yok | Akış hatası canlıda görülür | Test modu, taslak/yayın sürümü, zaman aşımı/eskalasyon düğümü |
| U15 | Yalnızca Türkçe; tarih/para biçimleri sabit | Çok dilli kurum dışarıda | i18n altyapısı, TR + EN |
| U16 | Marka özelleştirme (müşteri logosu, renk) görünmüyor | Partner kanalı beyaz etiket ister | Kurum logosu, renk, e-posta şablonu markalama |

## A5. Marka ve adlandırma notu

- "CorporateFlowSuite" jenerik ve İngilizce; ürünün tüm arayüzü Türkçe. Ya marka İngilizce kalır ve İngilizce arayüz gelir, ya da Türkçe pazara Türkçe kısa bir ürün adı düşünülür. Karar pazarlama ile ortak alınmalı; bu belge ürünü mevcut adıyla anar.
- Modül adları tutarsız: "Masraf Yönetimi", "İzin Yönetimi", "Ödeme Tahsilat Takip". Üçüncüsü "Nakit ve Tahsilat Yönetimi" gibi bir düzeye getirilmeli; URL'deki "hatırlatıcı" tamamen kaldırılmalı.

## A6. Rakip konumlandırma (özet)

| Kategori | Tipik rakip | CorporateFlowSuite'in üstünlüğü | CorporateFlowSuite'in açığı |
|---|---|---|---|
| Bulut İK/masraf (KOBİ) | Kullanıcı başı abonelikli yerli bulut İK ürünleri | Logo'ya gerçek yazım, veri şirkette, 4857 yerleşik | SSO, iOS, push, modern onboarding, API |
| Global masraf yönetimi | Concur, Expensya, Zoho Expense | Türkçe mevzuat, yerel OCR, fiyat | Kart ekstresi, harcırah, döviz, politika motoru, çok dil |
| Logo'nun kendi iş akışı ürünleri | Logo Flow ve İK ürünleri | Fiş fotoğrafı → fatura akışı, satır onayı, tahsilat panosu | Logo Bordro/J-HR ile canlı bağ, ekosistem güveni |
| Tahsilat/finans araçları | Banka ve fintech tahsilat platformları, mutabakat servisleri | Logo'dan canlı ve doğru okuma, sorumlu/not/erteleme | Ödeme linki, mutabakat, banka entegrasyonu, risk skoru |

## A7. Öncelik matrisi

| Öncelik | Kapsam | Gerekçe |
|---|---|---|
| **P0 — Kapı açıcı** | Tek oturum + SSO (Entra ID/AD/SAML/OIDC), RBAC ve görev ayrılığı, mobil push, iOS (MAUI zaten çapraz platform), açık REST API + webhook, gelen e-Fatura eşleştirme, tasarım sistemi ve tek ürün gezinmesi | Bunlar olmadan kurumsal satış toplantısı ikinci turu geçmez |
| **P1 — Değer derinleştirici** | Masraf: avans, harcırah, km, döviz, masraf merkezi/proje, politika motoru, personel geri ödeme. İzin: yasal izin türleri seti, Logo Bordro/J-HR/PDKS entegrasyonu, e-imza, ICS. Tahsilat: mutabakat, ödeme linki, banka hareketi eşleştirme, ödeme emri onayı, çok para birimi | Modülleri "not defteri"nden "iş süreci"ne taşır |
| **P2 — Kurumsal işletim** | Linux/container, HA, SIEM, gözlemlenebilirlik, yedekleme/DR, holding yapısı, çok dil (TR/EN), lisans portalı, beyaz etiket | BT ve satın almanın kontrol listesi |
| **P3 — Ayrıştırıcı zekâ ve analitik** | Anomali tespiti, risk skoru, doğal dil sorgu, rapor tasarımcısı, zamanlanmış rapor, BI konektörü, GPU/hız seçenekleri, senaryolu nakit tahmini | Fiyat primi ve yenileme oranı |

---

# BÖLÜM B — YOL HARİTASI GELİŞTİRME PROMPTU

> Aşağıdaki metin, geliştirme ekibine veya bir yapay zekâ geliştirme ajanına **olduğu gibi** verilmek üzere yazılmıştır. Köşeli parantez içindeki alanlar kuruma göre doldurulur.

```text
# ROL VE BAĞLAM

Sen, CorporateFlowSuite ürününün 1.3.0 sürümünden 2.0 sürümüne taşınmasından sorumlu
kıdemli ürün mühendisliği ekibisin (mimar, arka uç, ön uç, mobil, UX tasarımcısı, QA
ve teknik yazar rollerini birlikte üstlenirsin). Ürün; Logo Tiger / Go / Wings ERP ile
çalışan kurumlar için Masraf Yönetimi, İzin Yönetimi ve Ödeme-Tahsilat Takip
modüllerini masaüstü (Windows, WebView2), web portalı (ASP.NET Core 10, tek Windows
servisi "CfsApi") ve Android (.NET MAUI) istemcileriyle sunar. Veritabanı SQL Server
veya SQLite; fatura okuma PP-OCRv5 ve Qwen3.5-4B ile şirket içi ayrı bir 64 bit
süreçte çalışır. Denetim kaydı SHA-256 zincirlidir. Onay akışları görsel tasarımcıda
kurulur. Ürün bugün yalnızca Türkçedir.

Hedef: Ürünü, 50–2.000 çalışanlı kurumların BT güvenlik, İK, finans ve satın alma
komitelerinden geçecek "kurumsal sınıf" bir ürüne dönüştürmek; bunu yaparken ürünün
mevcut farklarını (Logo'ya gerçek yazım, satır bazlı onay, yerel OCR/LLM ile sıfır
bulut bağımlılığı, 4857 yerleşik, SHA-256 denetim zinciri, görsel akış tasarımcısı,
tek paket kurulum) hiçbir koşulda zayıflatmamak.

Çalışma biçimi: Önce mevcut kodu ve şemayı keşfet, bulgularını bir "Mevcut Durum
Raporu" olarak yaz, sonra aşağıdaki fazları sırasıyla uygula. Her faz için: tasarım
belgesi → şema/API sözleşmesi → uygulama → otomatik test → kullanım belgesi → sürüm
notu. Bir fazı bitirmeden diğerine geçme. Her kararı "neden" cümlesiyle yaz.
Belirsizlikte, bu promptta "Ürün İlkeleri" bölümündeki kuralı uygula; hâlâ belirsizse
en güvenli ve geri alınabilir seçeneği uygula ve "Açık Sorular" listesine ekle.

# ÜRÜN İLKELERİ (her kararda uygulanır)

1. Tek ürün, tek kimlik. Kullanıcı bir kez kimliğini kanıtlar; rolü hangi modülleri
   ve alanları göreceğini belirler. "Modül başına giriş" kaldırılır.
2. Kural tek yerde yaşar. İş kuralı servis katmanındadır; masaüstü, web ve mobil aynı
   uç noktayı kullanır. İstemcide tekrar edilen kural kabul edilmez.
3. Veri şirket dışına çıkmaz. Yeni her özellik, varsayılan olarak şirket içi çalışır.
   Bulut/dış servis yalnızca açık, belgelenmiş ve kapatılabilir bir seçenektir.
4. Her yazma işlemi denetim kaydına düşer; denetim kaydı zinciri kırılmaz. Yeni
   tablolar ve yeni eylemler için denetim kapsamı tasarım belgesinde yazılır.
5. Logo'ya yazma yalnızca işlemi yapan kişinin Logo kimliğiyle; okuma servis
   hesabıyla. Mükerrer koruma (izleme kodu ile Logo'da arama) her yeni yazma
   işleminde zorunludur.
6. Geri alınabilirlik. Şema değişiklikleri ileri/geri taşınabilir; her sürüm önce
   yedek alır; özellik bayrakları ile kademeli açılır.
7. Erişilebilirlik ve klavye: WCAG 2.2 AA, tam klavye gezinmesi, görünür odak, iki
   temada kontrast testi derlemede kalır ve yeni bileşenlere uygulanır.
8. Türkçe önce, çok dile hazır. Tüm metinler kaynak dosyalarında; tarih, para, sayı
   biçimleri kültüre göre; ikinci dil İngilizce.
9. Performans bütçesi: liste ekranları 10.000 satırda 1 sn altında ilk çizim;
   onay eylemi 300 ms altında yanıt; mobil açılış 2 sn altında.
10. Geriye dönük uyumluluk: 1.3.0 kurulumları 2.0'a veri kaybı olmadan yükselir;
    eski mobil sürüm, sürüm el sıkışmasında Türkçe ve İngilizce açık bir mesajla durur.

# TASARIM SİSTEMİ VE UI/UX REVİZYONU (Faz 0 ile başlar, tüm fazlarda uygulanır)

Aşağıdaki spesifikasyonu "CFS Tasarım Sistemi" adıyla belgele ve bileşen kütüphanesi
olarak uygula; masaüstü/web aynı bileşenleri, mobil aynı tokenları kullanır.

Tokenlar
- Renk: tek birincil marka rengi (Netveri kimliğinden türet); modül renkleri yalnızca
  ikincil vurgu (menü ikonu, rozet). Semantik renkler: başarı, uyarı, hata, bilgi,
  nötr; her biri açık/koyu temada AA kontrastlı. Tokenlar CSS değişkeni ve MAUI
  kaynak sözlüğü olarak tek dosyadan üretilir.
- Tipografi: tek yazı tipi ailesi; 12/14/16/20/24/32 ölçek; tablo yoğunluğu için
  "sıkı/normal/rahat" modları.
- Boşluk: 4 px tabanlı ölçek; kart, tablo ve form için sabit iç boşluk kuralları.
- Köşe, gölge, kenarlık: üç seviye; kartlar yalnızca 1. seviye.

Yerleşim ve gezinme
- Ana çerçeve: üst çubuk (ürün logosu, modül sekmeleri, genel arama Ctrl/⌘+K,
  bildirim zili, kullanıcı menüsü) + sol kenar çubuğu (seçili modülün gruplu menüsü,
  daraltılabilir, rol bazlı gizleme, arama).
- Açılış: "Nereden devam etmek istersiniz?" kaldırılır. Giriş sonrası rol tabanlı
  ana sayfa: onaycı için "Bekleyen işlerim", çalışan için "Taleplerim ve bakiyem",
  muhasebe için "Aktarım kuyruğu", finans için "Nakit pozisyonu". Kullanıcı
  ana sayfayı kişiselleştirebilir.
- Kurulum ve Veritabanı, yönetim alanına taşınır; kimlik kapısı korunur.

Bileşenler (zorunlu ortak set)
- Veri tablosu: sütun seç/gizle/sırala, çoklu filtre, kayıtlı görünüm, toplu işlem,
  satır yoğunluğu, sayfalama veya sanal kaydırma, Excel/CSV/PDF dışa aktarım,
  boş durum, yükleniyor iskeleti, hata durumu. Tüm listeler bu bileşeni kullanır.
- KPI kartı: değer, önceki döneme göre değişim, mini trend, hedef çizgisi, "neye
  tıklarsam nereye giderim" açık. Panolarda 1 birincil + 3 ikincil KPI kuralı;
  fazlası "Daha fazla" ile açılır.
- Form: iki katmanlı alanlar ("Çalışanın doldurduğu" / "Muhasebe alanı",
  ikincisi katlanır), satır içi doğrulama, taslak otomatik kaydetme, klavye ile satır
  ekleme, dosya sürükle-bırak.
- Onay kartı: talep özeti, satırlar, karar düğmeleri, gerekçe alanı, akış konumu;
  masaüstü/web/mobilde aynı içerik.
- Bildirim merkezi: okunmamış sayacı, türe göre gruplama, "hepsini okundu yap",
  bildirim tercihleri (e-posta, SMS, push, kanal başına aç/kapat).
- Boş durum ve ilk kullanım: her modülde 3 adımlı rehber; her boş listede ne
  yapılacağını söyleyen çağrı.
- Hata ve geri bildirim: tost, satır içi uyarı, engelleyici diyalog kuralları;
  her hata "ne oldu, neden, ne yapmalı" üçlüsüyle yazılır.

Sayfa düzeyi revizyonlar
- Yeni masraf talebi: birincil yol "Belge ekle → alanlar dolar → doğrula → gönder".
  Tedarikçi fatura no, VKN, ünvan OCR'dan dolar; çalışan düzenleyebilir ama boş
  bırakabilir. Miktar/birim fiyat/KDV yüzdesi varsayılan gizli; toplam tutar ve KDV
  tutarı görünür. Politika ihlali satır yanında sarı rozetle anında görünür.
- Talep ayrıntısı: sol içerik / sağ akış zaman çizelgesi düzeni korunur; karar alanı
  sayfa altında yapışkan (sticky) olur.
- Logo Aktarımı: kuyruk tablosu ortak veri tablosuna geçer; "yarıda kalan aktarım"
  uyarısı eylem düğmesiyle gelir ("Logo'da ara ve eşle").
- Panolar: KPI hiyerarşisi uygulanır; her grafikte dönem seçici, dışa aktar ve
  "tabloya geç"; renkler tasarım sistemi grafik paletinden.
- Akış tasarımcısı: sol araç kutusu korunur; "Simüle et" (örnek talep verisiyle
  yolu boyar), "Taslak / Yayında" sürümleri, değişiklik günlüğü, düğüm arama.
- URL şeması: /{modul}/{kaynak}/{id} biçiminde tek dilde (öneri: Türkçe,
  ASCII: /masraf, /izin, /tahsilat, /akislar, /yonetim). "/hatirlatici" kaldırılır,
  eski adresler 301 ile yönlenir.
- Adlandırma: "Panom" → "Pano"; "Ödeme Tahsilat Takip" → "Nakit ve Tahsilat
  Yönetimi" (ürün/pazarlama onayı ile); tüm metinler yeni sözlükten geçer.
- Marka özelleştirme: kurum logosu, birincil renk, e-posta/PDF şablon antedi
  yönetim alanından ayarlanır.

Mobil (MAUI, Android + iOS)
- Onay akışı mobil-önce tasarlanır: liste → kart → kaydırarak onayla/reddet, ret
  gerekçesi alt sayfa.
- Biyometrik giriş (Face/parmak izi), PIN; oturum süresi politikası.
- Push bildirim (FCM + APNs) — şirket içi servis üzerinden aracı, yük içeriği
  yalnızca kimlik ve tür; içerik cihazda servisten çekilir (veri dışarı çıkmaz).
- Çevrimdışı: masraf taslağı (var) + onay kararlarının kuyruğa alınması.
- Kamera akışı: çoklu sayfa, otomatik kırpma, "Fatura okunuyor" ilerleme çubuğu.

# FAZLAR VE İŞ KALEMLERİ

Her iş kalemi şu biçimde teslim edilir:
Amaç → Kapsam → Veri modeli/şema değişikliği → API sözleşmesi → UI davranışı →
Denetim kaydı kapsamı → Test planı (birim, entegrasyon, uçtan uca) → Kabul kriterleri.

## FAZ 0 — Temel ve hijyen (2.0 için zorunlu zemin)

0.1 Mevcut Durum Raporu: mimari, şema, uç noktalar, istemciler arasında tekrar eden
    kurallar, bilinen teknik borç, güvenlik açıkları, performans darboğazları.
0.2 Tasarım sistemi kütüphanesi (yukarıdaki spesifikasyon) ve eski ekranların
    envanteri; her ekran için "yeni bileşene taşınacak" işareti.
0.3 i18n altyapısı: tüm metinler kaynak dosyasına; kültüre bağlı biçimlendirme;
    TR tam, EN iskelet.
0.4 Gözlemlenebilirlik: /health ve /ready uç noktaları, yapılandırılmış log
    (korelasyon kimliği), metrik uç noktası (OpenTelemetry), tanı raporuna dahil.
0.5 Otomatik test omurgası: servis katmanı birim testleri, API sözleşme testleri,
    ekran bazlı erişilebilirlik testleri (var olan kontrast testi genişletilir),
    mobil için kritik yol UI testi.
0.6 Yükseltme ve geri dönüş: 1.3.0 → 2.0 taşıma; yedek al → taşı → doğrula →
    geri al senaryosu belgelenir ve test edilir.

Kabul: Tüm mevcut özellikler yeni bileşenlerle çalışır; regresyon testi yeşil;
1.3.0 demo veritabanı 2.0'a yükselir ve geri döner.

## FAZ 1 — Kurumsal kimlik, platform ve entegrasyon yüzeyi (P0)

1.1 Tek oturum ve kimlik sağlayıcı entegrasyonu
    - Yerel hesaplar korunur; ek olarak OpenID Connect ve SAML 2.0 ile Entra ID,
      ADFS, Keycloak, Google Workspace; LDAP/Active Directory ile parola doğrulama
      ve grup eşleme.
    - Kullanıcı ve grup sağlama: AD/Entra gruplarından rol ve organizasyon birimi
      eşleme; SCIM 2.0 isteğe bağlı.
    - Tek oturum: modül başına giriş kaldırılır; Logo kimliği yalnızca "Logo'ya yaz"
      anında istenir ve oturumda güvenli saklanır (parola saklanmaz, mevcut ilke).
    - Oturum politikası: süre, eşzamanlı oturum sınırı, cihaz listesi ve uzaktan
      oturum kapatma; 2FA ayrıcalıklı rollerde zorunlu kılınabilir (mevcut ilke).
    Kabul: Entra ID ile test kurumunda kullanıcı tek girişle üç modülü rolüne göre
    görür; AD grubundan çıkarılan kullanıcı 5 dakika içinde erişimini kaybeder.

1.2 Rol ve yetki modeli (RBAC) ve görev ayrılığı
    - Roller: Çalışan, Onaycı, Departman Yöneticisi, İK Uzmanı, İK Yöneticisi,
      Muhasebe, Finans/Tahsilat, Sistem Yöneticisi, Denetçi (salt okunur).
    - Yetkiler: eylem × kaynak × kapsam (kendi / ekibi / departmanı / şirketi /
      grup şirketleri) matrisi; alan bazlı gizleme (tutar, kişisel veri).
    - Görev ayrılığı kuralları: kendi talebini onaylayamaz; faturayı kesen onaycı
      olamaz; kural ihlali denetim kaydında.
    - Yetki değişikliği onaylı ve denetimli.
    Kabul: Yetki matrisi yönetim ekranında düzenlenir; her uç nokta yetki denetimi
    testinden geçer; denetçi rolü hiçbir şey değiştiremez.

1.3 Açık REST API ve webhook
    - Sürümlü (/api/v2), OpenAPI 3.1 belgesi, API anahtarı ve OAuth2 istemci
      kimliği; hız sınırı ve kapsam (scope) bazlı yetki.
    - Kaynaklar: talepler (masraf, izin), onay kararları, bakiye, personel,
      tahsilat kalemleri, denetim kaydı (salt okunur), akış tanımları (salt okunur).
    - Webhook: talep oluşturuldu/onaylandı/reddedildi, Logo'ya aktarıldı, tahsilat
      kalemi ertelendi/kapatıldı, kullanıcı pasife alındı; imzalı yük, yeniden
      deneme, teslimat günlüğü ekranı.
    - Geliştirici portalı: belge, örnekler, test anahtarı.
    Kabul: Üçüncü taraf bir örnek uygulama, yalnızca belge ile 1 saatte izin talebi
    oluşturup onay olayını webhook ile alır.

1.4 Mobil: iOS, push ve biyometri
    - .NET MAUI iOS hedefi; TestFlight ve App Store dağıtımı; kurumsal MDM
      (Intune) ile dağıtım kılavuzu.
    - Push: FCM ve APNs; şirket içi servis yalnızca bildirim kimliği gönderir.
    - Biyometrik giriş, PIN, oturum süresi; ekran görüntüsü engeli korunur.
    - Onay akışı mobil-önce yeniden tasarlanır (tasarım sistemi bölümü).
    Kabul: iPhone'da izin ve masraf onayı, push bildirimden iki dokunuşla biter;
    Android ile birebir aynı davranış.

1.5 Gelen e-Fatura ile eşleştirme (Masraf)
    - Masraf talebindeki VKN + fatura no + tutar, Logo'daki gelen e-Fatura/e-Arşiv
      kayıtlarıyla eşlenir; eşleşme varsa talep "Logo'da belge var" rozeti alır ve
      Alınan Hizmet Faturası yerine mevcut belgeye bağlanır (ikinci kayıt oluşmaz).
    - Muhasebe eşleştirme ekranı: öneri, elle eşle, ayır.
    Kabul: Aynı belge iki kez muhasebeleşemez; test setinde %100 yakalanır.

1.6 Web portalının kurumsal dağıtımı
    - Linux/container (Docker) dağıtımı ve Windows servisi eşdeğer; ters vekil
      (nginx/IIS) ve TLS kılavuzu; HA için durumsuz servis ve dış önbellek.
    - "Hazır internet adresi" (tünel) seçeneği kurumsal derleme profilinde varsayılan
      kapalı ve yönetici onayıyla açılır.
    Kabul: Aynı kurulum paketi Linux konteynerde tüm testleri geçer.

## FAZ 2 — Modül derinleştirme (P1)

2.1 Masraf Yönetimi
    a) Avans: talep → onay → Logo'da personel carisine avans kaydı (kesen kişinin
       kimliğiyle) → masrafla kapatma → kalan bakiye iade/ödeme.
    b) Harcırah: yurt içi/yurt dışı gündelik tabloları (yıllık güncellenebilir
       parametre), vergiden istisna sınır uyarısı, gün ve şehir bazlı hesap.
    c) Kilometre: araç kartı, km × birim tutar, rota notu.
    d) Döviz: satır bazında para birimi ve kur (Logo kur tablosundan), TL karşılığı;
       Logo'ya döviz cinsiyle yazım.
    e) Masraf merkezi ve proje: Logo masraf merkezi/proje kartlarıyla eşleşme;
       talep ve satır düzeyinde; raporlarda kırılım.
    f) Politika motoru: kural tanımı (tür, tutar tavanı, gün, konum, gerekli belge,
       tekrar sıklığı); ihlal seviyeleri (uyar / gerekçe iste / engelle); ihlal
       akış tasarımcısında koşul olarak kullanılabilir.
    g) Kurumsal kart ekstresi: CSV/OFX yükleme veya banka entegrasyonu; ekstre
       satırı ↔ masraf satırı eşleştirme; eşleşmeyen harcama listesi.
    h) Personel geri ödeme: onaylanan nakit/kişisel kart masrafları için ödeme
       listesi; toplu ödeme emri (banka dosyası) ve Logo'da kapatma; çalışana
       "ödendi" bildirimi.
    i) Mükerrer fiş tespiti: aynı tarih-tutar-tedarikçi ve görüntü benzerliği
       (yerel model) ile uyarı.
    Kabul: Saha çalışanı için uçtan uca "avans al → masraf yap → kapat → geri
    ödeme" senaryosu; muhasebe için "ekstre eşle → Logo'ya yaz" senaryosu geçer.

2.2 İzin Yönetimi
    a) Yasal izin türleri seti kurulumla gelir: mazeret izinleri (4857 Ek md. 2),
       analık/babalık izni (md. 74), süt izni, ücretsiz izin, raporlu gün, evlilik,
       ölüm; her türde süre, belge zorunluluğu, ücret etkisi ve dayanak cümlesi.
    b) Logo Bordro / Logo J-HR / Netsis entegrasyonu: personel ana verisinin
       Logo'dan çekilmesi (çift kayıt yok), puantajın CSV yerine doğrudan yazımı,
       çıkış ödemelerinin bordroya taşınması.
    c) PDKS entegrasyonu: giriş-çıkış verisinden devamsızlık, izinle çakışma,
       "izinli ama içeride" uyarısı.
    d) Takvim: ICS aboneliği (Outlook/Google), ekip takvimi paylaşımı, toplu
       kapalı gün/idari izin ekleme.
    e) e-İmza/mobil imza ile izin formu (isteğe bağlı modül), imzalı PDF arşivi.
    f) Ekip planlama: minimum kadro kuralı (aynı anda en fazla %X izinli),
       çakışma uyarısı talep anında.
    Kabul: İK, personel verisini Logo'dan çekip yıl sonu devri ve bordro puantajını
    ekrandan çıkmadan tamamlar; mevzuat tablosu "Sistemde nerede" sütunuyla
    güncellenir.

2.3 Nakit ve Tahsilat Yönetimi
    a) Cari mutabakat: BA/BS ve cari ekstre mutabakat mektubu üretimi, müşteriye
       e-posta ile gönderim, müşteri onay/itiraz bağlantısı (parolasız, süreli
       bağlantı), itiraz takibi.
    b) Ödeme linki: hatırlatma iletisinde sanal POS ödeme bağlantısı (sağlayıcı
       eklentisi: [iyzico / PayTR / banka sanal POS]); ödeme sonucunun kaleme
       işlenmesi (Logo'ya yazım yine muhasebe onayıyla, kesen kimliğiyle).
    c) Banka hareketi eşleştirme: banka ekstresi (MT940/CSV/API) ile açık kalem
       önerisi; eşleşen kalemler "tahsil edildi" görünür.
    d) Ödeme tarafı: tedarikçi vade planından ödeme takvimi, ödeme emri onay
       akışı (görsel tasarımcıda), banka ödeme dosyası üretimi.
    e) Çok para birimi: kalem para birimi, kur riski görünümü.
    f) Müşteri risk skoru: ödeme davranışı (gecikme ortalaması, sözünü tutma
       oranı, çek portföyü) ile 1–5 skor, kredi limiti aşımı uyarısı.
    g) Nakit akış tahmini: 8 hafta → 26 hafta, iyimser/kötümser senaryo,
       ertelenen kalemlerin etkisi.
    h) Hatırlatma kanalları: e-posta, SMS + WhatsApp Business (isteğe bağlı,
       açıkça açılır); "iki anahtar" güvenliği korunur.
    Kabul: Finans, ay sonu mutabakatını ekrandan çıkmadan gönderir ve itirazları
    takip eder; hatırlatma linkinden yapılan ödeme kaleme düşer.

2.4 Akış tasarımcısı
    - SLA/zaman aşımı düğümü (48 saatte karar yoksa eskalasyon), paralel onay
      (hepsi / çoğunluk), koşulda politika ihlali ve masraf merkezi alanları,
      simülasyon modu, taslak/yayın sürümleri, değişiklik günlüğü, akış kopyalama.
    Kabul: Örnek 5 kurumsal akış (tutar kademeli, departman + İK, paralel finans)
    simülasyonda beklenen yolu boyar.

## FAZ 3 — Kurumsal işletim (P2)

3.1 Holding/çok şirket: tek kurulumda grup şirketleri ağacı; ortak İK, ayrı
    muhasebe; kullanıcı birden fazla şirkette rol taşıyabilir; konsolide panolar.
3.2 Çok dil: İngilizce tam çeviri; kullanıcı dil tercihi; e-posta/PDF şablonları
    dil bazlı.
3.3 SIEM ve denetim: denetim kaydının syslog/CEF ile dışa akıtılması; mühürlü
    dışa aktarım korunur; SIEM'de zincir doğrulama aracı.
3.4 Yedekleme/DR: zamanlanmış yedek, geri yükleme sihirbazı, RPO/RTO belgesi,
    sürüm geri alma prosedürü.
3.5 Lisans yönetimi: modül bazlı, adlandırılmış/eşzamanlı kullanıcı, süre/bakım,
    çevrimdışı aktivasyon, lisans portalı; kullanım raporu.
3.6 Beyaz etiket ve partner araçları: kurum markası, partner kurulum profili,
    çoklu müşteri tanı panosu (partner için).
3.7 Güvenlik sertleştirme: bağımsız sızma testi, OWASP ASVS L2 kontrol listesi,
    bağımlılık tarama, gizli anahtar yönetimi (DPAPI'ye ek olarak Vault/KMS
    seçeneği), IP allowlist, oturum/cihaz politikası.
Kabul: Kurumsal BT kontrol listesi (SSO, RBAC, SIEM, HA, yedek, sızma testi,
KVKK) belgelerle eksiksiz yanıtlanır.

## FAZ 4 — Ayrıştırıcı zekâ ve analitik (P3)

4.1 Anomali tespiti (yerel model): mükerrer fiş, hafta sonu/tatil masrafı, tutar
    sapması, aynı tedarikçide sıklık artışı, bölünmüş fatura; sonuçlar onaycıya
    rozet olarak gelir.
4.2 Tahsilatta erken uyarı: ödeme davranışı bozulan müşteri, vade uzatma
    eğilimi; risk skoru ile birlikte.
4.3 Doğal dil sorgu (yerel model): "geçen ay ulaşım masrafı en yüksek 5 kişi";
    model yalnızca sorguyu üretir, rakamı program hesaplar (mevcut ilke).
4.4 Rapor tasarımcısı: alan/filtre/gruplama sürükle-bırak, kayıtlı rapor,
    zamanlanmış gönderim (e-posta, paylaşımlı klasör), Excel/PDF; BI konektörü
    (OData veya salt okunur görünümler + belge).
4.5 OCR/LLM hız ve kalite: GPU desteği (isteğe bağlı), kuyruk ve öncelik,
    e-Fatura XML doğrudan okuma (OCR'a gerek kalmaz), doğruluk panosu
    (alan bazlı isabet oranı), kullanıcı düzeltmelerinden öğrenme (şirket içi).
4.6 AI Özet'in yeniden tanımı: paragraf yerine "dikkat gerektiren 3 madde" ve
    her maddeye eylem bağlantısı.
Kabul: Anomali tespiti test setinde ≥ %90 yakalama, ≤ %5 yanlış alarm; e-Fatura
XML'den masraf formu ≤ 5 sn.

# FONKSİYONEL OLMAYAN GEREKLER (tüm fazlar)

- Güvenlik: OWASP ASVS L2; PBKDF2-SHA256 korunur, Argon2id'ye geçiş planı;
  5 hatalı denemede kilit ve IP hız sınırı korunur; tüm dışa aktarımlar denetimli.
- KVKK/GDPR: veri envanteri güncellenir; yeni her kişisel veri alanı için amaç,
  saklama süresi, anonimleştirme kuralı tasarım belgesinde; aydınlatma metni ve
  VERBİS şablonları yeni alanlarla güncellenir.
- Performans bütçeleri (Ürün İlkeleri 9) her sürümde ölçülür ve raporlanır.
- Erişilebilirlik: WCAG 2.2 AA; ekran okuyucu ile onay akışı test edilir.
- Uyumluluk: Windows 10/11, Server 2016+, Linux (Ubuntu LTS) konteyner; SQL Server
  2016+ ve SQLite korunur; PostgreSQL değerlendirme raporu.
- Belgeler: Ürün Belgesi, Kullanım Kılavuzu, Kurulum Kılavuzu, API Belgesi,
  Güvenlik Beyanı, KVKK paketi, Sürüm Notları — her fazda güncellenir.

# TESLİMAT BİÇİMİ VE TANIMLI BİTİŞ

Her faz sonunda şu çıktıları üret:
1. Tasarım belgeleri (mimari kararlar "ADR" biçiminde).
2. Şema taşıma betikleri (ileri/geri) ve API sözleşmesi (OpenAPI).
3. Kod + otomatik testler (kapsam hedefi: servis katmanı ≥ %80).
4. Ekran envanteri güncellemesi ve tasarım sistemi uyum raporu.
5. Kullanıcı belgeleri ve sürüm notu (TR, EN).
6. Demo veritabanı güncellemesi: yeni özellikler dolu ekranla gelir.
7. "Açık Sorular" ve "Ürün Kararı Gerektirenler" listesi.

Bir iş kalemi ancak şu koşullarda "bitti" sayılır: kabul kriterleri otomatik testle
kanıtlandı; masaüstü, web ve mobilde aynı davranış doğrulandı; denetim kaydı
kapsamı test edildi; erişilebilirlik testi geçti; belge güncellendi; 1.3.0'dan
yükseltme senaryosu kırılmadı.

# YAPMAYACAKLARIN

- Yerel OCR/LLM'i bulut servisle değiştirmek (yalnızca isteğe bağlı ek).
- Logo'ya servis hesabıyla yazmak.
- Denetim kaydında satır değiştirmek veya silmek.
- İstemci tarafında iş kuralı tekrar etmek.
- Test kapatarak veya atlayarak "yeşil" yapmak.
- Ürün adlarını, URL'leri ve menü metinlerini ürün onayı olmadan değiştirmek
  (öneriyi "Ürün Kararı Gerektirenler" listesine yaz, uygulamayı bayrak arkasında
  hazırla).

# İLK ADIM

Mevcut Durum Raporu'nu üret. Raporda: mimari şema, modül ve ekran envanteri,
uç nokta listesi, veri modeli özeti, istemciler arası kural tekrarları, güvenlik
bulguları, performans ölçümleri, teknik borç listesi ve bu prompttaki her faz
için "mevcut kodla uyum / çatışma" değerlendirmesi bulunsun. Raporu onaya sun,
ardından Faz 0'a başla.
```

---

## Ek — Yol haritası takvimi önerisi

| Dönem | Faz | Sürüm | Pazar mesajı |
|---|---|---|---|
| 2026 Q4 | Faz 0 + Faz 1.1–1.2 | 1.4 | "Tek oturum, kurumsal kimlik" |
| 2027 Q1 | Faz 1.3–1.6 | 2.0 | "iOS, push, açık API, e-Fatura eşleştirme" |
| 2027 Q2 | Faz 2.1, 2.4 | 2.1 | "Avans, harcırah, politika motoru" |
| 2027 Q3 | Faz 2.2, 2.3 | 2.2 | "Bordro/PDKS entegrasyonu, mutabakat, ödeme linki" |
| 2027 Q4 | Faz 3 | 2.3 | "Holding, çok dil, SIEM, kurumsal işletim" |
| 2028 Q1 | Faz 4 | 2.4 | "Anomali tespiti, rapor tasarımcısı, BI" |

## Ek — Ürün kararı gerektiren açık sorular

1. Marka: "CorporateFlowSuite" korunacak mı, Türkçe kısa ad mı? İngilizce arayüzle birlikte karar verilmeli.
2. Dağıtım modeli: Yalnızca şirket içi mi kalınacak, yoksa Netveri barındırmalı (özel bulut) seçeneği eklenecek mi? "Veri şirket dışına çıkmaz" mesajı ile çelişmeden "sizin adınıza barındırılan ayrık örnek" olarak konumlanabilir.
3. Fiyatlandırma: Kurulum lisansı korunacak mı, modül bazlı abonelik + bakım mı? Faz 3.5 bu karara bağlı.
4. Ödeme linki ve WhatsApp Business gibi dış servis eklentileri "isteğe bağlı, açıkça açılan" olarak mı kalacak, yoksa pakete mi girecek?
5. e-İmza sağlayıcısı seçimi ve maliyet paylaşımı.
