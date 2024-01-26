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

#Priting Methods
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


