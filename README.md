# QT QML Udemy course

Qt is not a programming language on its own. It is a framework written in C++. 
A preprocessor, the MOC (Meta-Object Compiler), is used to extend the C++ language with features like signals and slots.


*Kit selection in QT creator is a platform you want to deploy on*

As you more files into the project you will see more source here:
```
SOURCES += \
        main.cpp
```

in qDebug let's us print things
```
qInfo() 
```

QCoreApplication is used here to build the executable.

# Priting Methods
```
    QCoreApplication a(argc, argv); //Instance of QCodeApplication object "a"

    qInfo()<< "Hello QT world"; //1st way
    qInfo("Hello World again"); //2nd way

    return a.exec(); 

```
*return statement* in int main() takes care of the error codes.


## Different ways

```
    qInfo() << "A simple print"; //users can see this as well
    qDebug() << "Debug print"; //generally for programmers
    qCritical() << "Critical Print"; //let both users and programmer know about this
    qFatal("Exits the program after printing");
```

qDebug is for debuging purpose only and not for the users. It supports QT classes well and doesn't need any conversion like std::cout would need

cerr is for standard error and cout is for standard output


# Classes

## QObject
1. The Q_OBJECT macro must appear in the private section of a class definition that declares its own signals and slots or that uses other services provided by Qt's meta-object system.
2. The Q_OBJECT macro inside the private section of the class declaration is used to enable meta-object features, such as dynamic properties, signals, and slots.
3. NOTE : This macro requires the class to be a subclass of QObject. Use Q_GADGET or Q_GADGET_EXPORT instead of Q_OBJECT to enable the meta object system's support for enums in a class that is not a QObject subclass


## Copying Objects
QObject doesn't like to get copied and by default all the copy constructors and assignment constructors are deleted.
They are done with the macro:
```
Q_DISABLE_COPY(QObject)
```
In order to still work with them, you need to define them explicitly (in actual you need to define them).

# Meta Object Compiler
Qt's meta-object system provides the signals and slots mechanism for inter-object communication, run-time type information, and the dynamic property system.

1. The QObject class provides a base class for objects that can take advantage of the meta-object system.
2. The Q_OBJECT macro inside the private section of the class declaration is used to enable meta-object features, such as dynamic properties, signals, and slots.
3. The Meta-Object Compiler (moc) supplies each QObject subclass with the necessary code to implement meta-object features.
4. The moc tool reads a C++ source file. If it finds one or more class declarations that contain the Q_OBJECT macro, it produces another C++ source file which contains the meta-object code for each of those classes. This generated source file is either #include'd into the class's source file or, more usually, compiled and linked with the class's implementation.

