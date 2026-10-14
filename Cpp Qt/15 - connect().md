# connect()

---

## Definition

`connect()` creates a communication link between a signal and a slot.

It tells Qt:

> "When this event happens, execute this code."

---

## General Structure

```cpp
connect(sender,
        signal,
        receiver,
        slot);
```

---

## Four Parts

Sender
→ The object that emits the signal.

Signal
→ The event that occurred.

Receiver
→ The object that should respond.

Slot
→ The function that should execute.

---

## Example

```cpp
connect(button,
        &QPushButton::clicked,
        this,
        &MainWindow::createNote);
```

Meaning:

"When button is clicked,
call createNote()."

---

## Key Takeaways

- connect() links objects together.
- Signals do not know who receives them.
- Receivers decide how to respond.
- This keeps components loosely coupled.

---

## Previous

[[14 - Signals and Slots]]

## Next

[[16 - The this Pointer]]