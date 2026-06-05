---
name: dreamkas-ds-frontend
description: Use this skill when integrating Dreamkas Design System (rev 2 или новее) в любой веб-проект (React/Vite/Tailwind, Vue, чистый HTML и т.п.), при переписывании страниц на DS-классы, при дебаге визуальных регрессий после подключения ДС, или при добавлении новых компонентов поверх ДС-токенов. Скилл содержит правила, обнаруженные на реальной интеграции — каждое из них предотвращает молчаливую поломку, которую сложно отдиагностировать постфактум.
---

# Dreamkas Design System — frontend developer skill

Это не учебник по дизайну. Это набор правил, без которых ты потратишь часы на отладку «почему всё выглядит не так». Каждое правило — из реального кейса интеграции ДС rev 2 в React 19 + Vite + Tailwind v4 проект.

---

## TL;DR — пять правил, которые экономят день

1. **DS-импорты подключай в `@layer dreamkas { ... }` либо клади свои override'ы ВНЕ `@layer`** — иначе они проиграют DS по приоритету Cascading Layers.
2. **НИКОГДА не делай `@theme { --spacing-N: var(--space-N) }`** — Tailwind считает в rem, DS в px. Имена совпадают, значения отличаются в 4×. Сломаешь всё.
3. **Для фона страницы используй `--bg-primary`** (не `--bg-surface`, такого токена в ДС нет).
4. **5 компонентов «универсальные»** (avatar, badge, card, form-field, tooltip) — есть только в `Web/`, активны на всех платформах. Остальные 14 — платформо-зависимые, скоупь по media-query.
5. **App Shell ЕСТЬ в каноне** — `<div class="app"><aside class="sb"/><div class="app-main"><header class="tb"/><div class="app-content"/></div></div>` с grid. Канон похоронен в `preview/organism-app-shell-web.html` — читай его, не изобретай свой.

Дальше — подробно.

---

## Когда использовать этот скилл

- Подключаешь Dreamkas DS в новый или существующий проект.
- Переверстываешь страницу на DS-классы.
- Видишь «уехавший» лейаут после подключения ДС (паддинги схлопнулись, шрифт не тот, что-то торчит).
- Делаешь свой компонент, который должен выглядеть как часть ДС.
- Обновляешь ДС с одной ревизии на другую.

---

## Правило 1 — Cascading Layers, или почему твой override проигрывает

### Симптом

Написал в своём CSS:
```css
@layer base {
  body { background: var(--bg-primary); }
}
```
Открыл DevTools — фон белый по умолчанию, твоё правило перечёркнуто. Вроде всё верно, но не работает.

### Причина

CSS-файлы ДС подключены **вне `@layer`** через `@import "..."` без обёртки. По спеке Cascading Layers правила вне любого слоя имеют **более высокий приоритет**, чем правила внутри `@layer base` (даже если @import шёл раньше). Tailwind v4 кладёт свой preflight в @layer base — твои правила тоже там — и DS перекрывает их.

### Что делать

**Вариант A — твои override'ы вне слоёв:** держи DS-зависимые правила (`body { background: ... }`, `html { font-family: ... }`, layout-классы App Shell) на top-level CSS, не в `@layer`. Они идут после DS-импортов по source order и выигрывают.

```css
@import "./design-system/index.css";   /* unlayered */
@import "tailwindcss";                   /* preflight в @layer base */

/* НЕ в @layer — иначе проиграешь DS */
html { font-family: var(--text-body-family); }
body { background: var(--bg-primary); color: var(--text-primary); }
```

**Вариант B (предпочтительно) — обернуть DS в свой слой:** в агрегаторе DS оберни всё в `@layer dreamkas { ... }`. Тогда DS становится слоем, твои override'ы в `@layer overrides` (или любом более позднем слое) выигрывают идиоматически.

