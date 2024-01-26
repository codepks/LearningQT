# LearningQTQML

Qt is not a programming language on its own. It is a framework written in C++. 
A preprocessor, the MOC (Meta-Object Compiler), is used to extend the C++ language with features like signals and slots.

#Udemy Course

1. Kit selection in QT creator is a platform you want to deploy on

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

```
    QCoreApplication a(argc, argv); //Instance of QCodeApplication object "a"

    qInfo()<< "Hello QT world"; //1st way
    qInfor("Hello World again"); //2nd way

    return a.exec(); 

```
=======
return statement in int main() takes care of the error codes.


