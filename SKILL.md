---
name: tdlife-konspekt
description: >
  Generates a bilingual (Ukrainian + Russian) interactive HTML summary (konspekt) from a lesson or speech transcript.
  Use this skill IMMEDIATELY when the user pastes any transcript, lecture text, audio/video transcription, or speech text —
  even if they don't say "make a konspekt". The trigger is ANY block of text that looks like a lesson, webinar, speech,
  or recording transcription. Output is a self-contained HTML file in TDLife brand style with language switcher,
  collapsible sections, interactive checklist, and contact footer for Dmytro Tetervak.
---

# TDLife Konspekt Generator

When you receive a transcript (a block of text from a lesson, speech, webinar, or audio/video recording), immediately generate a self-contained bilingual HTML konspekt file and save it to the current working directory.

**Filename**: `konspekt-<short-slug-from-title>.html`

---

## Design System (use exactly these values)

```css
:root {
  --dark: #101827;        /* headings */
  --grey: #6B7280;        /* body text */
  --pink: #FA2F6B;        /* warnings, important */
  --blue: #2463EB;        /* insights, accents */
  --sky: #6AC2EA;         /* quotes */
  --green: #16A34A;       /* correct answers */
  --bg-body: #F9FAFB;
  --bg-card: #FFFFFF;
  --bg-blue-muted: #C9D7F5;
  --bg-pink-muted: #F9CCDB;
  --bg-green-muted: #DCFCE7; /* correct answer bg */
  --shadow: 0 4px 12px rgba(0,0,0,0.06);
  --radius: 16px;
}
```

Font: `Roboto` (400, 500, 700) from Google Fonts.

---

## Bilingual System

Every piece of content must be rendered in BOTH languages.

**CSS toggle** (add to `<style>`):
```css
body.uk .lang-ru { display: none; }
body.ru .lang-uk { display: none; }
```

**Inline text** (titles, labels, short strings):
```html
<span class="lang-uk">Текст українською</span>
<span class="lang-ru">Текст на русском</span>
```

**Block content** (paragraphs, lists, boxes, quotes):
```html
<div class="lang-uk">...Ukrainian block...</div>
<div class="lang-ru">...Russian block...</div>
```

Default language: `<body class="uk">`.

---

## Page Structure

### 0. Language Switcher (first element)

```html
<div class="lang-switcher">
  <span class="lang-question">
    <span class="lang-uk">Якою мовою вам краще отримати цю інформацію?</span>
    <span class="lang-ru">На каком языке вам удобнее получить эту информацию?</span>
  </span>
  <div class="lang-buttons">
    <button class="lang-btn active" data-lang="uk" onclick="setLang('uk')">🇺🇦 Українська</button>
    <button class="lang-btn" data-lang="ru" onclick="setLang('ru')">🇷🇺 Русский (пока, к сожалению)</button>
  </div>
</div>
```

CSS:
```css
.lang-switcher { background: var(--bg-card); border-radius: var(--radius); padding: 16px 20px; display: flex; align-items: center; gap: 16px; flex-wrap: wrap; margin-bottom: 20px; box-shadow: var(--shadow); }
.lang-question { color: var(--grey); font-size: 14px; }
.lang-buttons { display: flex; gap: 8px; }
.lang-btn { padding: 8px 18px; border-radius: 8px; border: 2px solid var(--bg-blue-muted); background: white; cursor: pointer; font-size: 14px; font-weight: 500; color: var(--dark); transition: all 0.2s; }
.lang-btn.active { background: var(--blue); border-color: var(--blue); color: white; }
```

### 1. Header

```html
<div class="header">
  <div class="header-pre lang-uk">Інтерактивний конспект</div>
  <div class="header-pre lang-ru">Интерактивный конспект</div>
  <h1 class="header-title">
    <span class="lang-uk">TITLE UA</span>
    <span class="lang-ru">TITLE RU</span>
  </h1>
  <p class="header-sub lang-uk">SUBTITLE UA (1-2 sentences)</p>
  <p class="header-sub lang-ru">SUBTITLE RU</p>
  <div class="tags lang-uk"><span class="tag">#TagUA</span> ...</div>
  <div class="tags lang-ru"><span class="tag">#TagRU</span> ...</div>
</div>
```

