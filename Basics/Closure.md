# Замыкания

Замыкания это функции без имени, с дополнительным функционалом, они могут захватить значения из окружающего контекста.

## Синтаксис замыкания

Синтаксис самого простого блока замыкания, который ничего не принимает и ничего не возвращает:

```
{
//код для выполнения
}
```

Создадим свойство, которому присвоим простой блок замыкания:

```
let greeting = {
print("Hello, Swift!")
}
//Сигнатура будет: let greeting: () -> ()
```

У данного замыкания нет ни каких параметров, и нет никакого возвращаемого значения.
По типу данных мы видим что свойство greeting это блок замыкания, воспринимаем его как копию функции, а это значит, что бы вызвать его, нам нужно поставить круглые скобки:

```
let greeting = {
print("Hello, Swift!")
}

greeting()
```

Что бы было более понятно, то посмотрим как это будет реализованно с использованием функции:

```
func getGreting() {
print("Hello, Swift!")
}

let greeting = getGreting //делаем копию функции

greeting() //вызываем копию
```

Оба примера работают одинаково, но разница в том что в случае с замыканием, мы сразу передаем реализацию свойству.
Мы также можем сразу запустить блок замыкания если после фигурных скобок определим круглые скобки:

```
let greeting = {
print("Hello, Swift!")
}()
//Тип данных: let greeting: ()
```

Дополнительный пример вызова замыкания:

```
let multiply = {(a: Int, b: Int) -> Int in 
a * b
}(2, 2)
//Тип данных: let multiply: Int
```

### Замыкающие принимающие параметры

Обратимся к синтаксису:

```
{ (параметры) in
//код для выполнения
}
```

В круглых скобках определяем параметры, после чего ключевое слово in. Это ключевое слово указывает, что объявление параметров замыкания закончено, и тело замыкания начинается.
Сделаем так, что бы на блок замыкания принимал параметр String, обратился к синтаксису выше, и реализуем следующий код:

```
let greeting = { (name: String) in
print("Hello, \(name)!"
}

greeting("Tim")
```

После открывающей фигурной скобки, мы можем определить входные параметры, так же как у обычной функции, а после ключевого слова in логику, в которой можем использовать параметр.
Вызов такого блока замыкания выглядит точно так же как и копия функции, снова приведу пример:

```
func printGreeting(name: String) {
print("Hello, \(name)")
}

let greeting = printGreeting
greeting("Tim")
```

Код будет работать одинаково и вызов выглядит одинаково.

Есть еще один вариант объявить параметры:

```
let greeting: (String) -> () = { name in
print("Hello, \(name)!"
}

greeting("Tim")
```

Если мы пропишем тип данных замыкания явно, то нам не нужно прописывать тип у параметра в фигурных скобках, такая запись короче  и выглядит аккуратней

Еще один пример, но на этот раз с двумя параметрами:

```
let multiply: (Int, Int) -> Void = { numberOne, numberTwo in
print(numberOne * numberTwo)
}

multiply(3, 5)
```

Это замыкание принимает в качестве параметров два целых числа, и выводит на консоль их произведение.

Еще один вариант записи такого замыкания:

```
let multiply = { (numberOne: Int, numberTwo: Int in 
print(numberOne * numberTwo)
}

multiply(3,5)
```

Для компилятора не имеет значение как мы объявим блок замыкания, главное что бы были прописаны типы данных, что бы он мог понимать с чем работает.

### Замыкание возвращающие значения

Замыкания могут не только принимать значения, но и возвращать, точно так же как и функции.

Синтаксис:

```
{ (параметры) -> тип результата in
//код для выполнения
}
```

Немного изменим блок замыкания multiply:

```
let multiply = { (numberOne: Int, numberTwo: Int) -> Int in
numberOne * numberTwo
}

let result = multiply(3, 5) // свойству result будет присвоено значение 15
print(result)
```

Реализация с явно прописанным типом:

```
let multiply: (Int, Int) -> Int = { numberOne, numberTwo in
numberOne * numberTwo
}

let result = multiply(3, 5) // свойству result будет присвоено значение 15
print(result)
```

### Замыкание в качестве параметров

Так же как и функции, блоки замыканиям мы можем передавать в качестве параметров.

Посмотрим на следующий пример:

```
// Блок замыкания с типом (Int, Int) -> Int
let multiplyClosure = { (numberOne: Int, numberTwo: Int) -> Int in
numberOne * numberTwo
}

// Функция с типом (Int, Int) -> Int
func multiply(numberOne: Int, numberTwo: Int) -> Int {
numberOne * numberTwo
}

let action = multiply // копия функции
```

Типы данных у функции и у замыкания одинаковый и вызваться они будут одинаково:

```
multiplyClosure(4, 3)
action(4, 3)
```

Посмотрим как выглядит замыкание в качестве параметра:

```
// Блок замыкания с типом (Int, Int) -> Int
let multiplyClosure = { (numberOne: Int, numberTwo: Int) -> Int in
numberOne * numberTwo
}

// Функция с типом (Int, Int) -> Int
func multiply(numberOne: Int, numberTwo: Int) -> Int {
numberOne * numberTwo
}

// Функция, которая принимает в качестве параметра тип блока замыкания
func performOperation(_ operation: (Int, Int) -> Int) {
let result = operation(4, 5)
print("The result is: \(result)")
}
```

Мы можем заметить тип блока замыкания и функции multiple, подходит к параметру operation, и мы можем, при вызове функции performOperation, передать наш блок замыкания или функцию:

