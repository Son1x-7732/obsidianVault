# Composition

---

## Definition

Composition is a relationship where one object contains or owns another object.

It models a "has-a" relationship.

---

## Examples

Car has an Engine.

House has Rooms.

Computer has a CPU.

MainWindow has a WelcomeScreen.

---

## Composition vs Inheritance

Inheritance

→ "is-a"

Composition

→ "has-a"

---

## Why use Composition?

It keeps classes focused on one responsibility.

Instead of one giant class doing everything, the application is built from smaller reusable components.

---

## NoteForge

MainWindow

↓

WelcomeScreen

↓

Buttons

↓

Labels

---

## Key Takeaways

- Composition builds large software from small components.
- Prefer composition unless inheritance truly represents an "is-a" relationship.

---

## Previous

[[16 - The this Pointer]]

## Next

[[18 - Custom Widgets]]