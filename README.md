# LearningQTQML

Qt is not a programming language on its own. It is a framework written in C++. 
A preprocessor, the MOC (Meta-Object Compiler), is used to extend the C++ language with features like signals and slots.

Although any build system can be used with Qt, Qt brings its own qmake


sources : https://wiki.qt.io/About_Qt

All librarbry : https://doc.qt.io/all-topics.html
QML           : https://doc.qt.io/qt-6/qtqml-index.html
QT QML        : https://doc.qt.io/qt-6/qtqml-index.html

QML classes used byt Renamp:
QJSEngine
QQmlApplicationEngine
QQmlComponent
QQmlContext
QQmlEngine
QQmlEngineExtensionPlugin
QQmlExtensionPlugin
QQmlFileSelector

//=============================Udemy Course=

1. Kit selection in QT creator is a platform you want to deploy on


CODE
We include <QTCoreApplication>

As you more files into the project you will see more source here:
SOURCES += \
        main.cpp

======
qInfo() in qDebug let's us print things

QCoreApplication is used here to build the executable.

```
    QCoreApplication a(argc, argv); //Instance of QCodeApplication object "a"

    qInfo()<< "Hello QT world";
OR
    qInfor("Hello World again");

    return a.exec(); 

```
=======
return statement in int ain() takes care of the error codes.


