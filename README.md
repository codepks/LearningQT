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
In order to still work with them, you need to define copy constructors explicitly (in actual you need to define them).

## Inheritance
Q_OBJECT doesn't allow multiple inheritance, it gives error moc files.
Multiple inheritance with Interfaces is allowed though.

*TIP : Don't alays trust memoery locations when working with stack as lot of optimizations happen around here".f

# Memory Management

> Parent object deletes the whole child objects

Suppose if you have message box which has OK and Cancel buttons, these buttons have set message box as their parent.
Now, it is enough to delete the parent message box and eventually it will delete it children elements (the buttons too).
This is an obvious behaviour.

```
MainWindow* window = new QMainWindow;
QWidget* widget = new QWidget; //widget has no owner
window->setCentralWidget(widget)
```
In the code above, deleting the window with delete the child widget.

>No need of Virtual destructor

If one class somewhere in the inheritance tree has a **virtual destructor**, every child class below will have a virtual destructor. In the case of a child class of QObject and deleting a QObject pointer to an instance of that child class, there is never an issue, even if you forget the virtual keyword in that subclass's destructor declaration.

> source : https://doc.qt.io/qt-5/objecttrees.html

When a QObject child object is deleted with a parent QObject, then it will remove itself from the parent object.
When a QObject parent object then all of its child objects are deleted.

**NOTE : Always prefer parent child relationship for better memory maangement**

# Meta Object Compiler
Qt's meta-object system provides the signals and slots mechanism for inter-object communication, run-time type information, and the dynamic property system.

1. The QObject class provides a base class for objects that can take advantage of the meta-object system.
2. The Q_OBJECT macro inside the private section of the class declaration is used to enable meta-object features, such as dynamic properties, signals, and slots.
3. The Meta-Object Compiler (moc) supplies each QObject subclass with the necessary code to implement meta-object features.
4. The moc tool reads a C++ source file. If it finds one or more class declarations that contain the Q_OBJECT macro, it produces another C++ source file which contains the meta-object code for each of those classes. This generated source file is either #include'd into the class's source file or, more usually, compiled and linked with the class's implementation.



# Signals and Slots
Signal is a way of letting know to destination object that something happened.

A Signal can be connected to as many slots as possible and vice versa. It is only used for emission and cannot be implemented.

Slot : A public slot behaves like a normal function since it needs to be implemented too. A normal function can be put under public slots.

> SIGNAL FILES

```
//source.h
#ifndef SOURCE_H
#define SOURCE_H
#include <QObject>

class Source : public QObject{
    Q_OBJECT
public:
    Source();
    void test();
signals:
    void emitSignal();
};
#endif // SOURCE_H
```
```
//source.cpp
#include "source.h"

Source::Source() {}

void Source::test(){ emit emitSignal(); }
```

> SLOT FILES

```
//destination.h
#ifndef DESTINATION_H
#define DESTINATION_H
#include <QDebug>
#include <QObject>

class Destination : public QObject{
    Q_OBJECT
public:
    Destination();
public slots:
    void receiveSignal();
};

#endif // DESTINATION_H
```

```
//destination.cpp
#include "destination.h"

Destination::Destination() {}

void Destination::receiveSignal(){ qInfo() << "recieved signal"; }
```

> MAIN FILE

```
//main.cpp
#include <QCoreApplication>
#include <QDebug>
#include "source.h"
#include "destination.h"

int main(int argc, char *argv[]){
    QCoreApplication a(argc, argv); // Instance of QCoreApplication object "a"
    Source * emitter = new Source();
    Destination *receiver = new Destination();

    // way 1
    QObject::connect(emitter, &Source::emitSignal, receiver, &Destination::receiveSignal);
    // way 2
    QObject::connect(emitter, SIGNAL(emitSignal()), receiver, SLOT(receiveSignal()));
    // way 3
    emitter->connect(emitter, &Source::emitSignal, receiver, &Destination::receiveSignal);
    emitter->test(); // Emit the signal
    return a.exec(); // The instance is of an executable
}
```