- 4–6 specific tags (e.g. `#КонтрольВитрат`, not `#Бизнес`)
- Top border: `border-top: 6px solid var(--blue)`

CSS:
```css
.header { background: var(--bg-card); border-radius: var(--radius); padding: 32px; margin-bottom: 20px; box-shadow: var(--shadow); border-top: 6px solid var(--blue); }
.header-pre { font-size: 12px; font-weight: 500; color: var(--blue); text-transform: uppercase; letter-spacing: 1px; margin-bottom: 12px; }
.header-title { font-size: 26px; font-weight: 700; color: var(--dark); line-height: 1.3; margin-bottom: 14px; }
.header-sub { color: var(--grey); font-size: 15px; line-height: 1.6; margin-bottom: 20px; }
.tags { display: flex; flex-wrap: wrap; gap: 8px; }
.tag { background: var(--bg-blue-muted); color: var(--blue); border-radius: 20px; padding: 4px 14px; font-size: 13px; font-weight: 500; }
```

### 2. Cases Block (required if transcript contains practical cases / кейсы)

**Position: immediately after the Header, before all Sections.** Top border: `border-top: 6px solid var(--sky)`.

Each case must show the situation + a two-column verdict grid:
- ❌ Left (pink) — рефлексивно / неправильно (what most people do instinctively)
- ✅ Right (green) — правильно (what Зорін / the speaker recommends)

```html
<div class="cases-block">
  <div class="cases-title lang-uk">🎬 Вводні кейси від [Speaker]</div>
  <div class="cases-title lang-ru">🎬 Вводные кейсы от [Speaker]</div>
  <div class="cases-desc lang-uk">Реальні ситуації з розбором: як робить більшість і як треба насправді</div>
  <div class="cases-desc lang-ru">Реальные ситуации с разбором: как делает большинство и как надо на самом деле</div>

  <div class="case-item">
    <div class="case-header">
      <div class="case-num">1</div>
      <div class="case-situation">
        <div class="lang-uk">Опис ситуації українською</div>
        <div class="lang-ru">Описание ситуации на русском</div>
      </div>
    </div>
    <div class="verdict-row">
      <div class="verdict-wrong">
        <div class="verdict-label">❌ Рефлексивно / неправильно</div>
        <div class="verdict-text lang-uk">Що робить більшість</div>
        <div class="verdict-text lang-ru">Что делает большинство</div>
      </div>
      <div class="verdict-right">
        <div class="verdict-label">✅ Правильно</div>
        <div class="verdict-text lang-uk">Що треба робити</div>
        <div class="verdict-text lang-ru">Что нужно делать</div>
      </div>
    </div>
  </div>
</div>
```

CSS:
```css
.cases-block { background: var(--bg-card); border-radius: var(--radius); padding: 28px; margin-bottom: 20px; box-shadow: var(--shadow); border-top: 6px solid var(--sky); }
.cases-title { font-size: 18px; font-weight: 700; color: var(--dark); margin-bottom: 6px; }
.cases-desc { font-size: 14px; color: var(--grey); margin-bottom: 24px; }
.case-item { border-bottom: 1px solid #f3f4f6; padding: 20px 0; }
.case-item:last-child { border-bottom: none; padding-bottom: 0; }
.case-header { display: flex; align-items: flex-start; gap: 12px; margin-bottom: 14px; }
.case-num { width: 28px; height: 28px; border-radius: 50%; background: var(--sky); color: white; font-weight: 700; font-size: 13px; display: flex; align-items: center; justify-content: center; flex-shrink: 0; margin-top: 1px; }
.case-situation { font-size: 14px; font-weight: 500; color: var(--dark); line-height: 1.5; }
.verdict-row { display: grid; grid-template-columns: 1fr 1fr; gap: 10px; margin-top: 12px; }
.verdict-wrong { background: var(--bg-pink-muted); border-left: 4px solid var(--pink); border-radius: 10px; padding: 12px 14px; }
.verdict-right { background: var(--bg-green-muted); border-left: 4px solid var(--green); border-radius: 10px; padding: 12px 14px; }
.verdict-label { font-size: 11px; font-weight: 700; text-transform: uppercase; letter-spacing: 0.5px; margin-bottom: 6px; }
.verdict-wrong .verdict-label { color: var(--pink); }
.verdict-right .verdict-label { color: var(--green); }
.verdict-text { font-size: 13px; color: var(--grey); line-height: 1.5; }
.verdict-text strong { color: var(--dark); }
@media (max-width: 600px) { .verdict-row { grid-template-columns: 1fr; } }
```

