---
name: arabic-humanizer
description: >-
  Use when writing, editing or reviewing Arabic text (Modern Standard Arabic / فصحى)
  so it reads natural, polished and human instead of machine-generated: posts,
  reports, emails, video scripts, theses and research papers. Fixes AI-sounding
  Arabic patterns (formulaic openers and closers, حيث/يُعدّ/بالإضافة إلى ذلك overuse,
  «ليس فقط… بل», triads, stacked synonyms, calques, passive تمّ, Latin punctuation,
  emoji and bold overuse), sets the academic register, and runs a proofreading
  checklist (hamzas, ة/ه, ى/ي, number agreement). Never adds or changes facts,
  numbers or citations.
license: MIT
metadata:
  based_on: "finestructure-ai/humanizer-multilingual, references/arabic.md (MIT, Copyright (c) 2026 Fine Structure): https://github.com/finestructure-ai/humanizer-multilingual"
  companion: "humanizer (blader/humanizer, MIT, Siqi Chen)"
---
# Arabic humanizer (فصحى)

Rewrite Arabic text so it reads like a careful Arabic writer wrote it for one reader, not like a translated English template. Work on style only.

> Attribution: the Typography, Syntax, Calques and Register sections build on `references/arabic.md` from [finestructure-ai/humanizer-multilingual](https://github.com/finestructure-ai/humanizer-multilingual) (MIT License, Copyright (c) 2026 Fine Structure), which in turn builds on [blader/humanizer](https://github.com/blader/humanizer) by Siqi Chen (MIT). The remaining rules and the checklist were written for this skill. This is not an official Anthropic skill.

## Non-negotiable rules
1. **Never add, remove or change a fact, number, date, name, quotation, statistic or citation.** If a sentence needs a detail you don't have, ask for it or write a simpler sentence. Keep numbers exactly as given, including the digit style (Arabic-Indic ٠١٢٣ or Western 0123); only fix grammar around them.
2. Keep technical terms, variable names, software names (SPSS, Stata, R), statistical notation such as (t = 2.31, p < 0.05), and references in their original form and order.
3. Don't promise or imply that the text will pass an AI detector, and don't treat detector evasion as a goal.
4. For academic work, remind the user (once, briefly) to follow the journal's or university's rules on disclosing AI assistance. These edits improve style; they don't change who or what produced the content.
5. Keep the requested register. Default is clear, modern فصحى. Don't introduce dialect words unless the user asks for dialect.

## Workflow
1. Read the whole text and decide the register: academic paper, report, social post, or script.
2. Mark the tells listed below, strongest first: formula openers and closers, empty significance, «ليس فقط… بل», triads, then the word-level overuse.
3. Rewrite once for meaning: state each point directly, vary sentence length, and start some sentences with the verb. Don't patch phrases one at a time.
4. Check that no fact, number or citation was added, dropped or altered. Compare claim by claim.
5. Run the proofreading checklist at the end of this skill.
6. Return the final text. Add a short list of changes only if the user asks for one.

## A. Formulaic openers and closers (delete, or replace with the actual point)
- Openers: «في عالمنا اليوم»، «في ظل التطورات المتسارعة»، «في العصر الرقمي»، «مما لا شك فيه»، «لا يخفى على أحد»، «تجدر الإشارة إلى أن»، «من الجدير بالذكر أن»، «في هذا السياق»، «دعونا نتعمق». Start with the claim itself.
- Closers: «في الختام»، «وختامًا يمكن القول»، «وفي النهاية»، «وبهذا نكون قد…»، «يبقى السؤال…»، and a final sentence that repeats the paragraph. End on the last real point. In a paper, the conclusion section states findings and recommendations without these formulas.

## B. Overused connectors and hedges
- **«حيث»** is used as an all-purpose "where/as/since". Keep it for place. Otherwise use «إذ», «فـ», «لأنّ», a relative clause, or a new sentence. Example: «ارتفعت الأسعار، حيث بلغ التضخم…» → «ارتفعت الأسعار؛ إذ بلغ التضخم…» or «ارتفعت الأسعار، وبلغ التضخم…».
- **«يُعدّ / يُعتبر»** hedges a plain statement. «يُعدّ الإحصاء أداة مهمة» → «الإحصاء أداة مهمة» or a verb that says what it does: «يكشف الإحصاء…».
- **«بالإضافة إلى ذلك»، «علاوة على ذلك»، «كما أنّ»، «فضلًا عن ذلك»** at the head of sentence after sentence. Usually «و» is enough, or join the related clauses. Use one such connector only where the addition needs emphasis.
- **«إنّ»** opening every sentence, and «قام بـ + مصدر» («قام الباحث بتحليل البيانات» → «حلّل الباحث البيانات»).
- **«من خلال»** for every "through/via" → «بـ», «عبر» or restructure: «من خلال استخدام الاستبانة» → «باستبانة».
- **«يتيح لك / يمكّنك من»** on every feature sentence → use the plain verb: «يمكّنك التطبيق من حساب…» → «يحسب التطبيق…».

## C. Rhythm by rule
- **«ليس فقط… بل»** is a calque of "not only… but". Use a native structure, or state both points plainly: «لا يقتصر على… بل…», «لا… فحسب، بل…», or simply «يساعد على فهم البيانات وتفسيرها».
- **Triads:** «دقيقة وموثوقة وشاملة»، «شاملة ومتكاملة ومستدامة». Keep only the words that carry meaning; two are often enough, and sometimes one.
- **Stacked synonyms:** «الأهمية والضرورة القصوى»، «التحديات والصعوبات والعقبات»، «سعيًا وجهدًا». Pick one.
- **Empty intensifiers and inflation:** «بالغ الأهمية»، «حجر الزاوية»، «نقلة نوعية»، «ثورة حقيقية»، «بشكل كبير جدًا»، «لا غنى عنه»، «دور محوري». Replace with the specific fact, or delete.
- **Uniform sentence length** and the same opening word on consecutive sentences. Vary them. Arabic legitimately chains clauses with «و», so long sentences alone are not a tell; sameness is.
- **Rigid subject-first order:** machine Arabic copies English SVO in every sentence. Use verb-initial sentences where natural: «الباحث استخدم المنهج الوصفي» → «استخدم الباحث المنهج الوصفي».

## D. Calques from English
| Machine Arabic | English source | Better |
|---|---|---|
| في نهاية اليوم | at the end of the day | في المحصّلة |
| إطلاق العنان لإمكانات | unlock the potential | الاستفادة من، توظيف |
| الانتقال إلى المستوى التالي | take it to the next level | تطوير، النهوض بـ |
| تغيير قواعد اللعبة | game changer | نقلة نوعية (only if true), or state the change |
| يلعب دورًا مهمًّا في | plays an important role | يسهم في، يؤثّر في |
| بشكل فعّال / بطريقة فعّالة | effectively | بفاعلية |
| على أساس يومي | on a daily basis | يوميًّا |
| من قِبَل الباحث | by the researcher | use the active voice: أجرى الباحث… |
| سوف لن | will not | لن |
| كلما زاد… كلما زاد | the more… the more | كلما زاد… زاد |
| تمّ + مصدر (تمّ إجراء الدراسة) | was conducted | أُجريت الدراسة / أجرى الباحث الدراسة |

Keep the passive with «تمّ» rare. Prefer the active voice, or the true passive verb (أُجريت، وُزّعت، حُلّلت) when the doer is unknown or unimportant.

## E. Punctuation, typography and formatting
- Use the Arabic comma «،», semicolon «؛» and question mark «؟», not «, ; ?». Put no space before punctuation and one space after it.
- Use guillemets «…» or "…", and be consistent within a document.
- The em dash «—» is not native Arabic punctuation. Replace it with «،» or «؛», or with parentheses for an aside.
- In mixed Arabic/Latin lines, check that Latin terms, numbers and trailing punctuation sit on the correct side (bidi damage). Keep statistical expressions together: (p < 0.05).
- **Emoji:** none in academic or formal text. In social posts, at most one or two where they add meaning, never one per line or per bullet.
- **Bold:** only for a term defined once, or a true warning. No bold labels at the start of every line, and no headings for a three-sentence post.

## F. Academic register (papers, theses, reports)
- Follow the conventions of the target journal or university: section names such as «المقدمة، مشكلة الدراسة، أهداف الدراسة، فرضيات الدراسة، منهجية الدراسة، النتائج ومناقشتها، الاستنتاجات والتوصيات», and the expected person (often «الباحث» or «الباحثون» in the third person, or «نحن»). Keep whatever the author already uses.
- Calibrate claims: «تشير النتائج إلى» or «تدل النتائج على», not «تثبت النتائج بما لا يدع مجالًا للشك». Don't upgrade a correlation to a cause.
- Report statistics precisely and leave the numbers alone: «أظهرت النتائج وجود علاقة ذات دلالة إحصائية عند مستوى (0.05)».
- Keep in-text citations and the reference list exactly as given (APA or the journal's style). Don't invent, merge or "complete" references.
- Define each term once and then use the same term throughout. Don't rotate synonyms for variety.

## G. Example (an illustration written for this skill, not a quotation)
Before:
«في عالمنا اليوم، يُعدّ التحليل الإحصائي حجرَ الزاوية في البحث العلمي، حيث إنه لا يساعد الباحث فقط على فهم البيانات، بل يمكّنه أيضًا من الوصول إلى نتائج دقيقة وموثوقة وشاملة. وفي الختام، يمكن القول إن الإحصاء ضرورة لا غنى عنها.»
Tells: formula opener, «يُعدّ», «حجر الزاوية», «حيث إنه», «لا… فقط… بل», «يمكّنه من», a triad, a formula closer, and a closing sentence that repeats the point.
After:
«يساعد التحليل الإحصائي الباحثَ على فهم بياناته والوصول إلى نتائج دقيقة وموثوقة، ولذلك لا يستغني عنه البحث العلمي.»
No fact was added; the filler word «شاملة» and the repeated closing were removed.

## H. Proofreading checklist (run last)
**Hamza at the start of a word (همزة القطع والوصل)**
- Hamzat al-wasl, written as a bare «ا»: the masdar, past and imperative of five- and six-letter verbs (اختبار، اقتصاد، استخدام، انتشار، اجتماع، استخدِمْ), the imperative of three-letter verbs (اكتبْ), and the nouns ابن، اسم، اثنان، اثنتان، امرؤ، امرأة. Common errors: «إختبار، إقتصاد، إستخدام، إنتشار» → «اختبار، اقتصاد، استخدام، انتشار».
- Hamzat al-qat', written «أ/إ»: four-letter verbs of the form أفعل and their masdar (أنتج، إنتاج، أجرى، إجراء، أحصى، إحصاء), and most nouns and particles (أداء، أسلوب، إلى، إنّ، أنّ، أو). Common errors: «اجراء، الاداء، الى» → «إجراء، الأداء، إلى».
**Hamza in the middle and at the end**
- Middle: the seat follows the stronger of the two surrounding vowels (kasra > damma > fatha > sukun): سُئِل، مسؤول، شؤون، سأل، رئيس.
- End: the seat follows the preceding vowel (قرأ، تباطؤ، شاطئ، مبادئ). After a sukun, the hamza stands alone on the line (جزء، شيء، بطء، عبء). Common error: «شئ» → «شيء».
**التاء المربوطة / الهاء**
- A noun or adjective ending that is pronounced «ت» in construct (دراسةُ الحالة) is «ة»: دراسة، نتيجة، عينة. A pronoun suffix is «ه»: نتائجه، بياناته. Common errors: «دراسه، اللغه، العينه» → «دراسة، اللغة، العينة». Verbs and sound feminine plurals take an open «ت»: كانت، بيانات.
**الألف المقصورة / الياء**
- «ى» at the end: على، إلى، حتى، مستوى، مبنى، أجرى، أدّى. «ي» for real ya: في، الذي، التي، and the nisba ending (العلمي، الإحصائي). Common errors: «فى، علي (for على)، الى، مستوي» → «في، على، إلى، مستوى».
- Three-letter roots: «ا» if the original letter is و (دعا، علا، سما), «ى» if it is ي (رمى، سعى، هدى). Longer words take «ى» (استدعى، مصطفى), unless a «ي» comes just before (دنيا، قضايا، هدايا).
**Numbers and the counted noun (إعراب العدد والمعدود)**
- 1 and 2 agree with the noun and follow it: «باحث واحد، استبانتان اثنتان».
- 3 to 10 take the opposite gender of the singular noun, and the noun is plural and genitive: «ثلاثة باحثين، ثلاث استبانات، عشرة أعوام، عشر سنوات».
- 11 and 12: both parts agree with the noun, and the noun is singular and accusative: «أحد عشر باحثًا، إحدى عشرة استبانة، اثنا عشر باحثًا (اثني عشر in the accusative and genitive)، اثنتا عشرة استبانة».
- 13 to 19: the unit takes the opposite gender and «عشر/عشرة» agrees with the noun, which is singular and accusative: «ثلاثة عشر باحثًا، ثلاث عشرة استبانة».
- Tens (20 to 90) have one form for both genders and inflect like a sound masculine plural: «شارك عشرون باحثًا»، «وُزّعت الاستبانة على عشرين مدرسة». Compounds (21 to 99): the unit follows the 3–10 rule: «ثلاثة وعشرون باحثًا، ثلاث وعشرون استبانة».
- 100 and 1000: the noun is singular and genitive: «مئة طالب، ألف استبانة» (مئة and مائة are both accepted; use one consistently).
- Ordinals agree with the noun: «الفصل الثالث، المرحلة الثالثة».
- In data-heavy academic text, digits are normal (٣٥٠ استبانة / 350 استبانة). Don't convert digits to words or switch digit styles unless the journal requires it.
**Final pass**
- Read the text once for meaning, then once only for spelling and punctuation. Confirm that every number, name and citation matches the source exactly.