```css
@layer base, dreamkas, overrides;

@import "tailwindcss";

@layer dreamkas {
  @import "./design-system/foundations/color/semantic.css";
  /* ... */
}

@layer overrides {
  body { background: var(--bg-primary); }
}
```

### Red flag

Если ты пишешь override в `@layer base` и он не работает — это правило 1. Вынеси либо обёрни DS.

---

## Правило 2 — НЕ алиасить `--spacing-N` на `--space-N`

### Симптом

После «прокидывания DS-spacing в Tailwind theme» все паддинги, отступы и gap'ы в существующем коде стали примерно в **4 раза меньше**. Карточки склеились, чарты наезжают на лейблы. Никаких ошибок в консоли.

### Причина

```
Tailwind v4: --spacing-4 = 1rem    = 16px   (шаг 0.25rem)
Dreamkas DS: --space-4   = 4px              (семантический pixel-шаг)
```

Имена совпадают, значения отличаются в 4×. Если написать:

```css
@theme {
  --spacing-4: var(--space-4);   /* ← НЕЛЬЗЯ */
}
```

то `p-4` (был 16px) станет 4px, `p-8` (был 32px) станет 8px, и так далее везде. Это catastrophic, но без CSS-ошибок.

### Что делать

**Не делай моста между Tailwind-spacing и DS-spacing.** Они — разные шкалы по семантике:

- **Tailwind spacing** оставь как есть для всего, что использует Tailwind-утилиты (`p-4`, `gap-6`, `m-2`). Это твой layout-glue.
- **DS spacing** (`var(--space-4)`, `var(--space-8)`...) используется ВНУТРИ DS-классов (`.btn`, `.sb`, `.tb` сами читают `var(--space-*)`). Не трогай Tailwind theme.

Если ты хочешь использовать DS-spacing в JSX напрямую (не через DS-классы) — пиши `style={{ padding: 'var(--space-16)' }}` или `className="my-block"` + CSS `.my-block { padding: var(--space-16); }`. Но не через Tailwind утилиты.

### Red flag

Любой `@theme { --spacing-* }` в проекте. Открыл — закрыл.

---

## Правило 3 — Имена токенов в DS: помни «`--bg-primary`», а не «`--bg-surface`»

### Симптом

Написал `body { background: var(--bg-surface); }` — фон стал прозрачным (`rgba(0,0,0,0)`). Никаких ошибок.

### Причина

В DS rev 2 нет токена `--bg-surface`. Есть:

- `--bg-primary` — **основной фон страницы** (то, что ты, вероятно, хочешь)
- `--bg-secondary` — дополнительный фон (для контейнеров уровнем выше)
- `--surface-default` — поверхность карточки/листа
- `--surface-raised` — приподнятая поверхность (попап, модал)
- `--surface-sunken` — утоплённая поверхность (input bg, fieldset)

CSS `var(--name)` без второго аргумента-фолбэка молча резолвится в `initial`, если переменная не объявлена. Поэтому ошибки нет, а результат пустой.

### Что делать

- Для фона страницы (`body`, `.app-bare`) — `var(--bg-primary)`.
- Для каркаса App Shell (`.app`) — `var(--bg-secondary)` (как в каноне).
- Для карточек, попапов — `var(--surface-default)`, `var(--surface-raised)` и т.д.
- Перед использованием токена — `grep --bg- design-system/foundations/color/semantic.css` или открой semantic.css и убедись, что переменная объявлена.

### Red flag

Прозрачный фон, белый текст на белом, элемент «потерял заливку». Скорее всего: токен в `var()` не существует.

---

## Правило 4 — Universal vs Platform-scoped компоненты

### Симптом

На tablet/mobile-ширине окна `.avatar`, `.badge`, `.card`, `.field`, `.tooltip` перестали стилизоваться. На web — всё ок.

### Причина

В DS rev 2 компоненты не симметричны по платформам:

