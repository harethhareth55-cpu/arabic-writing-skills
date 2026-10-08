# مهارات الكتابة العربية للمساعدات الذكية | Arabic Writing Skills for AI Assistants

ثلاث مهارات (Agent Skills بصيغة `SKILL.md`) تساعد المساعد الذكي، مثل Claude وغيره، على كتابة نصوص طبيعية مصقولة وتنظيفها من الرموز المخفية.

إعداد: د. حارث عدنان محمد، أستاذ مساعد، جامعة كركوك.

## المهارات

| المهارة | الوظيفة |
|---|---|
| [`arabic-humanizer`](skills/arabic-humanizer/SKILL.md) | تحرير النص العربي الفصيح ليبدو طبيعيًا ومتقنًا: إزالة العبارات القالبية والتراكيب المترجمة، وضبط السجل الأكاديمي، وقائمة تدقيق إملائي (الهمزات، التاء المربوطة، الألف المقصورة، العدد والمعدود). |
| [`humanizer`](skills/humanizer/SKILL.md) | تحرير النص الإنجليزي من 26 نمطًا شائعًا في الكتابة الآلية، استنادًا إلى دليل ويكيبيديا «Signs of AI writing». |
| [`text-cleaner`](skills/text-cleaner/SKILL.md) | سكربت بايثون (من المكتبة القياسية فقط) يزيل الرموز غير المرئية وبقايا اقتباسات روبوتات المحادثة، ويحافظ على ZWNJ/ZWJ اللازمة للعربية والفارسية والكردية. |

## الاستخدام

انسخ مجلد المهارة إلى مجلد المهارات في أداتك، مثلًا `~/.claude/skills/` في Claude Code، أو ارفعه في إعدادات المهارات في Claude.

```bash
python3 skills/text-cleaner/clean_text.py input.txt -o output.txt
```

## حدود الاستخدام

- المهارات تحسّن الأسلوب فقط، ولا تضيف حقائق أو أرقامًا أو مراجع ولا تغيّرها.
- ليست أداة للتحايل على كاشفات الذكاء الاصطناعي، ولا تعد بتجاوزها.
- `text-cleaner` لا يزيل علامات C2PA ولا العلامات المائية للذكاء الاصطناعي ولا علامات حقوق النشر، ويُستخدم على نصوصك أو ما يحق لك تعديله فقط.

---

## English

Three Agent Skills (`SKILL.md` format) for natural, polished writing:

- **arabic-humanizer**: style and proofreading rules for Modern Standard Arabic.
- **humanizer**: removes 26 common AI-writing patterns from English prose.
- **text-cleaner**: a stdlib-only Python script that strips hidden Unicode and chatbot citation leftovers while keeping Arabic/Persian ZWNJ/ZWJ. It does not remove C2PA, AI-provenance or copyright watermarks.

These skills edit style only and are not detector-evasion tools.

## Credits and licenses

- `humanizer` is [blader/humanizer](https://github.com/blader/humanizer) v3.1.0 by Siqi Chen, MIT License, included unchanged with its original [LICENSE](skills/humanizer/LICENSE).
- `arabic-humanizer` is adapted from the Arabic reference in [finestructure-ai/humanizer-multilingual](https://github.com/finestructure-ai/humanizer-multilingual), MIT License, Copyright (c) 2026 Fine Structure. See its [LICENSE](skills/arabic-humanizer/LICENSE).
- `text-cleaner` and the repository's own material: MIT License, Copyright (c) 2026 Harith Adnan Mohammed.

These are community skills and are not published or endorsed by Anthropic.
