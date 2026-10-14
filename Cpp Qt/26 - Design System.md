# Design System

---

## Definition

A design system centralizes the visual constants used throughout an application.

Instead of hardcoding numbers, reusable constants are defined.

---

## Examples

- Font sizes
- Margins
- Spacing
- Button sizes
- Corner radius
- Colors

---

## Benefits

- Consistency
- Easier maintenance
- Cleaner code
- Easy redesign

---

## Bad

label->setFont(QFont(..., 24));

button->setFixedHeight(42);

layout->setSpacing(17);

---

## Good

Theme::TitleFontSize

Theme::ButtonHeight

Theme::Spacing

---

Previous

[[25 - Application Theme]]

Next

[[27 - Design System]]