| Компонент | Web | Tablet | Mobile |
|---|---|---|---|
| Button, Input, Select, Selection, Modal, Sidebar, Topbar, Alert, Chart, Pagination, Table, TableToolbar, Tabs, Toast (**14 шт**) | ✓ | ✓ | ✓ |
| Avatar, Badge, Card, FormField, Tooltip (**5 шт**) | ✓ | ✗ | ✗ |

5 «универсальных» компонентов **есть только в `Web/`**, потому что их размер един под все платформы (тач-таргет не различается). Если ты в агрегаторе скоупишь по media-query **все** компоненты подряд, на tablet/mobile эти 5 окажутся без CSS.

### Что делать

В агрегаторе раздели на два блока:

```css
/* Универсальные — всегда активны, из web/ */
@import "./web/Foundations/Component tokens/avatar/tokens.css";
@import "./web/Components/Atoms/Avatar/avatar.css";
/* badge, card, form-field, tooltip аналогично */

/* Platform-scoped — скоупим */
@import "./mobile/Foundations/Component tokens/button/tokens.css" (max-width: 759px);
@import "./mobile/Components/Atoms/Button/button.css"               (max-width: 759px);
/* ... остальные 14 для mobile, и аналогично для tablet и web ... */
```

Список платформо-зависимых: `alert, button, chart, input, modal, pagination, select, selection, sidebar, table, table-toolbar, tabs, toast, topbar`.

Список универсальных: `avatar, badge, card, form-field, tooltip`.

### Red flag

Аватары без рамок на планшете, бейджи без фона, инпуты с лейблами но без обводки.

---

## Правило 5 — App Shell живёт в каноне, читай preview

### Симптом

«А почему вы не используете App Shell из ДС?» — спрашивает пользователь, и ты говоришь «потому что нет такого». А он есть.

### Причина

В `Web/Components/Organisms/` лежат `Sidebar/`, `Topbar/`, `Modal/`. App Shell как изолированный CSS-файл там нет. Канон живёт **inline-`<style>` файла `preview/organism-app-shell-web.html`**, строки 42–73:

```css
.app {
  display: grid;
  grid-template-columns: var(--sb-width) 1fr;
  grid-template-rows: 100%;
  background: var(--bg-secondary);
  overflow: hidden;
}
.app-main    { display: flex; flex-direction: column; min-width: 0; }
.app-content { flex: 1; overflow-y: auto; padding: 20px; }
```

И HTML-структура:

```html
<div class="app">
  <aside class="sb">...</aside>
  <div class="app-main">
    <header class="tb">...</header>
    <div class="app-content">...</div>
  </div>
</div>
```

### Что делать

- **Имена классов как в каноне:** `.app`, `.app-main`, `.app-content`. Никаких `.app-shell` / `.layout-root` / `.shell-wrapper`.
- **Структура как в каноне:** Sidebar — прямой ребёнок `.app`. Topbar — прямой ребёнок `.app-main`, перед `.app-content`. Контент страницы — в `.app-content`.
- **Layout — grid, не flex:** именно `display: grid; grid-template-columns: ... 1fr`. Это даёт Sidebar высоту = высоте окна (его футер прижимается к низу естественно).
- **Колонка под Sidebar — `auto 1fr`, не `var(--sb-width) 1fr`:** иначе при `.sb--collapsed` (Sidebar становится узким) grid-колонка остаётся широкой, появляется пустое место между Sidebar и контентом.

### Минимальная реализация

```css
.app {
  display: grid;
  grid-template-columns: auto 1fr;          /* ← auto, не var(--sb-width) */
  grid-template-rows: 100%;
  min-height: 100vh;
  background: var(--bg-secondary);
  overflow: hidden;
}
.app-main    { display: flex; flex-direction: column; min-width: 0; }
.app-content { flex: 1; overflow-y: auto; overflow-x: hidden; }

/* < 760: Sidebar скрыт в потоке, открывается оверлеем по бургеру */
@media (max-width: 759px) {
  .app > .sb:not(.sb--collapsed) { display: none; }
}
```

