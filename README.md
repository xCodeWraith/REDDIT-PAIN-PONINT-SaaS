# DEV Reddit Pain Point & SaaS Opportunity Scanner

DEV / xCodeWraith edition.

Bu n8n workflow'u Reddit'teki gercek kullanici sikayetlerini, arac arayislarini, manuel is yuklerini ve mevcut cozumlerden memnuniyetsizlikleri tarayarak SaaS, app, otomasyon, marketplace, servis veya internal tool firsatlarina cevirir.

## Ne Ise Yarar?

Workflow Reddit'i bir firsat arastirma kaynagi gibi kullanir.

Temel amac:

- Reddit postlarindan gercek pain point yakalamak
- Zayif, bos, eski veya tekrar eden postlari elemek
- AI ile "bu post para edebilecek bir urun/servis firsati mi?" kararini verdirmek
- Uygun postlari ozetlemek
- Her firsat icin structured opportunity report uretmek
- Sonuclari Google Sheets'e yazmak

Uygun firsat turleri:

- SaaS
- Mobile app
- Internal tool
- Marketplace
- Automation workflow
- Chrome extension
- Data product
- Agency/service
- Template veya productized service

## Workflow Mantigi

### 1. Build Reddit Search Matrix

20 farkli subreddit + keyword kombinasyonu uretir.

Kaynak kategorileri:

- SMB Operations
- SaaS Founder Pain
- No-code Automation
- Developer Tools
- Commerce Ops
- Go-to-market Ops
- Productivity

Ornek subredditler:

- `smallbusiness`
- `Entrepreneur`
- `startups`
- `SaaS`
- `indiehackers`
- `microsaas`
- `n8n`
- `Zapier`
- `Airtable`
- `Notion`
- `webdev`
- `devops`
- `sysadmin`
- `ecommerce`
- `shopify`
- `sales`
- `marketing`
- `productivity`

Ornek keywordler:

- `looking for a solution`
- `what software do you use`
- `alternative to`
- `is there an app`
- `manual process`
- `recommend software`
- `workflow problem`
- `too expensive`
- `spreadsheet`
- `automate`

### 2. Get Posts

Reddit node'u matrix'teki her satir icin arama yapar.

Varsayilan:

- Her arama icin limit: `15`
- Sort: `relevance`

### 3. Filter Posts By Features

AI token harcamadan once kaba kalite filtresi uygular.

Filtreler:

- `ups > 5`
- `selftext` bos degil
- Post son 180 gun icinde

### 4. Select Key Fields

Reddit'ten gelen ham veriyi temiz alanlara cevirir.

Uretilen ana alanlar:

- `source_category`
- `opportunity_surface`
- `subreddit`
- `subreddit_subscribers`
- `title`
- `reddit_post_body`
- `url`
- `date`
- `upvotes`
- `comments`
- `permalink`
- `reddit_id`

### 5. Dedupe Reddit Posts

Ayni post birden fazla aramada yakalanirsa tekillestirir.

Oncelik:

1. `reddit_id`
2. `url`
3. `subreddit:title`

### 6. Analysis Content By AI

AI gate node'udur.

Sadece `yes` veya `no` dondurur.

`yes` icin aranan sinyaller:

- Somut arac eksikligi
- Manuel is yuku
- Pahali workaround
- Vendor/urun memnuniyetsizligi
- Buying intent
- Yazilim/app/SaaS/otomasyon arayisi
- Tekrarlanabilir operasyonel darboğaz

`no` sayilanlar:

- Haber
- Promosyon
- Hiring spam
- Genel motivasyon
- Meme/politika
- Monetize edilemeyen genel soru
- Tek seferlik kisisel durum

### 7. Post Summarization

Uygun postu kisa ozetler.

### 8. Pain Point + Opportunity Report

Workflow'un ana deger ureten node'udur.

Urettigi bolumler:

- Pain Point
- Evidence From Post
- Buyer Segment
- Opportunity Type
- Product / Service Ideas
- Opportunity Score
- MVP Shape
- Validation Plan
- First Offer
- Risk Notes

### 9. Merge 3 Inputs

Metadata, post ozeti ve opportunity report'u tek kayitta birlestirir.

### 10. Output The Results

Sonucu Google Sheets'e append eder.

## Gereken Credential'lar

n8n icinde su credential'lar secilmelidir:

- Reddit OAuth2
- OpenAI API
- Google Sheets OAuth2

Import sonrasi credential ID'leri placeholder olabilir. Node'lari tek tek acip kendi credential'larini secmelisin.

## Reddit API Kurulumu

1. Reddit hesabina gir.
2. Reddit app/client olustur.
3. OAuth2 client bilgilerini al.
4. n8n'de Reddit OAuth2 credential olustur.
5. `Get Posts` node'unda bu credential'i sec.

