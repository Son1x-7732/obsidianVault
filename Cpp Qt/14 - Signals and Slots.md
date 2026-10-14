# Signals and Slots

---

## Definition

Signals and Slots are Qt's communication mechanism.

A signal announces that something happened.

A slot receives that signal and performs an action.

---

## Real-world Analogy

Button

↓

Signal

↓

Application Logic

↓

Action

---

## Terminology

Signal
→ Something happened.

Slot
→ What should happen.

Connection
→ Links a signal to a slot.

---

## Why does Qt use Signals and Slots?

GUI programs are event-driven.

Instead of continuously checking for user actions, widgets emit signals whenever something happens.

---

## Examples

Button Clicked

↓

clicked()

↓

Open a new note

---

Text Changed

↓

textChanged()

↓

Update preview

---

## Relationship with NoteForge

User

↓

Clicks "New Note"

↓

Signal emitted

↓

Slot executes

↓

Create Note

---

## Key Takeaways

- Signals announce events.
- Slots respond to events.
- Connections link them together.
- This is the foundation of event-driven programming.

---

## Previous

[[13 - Namespaces]]

## Next

[[15 - connect()]]