### Red flag

`.app-shell`, `.layout-main`, твои собственные имена для каркаса. Если они — у тебя не канон, и при следующем апдейте ДС структура разъедется.

---

## Sidebar — каноническая структура

```html
<aside class="sb">                        <!-- или .sb sb--collapsed -->
  <div class="sb-logo">
    <div class="sb-logo-mark">D</div>
    <span class="sb-logo-text">Бренд</span>
  </div>
  <nav class="sb-nav">
    <div class="sb-group">Основное</div>         <!-- опц. -->
    <a class="sb-item is-active">
      <span class="sb-item-icon"><svg/></span>
      <span class="sb-item-label">Товары</span>
      <span class="sb-item-badge">94</span>      <!-- опц. -->
    </a>
    <a class="sb-item">...</a>
    <!-- ... -->
  </nav>
  <div class="sb-footer">
    <div class="sb-profile">
      <span class="avatar avatar--md">АП</span>
      <div class="sb-profile-info">
        <div class="sb-profile-name">Анна Петрова</div>
        <div class="sb-profile-role">Администратор</div>
      </div>
    </div>
  </div>
</aside>
```

**Состояния пункта:** `is-active`, `is-disabled`. Tabbing, клавиатура — твоя ответственность (DS даёт визуал, не behavior).

**Свёрнут:** `.sb sb--collapsed` — ширина становится `var(--sb-width-collapsed)`, скрываются `.sb-item-label`, `.sb-item-badge`, `.sb-logo-text`, `.sb-profile-info`.

**Mobile drawer:** на `< 760px` Sidebar скрыт из потока. Открывается через `.sb-overlay` оверлеем — твоей React-логикой (обработчик клика на бургер в Topbar → toggle state → рендеришь `.sb-overlay` поверх).

---

## Topbar — каноническая структура

```html
<header class="tb">
  <button class="tb-icon-btn"><svg/></button>          <!-- бургер -->
  <div class="tb-title">Заголовок страницы</div>
  <div class="tb-search">                              <!-- опц. -->
    <svg/>
    <input type="text" placeholder="Поиск..."/>
    <kbd>/</kbd>                                       <!-- опц. -->
  </div>
  <div class="tb-spacer"></div>
  <span class="tb-shift is-open">Смена открыта</span>  <!-- опц. -->
  <span class="tb-status tb-status--online">          <!-- опц. -->
    <svg/>Онлайн
  </span>
  <button class="tb-icon-btn tb-bell">                 <!-- опц. -->
    <svg/>
    <span class="tb-bell-count">3</span>
  </button>
  <div class="tb-sep"></div>                           <!-- опц. -->
  <div class="tb-profile">
    <span class="avatar avatar--sm">АП</span>
    <div class="tb-profile-info">
      <div class="tb-profile-name">Анна Петрова</div>
      <div class="tb-profile-role">Администратор</div>
    </div>
    <span class="tb-profile-chevron"><svg/></span>
  </div>
</header>
<div class="tb-banner">                                <!-- опц., под Topbar -->
  <svg/>
  <span>Сообщение / алерт / индикатор</span>
</div>
```

**Status-варианты:** `.tb-status--online`, `.tb-status--offline`, `.tb-status--sync`.
**Banner-варианты:** свой `bg` и иконка для разных уровней (warning / error / info).

---

## Setup в новом проекте — пошагово

### 1. Скопируй ДС в проект

```
src/design-system/    (или просто design-system/ на корне)
  foundations/        ← Foundations целиком
  web/                ← Web/* целиком (Foundations/Component tokens + Components)
  tablet/             ← Tablet/* целиком
  mobile/             ← Mobile/* целиком
  index.css           ← агрегатор (см. ниже)
  VERSION.md          ← запиши дату копирования и ревизию
```

