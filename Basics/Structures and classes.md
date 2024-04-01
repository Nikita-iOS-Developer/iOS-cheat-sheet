# Структуры и классы

## Сравнение классов и структур

Классы и структуры в Swift имеют много общего. И в классах и в структурах можно:

* Объявлять свойства для хранения значений
* Объявлять методы, чтобы обеспечить функциональность
* Объявлять индексы, чтобы обеспечить доступ к их значениям, через синтаксис индексов
* Объявлять инициализаторы, чтобы установить их первоначальное состояние
* Они оба могут быть расширены, чтобы расширить их функционал за пределами стандартной реализации
* Они оба могут соответствовать протоколам, для обеспечения стандартной функциональности определенного типа

Классы имеют дополнительные возможности, которых нет у структур:

* Наследование позволяет одному классу наследовать характеристики другого
* Приведение типов позволяет проверить и интерпретировать тип экземпляра класса в процессе выполнения
* Деинициализаторы позволяют экземпляру класса освободить любые ресурсы, которые он использовал
* Подсчет ссылок допускает более чем одну ссылку на экземпляр класса.

# Классы

## Синтаксис класса

Класс является абстрактной конструкцией, на основе которой можно создать объект. Создадим абстрактный класс Dog, в котором будут свойства, описывающие характеристики собаки:

```
class Dog {
    var name = ""
    var breed = ""
    var tailLength = 0
}
//Это не сама собака, а только шаблон, на основе которого мы создадим конкретную собаку.
```

У абстрактной собаки есть имя, порода и длина хвоста. 
Это только шаблон собаки, и на основе этого шаблона, мы можем создать конкретную собаку и делается это следующим образом:

```
class Dog {
    var name = ""
    var breed = ""
    var tailLength = 0
}

let myDog = Dog()
```

Мы создаем свойство myDog и присваиваем ему объект класса Dog(). Это уже конкретная собака, которой можно задать свое имя, породу и длину хвоста. 

myDog это ссылка на объект класса, а объект класса это буквально ячейка памяти, которая создается в тот момент когда вы обращаетесь к имени класса с круглыми скобками: Dog(). Это называется инициализацией класса.

```
let myDog - Экземпляр класса, ссылка на объект -> [
                                                    Dog() - объект класса
                                                    {var name = ""
                                                     var breed = ""
                                                     var tailLength = 0
                                                    } - свойства класса
                                                                         ] - ячейка в памяти
```
## Свойства класса

Мы можем обращаться к свойствам внутри класса через его экземпляр следующим образом:

```
let myDog = Dog()

myDog.name
myDog.breed
myDog.tailLength
```

Прописываем экземпляр класса и после ставим точку, это как тропинка к свойствам класса, они нам все доступны.
Теперь мы можем задать имя, породу и длину хвоста нашей собаки так как все свойства переменные:

```
let myDog = Dog()

myDog.name = "Toby"
myDog.breed = "Labradane"
myDog.tailLength = 40
```

Теперь у нас в памяти хранится собака со всеми характеристиками, то есть мы на основе шаблона Dog создали конкретную собаку:

```
let myDog - Экземпляр класса, ссылка на объект -> [
                                                    Dog() - объект класса
                                                    {var name = "Toby"
                                                     var breed = "Labradane"
                                                     var tailLength = 40
                                                    } - свойства класса
                                                                         ] - ячейка в памяти
```

И мы можем воспользоваться всеми этими свойствами в дальнейшей логике:

```
let myDog = Dog()

myDog.name = "Toby"
myDog.breed = "Labradane"
myDog.tailLength = 40

if myDog.name.isEmpty {
    print("Собака без имени!")
} else {
    print("Имя собаки: \(myDog.name)")
}
```

На основе абстрактной конструкции мы можем создать любое количество экземпляров класса:

```
class Dog {
    var name = ""
    var breed = ""
    var tailLength = 0
}

let myDog = Dog()
let bulldog = Dog()
let airedaleTerrier = Dog()
```

Это три конкретные собаки, которые пока имеют пустые поля (свойства внутри класса с пустыми значениями), кроме первого экземпляра, ему мы уже дали имя и породу. И если сейчас заглянем в память, то это будет выглядеть примерно следующим образом:

```
let myDog - Экземпляр класса, ссылка на объект -> [
                                                    Dog() - объект класса
                                                    {var name = "Toby"
                                                     var breed = "Labradane"
                                                     var tailLength = 40
                                                    } - свойства класса
                                                                         ] - ячейка в памяти
                                                                         
let bulldog - Экземпляр класса, ссылка на объект -> [
                                                    Dog() - объект класса
                                                    {var name = ""
                                                     var breed = ""
                                                     var tailLength = 0
                                                    } - свойства класса
                                                                         ] - ячейка в памяти
                                                                         
let airedaleTerrier - Экземпляр класса, ссылка на объект -> [
                                                    Dog() - объект класса
                                                    {var name = ""
                                                     var breed = ""
                                                     var tailLength = 0
                                                    } - свойства класса
                                                                         ] - ячейка в памяти                                                                                     
```

