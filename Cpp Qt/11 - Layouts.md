# Layouts

---

## Definition

A Layout is an object that automatically arranges widgets inside another widget.

Instead of manually positioning every widget, Qt layouts calculate the size and position of child widgets.

---

## Why do Layouts exist?

Without layouts, every widget would need its position and size specified manually.

Layouts automatically adapt when:

- The window is resized.
- Widgets are added or removed.
- Fonts change.
- Display scaling changes.

---

## Responsibilities

A Layout:

- Positions widgets.
- Calculates widget sizes.
- Maintains spacing.
- Handles resizing automatically.

---

## Common Layouts

QVBoxLayout
→ Vertical arrangement

QHBoxLayout
→ Horizontal arrangement

QGridLayout
→ Grid arrangement

QFormLayout
→ Label / Field arrangement

---

## Relationship with NoteForge

MainWindow

↓

Central Widget

↓

Layout

↓

Widgets

---

## Key Takeaways

- Widgets should almost never be positioned manually.
- A layout controls the geometry of its child widgets.
- The Central Widget usually owns one root layout.

---

## Previous

[[10 - Central Widget]]

## Next

[[12 - QLabel]]