# Event Loop

---

## Definition

The Event Loop is an infinite loop that waits for events from the operating system and dispatches them to the appropriate objects.

---

## What is an Event?

An event is anything that happens while the application is running.

Examples:

- Mouse click
- Keyboard press
- Window resize
- Window close
- Timer expiration
- Drag and drop

---

## Why do GUI applications need an Event Loop?

Unlike console applications, GUI applications do not know when the user will interact with them.

Instead of continuously executing instructions, they spend most of their lifetime waiting for events.

---

## Responsibilities

The Event Loop:

- Waits for operating system events.
- Receives events.
- Delivers them to the correct widget.
- Repeats until the application exits.

---

## Simplified Flow

Wait

↓

Receive Event

↓

Find Target

↓

Process Event

↓

Wait Again

---

## Relationship with QApplication

The event loop starts when:

```cpp
app.exec();
```

and stops when the application exits.

---

## Key Takeaways

- Every GUI application has an event loop.
- Most of the application's lifetime is spent waiting.
- `app.exec()` starts the event loop.

---

## Previous

[[05 - QApplication]]

## Next

[[07 - Main Window]]