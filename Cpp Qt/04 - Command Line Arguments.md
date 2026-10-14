# Command Line Arguments

---

## Definition

Command line arguments are values passed to a program by the operating system when the program starts.

They allow the user (or another program) to modify the application's behaviour without changing its code.

---

## argc

`argc` stands for **Argument Count**.

It stores the total number of command line arguments.

---

## argv

`argv` stands for **Argument Vector**.

It is an array of C-style strings (`char*`) containing every command line argument.

---

## Relationship

Example:

```bash
./NoteForge myVault --debug
```

The operating system passes:

```text
argc = 3

argv[0] = "./NoteForge"
argv[1] = "myVault"
argv[2] = "--debug"
```

---

## Why Qt uses them

Qt supports its own command line options such as:

- platform selection
- display settings
- debugging options
- plugin loading

`QApplication` reads these arguments during startup.

---

## Key Takeaways

- `argc` = number of arguments.
- `argv` = list of arguments.
- They are provided by the operating system.
- Qt uses them to configure itself before creating any windows.

---
---

## Common Mistakes

### Accessing arguments without checking `argc`

```cpp
std::cout << argv[1];
```

If the program is started without additional arguments, `argv[1]` does not exist.

Always check:

```cpp
if (argc > 1)
{
    std::cout << argv[1];
}
```

Accessing an argument beyond `argc - 1` results in **Undefined Behaviour**.
## Previous

[[03 - Classes and Objects]]

## Next

[[05 - QApplication]]