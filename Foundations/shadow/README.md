# Dreamkas Shadows — фундамент

> Тени и focus-rings. Часть foundations-слоя «Размеры» вместе со spacing/radius.

---

## 🎯 Принципы

**Two-layer shadow** — каждая тень состоит из двух наложенных слоёв:

| Слой | Что имитирует | Параметры |
|---|---|---|
| **ambient** | Рассеянный свет (мягкая широкая тень) | бОльший blur, меньшая opacity |
| **key** | Направленный свет (контактная тень) | меньший blur, бОльший offset-Y |

Один слой выглядит «плоско». Два слоя дают объём, но тень остаётся ненавязчивой.

**Цвет тени** — `neutral-950` (`#001113`), а не чистый `#000`. Даёт еле уловимый холодный оттенок в тон палитры Dreamkas, без эффекта «грязной» тени.

**Шкала сжата до 3 уровней** — `sm / md / lg`. В реальных интерфейсах больше уровней elevation не нужно: всё либо лежит на странице (карточки), либо парит (меню/поповеры), либо является оверлеем (модалки/тосты). Лишние ступени плодят неопределённость «куда что».

---

## 📁 Структура файлов

```
Foundations/shadow/
├── primitive.css     ← 3 уровня + 2 focus-ring
├── semantic.css      ← 15 ролей
├── tokens.json       ← W3C DTCG (для Tokens Studio / Style Dictionary)
└── README.md         ← этот файл
```

---

## 📊 Шкала Primitive

| Токен | Где применять |
|---|---|
| **`--shadow-none`** | Плоские элементы, инпуты, секции на странице |
| **`--shadow-sm`** | Карточка в покое — едва заметный edge-lift |
| **`--shadow-md`** | Floating: dropdown, popover, menu, tooltip, fab, sticky, card-hover |
| **`--shadow-lg`** | Overlay: modal, drawer, dialog, toast — поверх затемнения |

**+** два focus-ring токена:
- `--shadow-focus-ring` — стандартный (brand-500 / 24%)
- `--shadow-focus-ring-danger` — на контроле с ошибкой (red-600 / 24%)

---

## 🎭 Роли Semantic

| Группа | Роли | Уровень |
|---|---|---|
| **Карточки** | `card` | sm |
| | `card-hover`, `sticky` | md |
| **Меню / поповеры** | `dropdown`, `popover`, `menu`, `tooltip` | md |
| **Floating actions** | `fab` → `fab-hover` | md → lg |
| **Модальные** | `modal`, `drawer`, `dialog`, `toast` | lg |
| **Focus** | `focus`, `focus-danger` | focus-ring |

---

## 🚦 Правила использования

### ✅ Делай

```css
.card {
  background: var(--surface-default);
  border-radius: var(--radius-card);
  box-shadow: var(--shadow-card);
  transition: box-shadow 200ms ease;
}
.card:hover { box-shadow: var(--shadow-card-hover); }

.dropdown { box-shadow: var(--shadow-dropdown); }
.modal    { box-shadow: var(--shadow-modal); }
.toast    { box-shadow: var(--shadow-toast); }

.button:focus-visible {
  outline: none;
  box-shadow: var(--shadow-focus);
}
```

### ❌ Не делай

```css
/* Primitive напрямую — нельзя в продукте */
.card { box-shadow: var(--shadow-sm); }

/* Захардкоженные значения — мимо системы */
.card { box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1); }

/* Тень-border-эмуляция — используй border-* токены */
.input { box-shadow: 0 0 0 1px #C2C2C2; }
```

---

## 🔄 Иерархия z-слоёв

Тени **визуально подкрепляют** иерархию, не задают её. Z-index — отдельная история (будут токены позже). Соответствие:

```
toast / drawer / modal  → z-index: 1000+, shadow-lg
dropdown / tooltip       → z-index:  900, shadow-md
sticky elements          → z-index:  100, shadow-md
card                     → z-index: auto, shadow-sm
```

---

## ⚠️ Известные ограничения

| Ограничение | Что это значит |
|---|---|
| **Нет dark mode** | Тени рассчитаны на светлый фон. На тёмном они не видны — там нужен другой подход (inner shadow / тонкие borders). |
| **Нет inset-теней** | Например, под утопленные инпуты. Если понадобятся — добавим shadow-inset-sm. |
| **Нет coloured shadows** | Брендированные тени (синие/красные ауры) не входят в систему. Если нужны для маркетинг-блоков — это исключение. |
| **Toast и modal на одном уровне** | Визуально различимы через overlay-маску за модалкой. Сам shadow одинаковый — `lg`. |

---

## 📚 Связанное

- `/preview/shadow-scale.html` — все уровни
- `/preview/semantic-shadow.html` — роли и use-cases
- `/Foundations/spacing/` — sizing/spacing/radius (один блок «Размеры»)

---

## 📝 Лог изменений

| Дата | Изменение |
|---|---|
| 2026-05-26 | Создана шкала: 6 уровней elevation + 2 focus-ring, 15 семантических ролей. |
| 2026-05-26 | **Шкала сжата до 3 уровней** (`sm / md / lg`) под t-shirt-naming. Все 15 ролей пересобраны. |
