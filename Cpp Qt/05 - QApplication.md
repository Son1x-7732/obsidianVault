# QApplication

---

## Definition

`QApplication` is the central object of every Qt Widgets application.

It initializes the Qt framework, communicates with the operating system, and manages the application's lifetime.

---

## Why do we need it?

A console application executes instructions and exits.

A GUI application must:

- create windows
- receive mouse events
- receive keyboard events
- redraw itself
- communicate with the operating system

`QApplication` provides all of these capabilities.

---

## Responsibilities

- Initializes Qt
- Parses Qt-specific command-line arguments
- Manages application-wide settings
- Creates the event system
- Starts the event loop
- Keeps the application running until it is closed

---

## Relationship with NoteForge

Operating System

↓

main()

↓

Application (our class)

↓

QApplication (Qt)

↓

MainWindow

↓

Editor

---

## Key Takeaways

- Every Qt Widgets application has exactly one QApplication.
- It must be created before any QWidget.
- Without QApplication, Qt cannot create GUI windows.

---

## Previous

[[04 - Command Line Arguments]]

## Next

[[06 - Event Loop]]