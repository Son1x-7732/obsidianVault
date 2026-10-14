# Classes and Objects

## Class

A class is a blueprint.

It describes what an object looks like.

Example:

```cpp
class Car
{
};
```

The class itself is **not** a car.

It only describes one.

---

## Object

An object is an instance created from a class.

```cpp
Car myCar;
```

Now an actual object exists.

---

## Why use classes?

Instead of placing everything inside `main()`:

```text
main()
├── GUI
├── Database
├── Search
├── Theme
├── Files
└── Settings
```

We separate responsibilities.

```text
Application
├── Database
├── Theme
├── Vault
└── MainWindow
```

This makes software easier to maintain.

---

## Principle Learned

Classes organize software into logical responsibilities.