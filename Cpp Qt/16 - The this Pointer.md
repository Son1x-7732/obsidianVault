# The `this` Pointer

---

## Definition

`this` is a pointer that refers to the current object.

Inside a member function, `this` points to the object that called the function.

---

## Example

```cpp
class MainWindow
{
public:
    MainWindow();
};
```

Inside:

```cpp
MainWindow::MainWindow()
```

`this` points to the current `MainWindow` object being constructed.

---

## Why is it useful?

It allows an object to refer to itself.

Example:

```cpp
connect(button,
        &QPushButton::clicked,
        this,
        ...);
```

Here, `this` means:

"Deliver the signal to this MainWindow."

---

## Key Takeaways

- `this` is automatically available inside member functions.
- `this` is a pointer.
- It always refers to the current object.

---

## Previous

[[15 - connect()]]

## Next

[[17 - Composition]]