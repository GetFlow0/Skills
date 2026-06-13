---
name: prompt-engineering
description: Anthropic'in resmi prompt engineering yaklaşımına göre Claude için yüksek kaliteli prompt'lar tasarlamayı sağlar. 10 bölümlük prompt yapısı, XML tag organizasyonu, örnek (few-shot) kullanımı, halüsinasyon önleme, prefill ve extended thinking tekniklerini kapsar. Kullanıcı bir LLM prompt'u yazmak, mevcut bir prompt'u iyileştirmek, n8n / API / webhook gibi otomasyon akışlarına LLM çağrısı yerleştirmek, structured (JSON) çıktı garantilemek, ya da Claude'un cevap kalitesini artırmak istediğinde bu skill'i kullan. "Prompt yaz", "system prompt kur", "LLM node'u tasarla", "JSON çıktısı al", "halüsinasyonu azalt" gibi ifadeler geçtiğinde, kullanıcı açıkça "prompt engineering" demese bile bu skill'e başvur.
---

# Prompt Engineering Skill

## Bu skill nedir? (okuma amaçlı özet)

Bu skill, Anthropic'in prompt engineering eğitiminde anlatılan yöntemleri tek
bir referansta toplar. Amacı, Claude'a (veya başka bir LLM'e) verilen
talimatları tutarlı, ölçeklenebilir ve gerçek üretim ortamında güvenilir hale
getirmektir.

Skill özellikle otomasyon senaryoları için tasarlanmıştır: n8n workflow'ları,
API entegrasyonları, webhook akışları ve LLM destekli veri işleme. Bu tür
sistemlerde prompt'un "bir kez çalışıp" bitmediğini, sürekli ve değişen verilerle
binlerce kez tetiklendiğini varsayar. Bu yüzden vurgu; rastgele iyi çıktı almakta
değil, **tekrarlanabilir ve öngörülebilir** çıktı almaktadır.

Kaynak: Anthropic Applied AI ekibinin "Prompting 101" sunumu (Hannah Moran &
Christian Ryan) ve Anthropic'in resmi prompt/context engineering rehberleri.
Prompt engineering, modele net talimat yazma, görevi tamamlaması için gereken
bağlamı verme ve bu bilgiyi en iyi sonuç için nasıl düzenleyeceğini düşünme
pratiğidir.

İçeride şu konular var:
1. Prompt mimarisinin katmanları (system / context / task).
2. Bir prompt'u baştan inşa etmek için 10 bölümlük standart yapı.
3. Bilgiyi XML tag'leriyle organize etmek (özellikle dinamik veri enjekte
   ederken kritik).
4. Örnek (few-shot) vererek istenen çıktı formatını sabitlemek.
5. Halüsinasyonu azaltmak için dört somut teknik.
6. Prefill ve extended thinking ile çıktıyı kontrol altına almak.
7. İterasyon döngüsü ve task decomposition pattern'ı.

Prompt yazımı ampirik bir iştir: bir taslak kur, test et, sonuçlara göre
düzelt, tekrarla. Bu skill o döngüde başvuru noktasıdır.

---

## 0. Prompt Mimarisi: Katmanlı Sistem

Modern bir LLM akışını tek bir sohbet penceresi değil, katmanlı bir sistem
olarak düşün. Her katmanın farklı bir sorumluluğu var:

- **System katmanı** — Ajanın kimliği ve hedefleri. Sabit, nadiren değişir.
  Örn: "Sen büyük React Native uygulamalarını refactor eden kıdemli bir
  full-stack mühendissin."
- **Context katmanı** — Modeli koda, belgelere, dış veriye bağlayan kısım.
  n8n'de webhook/API ile çekilen dinamik veri buraya girer. (Belgeler, hafıza
  dosyaları, alan bilgisi, mesaj geçmişi.)
- **Task katmanı** — O anki canlı talimat. Örn: "Store'u RTK Query ile
  refactor et."

Bu katmanlar düzgün orkestre edildiğinde model bir chatbot gibi değil, bir iş
arkadaşı gibi davranır. Otomasyonda bu ayrım kritiktir: system katmanını n8n
node'unun sabit system prompt'una, context katmanını dinamik değişkenlere,
task katmanını da o anki tetikleyici isteğe karşılık getir.

> Not: Anthropic, ajan sistemleri büyüdükçe işin "doğru kelimeleri bulmaktan"
> çıkıp "hangi bağlam konfigürasyonu istenen davranışı üretir?" sorusuna
> kaydığını söylüyor; buna context engineering diyor. Prompt engineering bu
> daha geniş sürecin bir parçasıdır.