---

### 3. Sections (5–8 per konspekt)

Each section = one semantic theme from the transcript.

```html
<section class="section">
  <div class="section-header" onclick="toggleSection(this)">
    <div class="section-icon">EMOJI</div>
    <h2 class="section-title">
      <span class="lang-uk">TITLE UA</span>
      <span class="lang-ru">TITLE RU</span>
    </h2>
    <div class="section-toggle">
      <svg width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2">
        <polyline points="6 9 12 15 18 9"></polyline>
      </svg>
    </div>
  </div>
  <div class="section-content">
    <div class="lang-uk">...full Ukrainian content...</div>
    <div class="lang-ru">...full Russian content...</div>
  </div>
</section>
```

**Inside each lang block, use these elements:**

`<p>` — body text, ideas paraphrased. `<strong>` for key thoughts.

`<ul>` / `<ol>` — steps, principles, examples.

`.box-pink` — warnings, mistakes, traps:
```html
<div class="box-pink"><h4>⚡ Title</h4><p>Text</p></div>
```

`.box-blue` — insights, tips, lifehacks:
```html
<div class="box-blue"><h4>💎 Title</h4><p>Text</p></div>
```

`.quote` — verbatim quotes from the speaker:
```html
<div class="quote">«Quote text»<div class="quote-author">— Speaker name</div></div>
```

`.stats` — numbers/facts if present in material:
```html
<div class="stats"><div class="stat-card"><div class="stat-val">42%</div><div class="stat-lbl">Description</div></div></div>
```

CSS for sections:
```css
.section { background: var(--bg-card); border-radius: var(--radius); margin-bottom: 16px; box-shadow: var(--shadow); overflow: hidden; }
.section-header { display: flex; align-items: center; gap: 14px; padding: 20px 24px; cursor: pointer; user-select: none; transition: background 0.2s; }
.section-header:hover { background: #f8faff; }
.section-icon { font-size: 22px; flex-shrink: 0; }
.section-title { font-size: 16px; font-weight: 700; color: var(--dark); flex: 1; }
.section-toggle { color: var(--grey); transition: transform 0.3s; flex-shrink: 0; }
.section.collapsed .section-toggle { transform: rotate(-90deg); }
.section-content { padding: 0 24px 24px; overflow: hidden; transition: max-height 0.35s ease; }
.section.collapsed .section-content { max-height: 0 !important; padding-bottom: 0; }
p { margin-bottom: 14px; color: var(--grey); }
p:last-child { margin-bottom: 0; }
strong { color: var(--dark); font-weight: 500; }
ul, ol { padding-left: 22px; margin-bottom: 14px; }
li { margin-bottom: 8px; }
.box-pink { background: var(--bg-pink-muted); border-left: 4px solid var(--pink); border-radius: 10px; padding: 16px 18px; margin: 16px 0; }
.box-pink h4 { color: var(--pink); font-size: 14px; margin-bottom: 8px; }
.box-blue { background: var(--bg-blue-muted); border-left: 4px solid var(--blue); border-radius: 10px; padding: 16px 18px; margin: 16px 0; }
.box-blue h4 { color: var(--blue); font-size: 14px; margin-bottom: 8px; }
.quote { border-left: 4px solid var(--sky); background: #f0faff; border-radius: 10px; padding: 16px 18px; margin: 16px 0; font-style: italic; color: var(--dark); }
.quote-author { margin-top: 8px; font-size: 13px; color: var(--grey); font-style: normal; }
.stats { display: flex; flex-wrap: wrap; gap: 12px; margin: 16px 0; }
.stat-card { background: var(--bg-body); border-radius: 12px; padding: 16px 20px; text-align: center; flex: 1; min-width: 100px; }
.stat-val { font-size: 28px; font-weight: 700; color: var(--blue); }
.stat-lbl { font-size: 12px; color: var(--grey); margin-top: 4px; }
```