Cамо свойство является ссылкой на объект, а это значит, что если мы ее копируем, то копия будет ссылаться на тот же объект:

```
let myDog = Dog()
let copyMyDog = myDog
```

В памяти это выглядит примерно так:

```
let myDog - Экземпляр класса, ссылка на объект -> [ <- let copyMyDog - Экземпляр класса, ссылка на объект
                                                    Dog() - объект класса
                                                    {var name = "Toby"
                                                     var breed = "Labradane"
                                                     var tailLength = 40
                                                    } - свойства класса
                                                                         ] - ячейка в памяти
```

Теперь два экземпляра класса ссылаются на один и тот же объект, и если мы меняем значения свойств у одного экземпляра, то эти изменения мы увидим и у второго:

```
let myDog = Dog()
let copyMyDog = myDog

myDog.name = "Tobyus"
myDog.name //"Tobyus"
copyMyDog.name //"Tobyus"
```

Мы переопределяем имя Toby на Tobyus, и смотрим на значения свойств. Если запустить компилятор, то справа вы увидите абсолютно одинаковые значения свойства name у myDog и copyMyDog, хотя мы изменили значение имени только у экземпляра myDog. Это говорит о том что два экземпляра класса ссылаются на одну область в памяти.

## Методы класса

По мимо свойств в классе есть еще методы, по сути это функции, которые вы определите внутри класса.

Давайте создадим кнопку:

```
class Button {

}
//Это не сама кнопка, а только шаблон, на основе которого мы создадим конкретную кнопку.

class Button {
    var color = "red"
    var title = "Button"
    var cornerRadius = 15
}
```

У нас есть шаблон, благодаря которому мы можем создать любое количество кнопок:

```
let buttonPink = Button()
let buttonGreen = Button()
```

В данном случае у нас получится две, внешне абсолютно одинаковые, кнопки (потому что созданы от одного шаблона, у которого свойства имеют начальные значения), и мы можем поменять значения их внутренних свойств:

```
let buttonPink = Button()
buttonPink.title = "pink"

let buttonGreen = Button()
buttonGreen.color = "green"
buttonGreen.title = "Green"
```

Теперь одна кнопка будет розовая, а вторая зеленая с заголовком “Green”. Теперь давайте подумаем над созданием метода. Как было уже сказано выше, метод это по сути функция, которая находится внутри объекта. И метод отражает какое то действие объекта. Какие действия могут быть у кнопки? Например кнопку можно нажимать:

```
class Button {
    var color = "red"
    var title = "Button"
    var cornerRadius = 15

    func pushTheButton() {
        print("Кнопку нажали")
    }
}
```

Метод pushTheButton будет отражать нажатие на кнопку, и как только мы его вызовем сработает print внутри метода. 
Вызывать мы его будем в нужный момент из экземпляра этого класса.

Методов в классе может быть сколько угодно, давайте создадим еще один, который будет задавать значение для свойства title:

```
class Button {
    var color = "red"
    var title = "Button"
    var cornerRadius = 15

    func pushTheButton() {
        print("Кнопку нажали")
    }

    func setTitle(_ value: String) {
        title = value
    }
}
```

Что делает метод setTitle? Когда мы его вызовем, мы передадим в параметр значение, которое будет передано в свойство класса title. Его вызов будет выглядеть следующим образом:

```
let buttonPink = Button()
buttonPink.title = "pink"

buttonPink.pushTheButton()
buttonPink.setTitle("PinkButton")
```

Вызвав метод, мы переопределили свойство title  на “PinkButton”.
Создадим еще один метод, который будет задавать цвет для свойства color:

```
class Button {
    var color = "red"
    var title = "PinkButton"
    var cornerRadius = 15

    func pushTheButton() {
        print("Кнопку нажали")
    }

    func setTitle(_ value: String) {
        title = value
    }

    func setColor(_ color: String) {
        self.color = color
    }
}
```

Метод setColor получает из параметра какой то цвет, и передает его в свойство класса color.  self - это сам класс, то есть мы показываем что свойство color относится к классу, а свойство без self относится к параметру, так компилятору легче разобраться как использовать свойства с одинаковыми названиями. 
Через этот метод мы можем задавать значение для цвета:

```
let buttonPink = Button()
buttonPink.title = "pink"
buttonPink.pushTheButton()
buttonPink.setTitle("PinkButton")
buttonPink.setColor("pink")
```

## Статичные свойства и методы

Статические свойства – это свойства, которые принадлежат типу, а не экземпляру класса. В Swift статические свойства объявляются с помощью ключевого слова static

```
class Car {
    static let maxSpeed = 200 // статическое свойство, максимальная скорость
}

var myCar = Car()
```

