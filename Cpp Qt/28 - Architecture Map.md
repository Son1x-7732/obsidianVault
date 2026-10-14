# NoteForge — Architecture Map

> Master reference for understanding the current project structure.

---

# 1. Project Structure

```text
NoteForge/
│
├── CMakeLists.txt
├── README.md
├── .gitignore
│
├── assets/
├── docs/
│
├── src/
│   ├── main.cpp
│   │
│   ├── Application.hpp
│   ├── Application.cpp
│   │
│   ├── theme/
│   │   └── Theme.hpp
│   │
│   └── ui/
│       ├── MainWindow.hpp
│       ├── MainWindow.cpp
│       ├── WelcomeScreen.hpp
│       └── WelcomeScreen.cpp
│
└── build/
    └── generated build files
```

---

# 2. File Responsibilities

| File | Responsibility |
|---|---|
| `main.cpp` | Program entry point |
| `Application.hpp` | Declares custom Application class |
| `Application.cpp` | Implements global application behavior |
| `Theme.hpp` | Shared visual constants |
| `MainWindow.hpp` | Declares main application window |
| `MainWindow.cpp` | Implements main window |
| `WelcomeScreen.hpp` | Declares WelcomeScreen |
| `WelcomeScreen.cpp` | Builds WelcomeScreen UI |
| `CMakeLists.txt` | Build configuration |
| `assets/` | Images/icons/etc. |
| `docs/` | Documentation |
| `build/` | Generated build output |

---

# 3. Runtime Hierarchy

```text
main()
│
├── Application
│     │
│     └── QApplication
│
├── MainWindow
│     │
│     └── QMainWindow
│           │
│           └── WelcomeScreen
│                 │
│                 └── QWidget
│                       │
│                       └── QVBoxLayout
│                             ├── Title
│                             ├── Subtitle
│                             └── Button Layout
│                                   ├── Open Vault
│                                   └── New Vault
│
└── app.exec()
      │
      └── Event Loop
```

---

# 4. C++ Inheritance

Inheritance means **IS-A**.

```text
QApplication
    ▲
    │
Application
```

Application IS-A QApplication.

```text
QMainWindow
    ▲
    │
MainWindow
```

MainWindow IS-A QMainWindow.

```text
QWidget
    ▲
    │
WelcomeScreen
```

WelcomeScreen IS-A QWidget.

---

# 5. Object Ownership

Ownership means **HAS-A / OWNS**.

```text
MainWindow
    │
    └── owns → WelcomeScreen
```

Created with:

```cpp
m_welcomeScreen = new WelcomeScreen(this);
```

`this` is the current MainWindow.

Qt's parent-child system manages the child's lifetime.

---

# 6. Application

```cpp
class Application : public QApplication
{
public:
    Application(int& argc, char** argv);
};
```

Application extends QApplication with NoteForge-specific behavior.

Current responsibility:

- Initialize QApplication
- Set application-wide font

```cpp
QFont appFont("JetBrains Mono Nerd Font");
appFont.setPointSize(11);

setFont(appFont);
```

---

# 7. MainWindow

MainWindow extends QMainWindow.

Responsibilities:

- Window title
- Window size
- Main window structure
- Decide which central screen is displayed

It does NOT build the contents of WelcomeScreen.

---

# 8. WelcomeScreen

WelcomeScreen extends QWidget.

Responsibilities:

- Build welcome screen UI
- Create labels
- Create buttons
- Create layouts
- Handle welcome-screen behavior

Current structure:

```text
WelcomeScreen
│
└── QVBoxLayout
      │
      ├── Title
      ├── Subtitle
      │
      └── Button QVBoxLayout
            ├── Open Vault
            └── New Vault
```

---

# 9. Theme

Theme is a namespace, not a class.

It stores shared design constants.

```cpp
namespace Theme
{
    inline constexpr int TitleFontSize = 28;
    inline constexpr int SubtitleFontSize = 13;
    inline constexpr int ButtonWidth = 220;
    inline constexpr int ButtonHeight = 42;
    inline constexpr int WindowPadding = 40;
    inline constexpr int Spacing = 16;
}
```

Usage:

```cpp
Theme::Spacing
Theme::ButtonWidth
Theme::TitleFontSize
```

Theme does not own widgets.

Widgets use Theme values.

---

# 10. Three Different Hierarchies

## C++ Inheritance

Answers:

> What IS this?

```text
QWidget
  ▲
  │
WelcomeScreen
```

---

## Object Ownership

Answers:

> What does this object OWN?

```text
MainWindow
  │
  └── WelcomeScreen
```

---

## Responsibility

Answers:

> Who is responsible for this?

```text
main.cpp
    → starts program

Application
    → application-wide behavior

MainWindow
    → window-level structure

WelcomeScreen
    → welcome UI

Theme
    → shared visual constants
```

These are different concepts.

---

# 11. Startup Sequence

```text
main()
  │
  ▼
Application app
  │
  ▼
QApplication initialized
  │
  ▼
Global application font configured
  │
  ▼
MainWindow window
  │
  ▼
QMainWindow initialized
  │
  ▼
WelcomeScreen created
  │
  ▼
WelcomeScreen UI constructed
  │
  ▼
window.show()
  │
  ▼
app.exec()
  │
  ▼
Event Loop
```

---

# 12. Important Concepts Learned

- Classes and objects
- Stack vs heap
- RAII
- Ownership
- Memory leaks
- Pointers
- `this`
- Dereferencing
- `->`
- `.`
- `::`
- Namespaces
- Inheritance
- Constructors
- Destructors
- Initializer lists
- Forward declarations
- QApplication
- QMainWindow
- QWidget
- Central widgets
- Layouts
- Nested layouts
- Stretch
- Alignment
- Application-wide font
- Theme/design constants
- Qt parent-child ownership
- Event loop
- Signals and slots

---

# 13. Golden Mental Model

When adding something new, ask:

### What is it?

Is it a:

- class?
- object?
- widget?
- layout?
- constant?
- service?

### Who owns it?

Which object should control its lifetime?

### Who is it responsible for?

Which class should contain the logic?

### Is it inheritance or ownership?

```text
IS-A → inheritance
HAS-A → ownership
```

### Does it need to survive?

If only needed temporarily:

```cpp
auto* something = ...
```

If it must be accessed later:

```cpp
m_something
```

---

# 14. Current Architecture

```text
                     NoteForge
                         │
              ┌──────────┴──────────┐
              │                     │
         Application             MainWindow
              │                     │
              ▼                     ▼
        QApplication            QMainWindow
              │                     │
              │                  owns
              │                     │
              │                     ▼
              │               WelcomeScreen
              │                     │
              │                  QWidget
              │                     │
              │                  contains
              │                     ▼
              │                QVBoxLayout
              │                     │
              │          ┌──────────┼──────────┐
              │          │          │          │
              │        Title     Subtitle   Buttons
              │                                │
              │                           ┌────┴────┐
              │                           │         │
              │                         Open      New
              │
              └────── global font ───────►
```

---

# Next

After this map is understood, continue with:

[[29 - Qt Style Sheets]]