ДС-файлы не редактируй. Все правки — в `design-system/index.css` или вне `design-system/`.

### 2. Напиши агрегатор `design-system/index.css`

Минимальная рабочая версия:

```css
/* === Foundations (always-on) === */
@import "./foundations/color/primitives.css";
@import "./foundations/color/semantic.css";
@import "./foundations/typography/fonts.css";
@import "./foundations/typography/primitives.css";
@import "./foundations/typography/semantic.css";
@import "./foundations/spacing/primitives.css";
@import "./foundations/spacing/semantic.css";
@import "./foundations/shadow/primitives.css";
@import "./foundations/shadow/semantic.css";
@import "./foundations/motion/primitives.css";
@import "./foundations/motion/semantic.css";
@import "./foundations/z-index/primitives.css";
@import "./foundations/z-index/semantic.css";

/* === Universal components (always-on, from web/) === */
@import "./web/Foundations/Component tokens/avatar/tokens.css";
@import "./web/Components/Atoms/Avatar/avatar.css";
@import "./web/Foundations/Component tokens/badge/tokens.css";
@import "./web/Components/Atoms/Badge/badge.css";
@import "./web/Foundations/Component tokens/tooltip/tokens.css";
@import "./web/Components/Atoms/Tooltip/tooltip.css";
@import "./web/Foundations/Component tokens/card/tokens.css";
@import "./web/Components/Molecules/Card/card.css";
@import "./web/Foundations/Component tokens/form-field/tokens.css";
@import "./web/Components/Molecules/FormField/form-field.css";

/* === Platform-scoped (mobile, tablet, web) === */
/* 14 компонентов × 3 платформы × 2 файла = 84 строки.
   Не списываю руками — используй generator-скрипт (см. ниже). */
```

Генератор `@import`-блока для platform-scoped:

```bash
PLATFORMS=(mobile tablet web)
MEDIA=(
  "(max-width: 759px)"
  "(min-width: 760px) and (max-width: 1259px)"
  "(min-width: 1260px)"
)
for i in 0 1 2; do
  p="${PLATFORMS[$i]}"
  m="${MEDIA[$i]}"
  echo "/* === $p === */"
  find "design-system/$p/Foundations/Component tokens" -name "tokens.css" 2>/dev/null | sort | while read f; do
    echo "@import \"./${f#design-system/}\" $m;"
  done
  for layer in Atoms Molecules Organisms; do
    find "design-system/$p/Components/$layer" -name "*.css" 2>/dev/null | sort | while read f; do
      echo "@import \"./${f#design-system/}\" $m;"
    done
  done
done
```

**Внимание:** `Tablet/Components/Atoms/Select/` содержит `select.v2.css`, не `select.css` (legacy cache-bust). `find -name "*.css"` это словит — проверь, что в выводе скрипта он есть.

### 3. Корневой `index.css` (или main entry CSS)

```css
@import "./design-system/index.css";   /* DS первым */
@import "tailwindcss";                  /* preflight + утилиты (если используешь) */

/* Override'ы — вне @layer, чтобы выигрывать у DS */
html { font-family: var(--text-body-family); }
body { background: var(--bg-primary); color: var(--text-primary); }

/* App Shell — каноническая структура */
.app {
  display: grid;
  grid-template-columns: auto 1fr;
  grid-template-rows: 100%;
  min-height: 100vh;
  background: var(--bg-secondary);
  overflow: hidden;
}
.app-main    { display: flex; flex-direction: column; min-width: 0; }
.app-content { flex: 1; overflow-y: auto; overflow-x: hidden; }

@media (max-width: 759px) {
  .app > .sb:not(.sb--collapsed) { display: none; }
}
```

### 4. **НЕ делай** `@theme { --spacing-N: var(--space-N) }`

Это правило 2. Не алиасить Tailwind-spacing на DS-spacing. Они в разных единицах.

### 5. Шрифт