У нас есть класс Car, который в себе содержит статичное свойство. Из определения выше мы понимаем что это свойство нам не доступно из экземпляра класса и следующий код выдаст ошибку:

```
class Car {
    static let maxSpeed = 200 // статическое свойство, максимальная скорость
}

var myCar = Car()
myCar.maxSpeed // Ошибка
```

Статичное свойство принадлежит типу а не экземпляру, поэтому мы к нему должны обращаться через тип Car:

```
class Car {
    static let maxSpeed = 200
}

var myCar = Car()
Car.maxSpeed // 200
```

В отличие от экземплярных свойств, которые хранятся внутри каждого экземпляра класса статические свойства хранятся в единственном экземпляре, общем для всех экземпляров класса, которые используют это свойство.
Если мы будем менять значение у статичного свойства, то эти изменения будут отражены везде где мы используем это свойство:

```
class Car {
    static var maxSpeed = 200

    func printMaxSpeed() {
        print(Car.maxSpeed)
    }
}

let myCar = Car()
myCar.printMaxSpeed() // 200

Car.maxSpeed = 80

let myNewCar = Car()

myNewCar.printMaxSpeed() // 80
myCar.printMaxSpeed() // 80
```

*К статическим свойствам внутри класса, мы так же обращаемся через сам класс (Тип данных)*

Та же самая история обстоит и с статичными методами. Вызвать такой метод мы можем только из типа данных и он будет общим для всех экземпляров класса:

```
class Car {
    static let maxSpeed = 200

    static func printMaxSpeed() {
        print(maxSpeed)
    }
}

Car.printMaxSpeed()
```

Статические методы класса выполняются в контексте самого класса, а не конкретного экземпляра класса, и могут использоваться для выполнения операций, которые не зависят от конкретного экземпляра класса. Например, статический метод может использоваться для конвертации валюты, вычисления математических функций или получения доступа к общим данным.

*Мы не можем внутри статичного метода использовать обычные свойства класса, только статичные, при этом к свойству можно обратиться на прямую print(maxSpeed) (без использования типа класса)*

*Помимо ключевого слова static есть еще слово class. Оба этих маркера используются для создания свойств и методов, которые принадлежат типу, а не экземпляру этого типа. Основное отличи между static и class заключается в том, что class позволяет переопределять методы и свойства в подклассах, в то время как static этого не позволяет*

# Структуры

Структуры во многом похожи на классы. Все базовые типы данных, такие как Int, массивы, коллекции - все они представляют структурами.

Для определения структуры используется ключевое слово struct, после которого идет название структуры:

```
struct User {
 
}
```

## Отличия структур от классов
1. Наследование - классы поддерживают наследование, тогда как структуры этого не делают. Это означает, что класс может наследовать свойства и методы от другого класса.
2. Управление памятью - классы используются с помощью ссылок, что означает, что при передаче или присвоении объекта класса создается ссылка на него. Структуры передаются по значению, то есть копируются при передаче или присвоении. Классы являются ссылочными типами, а структуры являются значимыми типами
3. Mutability (изменяемость) - свойства структур являются неизменяемыми (immutable) по умолчанию, если экземпляр структуры объявлен как константа. В то время как свойства классов могут быть изменены, независимо от того, объявлен ли объект класса как константа или переменная.
4. Классы могут содержать деинициализаторы

Одним из отличий структур от классов состоит в том, что для создания структур мы можем в инициализатор передать значения для всех его свойств. При этом не важно, что в самой структуре нет инициализатора, который принимает два параметра. По умолчанию для каждой структуры мы можем передать в инициализаторе значения для всех ее свойств. Параметры инициализатора в этом случае соответствуют свойствам структуры. При этом мы не можем задать только одно или два свойства, нам обязательно надо указать значения для всех свойств структуры.
Еще одним отличием структуры от классов является невозможность изменения в методах структуры свойств этой же структуры.
Чтобы выйти из этой ситуации, перед именем метода необходимо указать ключевое слово mutating.

*Почему мы в структурах пишем mutating а в классах нет? Это связано с тем, как Swift обрабатывает изменяемость (mutability) экземпляров типов структуры и класса.
 В Swift структуры являются значениями (value types), что означает, что когда вы изменяете экземпляр структуры, вы фактически создаете новый экземпляр с измененными значениями, вместо того чтобы изменять существующий экземпляр. Поэтому методы, которые изменяют свойства структуры, должны быть помечены ключевым словом mutating, чтобы указать компилятору, что этот метод изменяет сам экземпляр структуры.
С другой стороны, классы в Swift являются ссылочными типами (reference types), и когда вы изменяете экземпляр класса, вы изменяете его непосредственно, так как это ссылка на объект в памяти. Поэтому ключевое слово mutating не требуется для методов классов, так как они могут изменять свойства объекта напрямую.*