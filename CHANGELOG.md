# Changelog

Все значимые изменения Dreamkas Design System фиксируются здесь.

Формат — [Keep a Changelog](https://keepachangelog.com/ru/1.0.0/).
Версионирование — по ревизиям (`rev N`), как теги в git.

---

## [rev 2] — 2026-06-05

Текущая ревизия на `main`. Серьёзный рефакторинг архитектуры по сравнению с rev 1: добавлены новые компоненты, унифицирована раскладка Foundations, появилась шкала motion/z-index.

### Added — Foundations

- **`Foundations/motion/`** — токены анимаций (primitives + semantic + tokens.json). Раньше не было.
- **`Foundations/z-index/`** — каноническая шкала слоёв (`--z-modal`, `--z-tooltip` и т.п.). Раньше не было.
- **`Foundations/typography/fonts/`** + **`fonts.css`** — встроенный variable-шрифт Roboto (Variable + Italic-Variable, TTF). Раньше потребитель сам брал шрифт.

### Added — Web Components

- **Organisms:** `Modal`, `Sidebar`, `Topbar` — в rev 1 этих организмов не было вообще.
- **Molecules:** `Alert`, `Chart`, `FormField`, `Pagination`, `Table`, `TableToolbar`, `Tabs`, `Toast` — полный набор для backoffice.
- **Atoms:** `Avatar`, `Badge`, `Tooltip` — добавлены к существующим `Button`, `Input`, `Select`, `Selection`.

### Added — Tablet / Mobile

- Получили свой набор компонентов с touch-target адаптацией: `Button`, `Input`, `Select`, `Selection` (Atoms), все 7 Molecules + 3 Organisms (за вычетом `Avatar`, `Badge`, `Card`, `FormField`, `Tooltip` — они «универсальные» из `Web/`).

### Changed — Foundations

- **`Foundations/color/primitives.css` и `semantic.css`** — обновлены под новую палитру и нейминг семантики. Подробности — в `Foundations/color/AUDIT.md`.
- **`Foundations/typography/primitives.css`** — расширена иерархия токенов (добавились `--text-body-sm-*`, `--text-button-*`, и т.п.).
- **`Foundations/typography/tokens.json`** — синхронизирован с CSS.

### Changed — структура

- Mobile и Tablet раньше имели `Foundations/Primitive tokens/` и `Foundations/Semantic tokens/` как отдельные папки. Теперь они общие на корне `Foundations/` (color/typography/spacing/shadow/motion/z-index), а Mobile/Tablet содержат только `Foundations/Component tokens/<comp>/tokens.css` со своими специфичными размерами под touch.

### Removed — Mobile / Tablet

- **`Mobile/Components/Atoms/Avatar/`**, **`Tablet/Components/Atoms/Avatar/`** — удалены. Avatar теперь universal-компонент, живёт только в `Web/Components/Atoms/Avatar/` и применяется на всех платформах.
- **`Mobile/Foundations/Component tokens/avatar/`**, **`Tablet/Foundations/Component tokens/avatar/`** — по той же причине.

### Notes — что важно знать потребителю

- 5 компонентов «универсальных» (`Avatar`, `Badge`, `Card`, `FormField`, `Tooltip`) живут только в `Web/`. Скоупить по media-query их **не нужно** — они едины под все платформы. Подробности в `skills/dreamkas-ds-frontend/SKILL.md`, правило 4.
- Имена spacing-токенов `--space-N` (пиксели, семантический шаг) **не совпадают** по значениям с Tailwind v4 `--spacing-N` (rem, шаг 0.25rem). Не делать мост между ними. См. `skills/dreamkas-ds-frontend/SKILL.md`, правило 2.
- App Shell как композит существует, но пока живёт inline в `preview/organism-app-shell-web.html` (а не как отдельный CSS-файл). Канонические имена: `.app` / `.app-main` / `.app-content` + grid.

### Feedback от первого интегратора

Полный разбор находок при интеграции rev 2 в реальный проект — `docs/FEEDBACK-2026-06-05.md` (15 пунктов с обходными путями).

---

## [rev 1] — 2026-06-04

Первый релиз. Сохранён как git-тег `rev-1` (доступен через `git checkout rev-1`).

### Состав на момент rev 1

- `Foundations/`: color, typography, spacing, shadow (без motion и z-index).
- `Web/Components/Atoms/`: Button, Input, Select, Selection (без Avatar, Badge, Tooltip).
- `Web/Components/Molecules/`: Card (только).
- `Web/Components/Organisms/`: пусто.
- `Mobile/`, `Tablet/`: упрощённая структура с собственными Primitive/Semantic tokens папками.
- `preview/`: базовые demo-страницы.

Эту ревизию **не следует подключать к новым продуктам** — нужного для backoffice набора (Sidebar, Topbar, Modal, Table, Pagination и т.п.) в ней нет.