### 3. Q&A Block (required if transcript contains Q&A dialogue)

Extract 4–8 real questions from the transcript with the speaker's actual answers. Place after all sections, before the checklist.

```html
<div class="qa-block">
  <div class="qa-title lang-uk">❓ Запитання та відповіді</div>
  <div class="qa-title lang-ru">❓ Вопросы и ответы</div>
  <div class="qa-desc lang-uk">Найцікавіші питання з Q&A — слова спікера</div>
  <div class="qa-desc lang-ru">Самые интересные вопросы из Q&A — слова спикера</div>

  <div class="qa-item">
    <div class="qa-q">
      <span class="qa-icon">🙋</span>
      <div class="qa-q-text">
        <div class="lang-uk">Питання українською</div>
        <div class="lang-ru">Вопрос на русском</div>
      </div>
    </div>
    <div class="qa-a">
      <span class="qa-icon">💬</span>
      <div class="qa-a-text">
        <div class="lang-uk">Відповідь спікера українською</div>
        <div class="lang-ru">Ответ спикера на русском</div>
      </div>
    </div>
  </div>
</div>
```

CSS:
```css
.qa-block { background: var(--bg-card); border-radius: var(--radius); padding: 28px; margin-bottom: 20px; box-shadow: var(--shadow); }
.qa-title { font-size: 18px; font-weight: 700; color: var(--dark); margin-bottom: 6px; }
.qa-desc { font-size: 14px; color: var(--grey); margin-bottom: 20px; }
.qa-item { border-bottom: 1px solid #f3f4f6; padding: 16px 0; }
.qa-item:last-child { border-bottom: none; padding-bottom: 0; }
.qa-q { display: flex; gap: 12px; align-items: flex-start; margin-bottom: 10px; }
.qa-a { display: flex; gap: 12px; align-items: flex-start; background: var(--bg-body); border-radius: 10px; padding: 12px 14px; }
.qa-icon { font-size: 16px; flex-shrink: 0; margin-top: 2px; }
.qa-q-text { font-weight: 500; color: var(--dark); font-size: 14px; line-height: 1.5; }
.qa-a-text { color: var(--grey); font-size: 14px; line-height: 1.6; }
.qa-a-text strong { color: var(--dark); }
```

### 4. Checklist (required, after all sections)

8–12 action items grouped into 2–3 semantic groups.

```html
<div class="checklist">
  <div class="chk-title lang-uk">🚀 Чек-лист впровадження</div>
  <div class="chk-title lang-ru">🚀 Чек-лист внедрения</div>
  <div class="chk-desc lang-uk">Конкретні кроки з уроку — зроби це вже сьогодні</div>
  <div class="chk-desc lang-ru">Конкретные шаги из урока — сделай это уже сегодня</div>

  <div class="chk-group-title">
    <span class="lang-uk">ГРУПА УКРАЇНСЬКОЮ</span>
    <span class="lang-ru">ГРУППА НА РУССКОМ</span>
  </div>

  <div class="chk-item" onclick="toggleCheck(this)">
    <div class="checkbox"></div>
    <div class="chk-text">
      <strong>
        <span class="lang-uk">Дія українською</span>
        <span class="lang-ru">Действие на русском</span>
      </strong>
      <span class="lang-uk">Пояснення</span>
      <span class="lang-ru">Пояснение</span>
    </div>
  </div>
</div>
```

