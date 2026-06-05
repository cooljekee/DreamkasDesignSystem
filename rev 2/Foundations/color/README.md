# Dreamkas Colors — фундамент дизайн-системы

> Все цветовые токены продукта. Это первый файл, который надо прочитать перед тем как трогать любой цвет в коде или Figma.

---

## 🎯 Принципы

**3-слойная архитектура** (от сырого к контекстному):

```
Primitive   →   Semantic   →   Component
(сырые HEX)     (роли)         (per-component, по мере роста)
```

| Слой | Назначение | Пример |
|---|---|---|
| **Primitive** | Сырые цвета палитры, без смысла | `--color-brand-500: #0097A7` |
| **Semantic** | Цвет по роли в UI | `--text-primary`, `--bg-brand-default` |
| **Component** | Цвет конкретного компонента | `--button-primary-bg` (пока не создан) |

### ⚖️ Правило слоёв

> **Продукт использует ТОЛЬКО Semantic токены.**
> **Компоненты внутри DS** — только Component токены или Semantic.
> **Primitive** — внутренняя кухня, наружу не торчит.

Если в продакшн-коде встречается `var(--color-brand-500)` или `#0097A7` напрямую — это **ошибка**.

---

## 📁 Структура файлов

```
Foundations/color/
├── primitive.css     ← 67 сырых цветов (6 шкал + alpha)
├── semantic.css      ← ~82 ролевых токена
├── tokens.json       ← то же в формате W3C DTCG (для Tokens Studio, Style Dictionary)
└── README.md         ← этот файл
```

---

## 🎨 Шкалы Primitive

6 цветовых семейств + альфа-канал. Каждое семейство — шкала от `50` (самый светлый) до `900` (самый тёмный), с шагом 100.

| Семейство | Стопов | Якоря из Figma | Зачем |
|---|---|---|---|
| **brand** (teal) | 10 (50→900) | 50, 200, 500, 700 | Color_Primary и его варианты |
| **neutral** | 12 (0, 50→950) | 0, 100, 200, 300, 400, 800, 950 | Фоны, обводки, текст с альфой |
| **green** | 10 (50→900) | 50, 300, 500, 700 | Success, Color_Secondary |
| **red** | 10 (50→900) | 50, 600 | Danger, Color_Error |
| **amber** | 10 (50→900) | 100, 500 | Warning, Color_Notify |
| **blue** | 10 (50→900) | 600 | Ссылки |
| **alpha** | 5 значений | `0.87/0.54/0.24/0.60` + белый | Текст и иконки на светлых фонах |

**Анкоры** = реальные значения из исходной таблицы Figma. **Промежуточные стопы** сгенерированы интерполяцией через OKLCH, чтобы переходы выглядели визуально равномерными.

---

## 🎭 Группы Semantic

| Группа | Токены | Назначение |
|---|---|---|
| **bg** | primary, secondary, contrast, hover, pressed, disabled, selected, selected-hover, skeleton, skeleton-shine | Фоны страниц, секций, кликабельных областей |
| **surface** | default, raised, sunken, inverse, hover, pressed, disabled | Карточки, попапы, инпуты |
| **text** | primary, secondary, disabled, contrast, active, active-dark, link | Все цвета текста |
| **icon** | primary, secondary, contrast, active, disabled | Цвета иконок |
| **border** | default, subtle, strong, focus | Обводки в покое |
| **border-state** | hover, disabled, error | Обводки в интерактивных состояниях |
| **status** | success / warning / danger / info / notify × (bg + text + border) | Цветные плашки и состояния |
| **nav-item** | bg/text/icon × default/hover/pressed/selected/selected-hover/disabled | Пункты меню, сайдбара, табов |
| **bg-brand** | default, hover, pressed, disabled | Брендовые CTA-кнопки |
| **bg-danger** | default, hover, pressed, disabled | Деструктивные кнопки |
| **text-on** | brand, brand-disabled, danger | Текст/иконки НА цветных кнопках |
| **text-link** | (base), hover, pressed, disabled | Состояния ссылок |
| **divider** | default, strong | Линии-разделители |
| **overlay** | mask | Затемнение под модалками |

---

## 🚦 Правила использования

### ✅ Делай