Not: Reddit API ucretsiz sinirlar sunabilir fakat ticari kullanim, yogun veri cekimi ve platform kurallari icin Reddit API/Data Terms kontrol edilmelidir.

## OpenAI Kurulumu

1. OpenAI API key olustur.
2. n8n'de OpenAI credential ekle.
3. Su node'larda ayni OpenAI credential'i sec:
   - `OpenAI Chat Model`
   - `OpenAI Chat Model1`
   - `Pain Point + Opportunity Report`

Varsayilan model:

- `gpt-4o-mini`

## Google Sheets Kurulumu

1. Yeni bir Google Sheet olustur.
2. Ilk sheet adini n8n node'unda sececegin sheet ile eslestir.
3. Google Sheets OAuth2 credential'i n8n'e ekle.
4. `Output The Results` node'unda document ve sheet mapping'i yenile.

Onerilen kolonlar:

- `Source_category`
- `Opportunity_surface`
- `Subreddit`
- `Subreddit_size`
- `Post_date`
- `Upvotes`
- `Comments`
- `Post_title`
- `Post_url`
- `Post_summary`
- `Opportunity_report`
- `Post_body_excerpt`
- `Reddit_id`

## Import Etme

n8n UI ile:

1. n8n'i ac.
2. Workflows bolumune git.
3. Import from file sec.
4. `dev_reddit_pain_point_business_opportunity_scanner.json` dosyasini sec.
5. Credential'lari ilgili node'lara yeniden ata.
6. Google Sheets mapping'ini refresh et.

CLI ile:

```bash
docker cp workflows/reddit-opportunity/dev_reddit_pain_point_business_opportunity_scanner.json n8n:/tmp/dev_reddit_pain_point_business_opportunity_scanner.json
docker exec n8n n8n import:workflow --input=/tmp/dev_reddit_pain_point_business_opportunity_scanner.json
```

## Lokal Calistirma

Docker ile n8n calisiyorsa:

```bash
docker ps --filter name=n8n
```

Workflow URL:

```text
http://localhost:5678/workflow/Xx4zOjRFLI8W9PiC
```

Gorsel agac:

```text
http://localhost:8765/workflows/reddit-opportunity/workflow_tree.html
```

Gorsel agac icin basit server:

```bash
python3 -m http.server 8765
```

## Ilk Test Plani

1. n8n'de workflow'u ac.
2. Credential hatasi olan node var mi kontrol et.
3. `Build Reddit Search Matrix` node'unda matrix'i gerekirse 2-3 satira dusur.
4. `Get Posts` limitini test icin `2` veya `3` yap.
5. `Execute workflow` calistir.
6. Her node output'unu sirayla kontrol et:
   - Reddit post geliyor mu?
   - Filtreler postlari tamamen eliyor mu?
   - AI gate `yes/no` donuyor mu?
   - Report node'u structured Markdown uretiyor mu?
   - Google Sheets'e satir ekleniyor mu?
7. Test basariliysa matrix'i tekrar 20 satira ve limit'i `15` seviyesine getir.

## Production Kullanim

Manuel testten sonra workflow'a schedule trigger eklenebilir.

Onerilen frekans:

- Gunluk: dusuk hacimli firsat radari
- Haftalik: daha az API maliyeti
- Manuel: kontrollu arastirma

Onerilen guvenli kullanim:

- Once dusuk limit ile calistir
- Reddit API limitlerini takip et
- OpenAI maliyetini takip et
- Google Sheets'te duplicate kontrolu yap
- AI raporlarini dogrudan market truth kabul etme

## QA Durumu

Yapilan kontroller:

- JSON valid
- n8n'e import edildi
- Workflow ID: `Xx4zOjRFLI8W9PiC`
- Node sayisi: `19`
- Connection sayisi: `15`
- Missing node reference yok
- `pinData` bos
- Eski Bench.co sample execution verisi temizlendi
- `postcontent` yerine `reddit_post_body` kullaniliyor
- Gmail/sentiment draft yan dali kaldirildi
- Search matrix 20 kombinasyona genisletildi

Bilinen gereksinimler:

- Gercek credential'lar n8n UI'da secilmeli
- Google Sheets kolonlari/mapping'i hedef sheet ile eslestirilmeli
- Ilk calistirma dusuk limit ile test edilmeli

## Sinirlar

Bu workflow:

- Fikirlerin dogrulugunu garanti etmez
- Pazar buyuklugunu tek basina kanitlamaz
- Reddit postlarini hukuki/ticari izin yerine gecirmez
- AI raporlarini insan review'u olmadan karar mekanizmasi yapmaz

Bu workflow'un asil gorevi kaliteli sinyal uretmektir. Nihai karar icin manuel analiz, kullanici gorusmesi ve pazar validasyonu gerekir.
