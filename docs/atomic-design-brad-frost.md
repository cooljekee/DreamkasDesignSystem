# Atomic Design — Brad Frost (2016)

**Свод нотаций по книге для дизайн-системы Dreamkas.**
ISBN: 978-0-9982966-0-9. 5 глав, ~190 страниц. Канонический референс по методологии atomic design и устройству пром. дизайн-систем.

---

## TL;DR — что взять в работу

1. **Не страницы — система.** Проект скоупится через компоненты, не через количество страниц. «30 000 страниц» = 3 типа контента + 2 лейаута.
2. **5 уровней иерархии:** Atoms → Molecules → Organisms → Templates → Pages. Это **ментальная модель, не пошаговый процесс** — слои существуют одновременно и влияют друг на друга.
3. **Templates vs Pages.** Template — *скелет с FPO-контентом* (1250×703, «37 characters»). Page — *тот же template с реальным контентом* (фото Бейонсе, реальные заголовки). Page нужен, чтобы **проверить устойчивость системы** к реальным данным.
4. **Style Guide = продукт, не артефакт.** Если относиться как к артефакту проекта — окажется в мусорке рядом с PSD. Нужен governance, владельцы, roadmap, бюджет.
5. **Холи-грааль:** один источник правды для патерн-либы и прода. Изменил компонент — обновилось везде.

---

## Глава 1. Designing Systems — почему вообще системы

### Главный сдвиг: «не страницы, а компоненты»

> *"We're not designing pages, we're designing systems of components."* — Stephen Hay

- Метафора «page» пришла из бумаги/документов CERN. Она устарела: 25 лет в индустрии, мульти-девайс реальность, фуллстек подача под кучу контекстов.
- Объём работы определяется **функциональностью и компонентами на странице**, а не количеством страниц.
- Цитата для продажи системы стейкхолдерам: *«30 000 страниц = challenging?»* — нет, это **3 типа контента + 2 лейаута**.

### Модульность пронизывает весь стек