```css
/* Используй семантические токены */
.title { color: var(--text-primary); }
.button-primary { background: var(--bg-brand-default); }
.button-primary:hover { background: var(--bg-brand-hover); }
.card { border: 1px solid var(--border-subtle); }
.modal-backdrop { background: var(--overlay-mask); }
```

### ❌ Не делай

```css
/* Primitive напрямую — нельзя в продукте */
.title { color: var(--color-brand-500); }

/* Сырые HEX — мимо системы */
.button { background: #0097A7; }

/* RGBA подбираемые «на глаз» */
.backdrop { background: rgba(0, 0, 0, 0.5); }
```

---

## 🔄 Принципы состояний (Material-логика)

Hover/pressed строятся **сдвигом по шкале** относительно default-стопа.

| Состояние | Сдвиг по шкале | Пример (brand) |
|---|---|---|
| **default** | базовый стоп | `brand-500` (#0097A7) |
| **hover** | +1 стоп (темнее) | `brand-600` (#00838F) |
| **pressed** | +2 стопа (ещё темнее) | `brand-700` (#006978) |
| **disabled** | `neutral-200` + альфа на тексте | #E0E0E0 + black-24 |
| **selected** | `brand-50` фон + `brand-500` текст | #E0F3F4 + #0097A7 |

---

## 📤 Импорт в инструменты

### Tokens Studio (Figma plugin)
1. Открыть Tokens Studio в Figma
2. **Tools → Import → JSON**
3. Загрузить `tokens.json`
4. Применить к проекту

### Style Dictionary (production CSS/JS/iOS/Android)
```bash
npm install style-dictionary
```
Сконфигурировать source: `tokens.json` → output: `tokens.css` / `tokens.swift` / `colors.xml`.

### Tailwind config
```js
// tailwind.config.js
module.exports = {
  theme: {
    extend: {
      colors: {
        brand: { 50: '#E0F3F4', 100: '#B2E1E5', /* … */ 900: '#003841' },
        // … остальные шкалы
      }
    }
  }
}
```

---

## ⚠️ Известные ограничения

| Ограничение | Что это значит |
|---|---|
| **Нет dark mode** | Все токены рассчитаны под светлую тему. Если понадобится тёмная — нужно добавить параллельную карту значений. |
| **Нет полного инверсного набора** | Для тёмных поверхностей (tooltip/toast) есть `surface-inverse` (`neutral-900`) + `text-on-inverse`. Полноценного набора border/icon под тёмный фон нет — добавим, если понадобятся тёмные блоки. |
| **Нет доменной палитры Dreamkas** | Нет отдельных цветов для прибыли/убытка, уровней остатка, фискальных режимов, платёжных систем. Используем `status-*`. |
| **Нет skeleton-анимации в токенах** | Сами цвета есть (`bg-skeleton`, `bg-skeleton-shine`), но анимация shimmer — на стороне компонента. |

---

## 🔧 Платформенные различия

Цвета **общие для всех платформ** — Mobile, Web, Tablet используют один и тот же `primitive.css` + `semantic.css`. Различия в размерах, отступах, типографике — но **не в цвете**.

---

## 📚 Связанное

- `/preview/*.html` — визуальные карточки всех токенов (открываются в Design System tab)
- `/Foundations/typography/` — будет добавлено
- `/Foundations/spacing/` — будет добавлено
- `/Components/` — будет добавлено

---

## 📝 Лог изменений

| Дата | Изменение |
|---|---|
| 2026-05-26 | Создана базовая система: 67 примитивов в 6 шкалах, ~82 семантических токена. |
| 2026-05-26 | Удалены: purple-шкала, magenta-шкала, `text-link-visited`, `text-link-contrast`, `mark-default`. |
| 2026-05-26 | Добавлены: состояния навигации (14 токенов), кнопок (8 токенов), инпутов (3 токена), ссылок (3 токена), selected/skeleton/divider (6 токенов), overlay (1 токен). |

---

## ⚠️ Известные технические долги (см. AUDIT.md)

См. отдельный файл `AUDIT.md` рядом — там список найденных несостыковок, дубликатов значений и предложений по чистке. Эти вещи не блокируют использование системы, но стоит решить до того, как начнём массово делать компоненты.