CSS:
```css
.checklist { background: var(--bg-card); border-radius: var(--radius); padding: 28px; margin-bottom: 20px; box-shadow: var(--shadow); }
.chk-title { font-size: 18px; font-weight: 700; color: var(--dark); margin-bottom: 6px; }
.chk-desc { font-size: 14px; color: var(--grey); margin-bottom: 20px; }
.chk-group-title { font-size: 11px; font-weight: 700; color: var(--blue); text-transform: uppercase; letter-spacing: 1px; margin: 20px 0 10px; padding-bottom: 6px; border-bottom: 1px solid var(--bg-blue-muted); }
.chk-item { display: flex; align-items: flex-start; gap: 14px; padding: 12px 0; border-bottom: 1px solid #f3f4f6; cursor: pointer; }
.chk-item:last-child { border-bottom: none; }
.checkbox { width: 22px; height: 22px; border: 2px solid var(--bg-blue-muted); border-radius: 6px; flex-shrink: 0; margin-top: 2px; transition: all 0.2s; background: white; background-repeat: no-repeat; background-position: center; }
.chk-item.checked .checkbox { background-color: var(--blue); border-color: var(--blue); background-image: url("data:image/svg+xml,%3Csvg width='14' height='14' viewBox='0 0 14 14' fill='none' xmlns='http://www.w3.org/2000/svg'%3E%3Cpath d='M2 7L5.5 10.5L12 4' stroke='white' stroke-width='2' stroke-linecap='round' stroke-linejoin='round'/%3E%3C/svg%3E"); }
.chk-item.checked .chk-text { opacity: 0.45; text-decoration: line-through; }
.chk-text { flex: 1; }
.chk-text strong { display: block; color: var(--dark); font-weight: 500; margin-bottom: 2px; }
.chk-text span { font-size: 13px; color: var(--grey); }
```

### 5. Sources Block (required if speaker mentions ANY sources)

Extract ALL sources mentioned by the speaker: books, articles, YouTube channels/videos, tools, websites, courses, people, studies, podcasts, etc. Place after Checklist, before CTA Banner.

**Trigger**: Any mention of a book title, author, URL, tool name, channel, study, or "рекомендую почитати/послухати/подивитися" → this block is required.

```html
<div class="sources-block">
  <div class="sources-title lang-uk">📚 Джерела та рекомендації</div>
  <div class="sources-title lang-ru">📚 Источники и рекомендации</div>
  <div class="sources-desc lang-uk">Все що згадав спікер — книги, інструменти, канали, люди</div>
  <div class="sources-desc lang-ru">Всё что упомянул спикер — книги, инструменты, каналы, люди</div>

  <div class="source-item">
    <div class="source-type">📖 Книга</div>
    <div class="source-body">
      <div class="source-name">
        <span class="lang-uk">Назва книги</span>
        <span class="lang-ru">Название книги</span>
      </div>
      <div class="source-author lang-uk">Автор · контекст згадки</div>
      <div class="source-author lang-ru">Автор · контекст упоминания</div>
    </div>
  </div>
</div>
```

**Source type labels** (use the most fitting):
- 📖 Книга / Книга
- 🎥 YouTube / YouTube
- 🛠 Інструмент / Инструмент
- 🌐 Сайт / Сайт
- 🎙 Подкаст / Подкаст
- 📄 Стаття / Статья
- 👤 Особа / Персона
- 🎓 Курс / Курс

If the speaker provided a URL or enough info to identify the source, include it as a clickable link on the source name.

CSS:
```css
.sources-block { background: var(--bg-card); border-radius: var(--radius); padding: 28px; margin-bottom: 20px; box-shadow: var(--shadow); border-top: 6px solid var(--green); }
.sources-title { font-size: 18px; font-weight: 700; color: var(--dark); margin-bottom: 6px; }
.sources-desc { font-size: 14px; color: var(--grey); margin-bottom: 20px; }
.source-item { display: flex; align-items: flex-start; gap: 14px; padding: 14px 0; border-bottom: 1px solid #f3f4f6; }
.source-item:last-child { border-bottom: none; padding-bottom: 0; }
.source-type { font-size: 12px; font-weight: 700; color: var(--green); background: var(--bg-green-muted); border-radius: 8px; padding: 4px 10px; white-space: nowrap; flex-shrink: 0; margin-top: 2px; }
.source-body { flex: 1; }
.source-name { font-size: 15px; font-weight: 500; color: var(--dark); margin-bottom: 4px; }
.source-name a { color: var(--blue); text-decoration: none; }
.source-name a:hover { text-decoration: underline; }
.source-author { font-size: 13px; color: var(--grey); line-height: 1.5; }
```

---

### 6. CTA Banner (required, placed BEFORE footer)

