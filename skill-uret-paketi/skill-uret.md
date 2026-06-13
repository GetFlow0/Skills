# /skill-uret

Verilen notları analiz et, kullanım amacını kendin belirle ve uygun skill dosyalarını üret.

## Görevin

Sana bir ders/not seti verilecek. Sen:

1. Notları oku ve içeriği anla
2. Aşağıdaki 3 hedeften hangisine en uygun olduğuna **kendin karar ver**
3. Kararını ve gerekçeni açıkla
4. O hedefe özel skill dosyalarını üret

---

## Hedef Seçenekleri

### A) Claude.ai Prompt Rehberi
**Seç eğer:** Notlar genel AI kullanım prensipleri, iletişim teknikleri, soru sorma yöntemleri içeriyorsa
**Çıktı:** Claude.ai'de daha iyi sonuç almak için kullanılabilecek prompt şablonları ve rehber

### B) n8n LLM Node System Prompt Şablonu
**Seç eğer:** Notlar otomasyon, workflow, veri işleme, API entegrasyonu veya tekrar eden görevler içeriyorsa
**Çıktı:** n8n'deki LLM node'larına yapıştırılabilir system prompt'lar

### C) Claude Code Terminal Skill'i
**Seç eğer:** Notlar kod yazma, terminal kullanımı, geliştirme workflow'u, araç entegrasyonu içeriyorsa
**Çıktı:** `.claude/commands/` altına eklenebilecek slash command `.md` dosyaları

---

## Karar Verme Kriterleri

Şu soruları sor kendine:

- Notlarda "workflow", "otomasyon", "API", "veri" geçiyor mu? → **B**
- Notlarda "terminal", "bash", "kod", "commit", "tool" geçiyor mu? → **C**
- Notlarda "prompt", "context", "AI ile konuşma", "instruction" geçiyor mu? → **A**
- Birden fazla işaret varsa: hangi konu **ağırlıklı**? O hedefi seç.

---

## Çıktı Formatı

Önce kararını açıkla:
```
KARAR: [A / B / C]
GEREKÇE: [1-2 cümle neden bu hedefi seçtin]
```

Sonra seçilen hedefe göre dosyaları üret:

### Eğer A seçildiyse:
`prompt-rehberi.md` — Notlardan çıkan prompt yazma prensipleri ve şablonlar

### Eğer B seçildiyse:
`system-prompt-[konu].md` — n8n LLM node'una yapıştırılabilir system prompt
`kullanim-notu.md` — Hangi node'a, nasıl eklenir

### Eğer C seçildiyse:
`.claude/commands/[komut-adi].md` — Terminalde `/komut-adi` ile çalışan skill

---

## Başla

Notları al, analiz et, karar ver, üret.