```
// Функция, которая принимает в качестве параметра тип блока замыкания
func performOperation(_ operation: (Int, Int) -> Int) {
let result = operation(4, 5)
print("The result is: \(result)")
}

performOperation(multiplyClosure)
performOperation(multiply)
```

Оба вызова отработают одинаково. В первый раз мы передали блок замыкания, а во второй копию функции. Параметр operation будет вызываться как копия функции или блока замыкания, в зависимости от того что вы передадите в параметр. 

Очевидно, что в реальной жизни мы бы хотели целые числа передавать в параметрах:

```
func performOperation(between numberOne: Int,and numberTwo: Int , operation: (Int, Int) -> Int) {
let result = operation(numberOne, numberTwo)
print("The result is: \(result)")
}

performOperation(between: 4, and: 5, operation: multiplyClosure) // The result is: 20
performOperation(between: 3, and: 5, operation: multiply) // The result is: 15
```

Теперь мы передаем значения для параметра operation при вызове функции performOperation.

*Параметр с типом блока замыкания, лучше определять в конце*

### Замыкающие выражения

Замыкающие выражения, это способ написания встроенных замыканий.

Давайте вернемся к нашей функции performOperation. И попробуем сделать вызов, но немного иначе:

```
performOperation(between: 4, and: 5, operation: (Int, Int) -> Int)
Если мы выделим тип блока замыкания, и нажмем enter, то нам раскроется знакомый нам блок:
performOperation(between: 4, and: 5) { Int, Int in 
code
}
```

Это автономный блок замыкания, и мы можем придумать логику “не отходя от кассы”. Если нам нужны входные параметры, то мы их инициализируем:

```
func performOperation(between numberOne: Int,and numberTwo: Int , operation: (Int, Int) -> Int) {
let result = operation(numberOne, numberTwo)
print("The result is: \(result)")
}

performOperation(between: 5, and: 7) { numberOne, numberTwo in
numberOne + numberTwo
}
```
Gараметр operation (это блок замыкания, который мы реализуем при раскрытии), при вызове он будет принимать два параметра, в нашем случае это numberOne и numberTwo и возвращать результат. 
Соответсвенно numberOne примет значение  5, а numberTwo 7.
И результат работы этой функции мы увидим на консоли: “The result is: 12”


### Сокращенные имена параметров

Каждый параметр в блоке замыкания проиндексирован и мы можем к ним обращаться на прямую через индексы, без ключевого слова in:

```
let operation: (Int, Int) -> Int = { $0 + $1 }
```

Вызов такого блока замыкания будет выглядеть без изменений. 

Соответсвенно раскрытый блок замыкания у функции performOperation будет выглядеть так:

```
func performOperation(between numberOne: Int,and numberTwo: Int , operation: (Int, Int) -> Int) {
let result = operation(numberOne, numberTwo)
print("The result is: \(result)")
}

performOperation(between: 5, and: 7) { $0 + $1 }
```
Это избавляет от ключевого слова in, хотя, возможно, выражение становится труднее читать, поскольку в этом случае мы не можем назвать параметры. В этом случае разработчик должен решить, выгодна ли краткость функции по сравнению с именованием параметров.

То есть мы не называем параметры перед in, мы просто обращаемся к ним через индексы и знак доллара. 

## Захват переменной

У каждого замыкания есть список того, что он захватывает снаружи. Если мы изменем переменную внутри замыкания то она изменится и снаружи.

```
var integer = 5

var someClosure = {
integer += 1
}

someClosure() // integer был 5 стал 6 
integer += 1 // integer был 6 стал 7
someClosure() // integer был 7 стал 8
```

Мы можем указать список захвата явно, чтобы разрешить замыканию захватывать чтото или нет, при этом изменится и поведение замыкания. Замыкание будет КОПИРОВАТЬ переменную.

```
var integer = 5

var someClosure = { [integer] - скопированно значение
//integer += 1 выведет ошибку так как теперь замыкание не захватывает integer
print(integer)
}

someClosure() // integer 5 
integer += 1 // integer был 5 стал 6
someClosure() // integer 5
```

## Управление памятью

Когда класс и замыкания ссылаются друг на друга то возникает reference cycle т.е утечка памяти:

```
class SomeClass {
var closure: (() -> Void)?

deinit {
print("deinit")
}

let myClass = SomeClass()
myClass.closure = {print(a)} //здесь и класс ссылается на замыкание и замыкание ссылается на класс
}
```

Чтобы небыло reference cycle мы должны сделать слабую ссылку в на класс:

```
class SomeClass {
var closure: (() -> Void)?

deinit {
print("deinit")
}

let myClass = SomeClass()
myClass.closure = {[weak a] in print(a)} //здесь и класс ссылается на замыкание и замыкание ссылается на класс
}
```
## Сбегающие замыкания

Сбегающие замыкания (escaping closures) используются, когда замыкание передается в функцию как аргумент, но может быть сохранено и вызвано позже, после завершения выполнения функции. По умолчанию замыкания в Swift являются несбегающими (non-escaping), то есть они вызываются в рамках функции, в которую они переданы, и не могут быть сохранены для последующего использования.

Чтобы объявить замыкание как сбегающее, необходимо использовать аннотацию @escaping перед типом замыкания в сигнатуре функции. Это позволяет сохранить замыкание за пределами функции и вызвать его позже.

```
class SomeClass {
var closure: ((Int, Int) -> Int)?

func someFunc(closure: @escaping (Int, Int) -> Int) {
self.closure = closure //без @escaping выведет ошибку
}
}
```

*Все опциональные замыкания в вункции являются @escaping по умолчанию*