Dark gradient banner inviting readers to request their own konspekt. Always include Telegram link as primary CTA.

```html
<div class="cta-banner">
  <div class="cta-emoji">📩</div>
  <div class="cta-text lang-uk">Хочеш собі такий же конспект?</div>
  <div class="cta-text lang-ru">Хочешь себе такой же конспект?</div>
  <div class="cta-sub lang-uk">Пишіть — зроблю конспект з будь-якого курсу, лекції або запису</div>
  <div class="cta-sub lang-ru">Пишите — сделаю конспект из любого курса, лекции или записи</div>
  <div class="cta-links">
    <a href="https://t.me/tetervak" class="cta-link primary">✈️ @tetervak</a>
    <a href="https://wa.me/491605193224" class="cta-link">📱 WhatsApp</a>
    <a href="https://instagram.com/tetervak" class="cta-link">📸 Instagram</a>
  </div>
</div>
```

CSS:
```css
.cta-banner { background: linear-gradient(135deg, #101827 0%, #1e3a5f 100%); border-radius: var(--radius); padding: 28px 32px; margin-bottom: 16px; box-shadow: var(--shadow); text-align: center; }
.cta-emoji { font-size: 32px; margin-bottom: 12px; }
.cta-text { font-size: 18px; font-weight: 700; color: white; margin-bottom: 8px; line-height: 1.4; }
.cta-sub { font-size: 14px; color: rgba(255,255,255,0.65); margin-bottom: 20px; line-height: 1.5; }
.cta-links { display: flex; flex-wrap: wrap; gap: 10px; justify-content: center; }
.cta-link { background: white; border-radius: 10px; padding: 10px 20px; font-size: 14px; font-weight: 500; color: var(--dark); text-decoration: none; transition: all 0.2s; }
.cta-link:hover { background: var(--bg-blue-muted); color: var(--blue); }
.cta-link.primary { background: var(--blue); color: white; }
.cta-link.primary:hover { background: #1a50c8; }
```

---

### 6. Footer (required, last element)

```html
<div class="footer-contact">
  <img class="contact-avatar" src="https://raw.githubusercontent.com/tetervak80-max/tdlife-konspekt/main/photo.jpg" alt="Дмитро Тетервак">
  <div class="contact-name">Дмитро Тетервак</div>
  <div class="contact-desc">
    <span class="lang-uk">Ментор · Консультант · Фасилітатор мастермайндів</span>
    <span class="lang-ru">Ментор · Консультант · Фасилитатор мастермайндов</span>
  </div>
  <div class="contact-links">
    <a href="https://wa.me/491605193224">📱 +49 160 519 32 24</a>
    <a href="https://t.me/tetervak">✈️ @tetervak</a>
    <a href="https://instagram.com/tetervak">📸 @tetervak</a>
    <a href="https://t.me/konspekt_tdlife_bot">🤖 @konspekt_tdlife_bot</a>
  </div>
  <div class="footer-author">
    <span class="lang-uk">Конспект згенеровано асистентом <strong>Дмитра Тетервака</strong></span>
    <span class="lang-ru">Конспект сгенерирован ассистентом <strong>Дмитрия Тетервака</strong></span>
  </div>
</div>
```

CSS:
```css
.footer-contact { background: var(--bg-card); border-radius: var(--radius); padding: 28px; margin-top: 12px; box-shadow: var(--shadow); text-align: center; }
.contact-avatar { width: 80px; height: 80px; border-radius: 50%; object-fit: cover; object-position: center 15%; display: block; margin: 0 auto 12px; border: 3px solid var(--bg-blue-muted); }
.contact-name { font-size: 18px; font-weight: 700; color: var(--dark); }
.contact-desc { font-size: 13px; color: var(--grey); margin: 6px 0 16px; line-height: 1.6; }
.contact-links { display: flex; flex-wrap: wrap; gap: 10px; justify-content: center; }
.contact-links a { background: var(--bg-body); border-radius: 8px; padding: 7px 14px; font-size: 13px; color: var(--dark); text-decoration: none; border: 1px solid #e5e7eb; transition: all 0.2s; }
.contact-links a:hover { border-color: var(--blue); color: var(--blue); }
.footer-author { margin-top: 20px; font-size: 13px; color: var(--grey); }
.footer-author strong { color: var(--blue); }
```