---

## 1. 10 Bölümlük Prompt Yapısı

Bir prompt'u sıfırdan kurarken bu sırayı izle. Sıralama önemli — model bilgiyi
bu mantıkla daha kolay işler. Her bölüm zorunlu değildir; göreve göre gerekenleri
kullan.

1. **Task context** — Rol ve üst düzey görev tanımı. 1-2 cümle. Örn: "Sen
   AdAstra Careers tarafından oluşturulmuş, adı Joe olan bir AI kariyer
   koçusun."
2. **Tone context** — İstenen ton. Örn: "Samimi bir müşteri hizmetleri tonu
   koru."
3. **Background data, documents, images** — Referans alınacak belge, görsel,
   veri. XML tag içine koy.
4. **Detailed task description & rules** — Detaylı kurallar, sınırlar, edge
   case'ler. Modelin uyması gereken kurallar burada.
5. **Examples** — İstenen çıktının örnekleri. En etkili bölümlerden biri.
6. **Conversation history** — Önceki konuşma geçmişi (varsa).
7. **Immediate task description / request** — O anki asıl istek veya soru.
8. **Thinking step by step** — Cevaptan önce adım adım düşünmesini iste.
9. **Output formatting** — Çıktının formatı.
10. **Prefilled response** — Assistant cevabını önceden başlatma (varsa).

---

## 2. Bilgiyi XML Tag'leriyle Organize Et

Dağınık prompt'ları model zor anlar. Sınırları net çizmek için XML tag kullan.
Tıpkı başlıkların insanlara metni takip etmekte yardımcı olması gibi, XML
tag'leri de modelin prompt yapısını anlamasına yardım eder. Model her tür
ayraç anlar; XML tercih edilir çünkü sınırları en nettir.

