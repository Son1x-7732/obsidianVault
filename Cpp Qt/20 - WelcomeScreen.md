# WelcomeScreen Widget

---

## Definition

A WelcomeScreen is a custom widget built specifically for NoteForge.

Instead of placing all UI elements inside MainWindow, they are grouped into a reusable component.

---

## Why?

MainWindow should coordinate the application.

WelcomeScreen should manage the welcome page.

This follows the Single Responsibility Principle.

---

## Relationship

MainWindow

↓

WelcomeScreen

↓

Labels

Buttons

Layouts

---

## Benefits

- Cleaner MainWindow
- Better scalability
- Easier debugging
- Reusable widget

---

## Key Takeaways

- Custom widgets are ordinary Qt widgets.
- They inherit from QWidget.
- Complex applications are composed of many custom widgets.

---

## Previous

[[19 - Pointers]]

## Next

[[21 - Initializer Lists]]