# Custom Widgets

---

## Definition

A custom widget is a class created by the developer that inherits from an existing Qt widget.

Instead of only using Qt's built-in widgets, we can combine them into larger reusable components.

---

## Why create custom widgets?

As applications grow, placing all UI code inside MainWindow becomes difficult to manage.

Custom widgets allow us to separate different parts of the interface into independent components.

---

## Relationship

Qt Widgets

↓

Our Widgets

↓

Application

---

## Example

QWidget

↓

WelcomeScreen

↓

Labels

Buttons

Layouts

---

## Advantages

- Reusable
- Easier debugging
- Cleaner architecture
- Better scalability
- Easier maintenance

---

## Key Takeaways

- Everything in Qt is ultimately a widget.
- We can build our own widgets exactly like Qt built theirs.
- Large applications are collections of custom widgets.

---

## Previous

[[17 - Composition]]

## Next

[[19 - Pointers]]