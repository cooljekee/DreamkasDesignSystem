# Аудит цветовой системы Dreamkas

> Состояние на 2026-05-26, актуализировано 2026-06-05. Что нашёл, что нужно обсудить.

> **Статус по пунктам (2026-06-05):** ·1 — ✅ решён (удалён). ·2 — ✅ решён (`bg-contrast`/`text-contrast`/`icon-contrast` удалены; `surface-inverse` перепрофилирован). ·7 — ✅ решён (`amber-400` = `#FAB930`). ·3 — ✅ закрыт shadow-системой. Открыты: ·4 (`status-notify`), ·6 (`green-50` оливковый), ·5 (naming — косметика).

---

## 📊 Сводка

| Метрика | Значение |
|---|---|
| Примитивных цветов | **67** (6 шкал × ~10 стопов + 5 alpha + 12 neutral) |
| Семантических токенов | **82** |
| Карточек в Design System tab | **72** (Atoms/Molecules/Organisms/Colors/Type/Spacing) |
| Платформ покрыто | 1 (общие цвета для всех 3-х) |

---

## 🔴 Что требует решения (потенциальные баги / лишнее)

### 1. `--text-active-dark` — ✅ РЕШЕНО: удалён

> Токен убран из `semantic.css`. Для тёмного бренд-цвета внутри DS — `--color-brand-700` напрямую.

<details><summary>Исходная запись</summary>

```css
--text-active-dark: var(--color-brand-700);  /* "доп. кнопки" */
```

Этот токен пришёл из исходной таблицы Figma (`Color_Primary_Dark`). На практике он:
- **Дублирует** `--bg-brand-pressed` по значению (оба `brand-700`)
- Не используется ни в одной из 15 превью-карточек
- Не имеет чёткой роли — что значит «доп. кнопки»?

**Предложение:** удалить. Если понадобится тёмный бренд-цвет для текста — есть `--brand-700` через primitive (для DS-внутреннего использования).

</details>

---

### 2. `--bg-contrast` + `--surface-inverse` — ✅ РЕШЕНО: выбран вариант A+

> `bg-contrast`, `text-contrast`, `icon-contrast` удалены. `surface-inverse` **оставлен и перепрофилирован**: теперь `neutral-900` (было `neutral-800`), роль — тёмная поверхность на светлой теме (tooltip, toast, popover). Добавлен `--text-on-inverse` для текста/иконок на нём. Используется в Tooltip и Toast.

<details><summary>Исходная запись</summary>

```css
--bg-contrast: var(--color-neutral-800);     /* #283232 */
--surface-inverse: var(--color-neutral-800); /* то же значение */
```

Раз тёмные блоки/секции в Dreamkas не используются (твой ответ), эти токены не находят применения. Также вместе с ними висят `--text-contrast` и `--icon-contrast` (белый), которые имеют смысл только на тёмном фоне.

**Варианты:**
- **A.** Удалить все 4 (`bg-contrast`, `surface-inverse`, `text-contrast`, `icon-contrast`)
- **B.** Оставить — на всякий случай, для будущих тёмных модалок / тостов
- **C.** Удалить `bg-contrast` + `surface-inverse`, оставить `text-contrast` + `icon-contrast` (их используют `text-on-brand` и `text-on-danger` как «белый»)

**Рекомендую B** — пригодится для тёмных тостеров/snackbar когда дойдём до компонентов.

</details>

---

### 3. `--surface-raised` == `--surface-default` (по значению)

```css
--surface-default: var(--color-neutral-0);  /* #FFFFFF */
--surface-raised: var(--color-neutral-0);   /* #FFFFFF — то же */
```

Сейчас оба белые. По задумке `raised` отличается от `default` тенью (попап «приподнят» над страницей). Без shadow-системы — это **визуально неотличимые** токены.

**Решение:** оставить, добавить shadow в следующем большом блоке (Shadows). Тогда `surface-raised` обретёт смысл через `box-shadow`.

---

### 4. `--status-notify-*` дублирует `--status-warning-*`

```css
--status-warning-bg: var(--color-amber-100);     /* #F5E8CE */
--status-notify-bg:  var(--color-amber-100);     /* то же */
```

