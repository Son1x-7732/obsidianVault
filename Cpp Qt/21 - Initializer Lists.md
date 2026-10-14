# Initializer Lists

---

## Definition

An initializer list constructs base classes and member variables before the constructor body executes.

It is the preferred way to initialize objects in C++.

---

## Syntax

```cpp
ClassName::ClassName()
    : member(value),
      anotherMember(value)
{
}
```

---

## Initialization Order

1. Base classes
2. Member variables
3. Constructor body

---

## Why use Initializer Lists?

Objects are constructed directly with the desired values.

Without initializer lists, objects would first be default-constructed and then assigned, which can be less efficient or even impossible for some types.

---

## Example

```cpp
WelcomeScreen::WelcomeScreen(QWidget* parent)
    : QWidget(parent)
{
}
```

The base class `QWidget` is constructed first using the provided parent.

---

## Key Takeaways

- Initialization happens before the constructor body.
- Base classes are always initialized first.
- Prefer initializer lists for constructing members.

---

## Previous

[[20 - WelcomeScreen Widget]]

## Next

[[22 - Nested Layouts]]