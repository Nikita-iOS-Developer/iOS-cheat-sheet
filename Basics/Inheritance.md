# Наследование

Класс обладает уникальной способностью наследовать свойства и методы других классов.
Давайте посмотрим на следующий пример:

```
class View {
    var background = "white"
    var alpha = 1.0
}

class Button {
    var background = "white"
    var alpha = 1.0
}

class Label {
    var background = "white"
    var alpha = 1.0
}
```

Перед нами 4 элемента интерфейса, View это белый кран, Button это кнопка, Label – текст на экране. Мы можем заметить что внутри каждого из этих классов абсолютно одинаковый код, у каждого есть background – цвет фона, у каждого есть alpha – прозрачность объекта. Эти общие свойства и методы мы можем объединить в один родительский класс. Таким образом, они могут наследовать эти свойства и методы и не повторять их каждый раз в каждом классе. 
Синтаксис наследования выглядит следующим образом:

```
class ИмяКласса: КлассОтКоторогоНаследуемся {
Тело класса
}
```

После имени класса, мы ставим двоеточие и следом тот класс, от которого хотим наследоваться. Класс от которого наследуемся называется - суперклассом, а тот кто носледуется - подклассом.
Сделаем класс View родительским, то есть суперклассом, а все остальные подклассами, то есть наследниками:

```
class View {
    var background = "white"
    var alpha = 1.0
}

class Button: View {
}

class Label: View {
}
```

Класс Button и Label  унаследуют все что есть в классе View, все его свойства и методы, таким образом они нам будут доступны из коробки и мы можем спокойно к ним обращаться из экземпляра класса:

```
let button = Button()
button.background // white
button.alpha // 1

let label = Label()
label.background // white
label.alpha // 1
```

Не смотря на то что в данных классах не написано ни строчки кода, тем не менее у нас есть доступ ко всем свойствам класса View от которого мы унаследовались. 
Наследование можно сравнить с семейными отношениями, где родители передают свои свойства и характеристики потомкам, например цвет глаз и предрасположенность к аллергии. Но как мы понимаем потомки могут так же иметь свои собственные характеристики, не унаследованные, например скверный характер. 
То есть любой подкласс, может так же иметь и свои собственные свойства и методы:

```
class Button: View {
    var cornarRadius = 15
}

class Label: View {
    var title = "Label"
}

let button = Button()
button.background // white
button.alpha // 1
button.cornarRadius // 15

let label = Label()
label.background // white
label.alpha // 1
label.title // Label
```

И здесь важный момент, ребенок наследует все что есть у родителя, но родитель никогда не сможет унаследовать что то от ребенка. То есть класс View не знает что происходит в его дочерних классах, их личные свойства и методы ему не доступны.
Если мы создадим экземпляр класса View, и попытаемся обратиться к его свойствам, мы увидим в списке только alpha и background, а свойства cornarRadius не будет, потому что оно имеет отношение только к классу Button. 
Со всеми унаследованными от суперкласса свойствами вы можете работать как с родными, изменять их, использовать во внутренней логике.
Давайте добавим нашему супер классу вычисляемое свойство с описанием:

```
class View {
    var background = "white"
    var alpha = 1.0

    var description: String {
        "background: \(background) and alpha: \(alpha)"
    }
}
```

Вычисляемое свойство description выводит на консоль сообщение, которое включает в себя значения свойств background и alpha.
Создадим класс Button, который наследуется от класса View:

```
class View { // Базовый класс
    var background = "white"
    var alpha = 1.0

    var description: String {
        "background: \(background) and alpha: \(alpha)"
    }
}

class Button: View { // Класс наследник
    var cornerRadius = 15
}

let button = Button()
button.background = "red"
button.alpha = 0.5
button.cornarRadius = 16

print(button.description)
```

Когда создается класс для него выделяется определенное количество памяти. При этом в память загружаются все поля, методы и свойства, которые определены внутри этого класса.
Когда создается класс-наследник, то он получает все поля и методы, определенные в его родительском классе (базовом классе). Для этого в памяти выделяется дополнительное место для хранения всех наследованных членов базового класса. 
Для понимания того как это все работает, можно представить класс наследник Button так:

```
                                let button -> [
                                [var background = "white"
                                 var alpha = 1.0
                                 var description: String {
                                 "background: \(background) and alpha: \(alpha)"
                                 }
                                ] <- Наследие от View
                                var cornerRadius = 15 <- свойство класса Button()
                                ] <- Button()
```

