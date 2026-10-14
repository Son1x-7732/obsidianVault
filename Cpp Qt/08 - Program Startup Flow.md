# Program Startup Flow

---

## Complete Startup Flow

```text
User launches NoteForge

↓

Operating System creates process

↓

main()

↓

Create QApplication

↓

Initialize Qt

↓

Create MainWindow

↓

Request window to be shown

↓

Start Event Loop

↓

Wait for Events

↓

Application closes

↓

Event Loop exits

↓

main() returns

↓

Process destroyed
```

---

## Responsibilities

### main()

Entry point of the application.

### QApplication

Creates the Qt environment.

### MainWindow

Represents the application's graphical interface.

### app.exec()

Starts the Event Loop.

---

## Key Takeaways

- Startup follows a well-defined order.
- QApplication must exist before any QWidget.
- The Event Loop keeps the application alive.

---

## Previous

[[07 - Main Window]]

## Next

[[09 - QWidget]]