# Skills

Claude Code / Claude.ai için hazırlanmış skill koleksiyonu. Her klasör bağımsız bir skill veya skill paketidir.

## İçerik

### `prompt-nasil-yazilir/`
**Prompt Engineering Skill** — Anthropic'in resmi prompt engineering yaklaşımına göre Claude için yüksek kaliteli prompt tasarlama rehberi. 10 bölümlük prompt yapısı, XML tag organizasyonu, few-shot örnekler, halüsinasyon önleme, prefill ve extended thinking tekniklerini kapsar. n8n / API / webhook otomasyon akışlarında tekrarlanabilir, öngörülebilir LLM çıktısı almaya odaklıdır.

- `SKILL.md`

### `skill-uret-paketi/`
**/skill-uret** — Verilen notları analiz edip kullanım amacını belirleyen ve uygun skill dosyalarını üreten yardımcı paket. Notların hangi hedefe (ör. Claude.ai prompt rehberi) uygun olduğuna karar verir ve o hedefe özel çıktı üretir.

- `skill-uret.md`
- `OKUYUN.md`

### `ui-ux-pro-max-skill-main/`
**UI/UX Pro Max** — `.claude/skills/` altında çok parçalı bir tasarım skill seti (claudekit). Banner tasarımı, marka kimliği, genel tasarım, tasarım sistemi, slaytlar ve UI styling skill'lerini içerir; AI üretimli görsellerle birden fazla sanat yönü sunar.

İçerdiği skill'ler:

| Skill | Açıklama |
|-------|----------|
| `banner-design` | Sosyal medya, reklam, web hero ve baskı için banner tasarımı |
| `brand` | Marka sesi, görsel kimlik, mesajlaşma çerçeveleri ve tutarlılık |
| `design` | Genel tasarım üretimi (logo, ikon, mockup vb.) |
| `design-system` | Tasarım sistemi ve token yönetimi |
| `slides` | Sunum / slayt tasarımı |
| `ui-styling` | UI stil ve tema uygulaması |
| `ui-ux-pro-max` | Kapsamlı UI/UX tasarım skill'i |

## Kurulum

Skill'leri kullanmak için ilgili klasörü Claude projenizin `.claude/skills/` dizinine kopyalayın. `ui-ux-pro-max-skill-main` zaten `.claude/skills/` yapısıyla gelir.