В верхней части все свойства и методы, которые мы унаследовали у супер класса, в том числе и его пустой инициализатор, который мы не видим, но он нам дан по умолчанию, при создании экземпляра класса Button у нас выделилась дополнительная память для них. Конечно это грубое представление, того как это все размещается в памяти, но нам сейчас главное понимать как мы можем взаимодействовать с унаследованными свойствами и методами. таким образом когда мы меняем значения свойств background и alpha:

```
let button = Button()
button.background = "red"
button.alpha = 0.5
button.cornarRadius = 16

print(button.description)
```

то description будет отображать новые значения: background: red and alpha: 0.5.

## Инициализатор у класса наследника

Инициализатор наследуется от супер класса.
Инициализируем свойства супер класса через инициализатор:

```
class View { // Базовый класс
    let background: String
    let alpha: Double

    init(background: String, alpha: Double) {
        self.background = background
        self.alpha = alpha
    }
}

class Button: View { // Класс наследник
}

let button = Button(background: "green", alpha: 1.0)
```

Обратим внимание что мы  унаследовали от супер класса так же инициализатор. Button унаследует от View всё его содержимое в том числе и инициализатор. Но что если мы хотим создать в классе Button свой собственный инициализатор:

```
class View { // Базовый класс
    let background: String
    let alpha: Double

    init(background: String, alpha: Double) {
        self.background = background
        self.alpha = alpha
    }
}

class Button: View { // Класс наследник
    let cornerRadius: Int

    init(cornerRadius: Int) { // Ошибка
        self.cornerRadius = cornerRadius
    }
}
```

Унаследованные нами свойства остались не инициализированными, потому что мы не можем вызвать два инициализатора сразу, и из-за этого у нас возникла ошибка, но мы можем вызвать унаследованный инициализатор у супер класса:

```
class Button: View { // Класс наследник

    let cornerRadius: Int

    init(cornerRadius: Int) {
        self.cornerRadius = cornerRadius
        super.init(background: "white", alpha: 1.0) // Обращаемся к супер классу и вызываем его инициализатор
    }
}

let button = Button(cornerRadius: 7)
```

Что бы обратиться к инициализатору супер класса мы должны прописать super и вызвать нужный инициализатор, как мы знаем их может быть несколько в классе. 
Таким образом в момент создания экземпляра класса button мы вызовем инициализатор с cornerRadius и по цепочке зацепим инициализатор супер класса и инициализируем все свойства в супер классе. 
Как можно заметить, значения для свойств супер класса будут заданы автоматически, всегда белый цвет с прозрачностью 1 и мы их не сможем поменять. Это не очень удобно и чаше всего мы бы хотели контролировать инициализацию этих свойств. В таком случае можно добавить параметры в инициализатор класса Button:

```
class Button: View { // Класс наследник

    let cornerRadius: Int

    init(cornerRadius: Int, background: String, alpha: Double) {
        self.cornerRadius = cornerRadius
        super.init(background: background, alpha: alpha)
    }
}

let button = Button(cornerRadius: 7, background: "red", alpha: 1.0)
```

Теперь мы можем задавать значения для этих свойств в момент создания экземпляра класса.
Мы так же можем унаследоваться от этой кнопки и создать на ее основе кастомную кнопку:

```
class View { // Базовый класс
    let background: String
    let alpha: Double

    init(background: String, alpha: Double) {
        self.background = background
        self.alpha = alpha
    }
}

class Button: View { // Класс наследник

    let cornerRadius: Int

    init(cornerRadius: Int, background: String, alpha: Double) {
        self.cornerRadius = cornerRadius
        super.init(background: background, alpha: alpha)
    }
}

class CustomButton: Button {

}
```

При создании экземпляра CustomButton нам будет доступен последний инициализатор в иерархии наследования, то есть тот который находится в Button:

```
class CustomButton: Button {

}

let customButton = CustomButton(cornerRadius: 6, background: "pink", alpha: 0.3)
```

*Если в классе Button не будет личного инициализатора, в таком случае CustomButton воспользуется инициализатором класса View. Так же в инициализаторе мы должны сперва инициализировать все свойства подкласса, а потом уже свойства его супер класса.*
