# Pointers

---

## Definition

A pointer is a variable that stores the memory address of another object.

It does not store the object itself.

---

## Objects vs Pointers

Object

```cpp
QLabel label;
```

Stores the actual object.

Pointer

```cpp
QLabel* label;
```

Stores the address of a QLabel object.

---

## Why use pointers?

Pointers allow:

- Dynamic memory allocation
- Object ownership
- Sharing objects
- Polymorphism
- Efficient parameter passing

---

## Common Operators

&

Address-of operator

Obtains an object's memory address.

*

Pointer declaration

or

Dereference operator

Accesses the object pointed to.

->

Calls a member function through a pointer.

.

Calls a member function on an object.

---

## nullptr

Represents a pointer that points to nothing.

---

## Key Takeaways

- Objects store data.
- Pointers store addresses.
- new returns a pointer.
- delete destroys the object pointed to.
- Qt uses pointers heavily because widgets have dynamic lifetimes.

---

## Previous

[[18 - Custom Widgets]]

## Next

[[20 - WelcomeScreen]]