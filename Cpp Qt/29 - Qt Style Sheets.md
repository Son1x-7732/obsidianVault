````
# Qt Style Sheets (QSS)

Qt Style Sheets provide CSS-like styling for Qt widgets.

---

## Responsibility

QSS controls appearance.

C++ controls behavior.

```text
Widget
├── C++ → behavior
└── QSS → appearance
````

---

## Basic example

```
button->setStyleSheet(R"(
    QPushButton {
        border-radius: 8px;
        padding: 8px;
    }
)");
```

---

## States

QSS supports widget states such as:

```
QPushButton:hover
QPushButton:pressed
QPushButton:disabled
```

---

## Raw String Literals

C++ raw strings use:

```
R"(
    text
)"
```

They are useful for multi-line QSS because quotes and newlines don't need to be escaped.

---

## Architecture

Avoid duplicating styles:

```
openButton->setStyleSheet(...);
newButton->setStyleSheet(...);
```

Instead, shared styles should eventually live in the theme system.

```
Theme
├── Dimensions
├── Typography
├── Colors
└── Styles
```

---

Previous

[[Layout - Spacing and Margins]]

Next

[[29 - QSS Selectors and States]]