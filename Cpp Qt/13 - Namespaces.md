# Namespaces

---

## Definition

A namespace is a named scope used to organize related code and prevent naming conflicts.

---

## Why do namespaces exist?

Different libraries may define functions or classes with the same name.

Namespaces separate them.

---

## Examples

```cpp
std::string
```

`string` inside the Standard Library.

```cpp
Qt::AlignCenter
```

`AlignCenter` inside the Qt namespace.

---

## Scope Resolution Operator

The `::` operator accesses something inside a namespace or class.

Examples:

```cpp
std::cout
Qt::AlignCenter
MainWindow::MainWindow()
```

---

## Common Operators

| Operator | Used With | Example |
|-----------|-----------|---------|
| `.` | Object | `font.setBold(true)` |
| `->` | Pointer | `label->setText("Hello")` |
| `::` | Namespace/Class | `Qt::AlignCenter` |

---

## Key Takeaways

- Namespaces organize code.
- They prevent naming collisions.
- `::` accesses members inside a namespace or class.

---

## Previous

[[12 - QLabel]]

## Next

[[14 - Signals and Slots]]