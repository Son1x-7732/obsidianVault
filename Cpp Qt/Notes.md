# Here is the rule you'll follow throughout NoteForge

|Object|Owner|How we create it|
|---|---|---|
|`Application`|Stack|`Application app;`|
|`QApplication`|Stack|`QApplication app(argc, argv);`|
|`MainWindow`|Stack (initially)|`MainWindow window;`|
|`QTextEdit` inside MainWindow|Qt parent|`new QTextEdit(this);` or `setCentralWidget(...)`|
|`Database`|Our code|`std::make_unique<Database>()`|
|`VaultManager`|Our code|`std::make_unique<VaultManager>()`|
|`SearchEngine`|Our code|`std::make_unique<SearchEngine>()`|

Notice the pattern: **GUI widgets usually participate in Qt's parent-child ownership model, while non-GUI components use modern C++ ownership (`std::unique_ptr`) unless there's a specific reason not to.**