---

## JavaScript (before `</body>`)

```html
<script>
function setLang(lang) {
  document.body.className = lang;
  document.querySelectorAll('.lang-btn').forEach(btn => {
    btn.classList.toggle('active', btn.dataset.lang === lang);
  });
  document.querySelectorAll('.section:not(.collapsed) .section-content').forEach(c => {
    c.style.maxHeight = c.scrollHeight + 'px';
  });
  localStorage.setItem('tdlife-lang', lang);
}
function toggleSection(header) {
  const section = header.parentElement;
  const content = section.querySelector('.section-content');
  section.classList.toggle('collapsed');
  if (!section.classList.contains('collapsed')) {
    content.style.maxHeight = content.scrollHeight + 'px';
  }
}
function toggleCheck(item) { item.classList.toggle('checked'); }
const savedLang = localStorage.getItem('tdlife-lang') || 'uk';
setLang(savedLang);
document.querySelectorAll('.section-content').forEach(c => {
  c.style.maxHeight = c.scrollHeight + 'px';
});
</script>
```

---

## Responsive (add to `<style>`)

```css
@media (max-width: 600px) {
  body { padding: 12px; }
  .header { padding: 20px; }
  .header-title { font-size: 20px; }
  .section-header { padding: 16px; }
  .section-content { padding: 0 16px 20px; }
  .lang-switcher { flex-direction: column; align-items: flex-start; gap: 10px; }
  .stats { flex-direction: column; }
}
```

---

## Content Rules

**Page order (strict):**
1. Language Switcher
2. Header
3. Cases Block (if transcript has practical cases/кейсы — required, goes here)
4. Sections (5–8 thematic sections)
5. Q&A Block (if transcript has Q&A)
6. Checklist
7. Sources Block (if speaker mentioned ANY sources — books, tools, channels, people, etc.)
8. CTA Banner (always required)
9. Footer (always required, last)

---

1. **Keep speaker's voice** — preserve their examples, jokes, metaphors. Don't sanitize into Wikipedia style.
   Rule: keep exact numbers, named tools, named people, named mistakes the speaker mentioned.
   ✗ "Він покращив показники сайту" — sanitized, no data
   ✓ "За 15 ітерацій Lighthouse score виріс з 83 до 100 — агент сам прибрав React"
2. **Quotes** — copy the most vivid phrases verbatim. Attribute to speaker if name is known.
3. **box-pink** = mistakes and dangers. **box-blue** = insights and lifehacks.
4. **Tags** — 5-7 tags max. Each = a unique term spoken in the video (method, tool, person, framework, specific number).
   Formula: #[ConceptFromVideo] — would break if the video didn't exist?
   BANNED words in tags: AI, ШІ, Business, Marketing, Growth, Product, Tool, Learning, Productivity, Strategy
   ✗ #AIAgent, #MetricOptimization, #IterativeAI — these apply to 1000+ videos
   ✓ #AutoResearch, #KarpatyPattern, #LighthouseScore83to100, #programMD, #trainPY — only THIS video
5. **Section emojis** — semantic: 🧠 psychology, 💰 money, 🚫 mistakes, 📊 analytics, 🎯 goals, etc.
6. **Checklist** — each item must answer: WHAT exactly + WHERE (tool/file/platform) + first action today.
   Formula: "[Verb] [specific object] in/via [tool/file/platform]"
   ✗ "Визнач цільову аудиторію" — vague, no entry point
   ✓ "Відкрий Notion → створи таблицю 'Аудиторії' → впиши 3 сегменти з болями"
   ✗ "Налаштуй воронку" — no tool, no first step
   ✓ "Зайди в ManyChat → Flow Builder → додай 'Welcome' тригер з кнопками сегментації"
7. **Reading time** — word count ÷ 200, round up.
8. **Self-contained file** — all CSS in `<style>`, all JS in `<script>`, Google Fonts via CDN link only.

---

## Output

Save the HTML file to the user's current working directory with a descriptive filename.
Then tell the user the filename and the command to open it:
```
open "filename.html"
```
