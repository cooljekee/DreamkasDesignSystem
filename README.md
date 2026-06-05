# Dreamkas Design System

Дизайн-система ритейл-софта Dreamkas — товароучёт, касса (POS), склад, отчёты. Enterprise UI под три платформы: **Web** (backoffice), **Tablet**, **Mobile** (POS).

Куратор системы — скилл **«Софонов Дизайнер»** (см. [`CLAUDE.md`](./CLAUDE.md)): роль, доменный контекст, правила работы с токенами и визуальная подача.

---

## Структура

```
Foundations/                  токены-фундамент (CSS-переменные)
  color/        primitives.css · semantic.css · tokens.json · README.md · AUDIT.md
  typography/   primitives.css · semantic.css · tokens.json
  spacing/      primitives.css · semantic.css   (space, radius, border-width, size, breakpoints, grid)
  shadow/       primitives.css · semantic.css

Web/  Tablet/  Mobile/        реализация под платформу
  Foundations/Component tokens/<comp>/tokens.css    только var() на semantic/primitive
  Components/Atoms|Molecules|Organisms/<Comp>/<comp>.css
  Patterns/    Assets/ (Icons 16/20/24 · Illustrations · Logos · Media)

preview/                      превью-карточки (открывать в браузере)
  component-<name>-<platform>.html
  <foundation-slug>.html

CLAUDE.md                     скилл «Софонов Дизайнер» — контекст и правила
```

---

## Архитектура токенов (3 слоя)

1. **Primitive** — сырые значения (`--color-brand-500: #0097A7`). Только внутри foundation.
2. **Semantic** — роли (`--bg-brand-default`, `--border-focus`, `--status-danger-bg`). Используются в компонентах.
3. **Component** — параметры компонента (`--btn-primary-bg`), ссылаются на semantic/primitive через `var()`.

> Сырые HEX и `rgba()` «на глаз» в компонентах запрещены. Нет нужного токена — сначала добавь в foundation.

---

## Компоненты

**Atoms** — Button, Input, Selection (checkbox/radio/switch), Select (×3 платформы: различается тач-таргет) · Badge, Avatar, Tooltip (×1).

**Molecules** — Card (5 типов), Table (сетка ↔ stack-адаптив), Form Field, Tabs, Pagination, Table Toolbar (поиск + фильтры + bulk-actions), Alert, Toast, Chart (KPI + бар-чарт).

**Organisms** — Modal, Sidebar, Topbar · композиты App Shell (Sidebar + Topbar) и Data Table (Table + Toolbar + Pagination).

Бренд — teal `#0097A7`. Текст — Dreamkas Sans (встроенный variable), числа — моноширинные (tabular), русский формат `1 290,90`.

---

## Как смотреть

Открой любой файл из `preview/` в браузере — каждая карточка самодостаточна и подключает нужные токены и CSS компонента напрямую.

```bash
# локально
open preview/component-button-web.html
```

---

## Брейкпоинты

| Window      | Breakpoint | Align                    |
|-------------|------------|--------------------------|
| < 760       | —          | Vertical, horiz. scroll  |
| 760–1260    | 660        | Vertical                 |
| 1260–1440   | 960        | Horizontal, Mixed        |
| 1440–2560   | 1340       | Horizontal               |
| > 2560      | 2260       | Horizontal, + background |

`breakpoint = window width − menu width`