`warning` и `notify` используют один и тот же фон (amber-100). Различие только в `border`:
- `warning-border` = `amber-500` (#F7AA13 — яркий)
- `notify-border` = `amber-200` (#FFE082 — мягкий)

И `notify-text` = `black-87` (обычный текст), а `warning-text` = `amber-700` (тёмный янтарь).

**Вопрос:** в Dreamkas реально есть **2 разные роли** для нейтральных уведомлений и предупреждений? Если нет — `notify` можно убрать, использовать только `warning`. Если да — оставить.

---

### 5. Несогласованный naming

Есть несколько мест, где имена группы не следуют единому паттерну:

| Несогласованность | Сейчас | Лучше |
|---|---|---|
| Группа `border` vs `border-state` | `border-default/subtle/strong/focus` + `border-state-hover/disabled/error` | Все варианты в одну группу: `border-default/subtle/strong/focus/hover/disabled/error` |
| `text-link` без `default` суффикса | `text-link` + `text-link-hover/pressed/disabled` | `text-link-default` + остальные. Или `bg-brand` тоже без `default`. |
| `nav-item-*` слишком длинно | `--nav-item-bg-selected` | `--nav-bg-selected` (короче) |

**Решение:** не критично, но если хочешь чистоты — могу переименовать одним движением.

---

### 6. `green-50` = `#E1ECD3` — оливковый, а не «light pale green»

```css
--color-green-50: #E1ECD3;
```

Это твой `Color_Light_Green_Bg` из Figma. Визуально цвет ближе к **оливковому/желтоватому**, чем к мятному pale-green как обычно бывает в светлой части шкалы. Это **намеренно** или это была опечатка в Figma?

Сравни визуально:
- Сейчас: `#E1ECD3` (этот желтоватый оттенок)
- Обычно для зелёной шкалы 50: `#E8F5E9` (Material) или `#F0FDF4` (Tailwind)

**Если оставить:** успех-плашки будут выглядеть тёпло-оливковыми, не классически зелёными. Это создаёт особенный визуальный язык — может, у Dreamkas так и задумано?

---

### 7. `amber-400` — ✅ РЕШЕНО: пересчитан

> `--color-amber-400` теперь `#FAB930` (было `#FFC107`). Монотонность шкалы восстановлена: 400 светлее анкора 500 (`#F7AA13`), но не ярче.

<details><summary>Исходная запись</summary>

```css
--color-amber-400: #FFC107;  /* очень яркий жёлтый */
--color-amber-500: #F7AA13;  /* анкор из Figma, чуть глуше */
```

`#FFC107` ярче и насыщеннее, чем `#F7AA13`. Это **нарушает** принцип «чем больше число — тем темнее». В Figma анкор только на 500 — я взял `#FFC107` для 400 из Material, и это поломало плавность.

**Решение:** пересчитать `amber-400` через OKLCH от анкора 500, чтобы он был светлее но не ярче.

</details>

---

### 8. Дублирующие значения (не баги, но проверь нужны ли)

Несколько токенов указывают на **один и тот же цвет**. Это **нормально** — семантика разная — но стоит убедиться что роли реально различные:

| Токены, ссылающиеся на одно значение | Значение |
|---|---|
| `bg-secondary`, `bg-hover`, `bg-disabled`, `bg-skeleton`, `surface-sunken`, `surface-pressed`, `nav-item-bg-hover` | `neutral-100` (#F2F2F2) |
| `bg-selected`, `nav-item-bg-selected`, `status-info-bg` | `brand-50` (#E0F3F4) |
| `text-active`, `icon-active`, `nav-item-text-selected`, `nav-item-icon-selected`, `border-focus`, `status-info-border` | `brand-500` (#0097A7) |
| `border-default` | `neutral-300` |
| `border-state-hover`, `border-strong`, `icon-secondary` | `neutral-400` (#969696) |

**Это нормально**, но если какие-то роли реально слились — стоит подумать о консолидации.

---

## 🟢 Что хорошо

- Полное покрытие интерактивных состояний (hover/pressed/disabled/focus)
- Семантика приведена 1:1 к исходным именам Figma (Bg_Primary → bg-primary и т.д.)
- 3-слойная архитектура соблюдена строго
- Шкалы математически согласованы (OKLCH-интерполяция между анкорами)
- W3C DTCG формат для tokens.json — стандарт индустрии
- Каждый токен имеет описание use case в семантическом файле

---

## 📋 Что предлагаю решить до перехода к компонентам

| Приоритет | Решение | Влияние |
|---|---|---|
| ✅ | `text-active-dark` — удалён | 1 токен |
| ✅ | `bg-contrast`/`text-contrast`/`icon-contrast` удалены, `surface-inverse` перепрофилирован | 4 токена |
| ✅ | `amber-400` пересчитан (`#FAB930`) | плавность шкалы |
| 🟡 | `status-notify` — нужен ли отдельно от `warning`? **(открыт)** | 3 токена |
| 🟡 | `green-50 = #E1ECD3` — оливковый или классический pale-green? **(открыт)** | впечатление success |
| 🟢 | Naming consistency (`border-state-*` → `border-*`) — косметика **(открыт)** | косметика |

Осталось решить 2 визуальных вопроса (`status-notify`, `green-50`) + одну косметику (naming).
