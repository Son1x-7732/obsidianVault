# NoteForge

## Objective

Build a professional, cross-platform knowledge management application using Modern C++ and Qt.

The goal of this project is not only to create a notes application but also to learn software engineering principles used in professional desktop applications.

---

## Tech Stack

- Language: C++20
- GUI Framework: Qt6 (Widgets initially)
- Build System: CMake
- Build Backend: Ninja
- Compiler: GCC
- IDE: Zed
- Database: SQLite (later)
- Markdown: md4c (later)
- Search: SQLite FTS5 / Tantivy alternative (later)
- Version Control: Git

---

## Learning Philosophy

Every concept should be understood before writing code.

Each topic follows:

Concept

↓

Real-world Analogy

↓

Computer Science Explanation

↓

C++ Explanation

↓

Implementation

No code will be copied without understanding why it exists.

---

## Engineering Principles

- Prefer readability over cleverness.
- One responsibility per class.
- Prefer stack allocation whenever possible.
- Use RAII.
- Avoid raw `new` unless ownership is clearly transferred.
- Build incrementally.