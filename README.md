<div dir="rtl">

# مهارات الكتابة العربية للمساعدات الذكية

يضمّ هذا المستودع ثلاث مهارات جاهزة للمساعدات الذكية، مثل Claude وغيره. تعين المهارات الباحث والكاتب على نصّ عربي فصيح سليم يقرؤه القارئ بسهولة، وتراجع النص الإنجليزي بالطريقة نفسها، وتنظّف أيّ نص من الرموز المخفية التي يتركها النسخ من روبوتات المحادثة.

أعدّ المستودع د. حارث عدنان محمد، الأستاذ المساعد في جامعة كركوك، ضمن مجتمع «المعرفة قوة» للباحثين العرب.

## لمن هذا المستودع؟

- الباحثون وطلبة الدراسات العليا الذين يكتبون رسائلهم وبحوثهم بالعربية ويستعينون بالذكاء الاصطناعي في الصياغة.
- الأساتذة وصنّاع المحتوى التعليمي الذين يريدون نصًّا فصيحًا خاليًا من العبارات المكرّرة والتراكيب المترجمة.
- كلّ من ينسخ نصوصًا من ChatGPT أو Gemini أو غيرهما ويريد التخلّص من الرموز غير المرئية وبقايا الاقتباسات قبل النشر.

## المهارات

### ١. تحرير النص العربي الفصيح: [`arabic-humanizer`](skills/arabic-humanizer/SKILL.md)

تراجع المهارة أسلوب النص ولا تمسّ مضمونه. فهي لا تضيف حقيقة ولا رقمًا ولا مرجعًا، ولا تحذف شيئًا منها. وتعالج ما يأتي:

- العبارات القالبية في الافتتاح والختام، مثل «في عالمنا اليوم» و«مما لا شك فيه» و«في الختام».
- الإفراط في أدوات بعينها، مثل «حيث» و«يُعدّ» و«بالإضافة إلى ذلك» و«قام بـ».
- التراكيب المنقولة عن الإنجليزية، مثل «ليس فقط… بل»، وتكديس المترادفات والصفات الثلاثية.
- ضبط مستوى اللغة بحسب النص: بحث علمي أو تقرير أو منشور أو نص فيديو.
- قائمة تدقيق إملائي ونحوي تشمل الهمزات والتاء المربوطة والألف المقصورة وأحكام العدد والمعدود.

مثالان من قواعد المهارة:

| قبل | بعد |
|---|---|
| ارتفعت الأسعار، حيث بلغ التضخم… | ارتفعت الأسعار؛ إذ بلغ التضخم… |
| قام الباحث بتحليل البيانات | حلّل الباحث البيانات |

### ٢. تحرير النص الإنجليزي: [`humanizer`](skills/humanizer/SKILL.md)

تكشف المهارة ٢٦ نمطًا شائعًا في الكتابة الآلية بالإنجليزية وتعيد الصياغة دون تغيير المعنى. وهي مبنية على دليل ويكيبيديا «Signs of AI writing».

### ٣. تنظيف النصوص من الرموز المخفية: [`text-cleaner`](skills/text-cleaner/SKILL.md)

سكربت بلغة بايثون لا يحتاج إلى تثبيت أيّ مكتبة. يزيل المسافات الصفرية وعلامة BOM والشرطات اللينة ومحارف الاتجاه الزائدة، ويحذف بقايا اقتباسات روبوتات المحادثة مثل `citeturn` و`【n†source】` و`utm_source=chatgpt.com`. ويحافظ على المحارف اللازمة للعربية والفارسية والكردية (ZWNJ وZWJ). ويستطيع عند الطلب أن يمسح اسم المؤلف من ملفات Word وExcel وPowerPoint.

```bash
# تنظيف ملف نصي وحفظ النتيجة في ملف جديد
python3 skills/text-cleaner/clean_text.py input.txt -o output.txt

# فحص الملف وعرض ما فيه من رموز دون تعديله
python3 skills/text-cleaner/clean_text.py input.txt --check

# مسح اسم المؤلف من ملف Word
python3 skills/text-cleaner/clean_text.py report.docx --office-meta -o report_clean.docx
```

## طريقة الاستخدام

1. نزّل المستودع بزر **Code** ثم **Download ZIP**، أو بالأمر:

   ```bash
   git clone https://github.com/harethhareth55-cpu/arabic-writing-skills.git
   ```

2. انسخ مجلد المهارة التي تريدها من مجلد `skills` إلى مجلد المهارات في أداتك. في Claude Code مثلًا يكون المجلد `~/.claude/skills/`.
3. اطلب من المساعد أن يراجع نصّك، مثل: «راجع هذه الفقرة من رسالتي بالفصحى ولا تغيّر أيّ رقم أو مرجع».

## حدود الاستخدام

- تحسّن المهارات الأسلوب وحده، ولا تضيف معلومات ولا تغيّرها.
- ليست أداة للتحايل على كاشفات الذكاء الاصطناعي، ولا تعد بتجاوزها.
- في العمل الأكاديمي، التزم بتعليمات المجلة أو الجامعة في الإفصاح عن الاستعانة بالذكاء الاصطناعي.
- لا يزيل `text-cleaner` بيانات C2PA ولا العلامات المائية للذكاء الاصطناعي ولا علامات حقوق النشر، ويُستخدم على نصوصك أو على ما يحقّ لك تعديله.

## روابط

- [قناة د. حارث عدنان محمد على يوتيوب](https://www.youtube.com/@Dr.harethadnanmohammed1657)
- [موقع مجتمع «المعرفة قوة»](https://almarifa-quwwa.pages.dev)

</div>

---

## English

Three Agent Skills (`SKILL.md` format) for AI assistants such as Claude:

- **arabic-humanizer**: style and proofreading rules for Modern Standard Arabic. It removes formulaic openers and closers, overused connectors, English calques and stacked synonyms, and runs a spelling and grammar checklist.
- **humanizer**: finds 26 common AI-writing patterns in English prose and rewrites them without changing the meaning.
- **text-cleaner**: a standard-library Python script that strips hidden Unicode and chatbot citation leftovers while keeping the ZWNJ/ZWJ that Arabic, Persian and Kurdish need. It can optionally blank author fields in DOCX/XLSX/PPTX files.

To install, copy a skill folder from `skills/` into your tool's skills folder (for Claude Code, `~/.claude/skills/`).

These skills edit style only. They never add or change facts, numbers or citations, and they are not detector-evasion tools. `text-cleaner` does not remove C2PA, AI-provenance or copyright watermarks.

## Credits and licenses

- `humanizer` is [blader/humanizer](https://github.com/blader/humanizer) v3.1.0 by Siqi Chen, MIT License, included unchanged with its original [LICENSE](skills/humanizer/LICENSE).
- `arabic-humanizer` builds on the Arabic reference in [finestructure-ai/humanizer-multilingual](https://github.com/finestructure-ai/humanizer-multilingual), MIT License, Copyright (c) 2026 Fine Structure. See its [LICENSE](skills/arabic-humanizer/LICENSE).
- `text-cleaner` and the rest of this repository: MIT License, Copyright (c) 2026 Harith Adnan Mohammed.

These are community skills. They are not published or endorsed by Anthropic.
