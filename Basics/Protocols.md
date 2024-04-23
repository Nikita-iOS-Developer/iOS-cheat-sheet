# Protocols

Протоколы - это набор методов и свойств без реализаций. Протокол - это тип (как класс, структура, перечисление).
Класс, структура и перечисление, который соответствует протоколу, обязан реализовать все свойства и методы что прописаны в нем (в Swift).

Пример объявления протокола:

```
protocol SomeProtocol {
    var someProperty: Int {get set}
    func aMethod(arg1: Double, anotherArgument: String) -> SomeType
    mutating func changeIt()
    init(arg: Type)
}
```

В примере выше мы можем установить только get для SomeProperty тогда оно не обязано быть устанавливаемым. someProperty может быть как stored так и computed property, главное чтобы был установлен геттер.
Указываем mutating если метод будет менять сам объект, в таком случае нам не придется указывать это в классах, но обязательно будет в структурах и перечислениях (при автозаполнении в структуре иксКод автоматически подставит слово mutating в метод).
Так же мы можем указать в протоколах свойство и метод типа (static) и все виды инициализаторов.
Протоколы могут наследоваться от других протоколов. Каждый кто реализует протокол, который наследуется от другого протокола, должен также реализовать все свойства, методы и т.д от родительских протоколов.

Пример работы с Protocol:

```
protocol Moveable {
    mutating func move(to point: CGPoint)
}
class Car: Moveable {
    func move(to point: CGPoint) { print("car is moving") }
    func changeOil() { print("Oil has been changed") }
}
struct Shape: Moveable {
    mutating func move(to point: CGPoint) { print("Shape is moving") }
    func draw() { print("Shape has been draw") }
}

var prius: Car = Car()
var square: Shape = Shape()

var thingToMove: Moveable = prius
thingToMove.move(to: CGPoint.zero)
thingToMove = square
thingToMove.move(to: CGPoint.zero)
let thingsToMove: [Moveable] = [prius, square]
```

Мы не можем создать объект протокола Moveable так как протоколы это только описание, но мы можем объявить переменную типа Moveable и присвоить в эту переменную абсолютно любой тип, объект, который этому протоколу соответствует. Это показанно в примере выше в переменной thingToMove. 

## Протоколы как тип данных

Рассмотрим пример:

```
protocol Drive {
    var speed: Double { get }
    func drive()
}

class Person: Drive {
    private (set) var speed: Double = 50 //Сеттер будет недоступен в экземпляре класса Person
    
    func drive() {
        print("Drive!")
    }
    var name: String = "Ivan"
}

//1
var person = Person
person.speed
person,drive()
person.name

//2
var person: Drive = Person
person.speed
person.drive
person.name //Ошибка так как в протоколе Drive нет описанного свойства name
```

В примере выше под номером 1 экземпляр типа Person person полностью соответствует протоколу Drive это значит что все методы и свойства описанные в протокле Drive и в классе Ptrson будут доступны в экземпляре person.
В примере под номером 2 переменная person имеет тип данных Drive и ссылается на класс Person. Так как переменная person имеет тип данных Drive то она сможет получить доступ только к тем свойствам и методам что имеет  протокол Drive. Именно поэтому у нас будет ошибка так как свойство name описанно в типе данных Person, а присвоить Person мы смогли Drive так как Person соответствует протоколу Drive.
Так как мы можем создать переменную типа Drive, то мы можем создать и массив который содержит тип Drive, функцию которая принимает или возвращает тип Drive.

## Наследование в протоколах

Наследование в протоколах похожи на наследования в классах. Наш класс может наследоваться только от одного класса, но класс может соответствовать множеству протоколов. Принято осуществлять реализацию протоколов в расширении.
Рассмотрим пример:

```
protocol Drive {
    var speed: Double { get }
    func drive()
}
protocol Bicycle: Drive {}
protocol Car: Drive {}

class Person {
    var name: String = "Tom"
}

extension Person: Bicycle, Car {
    var speed: Double {
        50
    }
    
    func drive() {
        print("Drive!")
    }
}
```

Когда мы наследуемся в протоколах это можно воспринимать как дополнение протокола. Еще наследование в протоколах можно воспринимать как разбиение интерфейса. Привидем пример:

```
//Общее описание протокола Водить
protocol Drive {
    var speed: Double { get }
    func drive()
}
///Дополняем протокол Drive и разбиваем интерфейс (лицензия водителя в протоколе Водитель)
protocol Driver: Drive {
    var driverLicense: String { get }
}
```