DS даёт Roboto Variable в `foundations/typography/fonts/`. Включается через `foundations/typography/fonts.css`. Удаляй свои `@font-face` для Inter / Roboto / других — DS уже сделал.

В index.html НЕ оставляй inline `<style>` с `font-family: 'Inter'` или похожим — это перебьёт DS с большей специфичностью.

### 6. Проверка цепочки

В консоли браузера после загрузки:

```js
getComputedStyle(document.documentElement).getPropertyValue('--bg-brand-default').trim()
// → "#0097A7" — значит DS-цепочка работает

getComputedStyle(document.body).fontFamily
// → "Dreamkas Sans, ..." — значит шрифт подтянулся
//   Если "Inter, sans-serif" — где-то остался inline @font-face или CSS

getComputedStyle(document.body).backgroundColor
// → "rgb(255, 255, 255)" (для --bg-primary = --color-neutral-0)
//   Если "rgba(0, 0, 0, 0)" — токен не существует (см. правило 3)
```

---

## Список «не делать» (антипаттерны)

| Антипаттерн | Что вместо |
|---|---|
| `@theme { --spacing-4: var(--space-4); }` | Не алиасить Tailwind-spacing. Пользуйся DS-шкалой внутри DS-классов или через прямой `var()` |
| `<div className="bg-blue-500">` для брендового фона | `<div className="btn btn--primary">` или `style={{ background: 'var(--bg-brand-default)' }}` |
| `style={{ color: '#0097A7' }}` сырой HEX | `style={{ color: 'var(--bg-brand-default)' }}` или DS-класс |
| `<aside className="sidebar">`, свои имена | `<aside className="sb">` — канон |
| `display: flex` для каркаса App Shell | `display: grid; grid-template-columns: auto 1fr` |
| `@layer base { body { background: ... } }` | Без `@layer` (правило 1) или в свой `@layer overrides` |
| Подключать только `Web/` компоненты на mobile-ширине | Скоупить platform-зависимые по media-query, оставлять universal always-on |
| Самостоятельно искать `select.css` в Tablet | Использовать `find -name "*.css"` или сразу `select.v2.css` |

---

## Что делать, если ДС обновился

1. Обнови `design-system/VERSION.md` — новая ревизия + дата.
2. Сравни старую и новую версии файлов **в `Foundations/`** — если изменились имена токенов (например, добавился `--bg-surface` как алиас на `--bg-primary`), обновляй свой код.
3. Сравни `preview/organism-app-shell-web.html` — если структура каркаса изменилась, обнови `.app`/`.app-main`/`.app-content` правила.
4. Прогон визуальных регрессий (playwright/puppeteer скриншоты ключевых экранов) — лучший способ поймать молчаливые поломки.

---

## Когда ты понимаешь, что ушёл в самопис

**Признаки:**
- Ты пишешь свой CSS, который по структуре похож на DS-канон (`.my-button { background: ... }`).
- Ты используешь Tailwind-классы для цветов/паддингов/типографики в JSX.
- Ты создал свои имена для каркаса (`.layout`, `.shell`, `.page-wrap`).

**Что делать:**
- Открой `Web/Components/<Layer>/<Component>/<component>.css` — там точно есть DS-класс под твою задачу. Используй его.
- Открой `preview/` — там есть demo с правильной разметкой компонента. Скопируй структуру.
- Если ДС реально нет нужного компонента — открой issue в репо ДС, не пиши «временный» свой (он останется навсегда).

---

## Связанное

- `FEEDBACK-2026-06-05.md` — обратная связь от первого реального интегратора (антиштраф), 9 находок с конкретными кейсами и рекомендациями ДС. Полезно прочитать, чтобы понимать «откуда взялись правила в этом скилле».
- `CLAUDE.md` (в корне репо) — контекст роли «Софонов Дизайнер», работающего над самой ДС. Не для frontend-потребителя.
- `README.md` — общий обзор архитектуры ДС.
