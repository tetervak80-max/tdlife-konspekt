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
  --bg-body: #F9FAFB;
  --bg-card: #FFFFFF;
  --bg-blue-muted: #C9D7F5;
  --bg-pink-muted: #F9CCDB;
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
    <button class="lang-btn" data-lang="ru" onclick="setLang('ru')">🇷🇺 Русский</button>
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

### 2. Sections (5–8 per konspekt)

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

### 3. Checklist (required, after all sections)

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

### 4. Footer (required, last element)

```html
<div class="footer-contact">
  <div class="contact-avatar">ДТ</div>
  <div class="contact-name">Дмитро Тетервак</div>
  <div class="contact-desc lang-uk">
    Ментор · Консультант · Фасилітатор мастермайндів<br>
    Допомагає підприємцям вийти з операційки і побудувати систему
  </div>
  <div class="contact-desc lang-ru">
    Ментор · Консультант · Фасилитатор мастермайндов<br>
    Помогает предпринимателям выйти из операционки и построить систему
  </div>
  <div class="contact-links">
    <a href="https://wa.me/491605193224">📱 +49 160 519 32 24</a>
    <a href="https://t.me/tetervak">✈️ @tetervak · +38 067 536 38 02</a>
    <a href="https://instagram.com/tetervak">📸 @tetervak</a>
    <a href="https://tiktok.com/@tetervakdmytro">🎵 @tetervakdmytro</a>
  </div>
  <div class="footer-time">
    <span class="lang-uk">⏱ ~X хв читання</span>
    <span class="lang-ru">⏱ ~X мин чтения</span>
  </div>
  <div class="footer-author">
    <span class="lang-uk">Підготовлено асистентом <strong>Дмитра Тетервака</strong></span>
    <span class="lang-ru">Подготовлено ассистентом <strong>Дмитрия Тетервака</strong></span>
  </div>
</div>
```

CSS:
```css
.footer-contact { background: var(--bg-card); border-radius: var(--radius); padding: 28px; margin-top: 12px; box-shadow: var(--shadow); text-align: center; }
.contact-avatar { width: 52px; height: 52px; border-radius: 50%; background: var(--blue); color: white; font-weight: 700; font-size: 18px; display: flex; align-items: center; justify-content: center; margin: 0 auto 12px; }
.contact-name { font-size: 18px; font-weight: 700; color: var(--dark); }
.contact-desc { font-size: 13px; color: var(--grey); margin: 6px 0 16px; line-height: 1.6; }
.contact-links { display: flex; flex-wrap: wrap; gap: 10px; justify-content: center; }
.contact-links a { background: var(--bg-body); border-radius: 8px; padding: 7px 14px; font-size: 13px; color: var(--dark); text-decoration: none; border: 1px solid #e5e7eb; transition: all 0.2s; }
.contact-links a:hover { border-color: var(--blue); color: var(--blue); }
.footer-time { margin-top: 20px; font-size: 13px; color: var(--grey); }
.footer-author { margin-top: 6px; font-size: 13px; color: var(--grey); }
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

1. **Keep speaker's voice** — preserve their examples, jokes, metaphors. Don't sanitize into Wikipedia style.
2. **Quotes** — copy the most vivid phrases verbatim. Attribute to speaker if name is known.
3. **box-pink** = mistakes and dangers. **box-blue** = insights and lifehacks.
4. **Tags** — specific (#КонтрольВитрат, #ТипиКлієнтів), never generic (#Бізнес).
5. **Section emojis** — semantic: 🧠 psychology, 💰 money, 🚫 mistakes, 📊 analytics, 🎯 goals, etc.
6. **Checklist** — concrete verbs only. "Зроби X" / "Сделай X". No vague items.
7. **Reading time** — word count ÷ 200, round up.
8. **Self-contained file** — all CSS in `<style>`, all JS in `<script>`, Google Fonts via CDN link only.

---

## Output

Save the HTML file to the user's current working directory with a descriptive filename.
Then tell the user the filename and the command to open it:
```
open "filename.html"
```
