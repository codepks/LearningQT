# QT Udemy course

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

> Significance

1. It generates the code for connecting signals and slots
2. It takes care of needed runtime information like dynamic_casting - qObject_cast
3. IT takes care of language translations

> Note

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

## Easy Example

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

> NOTE
 
1. If put in the same main file then this gives strange moc file errors
2. emitter function and slot function should be of the same function (the ones you put in the QObject::connect()
3. Only slot implementation is required, singla implementataion is taken care by QT


## One to Many Connection
Example of Radio and Channels. 
1. We will use QueuedConnection and Application Quit over here.
2. Demo One to May Connection
3. How to Connect and Disconect Signals and Slots


> Radio Files

```
//radio.h
class radio : public QObject
{
    Q_OBJECT
public:
    radio(QObject * parent = nullptr);

    void tuneFrequencyTo(float frequency);
    void turnOffRadio();


signals:
    void connectTo(float frequency);
    void turningOff();  //Mean to quit the application
};
```
```
//radio.cpp
radio::radio(QObject * parent) : QObject(parent)
{

}

void radio::tuneFrequencyTo(float frequency)
{
    emit connectTo(frequency);
}

void radio::turnOffRadio()
{
    qInfo() << "Turning off the Radio";
    emit turningOff();
}
```

>  Channel Files

```
class channels : public QObject
{
    Q_OBJECT
public:
    channels(QObject * parent = nullptr, QString channelName = "empty", float channelFrequency = 98.3);

public slots:
    void connectedToChannel(float frequency);

private:
    QString channelName;
    float channelFrequency;

};
```

```
channels::channels(QObject * parent, QString channelName, float channelFrequency) : QObject(parent)
{
    this->channelName = channelName;
    this->channelFrequency = channelFrequency;
}

void channels::connectedToChannel(float frequency)
{
    if(frequency == this->channelFrequency)
        qInfo() << "\nYou are listerning to : "<< this->channelName;
}
```


> Implementation with Connect and Disconnect

```

int main(int argc, char *argv[])
{
    QCoreApplication a(argc, argv);

    //created a radio object
    radio *myRadio = new radio();

    //creating different channels
    channels *radioMirchi = new channels(&a, "Radio Mirchi", 93.5);
    channels *radioCity = new channels(&a, "Radio City", 91.0);
    channels *redFM = new channels(&a, "Red FM", 98.3);

    //establish connection between radio and the channels
    myRadio->connect(myRadio, &radio::connectTo, radioMirchi, &channels::connectedToChannel);
    myRadio->connect(myRadio, &radio::connectTo, radioCity, &channels::connectedToChannel);
    myRadio->connect(myRadio, &radio::connectTo, redFM, &channels::connectedToChannel);

    //establish connection of radio to quit the application
    myRadio->connect(myRadio, &radio::turningOff, &a, &QCoreApplication::quit, Qt::QueuedConnection);

//===============Tune to required frequency and start listing==============
    myRadio->tuneFrequencyTo(98.3);

    //Disconnecting Signals and Slots
    myRadio->disconnect(myRadio, &radio::connectTo, radioMirchi, &channels::connectedToChannel);
    myRadio->disconnect(myRadio, &radio::connectTo, radioCity, &channels::connectedToChannel);
 //=========================Turn off the radio=========================

    myRadio->turnOffRadio();

    return a.exec(); 

}
```


> NOTE

1. QCoreApplication enters the event loop after a.exec() only, that's why calling QCoreApplicatio::quit() via myRadio->turnOffRadio() is senseless as there is no event loop to quit which is what happens in DirectConnection.
2. If you use QueuedConnection, it will be picked up once the event loop starts and will be executed later. Also it is a good practice to do so.
*source : https://stackoverflow.com/questions/19141910/qcoreapplication-ignores-quit-signal-and-hangs*

## QProperty
1. Here we will use Q_Property to send a signal instead of using a function directly
2. Q_PROPERTY is generally used in qml

> EMITTER FILE

```
//emitter.h
class Emiiter : public QObject{
    Q_OBJECT
public:
    Emiiter(QObject* parent = nullptr);
    Q_PROPERTY(QString message READ getMessage WRITE setMessage NOTIFY messageChanged)
    QString getMessage();
    void setMessage(QString message);

signals:
    void messageChanged(QString message);

private:
    QString message_;
};
```

```
//emitter.cpp
Emiiter::Emiiter(QObject* parent) : QObject(parent) { }

QString Emiiter::getMessage() {  return message_; }

void Emiiter::setMessage(QString  message){
    this->message_ = message;
    emit messageChanged(message);
}
```

> RECIEVER FILE

```
//receiver.h
class Receiver : public QObject{
    Q_OBJECT
public:
    Receiver(QObject* parent = nullptr);

public slots:
    void getMessage(QString message);
};
```

```
//receiver.cpp
Receiver::Receiver(QObject* parent) : QObject(parent) { }

void Receiver::getMessage(QString message) { qInfo() << message; }
```

> MAIN FILE

```
int main(int argc, char *argv[])
{
    QCoreApplication a(argc, argv);

    Emiiter *emitter = new Emiiter();
    Receiver *receiver = new Receiver();

    QObject::connect(emitter, &Emiiter::messageChanged, receiver, &Receiver::getMessage);

    //instead of using a function here to call the eisngla, we will use Q_PROPERTY

    emitter->setMessage("Sending a signal");

    QString str= "Sending another Signal";
    emitter->setProperty("message", QVariant(str));


    return a.exec();
}
```

# Casting

Implicit conversion means the conversions that compiler is doing for you.

qobject_cast doesn't work with C++ RTTI.
It is only applicable for QObjects.
With qobject_cast from base to derived and derived to base easily.
```
car* mycar = qobject_cast<car*> racecar;
```
Note: To avoid warning on unused variable, use 
```
Q_UNUSED(variable)
```

# Error Handling

The given syntax of catch given below is generally not recommended as it doesn't work most of the times:
```
catch (...)
```

## Generic try and catch mechanism

TIP : Always narrow down you erro handling with different catch statements
```
void divide(int num){
    try    {
        if(num == 0)
            throw "Cannot divide by 0";
        else if ( num > 5)
            throw 99;
        else if ( num == 1)
            throw std::runtime_error("Should be greater than 1");

        int result = 10 / num;
        Q_UNUSED(result);
    }
    catch (int a)    { qInfo() << "Error is an int : " << a; }
    catch (char* c)  { qInfo() << "Erro is a string : " << c;}
    catch (std::exception & e) { qInfo() << "Error is a standard one : " << e.what(); }
}
```

## qWarning()

```
void divide(int num){
    if(num == 0)
        qWarning() << "Error is an int : ";
    else if ( num > 5)
        qWarning() << "Error is an int : 99 ";
    else if ( num == 1)
        qWarning() << " It isshoudl not be 1";

    int result = 10 / num;
    qInfo() << "results are : " << result;
}
```

# Custom Handling

Making an std::exception structure
```
struct MyIssue : public std::exception //what () is a virtual function
{
    const char* description;
    const char* what() const throw() //in place of noexcept we can throw    {
        return "Yaiikks, your program crashsed";
    }
};

void divide(int num){
    MyIssue e;
    try    {
        if(num == 0)        {
            e.description = "Cannot divide by 0";
            throw e;
        }
        int result = 10 / num;
        Q_UNUSED(result);
    }
    catch (int a)    { qInfo() << "Error is an int : " << a; }
}
```

# QT Classes

## QInt
qint8, qint16, qint32, qint64 are guaranteed to be 8 bit, 16 bits, 32 bits and 64 bits on any platform be it 32 bit or 64 bit operating system.

qintptr is the pointer pointing to intergers, and it could be 32 bit or 64 bit depending on the OS.

## QString
> Some usages

```
QString name = "Prashant Sharma";
qInfo() << name.mid(2,3);
qInfo() << name.insert(0,"Mr. ");
qInfo() << name.split(" ");

int index = name.indexOf(".");
qInfo() << name.remove(0, index + 1).trimmed();

QString title = "SDE 2";
QString full = name.tr
```

> Output

```
"ash"
"Mr. Prashant Sharma"
QList("Mr.", "Prashant", "Sharma")
"Prashant Sharma"
"Prashant Sharma SDE 2"
```
## QDate , QTime

```
QDate date = QDate::currentDate();
qInfo() << date;

QTime time1 = QTime::currentTime();
qInfo() << time1;

QTime time2 = time1.addSecs(100);

if(time2 > time1)
qInfo() << "Time is ticking";
```

## QByteArray

Used well in socket programming

## QVariant

> Fundamental Datatypes
```
    // Way 1
    qint8 val1 = 2;
    QVariant value = val1;
    qInfo() << value;

    // Way 2
    QVariant value2;
    value2.setValue(val1);
    qInfo() << value2;

    //Retrieving value
    int retrieve = value.value<qint8>();
    qInfo() << "Retieved value is ; " << retrieve;

    //Checking if it is int
    bool ok = false;
    value.toInt(&ok);

    ok ? qInfo() << "TRUE" : qInfo() << "FALSE";
```

> Custom Datatypes

To use the custom data types or structure with QVariant one has to register the structure with **Q_DECLARE_METATYPE.**

```
struct CustomStruct{
    int a = 10;
    float b = 30.2;
    QString name = "Prashant";
};

Q_DECLARE_METATYPE(CustomStruct) //Required for line 602

int main(int argc, char *argv[])
{
    QCoreApplication a(argc, argv);
    CustomStruct struc{20,40, "variant"};
    QVariant variant;
    //variant = struc; //this doesn't work
    variant.setValue(struc);

    qInfo()  << variant.value<CustomStruct>().a; 
    return a.exec();
}
```

## QStringList

It is list of string with a lot of functionalities.
```
    QStringList lst;
    lst.append("Hi");
    lst.append("Prashant");
    lst.append("here");
    qInfo() << lst;

    QString str = "here";
    int index = lst.indexOf(str);
    qInfo() << str << "is located at : " << index;
```

## QList<typename>

QList is more versatile and can be used with different datatypes.
```
    QList<int> ab {1,2,3,4};

    foreach (int var, ab) { qInfo() << var;}

    std::list<int> bc {1,2,3,4,5};
    qInfo() << bc;   //this also works
```

## QVector<typename>

QVector should be prefferred over QList as Qvector is a sequential memory and QList used more of heap memory.
QList is closer to boost::ptr_deque, despite the apparent association with std::list. It does not store objects directly, but instead stores pointers to them. 
> Advantage

You gain all the benefits of quick insertions at both ends, and reallocations involve shuffling pointers instead of copy constructors

> Disadvantage  

You lose the spacial locality of an actual std::deque or std::vector, and gain a lot of heap allocations.

> BUT

 The QT library heavily favors the use of QList objects

> So

It is suggested using QList until profiling suggests changing to a QVector

source : https://stackoverflow.com/questions/6602036/qvector-vs-qlist
