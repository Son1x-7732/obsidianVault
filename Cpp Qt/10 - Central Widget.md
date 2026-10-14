# Central Widget

---

## Definition

The Central Widget is the primary content area inside a `QMainWindow`.

It occupies the largest part of the window and is where the application's main interface is placed.

---

## Why does it exist?

A desktop application usually has several regions:

- Menu Bar
- Tool Bar
- Status Bar
- Main Content

Qt separates these responsibilities.

The main content is represented by the Central Widget.

---

## Structure

QMainWindow

├── Menu Bar

├── Tool Bar

├── Status Bar

└── Central Widget

---

## Rule

A `QMainWindow` can have **only one** Central Widget.

However, that Central Widget can contain many other widgets.

---

## Relationship with NoteForge

MainWindow

↓

Central Widget

↓

Layouts

↓

Editor

Sidebar

Preview

---

## Key Takeaways

- A MainWindow owns one Central Widget.
- The Central Widget becomes the root of the application's interface.
- Every major UI element is eventually placed inside it.

---

## Previous

[[09 - QWidget]]

## Next

[[11 - Layouts]]