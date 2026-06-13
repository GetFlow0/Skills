# skill-uret Nedir ve Ne İşe Yarar?

## Kısaca

`skill-uret`, herhangi bir ders notu, doküman veya metin alıp
bunu doğrudan kullanılabilir bir Claude skill'ine dönüştüren
bir terminal komutudur.

Sen sadece notları yapıştırırsın.
Claude içeriği analiz eder, amacına karar verir, skill'i yazar.

---

## Hangi Problemi Çözüyor?

Bir ders izledin, bir doküman okudun, notlar aldın.
Bunları tekrar tekrar kullanmak istiyorsun ama her seferinde
aynı şeyleri Claude'a açıklamaktan yoruldun.

`skill-uret` bu notları bir kereye mahsus işler ve
bir daha hiç açıklamana gerek kalmayacak şekilde
kalıcı bir komuta dönüştürür.

---

## Nasıl Çalışır?

```
Sen          →  Notları terminale yapıştırırsın
Claude       →  İçeriği analiz eder
Claude       →  3 hedeften birini seçer (kendi kararı)
Claude       →  Kararını ve gerekçesini açıklar
Claude       →  Kullanıma hazır skill dosyasını yazar
```

### 3 Hedef Nedir?

**A — Claude.ai Prompt Rehberi**
Notlar genel AI kullanım prensipleri içeriyorsa,
Claude.ai'de daha iyi sonuç almak için şablonlar üretir.

**B — n8n LLM Node System Prompt'u**
Notlar otomasyon, workflow veya API konularını içeriyorsa,
n8n'deki LLM node'larına yapıştırılabilir system prompt üretir.

**C — Claude Code Terminal Slash Command'ı**
Notlar kod yazma, terminal veya geliştirme araçlarını içeriyorsa,
`.claude/commands/` altına eklenecek `.md` dosyası üretir.

---

## Kurulum

### Adım 1: Dosyayı doğru yere koy

Sadece bu proje için:
```bash
cp skill-uret.md /projen/.claude/commands/skill-uret.md
```

Tüm projelerde kullanmak için:
```bash
cp skill-uret.md ~/.claude/commands/skill-uret.md
```

### Adım 2: Terminali aç

VS Code terminalinde projenin klasöründeyken:
```bash
claude
```

### Adım 3: Komutu çalıştır

```
/skill-uret
```

Notlarını yapıştır, Enter'a bas.

---

## Örnek Kullanım

```
/skill-uret

[buraya ders notlarını yapıştır]
```

Claude'un vereceği çıktı şöyle görünür:

```
KARAR: C
GEREKÇE: Notlar Claude Code terminal kullanımı, araç entegrasyonu
ve workflow kalıpları içeriyor. Terminal skill olarak üretmek
en doğrudan kullanım sağlar.

--- Üretilen Dosya: .claude/commands/yeni-gorev.md ---
[dosya içeriği]
```

---

## Kimler İçin?

- n8n ile otomasyon geliştirenler
- Claude Code'u VS Code terminalinde kullananlar
- Ders notlarını, dokümanları tekrar kullanılabilir hale getirmek isteyenler
- Her proje için sıfırdan prompt yazmaktan bıkanlar
