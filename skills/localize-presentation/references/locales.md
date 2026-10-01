# Languages and locales

The presentation language (`setPresentationLanguage`) takes one of these codes. It sets the player
interface, date formats and text direction; it does not translate the content. The `Player` column
says how mature the player's own translation is: languages marked beta have an interface translation
that is still being reviewed.

Contents: 1 Supported languages, 2 Scripts and fonts, 3 Text length, 4 Right-to-left decks,
5 Conventions

## 1. Supported languages

| Code | Language | Direction | Script | Player |
|---|---|---|---|---|
| `en-US` | English | LTR | Latin |  |
| `vi-VN` | Vietnamese | LTR | Latin (Vietnamese diacritics) |  |
| `zh-CN` | Chinese Simplified | LTR | Han | beta |
| `zh-TW` | Chinese Traditional | LTR | Han | beta |
| `ja-JP` | Japanese | LTR | Han, Hiragana, Katakana |  |
| `ko-KR` | Korean | LTR | Hangul | beta |
| `es-ES` | Spanish | LTR | Latin | beta |
| `fr-FR` | French | LTR | Latin | beta |
| `de-DE` | German | LTR | Latin | beta |
| `pt-BR` | Portuguese Brazil | LTR | Latin | beta |
| `it-IT` | Italian | LTR | Latin | beta |
| `nl-NL` | Dutch | LTR | Latin | beta |
| `ru-RU` | Russian | LTR | Cyrillic | beta |
| `ar-EG` | Arabic | RTL | Arabic | beta |
| `hi-IN` | Hindi | LTR | Devanagari | beta |
| `id-ID` | Indonesian | LTR | Latin | beta |
| `th-TH` | Thai | LTR | Thai | beta |
| `tr-TR` | Turkish | LTR | Latin | beta |
| `pl-PL` | Polish | LTR | Latin | beta |
| `uk-UA` | Ukrainian | LTR | Cyrillic | beta |
| `ms-MY` | Malay | LTR | Latin | beta |
| `fil-PH` | Filipino | LTR | Latin | beta |
| `bn-BD` | Bengali | LTR | Bengali | beta |
| `ur-PK` | Urdu | RTL | Arabic (Nastaliq style) | beta |
| `he-IL` | Hebrew | RTL | Hebrew | beta |
| `fa-IR` | Persian | RTL | Arabic | beta |
| `cs-CZ` | Czech | LTR | Latin | beta |
| `sv-SE` | Swedish | LTR | Latin | beta |
| `da-DK` | Danish | LTR | Latin | beta |
| `fi-FI` | Finnish | LTR | Latin | beta |
| `nb-NO` | Norwegian Bokmal | LTR | Latin | beta |
| `ro-RO` | Romanian | LTR | Latin | beta |
| `el-GR` | Greek | LTR | Greek | beta |
| `hu-HU` | Hungarian | LTR | Latin | beta |
| `bg-BG` | Bulgarian | LTR | Cyrillic | beta |
| `hr-HR` | Croatian | LTR | Latin | beta |
| `sr-RS` | Serbian | LTR | Cyrillic | beta |
| `sk-SK` | Slovak | LTR | Latin | beta |
| `sl-SI` | Slovenian | LTR | Latin | beta |
| `lt-LT` | Lithuanian | LTR | Latin | beta |
| `lv-LV` | Latvian | LTR | Latin | beta |
| `et-EE` | Estonian | LTR | Latin | beta |

## 2. Scripts and fonts

A template's fonts cover the letters of its own language. For another script check the deck's fonts
(`getPresentationInfo` shows them; `listTemplates` shows each template's `fonts` and `language`) and
then look at the slides: a different-looking fallback font on some letters, or empty boxes, means
the font lacks them. Families that cover each script (all on Google Fonts):

| Script | Families |
|---|---|
| Latin | most fonts; Vietnamese needs a font with Vietnamese letters |
| Latin (Vietnamese diacritics) | a font with the Vietnamese subset, for example Noto Sans, Be Vietnam Pro, Inter |
| Cyrillic | Noto Sans, Noto Serif, Inter, Roboto |
| Greek | Noto Sans, Noto Serif, Inter, Roboto |
| Han | Noto Sans SC (Simplified) or Noto Sans TC (Traditional); Noto Serif SC/TC for serif |
| Han, Hiragana, Katakana | Noto Sans JP, Noto Serif JP |
| Hangul | Noto Sans KR, Noto Serif KR |
| Arabic | Noto Sans Arabic, Noto Naskh Arabic; for Persian also Vazirmatn |
| Arabic (Nastaliq style) | Noto Nastaliq Urdu, Noto Sans Arabic |
| Hebrew | Noto Sans Hebrew, Heebo |
| Devanagari | Noto Sans Devanagari |
| Bengali | Noto Sans Bengali |
| Thai | Noto Sans Thai, Sarabun |

When a font is missing letters, prefer a template whose fonts already cover the script; changing a
font on every shape by hand fights the template's design.

## 3. Text length

Text boxes are sized for the template's language, so a translation changes how much fits. Rules of
thumb (characters, compared with English):

| Target | Length | What to do |
|---|---|---|
| German, Russian, Spanish, French, Portuguese, Italian, Vietnamese, Indonesian and most European languages | about 10-30% longer | Shorter sentences, shorter headings, stay inside `maxChars` |
| Thai | about the same, no spaces | Check line breaks in the picture |
| Chinese, Japanese, Korean | about half the characters, but each is full width | Keep to about half of `maxChars` (the app itself halves budgets for these languages) |
| Arabic, Hebrew, Persian, Urdu | about the same | Check the mirrored layout in the picture |

## 4. Right-to-left decks

For `ar-EG`, `he-IL`, `fa-IR` and `ur-PK` the app mirrors the slide: text aligns to the right, lists
and answer choices start from the right. Formulas stay left to right inside the text. Layouts drawn
for left-to-right reading can look unbalanced (decor on the wrong side, arrows pointing the wrong
way), so always look at the slides with `getSlideImage` and tell the user what you see.

## 5. Conventions

Write numbers, dates and money the way the target audience does: decimal and thousands separators
(Vietnamese, German and French use a decimal comma), date order (day-month-year in most of the
world), currency symbol position, 24-hour clock, metric units. Adapt examples that only make sense
in one country (currencies, school grades, public holidays) and keep proper nouns as they are.