В примере выше мы теперь можем подписать класс, перечисление или структуру на протокол Drive что даст нам общие характеристики, или подписаться под протокол Driver что позволит нам уже точно описать данный класс, перечисление или структуру. Этот метод наследования в протоколах дает нам гибкость.  

## Протоколы для классов

Есть некоторые моменты когда протокол должен работать только на value type или reference type. Для этого мы можем обозначить протокол что он может работать только с reference type. Чтобы обозначить что протокол может работать только с классами, нам нужно написать слов AnyObject:

```
protocol Drive: AnyObject {
    var speed: Double { get }
    func drive()
}
```
 
## is и as в протоколах

Мы можем конвертировать тип из протокола в тип класса. Рассмотрим пример:

```
protocol Drive {
    var speed: Double { get }
    func drive()
}
protocol Bicycle: Drive {}
protocol Car: Drive {}

class Person {
    var name: String = "Tom"
}

extension Person: Bicycle, Car {
    var speed: Double {
        50
    }
    
    func drive() {
        print("Drive!")
    }
}

var person: Bicycle = Person()
var person2: Person? = person as? Person
```

В примере выше мы используем ключевое слово as чтобы ковертировать в тип Person. Без as будет ошибка так как под протоколом Bicycle может быть структура.
Мы так же можем проверить соответствует ли тип какому либо протоколу с помощью ключевого слова is:

```
var person: Bicycle = Person()
person is Drive //true
```


## Расширения в протоколах

Расширения в структурах, перечислениях и классах нужны в основном для того, чтобы расширить их функциональность самих типов данных и вынести эту фанкциональность за пределы этих типов. Также можно использовать расширения для разбития кода на логические части.
Расширения в протоколах нужны для того, чтобы объявить в расширении реализацию этого метода.
Рассмотрим пример:

```
protocol Drive {
    var speed: Double { get }
    func drive()
}
protocol Bicycle: Drive {}
protocol Car: Drive {
    var driverLicense: String { get }
}

extension Car {
    var driverLicense: String {
        "No driver license"
    }
}
class Person {
    var name: String = "Tom"
}

extension Person: Car {
    var speed: Double {
        50
    }
    
    func drive() {
        print("Drive!")
    }
}
```
В примере выше наш класс Person подписан под протокол Car, при этом протокол не требует реализации свойства driverLicense в классе Person, так как мы реализовали это свойство в расширении протокола.
Когда мы объявляем расширения протокола с реализацией, это значит, что любой кто подпишется на этот протокол по умолчанию сразу же получит реализацию. Мы можем реализовывать в расширении неограниченное количество свойств и методов. Так же мы можем осуществлять реализацию в расширении родительских свойств и методов в дочернем протоколе.
Когда мы подписываем тип данных под протокол с реализованными свойствами или методами, мы без проблем можем изменить реализованные свойства и методы в структуре, перечислении или классе:

```
protocol Drive {
    var speed: Double { get }
    func drive()
}
protocol Bicycle: Drive {}
protocol Car: Drive {
    var driverLicense: String { get }
}

extension Car {
    var driverLicense: String {
        "No driver license"
    }
}
class Person {
    var name: String = "Tom"
}

extension Person: Car {
    var driverLicense: String {
        "Has license"
    }
    var speed: Double {
        50
    }
    
    func drive() {
        print("Drive!")
    }
}
```

## Diamond problem

Проблема Diamond (ромбовидной) иерархии возникает, когда у нас есть иерархия протоколов, где один класс подписан от двух разных протоколов, которые в свою очередь наследуются от общего родителя и у этих протоколов есть реализация их свойств и методов. Это может привести к неоднозначности при вызове методов или доступе к свойствам.
Рассмотрим пример:

```
protocol Bicycle {
    var wheels: Int { get }
}

protocol Car {
    var wheels: Int { get }
}
extension Bicycle {
    var wheels: Int { 2 }
}

extension Car {
    var wheels: Int { 4 }
}

class Transport {}

extension Transport: Car, Bicycle {
    //Ошибка! Тип Transport не соответствует протоколам! 
}
```

В примере выше у нас оба протокола имеют реализацию и когда мы подписали наш класс на эти два протокола то Swift не знает какое свойство wheels ему реализовать в классе. Для того чтобы решить эту проблему, нужно просто переопределить в классе свойство wheels:

```
extension Transport: Car, Bicycle {
    var wheels: Int { 4 }
}
```
