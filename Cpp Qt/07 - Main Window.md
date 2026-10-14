# Main Window

---

## Definition

A `QMainWindow` is the primary window of a Qt Widgets application.

It provides a ready-made framework for building desktop applications.

---

## What does a QMainWindow provide?

- Title Bar
- Menu Bar
- Tool Bars
- Status Bar
- Central Widget
- Dock Widgets

---

## Why use QMainWindow?

Instead of creating every part of a desktop application manually, Qt provides `QMainWindow` as a foundation.

Applications like IDEs, text editors, and browsers are naturally modeled using a main window.

---

## Relationship with NoteForge

Application

↓

QApplication

↓

MainWindow

↓

Editor

↓

Markdown Document

---

## Key Takeaways

- There is usually one main window.
- It is responsible for the user interface, not the application logic.
- It inherits from `QMainWindow`.

---

## Previous

[[06 - Event Loop]]

## Next

[[08 - Program Startup Flow]]