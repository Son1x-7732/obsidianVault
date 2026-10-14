From the project root:

```
rm -rf build
```

Then configure again:

```
cmake -S . \
      -B build \
      -G Ninja \
      -DCMAKE_EXPORT_COMPILE_COMMANDS=ON
```

Then:

```
cmake --build build
```

Now check:

```
ls build
```

You should see:

```
compile_commands.json
```



## Let's verify the generator

Run:

```
grep CMAKE_GENERATOR build/CMakeCache.txt
```

I expect it to print:

```
CMAKE_GENERATOR:INTERNAL=Unix Makefiles
```

instead of:

```
CMAKE_GENERATOR:INTERNAL=Ninja
```

---

# Why does `compile_commands.json` matter?

Think of `clangd` (the engine behind Zed's C++ support) as another compiler.

Your actual compiler knows:

```
-I/usr/include/qt6
-I/usr/include/qt6/QtWidgets
...
```

because CMake passes those include paths.

But `clangd` doesn't automatically know them.

It reads them from:

```
compile_commands.json
```

Without that file, `clangd` thinks:

> "I've never heard of `QMainWindow`."

So it highlights it in red, even though the project builds correctly.