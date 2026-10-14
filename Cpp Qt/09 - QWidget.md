# QWidget

---

## Definition

`QWidget` is the fundamental building block of the Qt Widgets framework.

Almost every visible element in a Qt Widgets application is either a `QWidget` or inherits from it.

---

## What is a Widget?

A widget is a graphical user interface (GUI) object that the user can see or interact with.

Examples:

- Button
- Text Box
- Label
- Check Box
- Menu
- Main Window
- Text Editor

---

## Why QWidget Exists

Instead of every GUI element implementing common functionality independently, Qt provides `QWidget` as a common base class.

Every widget automatically gains common behavior such as:

- Showing and hiding
- Moving
- Resizing
- Receiving mouse events
- Receiving keyboard events
- Painting itself

---

## Inheritance

QObject

↓

QPaintDevice

↓

QWidget

↓

QMainWindow

↓

MainWindow (NoteForge)

---

## Relationship with NoteForge

MainWindow is a QWidget.

Every widget we add inside MainWindow is also a QWidget.

---

## Key Takeaways

- QWidget is the base class for almost every visible Qt object.
- QMainWindow inherits from QWidget.
- Most UI components are specialized widgets.

---

## Previous

[[08 - Program Startup Flow]]

## Next

[[10 - Central Widget]]