Frost перечисляет, что модульность проникла во всё:
- **Стратегия:** уход от monolithic redesigns каждые 3–8 лет → MVP, итерации.
- **Контент:** content chunks (Karen McGrane, NPR's COPE — Create Once, Publish Everywhere).
- **Код:** OOCSS, SMACSS, BEM, JS-паттерны.
- **Визуальный дизайн:** style tiles (Samantha Warren), element collages (Dan Mall).
- **UI-фреймворки:** Bootstrap, Foundation.

### Bootstrap-критика (важно — обоснование «своей» DS)

Плюсы фреймворков: скорость, тестированность, ком'юнити.
Минусы:
- **«Sci-fi jumpsuit problem»** — все сайты на Bootstrap похожи друг на друга.
- **Bloat** — пользователь грузит неиспользуемые CSS/JS.
- **Naming conventions** чужие (`jumbotron` — не подходит конкретной компании).
- Кастомизация = борьба с фреймворком.

> *"Responsive deliverables should look a lot like fully-functioning Twitter Bootstrap-style systems custom tailored for your clients' needs. […] tiny Bootstraps for every client."* — Dave Rupert

**Вывод:** «Make your own system», а не используй чужой.

### 6 типов style guides

| Тип | Что описывает | Пример |
|---|---|---|
| **Brand identity** | Логотип, палитра, миссия, тон | West Virginia University |
| **Design language** | Философия, принципы, общее направление | Google Material Design |
| **Voice and tone** | Как меняется тон в разных ситуациях | MailChimp Voice & Tone |
| **Writing** | Грамматика, пунктуация, правила копирайта | The Economist, Dalhousie |
| **Code style** | Конвенции HTML/CSS/JS | GitHub Styleguide |
| **Pattern Library (UI)** | **Компоненты — главный фокус книги** | Code for America, Salesforce |

### Выгоды style guide

- Consistency / cohesion.
- Shared vocabulary (унификация имён компонентов в команде).
- Education для стейкхолдеров.
- Empathetic workflow — заставляет думать о системе, а не только о текущей странице.
- Testing — изоляция компонентов упрощает QA.
- Speed (после initial investment).
- In it for the long haul — не выкидывается при редизайне.

### Челленджи

- **Hard sell** — короткосрочное мышление стейкхолдеров.
- **Matter of time** — *"The hard part is building the machine that builds the product."* — Dennis Crowley.
- **Auxiliary projects trap** — не делать DS «когда будет время», встраивать в workflow.
- **Maintenance** без governance = смерть.
- **Audience confusion** — слишком технический → не пользуются.
- **Lack of context** — паттерн без указания где используется бесполезен.
- **Lacking methodology** — большинство pattern-libs = «loosely arranged sprays of modules».

---

## Глава 2. Atomic Design Methodology — главная глава

Аналогия с химией: атомы → молекулы → организмы. Frost проецирует это на UI.

### 5 уровней

| Уровень | Определение | Примеры из книги |
|---|---|---|
| **Atoms** | Базовые HTML-элементы. Дальше не делятся без потери смысла. | label, input, button, иконка, заголовок, цвет, шрифт |
| **Molecules** | Группы атомов как маленький функциональный юнит. **Single responsibility principle** — делают одну вещь. | Search form (label + input + button), block-post, primary nav |
| **Organisms** | Относительно сложные компоненты из молекул/атомов/других организмов. **Distinct sections of an interface.** | Header (logo + nav + search), product grid, footer |
| **Templates** | Page-level объекты. Размещают компоненты в layout. **Show content structure** (FPO-заглушки: 1250×703, «37 characters»). | Homepage template Time Inc. с серыми боксами и указанием размеров |
| **Pages** | Конкретные инстансы templates с **реальным контентом**. Здесь тестируется устойчивость системы. | Homepage Time Inc. с фото Бейонсе и реальными заголовками |

### Ключевые тезисы

> *"Atomic design is not a linear process, but rather a mental model to help us think of our user interfaces as both a cohesive whole and a collection of parts at the same time."*

- **Не процесс «шаг 1–5», а одновременная работа** на всех уровнях.
- **Чем выше уровень — тем конкретнее.** Atoms — абстрактны, pages — финал.
- Templates **отделяют структуру от контента** (skeleton).
- Pages **показывают вариации**: 1 vs 10 items в корзине, заголовок 40 vs 340 символов, admin vs обычный юзер, dashboard для first-time vs returning.

> *"You can create good experiences without knowing the content. What you can't do is create good experiences without knowing your content structure."* — Mark Boulton

### Преимущества методологии

1. **The part and the whole** — одновременно зум-ин на компонент и зум-аут на страницу.
   > *"It is a dance of switching contexts, a pitter-patter pacing across the studio floor."* — Frank Chimero
2. **Clean separation between structure and content** — но они влияют друг на друга.
3. **Иерархия в названиях** (atom < molecule < organism) — лучше плоского `components/modules/elements`. Хотя GE переименовал в «Principles/Basics/Components/Templates/Features/Applications» — и это ОК, лишь бы команде было понятно.

### Atomic Design ≠ web-only

> *"Atomic design has nothing to do with web-specific subjects like CSS or JavaScript architecture."*

Применимо к Instagram, Photoshop, ATM, любому UI. Это про **иерархию интерфейса**, не про технологии.

---

## Глава 3. Tools of the Trade — Pattern Lab

Pattern Lab — open-source SSG-инструмент Frost'а + Dave Olsen + Brian Muenzenmeyer для построения atomic-DS. Не UI-фреймворк, не CMS. Pattern Lab дает:

### Концепт «русских матрёшек»

```
{{> atoms-thumbnail }}     ← атом
↓ включается в
{{> molecules-block-post }} ← молекула
↓ включается в
{{> organisms-header }}    ← организм
↓ включается в
template → page
```

Принцип **DRY** (don't repeat yourself). Изменил атом — обновилось во всех молекулах/организмах/темплейтах автоматически.

### Работа с динамическим контентом

- `data.json` — дефолтные данные (FPO).
- `00-homepage.json` — оверрайды для конкретной страницы.
- **Pseudo-patterns** (`dashboard~admin.json`) — наследует dashboard.json + переопределяет. Меняешь одну переменную `isAdmin: true` — UI меняется радикально (показываются edit/delete на каждой карточке).

### Качества хорошей pattern library (чек-лист, не привязан к Pattern Lab)

- Pattern descriptions + annotations (markdown-файл `pattern-name.md` рядом).
- Code view: HTML, шаблонный код, CSS, JS.
- **Viewport tools** — `ish.` (small-ish/medium-ish/large-ish — рандомные размеры в диапазоне, чтобы не зацикливаться на конкретных девайсах).
- Pattern variations (active/disabled/hover/error/etc).
- Dynamic data (JSON / YAML / Markdown).
- **Lineage** — «этот pattern состоит из X, Y, Z» + «используется в A, B, C». Критично для QA: меняешь pattern — знаешь, что перетестить.

---

## Глава 4. The Atomic Workflow — процесс и люди

> *"The design process is weird and complicated, because people are weird and complicated."* — Mark Boulton

### Как «продать» дизайн-систему

Не «design system saves time», а **«do you like saving time and money? yes/no?»** — переформулировка через язык бизнеса:
- Cohesion → пользователи быстрее осваивают UI → больше конверсий.
- Reuse → быстрее запуск фич.
- Shared vocabulary → меньше митингов и недопониманий.
- Тестирование (cross-browser, a11y, perf) сразу на уровне системы.
- Future-friendly foundation — итерации, A/B-тесты, оптимизации катятся в живую систему.

### Interface Inventory — главное упражнение

**Не контент-инвентаризация, а UI-инвентаризация.** Собрать **скриншоты всех уникальных UI-паттернов** существующего продукта.

**Шаги:**
1. **Round up the troops.** UX, визуальщики, фронты, бэки, копирайтеры, PM, бизнес. Чем больше дисциплин, тем лучше.
2. **Prepare tool.** Google Slides (есть шаблон Frost'а), Keynote, PowerPoint — главное одинаковое для всех.
3. **Screenshot exercise.** 30–90 минут, музыка, по категориям. Каждый ловит **уникальные** инстансы.
4. **Present findings.** По 5–10 минут на категорию. Обсудить нейминг.
5. **Regroup.** Свести в один über-document, показать стейкхолдерам («ужас в твоей UI»), решить что остаётся / уходит / мерджится.

**Категории для инвентаризации:**
Global elements, Navigation, Image types, Icons, Forms, Buttons, Headings, Blocks (touts/cards), Lists, Interactive components, Media, Third-party, Advertising, Messaging (alerts/toasts), Colors, Animation.

**Зачем нужно:**
- Все видят боль (37 разных кнопок).
- Buy-in от стейкхолдеров.
- Scope-оценка для редизайна.
- Фундамент под pattern library.

### «Ask forgiveness, not permission»

Frost явно поддерживает: если стейкхолдеры не утверждают DS — **делайте всё равно**. Аналогия с Lego: можно начать собирать машинку из кучи деталей, а можно сначала отсортировать по категориям. Финальное «изделие» одно и то же, но второй путь — быстрее и качественнее. После релиза: «Кстати, мы собрали DS — вот зачем».

### Сброс ожиданий

- **Print mentality vs web reality.** Веб — fluid, multi-device, interactive. Дизайн = не только эстетика. Должно учитывать: flexibility, network impact, motion, ergonomics, color/text rendering, pixel density, scrolling perf, device/browser quirks, user preferences.
- **Death to the waterfall.** Классический сценарий «UX → визуал → фронт → 9 версий → fight» = плохо. Альтернатива — **параллельная работа всех дисциплин с первого дня**.
- **Development is design.** Фронт — не «производственный цех», а **участник дизайн-процесса**. HTML/CSS — не Java/Python, это **дизайн-материал**.
- **Iterative iterative iterative iterative.** Аналогия со скульптурой: сначала груда камня, потом грубая форма, потом детали. Не painting, а sculpting.

### Артефакты на ранних стадиях

- **Lo-fi sketches** (UX) — серые блоки, где что лежит и в каком порядке. Mobile-first для фокуса.
- **Spreadsheet content & display patterns** (Dan Mall) — таблица где для каждого темплейта прописано: organism / molecule / описание / пример контента. Очень эффективно — серьёзный совет от Frost.
- **20-second gut test** (визуальщики) — показать 20–30 чужих сайтов по 20 сек, голосуют 1–10. Обсуждаются low/high/контроверсные. Достать эстетические ценности без полноценных макетов.
- **Style tiles** (Samantha Warren) — цвет/типографика/текстуры/фактуры, без layout. Уже устоявшийся артефакт.
- **Element collages** (Dan Mall) — collage UI-элементов в estimated стилях, без layout. Между style tile и full comp.
- **Front-end prep chef** — фронт начинает кодить сразу: env, scaffolding, заглушки для известных паттернов (header/footer/dashboard/forms).

### Роль full comps в post-PSD эпоху

PSD не «devil incarnate», но используются **ограниченно**:
- Только когда уже есть согласие по style tile / element collage.
- Цель — facilitate conversation, не «pixel-perfect spec».
- Дальше — **deciding in the browser** (Dan Mall).

### In-browser iteration

> *"Let's change the phrase 'designing in the browser' to 'deciding in the browser.'"* — Dan Mall

После того как макет в браузере — **остаётся в браузере**. Дизайнер может делать spot-comps в Sketch для конкретного организма, но решения принимаются на живом коде. Loop «build → design → build → design» (Trent Walton).

---

## Глава 5. Maintaining Design Systems — самая важная глава для долгосрока

> *"A style guide is an artifact of design process. A design system is a living, funded product with a roadmap & backlog, serving an ecosystem."* — Nathan Curtis

### Главный сдвиг мышления

❌ «Делаем сайты, style guide — побочный артефакт».
✅ «Делаем дизайн-систему, она питает и сайты, и pattern-library».

Friction is friendly: если кастомный dropdown плохо перформит на product page — изменяй **в системе**, не локально. Изменения на уровне системы > локальные хотфиксы.

### 10 правил для maintainable DS

1. **Make it official** — выделить бюджет, время, людей. Roadmap «make a thing → show it's useful → make it official».
2. **Make it adaptable** — clear governance. Что делать с модификацией / добавлением / удалением паттернов. Канвас Canonical (Vanilla framework) — диаграмма решений по новому патерну.
3. **Make it maintainable** — гнаться за **holy grail**: единый источник правды для DS и прода. Lonely Planet Rizzo — API для UI-патернов, Phase2 + Drupal + Twig — шаблонный движок как мост между Pattern Lab и продом.
4. **Make it cross-disciplinary** — watering hole для всей компании.
5. **Make it approachable** — красивая, понятная навигация, привлекательная домашняя.
6. **Make it visible** — evangelism, changelogs, roadmap, success stories, tutorials, regular «state of the union» митинги. Авто-уведомления (Slack hooks на PR).
7. **Make it public** — публичная DS = visibility + accountability + рекрутинг. Jina Bolton пришла в Salesforce, увидев их Lightning DS.
8. **Make it bigger** — расширять: brand, voice & tone, code, design principles, writing guidelines. Intuit Harmony — DS + принципы + voice & tone + маркетинг.
9. **Make it context-agnostic** — нейминг по структуре, не по контексту. ❌ «product card» → ✅ «card». ❌ «homepage carousel» → ✅ «carousel». Упражнение: размывать содержимое скриншота при нейминге, чтобы фокус был на структуре.
10. **Make it contextual** — для каждого pattern показывать **lineage** (из чего состоит + где используется), скриншоты/видео из реальных продуктов, do/don't.

### Makers vs Users

- **Makers** — команда, которая создает и поддерживает систему. У Salesforce — ~12 fulltime в design systems team.
- **Users** — все, кто использует DS для построения продуктов.
- Спектр: маленькая компания (makers = users) ↔ enterprise (распределены глобально). Чем больше дистанция, тем важнее документация и lineage.

### Types of change

- **Modifying** — багфиксы, визуальные правки, perf-улучшения, a11y-улучшения.
- **Adding** — нужны только если кейс reusable. Один use case = «one-off», пока другая команда не скажет «I want that».
- **Removing** — Salesforce Sass Deprecate: SCSS-флаги предупреждают пользователей о грядущем удалении пaттерна + предлагают замену.

### Training & Support — отдельный продукт

- Pair sessions, workshops, webinars, tutorials, onboarding.
- Issue trackers (JIRA/GitHub), office hours, Slack/Yammer/HipChat, forums.
- Pull requests от пользователей.
- Регулярные интервью / roundtables / surveys / «state of the union».

---

## Применение к Dreamkas DS — соответствие и где растём

| Принцип Frost'а | Что у нас уже есть | Где растём |
|---|---|---|
| 5 уровней иерархии | Foundations (= atoms-tier токены), Atoms (Button/Input/Selection/Select/Badge/Avatar/Tooltip), Molecules (Card/Table/Form Field) | **Organisms** пока пусто — план Frost'а: «Data Table» как первый organism |
| Russian-doll включения | Component tokens ссылаются на semantic через `var()` — аналог «include» | — |
| Templates с FPO-контентом | Превью-карточки с подписями `37 characters`, `1080×1080` — это и есть templates | Нет «pages»-уровня с реальными данными в продакшн-контексте |
| Context-agnostic нейминг | `card`, `block`, `badge` — без привязки к use case | Проверять при росте |
| Cross-platform DS | Web/Tablet/Mobile разделены **только** где меняется тач-таргет (Button/Input/Selection/Select) — это правильно по Frost'у | — |
| Lineage | Manifest по группам в превью | Pattern Lab-style автоматический lineage отсутствует |
| Style guide structure | README по color, AUDIT.md, preview/breakpoints.html, preview/grid.html | Public-facing полноценного style guide с навигацией нет |
| Interface inventory | Атомарная инвентаризация компонентов фактически проводилась | Не было large-group cross-disciplinary упражнения |
| Code style enforcement | CLAUDE.md задаёт правила (semantic-only в продакшне, ×3 файла только для тач-таргетов, primitives.css не primitive.css) | — |
| Make it adaptable / governance | Внутренний review через `<design_system_feedback>` | Не описан публично flow «add / modify / remove pattern» |

### Конкретные рекомендации из книги для нашего следующего шага

1. **Documentation page для каждого компонента** — Frost подчёркивает важность markdown-описания, **где** компонент используется, **когда**, **варианты**, **a11y-соображения**, **gotchas**. Сейчас у нас это рассыпано по превью.
2. **Organisms — Data Table** как первый организм (Table Toolbar + Pagination + Table — уже в плане). Это пример «multiple molecules → organism» из книги.
3. **«Spreadsheet content & display patterns»** (Dan Mall) — для новых страниц POS / backoffice. Колонки: organism / molecule / описание / пример контента. Эффективнее wireframe-ов на старте.
4. **Public-facing style guide** — даже минимальный, по совету Frost'а («increases accountability + recruitment tool»). Сейчас всё в локальных HTML-превью.
5. **Lineage в превью** — хотя бы текстом подписать «Card используется в: Product Card, Order Card, KPI Card; состоит из: Heading, Body, Footer, Badge?, Button?».
6. **Pages-уровень** — отдельные превью с **реальным контентом** (Касса с реальными товарами, Сотрудники с реальными именами/фото, Отчёт с реальными цифрами) — для проверки устойчивости системы к реальности.
7. **Holy grail для нас недостижим** (Pattern Lab + Drupal история не подходит) — но **shared tokens.css** + единый CSS компонента, подключаемый и в превью, и в проде = аналог принципа.

---

## Ключевые цитаты

> *"We're not designing pages, we're designing systems of components."* — Stephen Hay

> *"Atomic design is not a linear process, but rather a mental model."* — Brad Frost

> *"You can create good experiences without knowing the content. What you can't do is create good experiences without knowing your content structure."* — Mark Boulton

> *"Get your content ready to go anywhere, because it's going to go everywhere."* — Future Friendly manifesto

> *"It's not just about using a design system, it's about creating your system."* — Brad Frost

> *"A style guide is an artifact of design process. A design system is a living, funded product with a roadmap & backlog, serving an ecosystem."* — Nathan Curtis

> *"The biggest existential threat to any system is neglect."* — Alex Schleifer, Airbnb

> *"Let's change the phrase 'designing in the browser' to 'deciding in the browser.'"* — Dan Mall

> *"The hard part is building the machine that builds the product."* — Dennis Crowley

> *"Ideas are meant to be ugly."* — Jason Santa Maria

---

## Терминарий (EN → RU перевод нами)

| EN | RU | Заметка |
|---|---|---|
| Atom | Атом | Базовый элемент: токен, иконка, label |
| Molecule | Молекула | Группа атомов как функциональный юнит (Search form) |
| Organism | Организм | Распознаваемая секция UI (Header, Product grid) |
| Template | Шаблон | Layout с FPO-контентом, структурный скелет |
| Page | Страница | Шаблон с реальным контентом, проверка системы |
| Pattern library | Библиотека компонентов | Каталог UI-патернов |
| Style guide | Гайд по стилю | Более широкое — может включать brand/voice/code/UI |
| Design system | Дизайн-система | Зонтик: pattern lib + tokens + guidelines + processes |
| Interface inventory | UI-инвентаризация | Сбор всех уникальных UI-патернов из продукта |
| Style tile | Style tile | Артефакт Samantha Warren: палитра + типографика + фактура |
| Element collage | Коллаж элементов | Артефакт Dan Mall: UI-элементы в стилях без layout |
| FPO (For Placement Only) | Заглушка | Серый бокс с размерами, placeholder-текст |
| Russian-doll includes | Матрёшечные include | Pattern Lab-подход вложенности компонентов |
| Holy grail | Святой Грааль | Pattern lib и прод — один источник правды |
| Maker / User | Создатель / Пользователь системы | Внутренние роли вокруг DS |
| Special snowflake syndrome | Синдром «особой снежинки» | Отдел думает что у них особые требования и надо custom UI |

---

## Ресурсы из книги (избранное)

- **Pattern Lab** — patternlab.io
- **Styleguides.io** — каталог 150+ публичных style guides
- **Material Design** — google.com/design
- **Lightning Design System** (Salesforce) — lightningdesignsystem.com
- **Rizzo** (Lonely Planet) — rizzo.lonelyplanet.com
- **Harmony** (Intuit) — пример cross-platform DS (web + iOS + Android)
- **Draft U.S. Web Design Standards** — standards.usa.gov
- **MailChimp Voice and Tone** — voiceandtone.com
- **Style Tiles** (Samantha Warren) — styletil.es
- **Element Collages** (Dan Mall) — danielmall.com/articles/rif-element-collages/
- **This Is Responsive** (Brad Frost) — bradfrost.github.io/this-is-responsive/
- **OOCSS / SMACSS / BEM** — методологии модульного CSS
- **Anna Debenham — Front-End Style Guides** — книга-компаньон

---

*Источник: Atomic Design, Brad Frost, 2016 (ISBN 978-0-9982966-0-9). Конспект подготовлен для Dreamkas Design System — `rev 2/docs/atomic-design-brad-frost.md`.*
