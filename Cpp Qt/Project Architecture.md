# Project Architecture

## High Level Flow

```text
Operating System

↓

main()

↓

Application

↓

QApplication

↓

MainWindow

↓

Editor
```

---

## Responsibilities

### Operating System

Starts the process.

---

### main()

The entry point of the application.

Its only job is to start the application.

---

### Application

Coordinates the startup of the application.

Responsibilities:

- create QApplication
- initialize services
- create MainWindow
- start the event loop

Application should **not** contain business logic.

---

### QApplication

Provided by Qt.

Responsible for:

- event loop
- keyboard events
- mouse events
- window management
- application lifetime

---

### MainWindow

Owns the user interface.

Eventually it will contain:

- menus
- toolbar
- editor
- sidebars
- status bar

---

## Design Principle

Every class should have one primary responsibility.

This follows the Single Responsibility Principle (SRP).