Dinamik veri enjekte ederken (n8n'de `{{ $json.x }}` gibi) değişkeni mutlaka
tag içine koy:

```
<location>
{{LOCATION}}
</location>

<num_days>
{{NUM_DAYS}}
</num_days>

<user_preferences>
{{USER_PREFERENCES}}
</user_preferences>
```

Çıktıyı da tag içine almasını isteyebilirsin: "Cevabını <itinerary></itinerary>
etiketleri içine koy." Bu, n8n'de cevabı parse etmeyi kolaylaştırır.

---

## 3. Örnek (Few-shot) Ver

- Örnekler somut şablon görevi görür; modelin istenen çıktıyı anlayıp
  tekrar üretmesini kolaylaştırır.
- Tutarlı format, özel jargon veya sektör standardı gerektiren görevlerde
  özellikle kritiktir.
- Tüm nüansı metinle anlatmaya çalışmak yerine modele bir-iki örnek göstermek
  çoğu zaman çok daha verimlidir.
- Örnekleri seçerken üç ölçüte dikkat et: **İlgililik (Relevance), Çeşitlilik
  (Diversity), Miktar (Quantity — en az 3-5 örnek).**

Örnekleri XML tag içine koymak iyi bir alışkanlıktır:

```
<example>
User: Merhaba, nasıl oluşturuldun ve ne yapıyorsun?
Joe: Merhaba! Benim adım Joe, kariyer tavsiyesi vermem için AdAstra Careers
tarafından oluşturuldum. Bugün size nasıl yardımcı olabilirim?
</example>
```

---

## 4. Halüsinasyonu Önle

Otomasyon sistemlerinde yanlış ama "kendinden emin" çıktı en tehlikeli
durumdur. Şu dört tekniği uygula:

1. **Bilmiyorsa "bilmiyorum" demesine izin ver.** Prompt'a açıkça yaz:
   "Emin değilsen 'bilmiyorum' de."
2. **Yalnızca çok eminse cevaplamasını söyle.** "Sadece cevabından çok
   eminsen yanıt ver."
3. **Cevaptan önce düşündür.** "Cevaplamadan önce düşün." (Thinking adımı)
4. **Uzun belgelerde önce alıntı, sonra cevap.** "Önce belgeden ilgili
   alıntıları bul, sonra yalnızca o alıntılara dayanarak cevapla."

---

## 5. Prefill (Cevabı Önceden Başlat)

Assistant alanına metin yazarak modeli yönlendirirsin; model senin
bıraktığın yerden devam eder. Bu sana iki avantaj verir: davranışı/cevabı
yönlendirmek ve çıktı formatı üzerinde daha fazla kontrol.

**JSON garantilemek için en güçlü hile:** Assistant alanına `{` koy. Model
açıklama, markdown veya önsöz yazmadan doğrudan JSON üretmek zorunda kalır.
n8n'de structured output isterken bunu kullan.

Örnek:
```
Assistant (prefill): <itinerary>
```
Model `<itinerary>` etiketinden itibaren devam eder.

---

## 6. Extended Thinking vs. Prompt Engineering

**Extended thinking ne zaman kullanılır?**
- Modele düşünmesi için daha fazla zaman vermenin iyi bir ilk adımıdır.
- Düşünme izlerini (trace) takip ederek modelin nasıl düşündüğünü anlayabilir,
  bu içgörüyü system prompt'u iyileştirmek için kullanabilirsin.

**Dezavantajları:**
- Çoğu zaman "tekerleği yeniden icat eder", bu da yüksek token kullanımı
  demektir.
- Düşünme `temperature = 1` gerektirir; bu yüzden zaman zaman daha az
  tekrarlanabilir (reproducible değil) olur.

**Otomasyon için pratik kural:** Tutarlılık ve düşük maliyet istiyorsan,
önce extended thinking ile modelin nasıl akıl yürüttüğünü gözlemle, sonra o
mantığı doğrudan system prompt'a göm. Üretimde sabit, ucuz ve tekrarlanabilir
bir prompt, her seferinde sıfırdan düşünen bir modele yeğdir.

---

## 7. İterasyon Döngüsü ve Task Decomposition

### Empirical refinement (ampirik düzeltme)

Prompt'lama ampirik bir bilimdir. Şu döngüyü uygula:

1. **Engineer** — Ön taslak prompt'u yaz.
2. **Test** — Gerçek vakalara karşı test et.
3. **Refine** — Sonuçlara göre düzelt ve döngüye geri dön.

Her zaman test et ve sık sık iterasyon yap. "Doğru" prompt'u ilk seferde
bulmaya çalışma; gerçek çıktılara bakıp düzelt. Otomasyonda bu döngüyü
sistematikleştir: birkaç gerçek girdiyi sabit test seti olarak tut, prompt'u
her değiştirdiğinde aynı setle yeniden çalıştır, çıktıların bozulup
bozulmadığını gör.

### Modele göre güncelleme

Prompt'lar modele bağımlıdır. Bir model için ayarlanmış prompt, yeni bir
modelde aynı performansı vermeyebilir. Model sürümünü değiştirdiğinde (örn.
n8n node'unda model string'ini güncellediğinde) prompt'u sabit kabul etme;
test setini yeniden çalıştırıp gerekirse talimatları güncelle.

### Task decomposition (görev ayrıştırma)

Karmaşık bir görevi tek bir dev prompt'a sığdırmaya çalışma. Görevi alt
adımlara böl. "Prompting 101"deki kaza raporu örneğinde olduğu gibi, model
önce formu analiz eder, sonra çizimi yorumlar, sonra ikisini birleştirip
sonuca varır — her adım ayrı ve net tanımlıdır.

Bunu prompt içinde `<task id=1>`, `<task id=2>` gibi numaralı bloklarla
yapabilirsin:

```
<tasks>
<task id=1>
Önce formu incele ve işaretli kutuları çıkar.
</task>
<task id=2>
Sonra çizimi <accident_summary> bağlamını akılda tutarak yorumla.
</task>
<task id=3>
İkisini birleştirip kusurun kimde olduğuna güvenle karar verilebilir mi,
yoksa insan denetçinin ek bilgi araması mı gerekir, belirt.
</task>
</tasks>
```

n8n'de çok karmaşık görevleri tek node yerine birden fazla LLM node'una
bölmek de bir decomposition biçimidir: her node tek ve net bir alt görevi
yapar, çıktısı bir sonrakine girdi olur. Bu hem hata ayıklamayı kolaylaştırır
hem de her adımı ayrı ayrı iyileştirmene izin verir.

### Ajanlar için not: katı örnekten kavramsal mühendisliğe

Tek seferlik (one-shot) görevlerde işe yarayan katı şablon ve örnek teknikleri,
ajanlar bir döngü içinde otonom çalışırken ters tepebilir. Ajan kurarken modele
her senaryonun katı kuralını vermek yerine, iyi karar verebilmesi için sezgiler
ve ilkeler ver. Prompt'a her olası edge case'i tıkıştırmak yerine, beklenen
davranışı temsil eden çeşitli ve kanonik birkaç örnek seç — LLM için örnekler
"bin kelimeye bedel resimlerdir". Bağlamı bilgilendirici ama sıkı tut.
