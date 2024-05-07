# Дженерики

## Дженерики основы

Рассмотрим синтаксис джинериков:

```
func swapTwoValues<T>(_ a: inout T, _ b: inout T) {
    let temporaryA = a
    a = b
    b = temporaryA
}
```

В примере выше симво <T> подразумевает собой Type, но мы можем написать любое подходящее нам по смыслу слово. В аргументы функции может быть вложен любой тип данных (один тип).

```
var a = 1
var b = 2

swapTwoValues(&a, &b)

var c = true
var d = false

swapTwoValues(&c, &d)
```

Если мы попробуем вписать в аргументы функции разные типы данных то мы столкнемся с ошибкой.
Мы можем сделать так чтобы в аргументах функции могли быть разные или одинаковые типы. Для того чтобы это сделать нам нужно внутри скобок через запятую указать дженерики:

```
func swapTwoValues<Type, MyType>(_ a: inout Type, _ b: inout MyType) {
    print(a)
    print(b)
}

var a = 1
var b = true

swapTwoValues(&a, &b)

var c = true
var d = "hello"

swapTwoValues(&c, &d)
```

В основном дженерики являются контейнерами которые не связанны с типом. Например массивы, словари или множества являются дженериками. Они могут посчитать количество элементов внутри себя, найти элемент по индексу или ключу или сделать другие действия которые не связанны с типом.

## Дженерики недостатки

1. Дженерики - это усложнение кода
2. Компилятор генерирует специальный код для дженериков. У нас не будет просадки по перфомансу, но будет просадка по памяти приложения.
3. Будеи непросто разобраться во время дебагинга

## Дженерики в структурах

Рассмотрим следующую структуру. Она представляет из себя стек.

```
struct IntStack {
    var items: [Int] = []
    mutating func push(_ item: Int) {
        items.append(item)
    }
    mutating func pop() -> Int {
        return items.removeLast()
    }
}
```

В структуре выше мы ложим элементы одним за другим и убираем элементы с конца по одному. Наша структура может складывать только значения Int.
Давайте сделаем так, чтобы наша структура могла складывать любой элемент и посмотрим как она работает:

```
struct Stack <Element> {
    var items: [Element] = []
    mutating func push(_ item: Element) {
        items.append(item)
    }
    mutating func pop() -> Element {
        return items.removeLast()
    }
}

var stackOfStrings = Stack<String>()
stackOfStrings.push("uno")
stackOfStrings.push("dos")
stackOfStrings.push("tres")
stackOfStrings.push("cuatro")
```

В коде выше мы описали структуру дженерик и создали экземпляр этой структуры с типом String. Если мы определили однажды тип экземпляра данной структуры, то попытка вложить другой тип данных в методы экземпляра этой структуры вызовет ошибку.
Мы можем написать расширение к данной структуре с таким же синтаксисом:

```
extension Stack {
    var topItem: Element? {
        return items.isEmpty ? nil : items[items.count - 1]
    }
}
```

## Уточнение типов в дженериках

Дженерики могут работать с любым типом, но иногда нам нужно чтобы этот дженерик был специализированным, чтобы этот тип что-то умел. К таким случаям у нас есть Type Constraint (ограничение типа). Синтаксис выглядит следующим образом:

```
func someFunction<T: SomeClass, U: SomeProtocol>(someT: T, someU: U) {
    // function body goes here
}
```

В примере выше T это наш тип который должен обязательно наследоваться от класса SomeClass или это может быть и сам класс SomeClass, тип U это тип, который должен быть подписан под протокол SomeProtocol:

```
class SomeClass {}

class Delegate: SomeProtocol {}

protocol SomeProtocol {}

var someClass = SomeClass()
var delegate = Delegate()

func someFunction<T: SomeClass, U: SomeProtocol>(someT: T, someU: U) {
    // function body goes here
}

someFunction(someT: someClass, someU: delegate)
```

Рассмотрим пример ограничений типов в действии:

```
func findIndex<T: Equatable>(of valueToFind: T, in array:[T]) -> Int? {
    for (index, value) in array.enumerated() {
        if value == valueToFind {
            return index
        }
    }
    return nil
}

let strings = ["cat", "dog", "llama", "parakeet", "terrapin"]
if let foundIndex = findIndex(of: "llama", in: strings) {
    print("The index of llama is \(foundIndex)")
}
```

В примере выше, чтобы сравнить на два наших типа, они должны соответствовать протоколу Equatable. Без указания протокола компилятор выдаст ошибку так как наши типы могут не поддерживать этот протокол, но для сравнения он нужен.

## Associated Types

Ассоциированные типы в основном встречаются в протоколах. Когда мы хотим чтобы класс, структура или перечисление сами указывали тип с которым они хотят работать, мы используем Associated types.
Ниже пример такого протокола:

```
protocol Container {
    associatedtype Item
    mutating func append(_ item: Item)
    var count: Int { get }
    subscript(i: Int) -> Item { get }
}

struct IntStack: Container {
    // original IntStack implementation
    var items: [Int] = []
    mutating func push(_ item: Int) {
        items.append(item)
    }
    mutating func pop() -> Int {
        return items.removeLast()
    }
    // conformance to the Container protocol
    typealias Item = Int
    mutating func append(_ item: Int) {
        self.push(item)
    }
    var count: Int {
        return items.count
    }
    subscript(i: Int) -> Int {
        return items[i]
    }
}
```

В коде выше чтобы протокол мог быть дженериком внутри его описания пишется ключевое слово associatedtype и название нашего типа. При подписании объекта под этот протокол, внутри объекта пишется ключевое слово typealias, название нашего элемента и через знак присваивания мы указываем тип данных который хотим использовать.
Если мы подпишем дженерик структуру под дженерик протокол то слово typealias можно не писать так как swift сам сделает вывод что тип в протоколе будет соответствовать типу дженерик структуры. Но лучше для уточнения написать typealias в структуре:

```
struct Stack<Element>: Container {
    // original Stack<Element> implementation
    var items: [Element] = []
    mutating func push(_ item: Element) {
        items.append(item)
    }
    mutating func pop() -> Element {
        return items.removeLast()
    }
    // conformance to the Container protocol
    mutating func append(_ item: Element) {
        self.push(item)
    }
    var count: Int {
        return items.count
    }
    subscript(i: Int) -> Element {
        return items[i]
    }
}
```
