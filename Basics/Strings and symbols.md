# Строки и символы

## Строковые литералы

Мы можем включить предопределенные String значения в нашем коде как строковые литералы. Строковый литерал - это фиксированная последовательность текстовых символов, окруженная парой двойных кавычек ("").
Используем строковый литерал как начальное значение для константы или переменной:

```
let someString = "Hello world!"
```
Если мы хотим написать кавычки в однострочном литерале то необходимо поставить знак экранирования \:

```
let wiseWords = "\"Imagination is more important than knowledge\" - Einstein"
```
Чтобы перенести строку используем знак переноса строки \n:

```
let name = "Ivan\nIvanov"
//Ivan
//Ivanov
```

## Многострочные литералы строк

Если нам нужно создать строку, которая поддерживает многострочный вид, используем литерал многострочной строки - последовательность символов, обернутых в три двойные кавычки:

```
let quotation = """
I have been learning iOS coding since last year.

I want to become an iOS developer!
"""
```

Многострочный литерал строки включает в себя все строки между открывающими и закрывающими кавычками. Строка начинается на первой строке после открывающих кавычек ("""), а заканчивается на строке предшествующей закрывающим кавычкам, что означает, что ни одна из строк ниже ни начинается, ни заканчивается символом переноса строки:

```
let singleLineString = "These are the same."
let multilineString = """
These are the same.
"""
```

Когда наш исходный код включает в себя символ переноса строки внутри литерала многострочной строки, то этот символ переноса строки так же появляется уже внутри значения этой строки. Если мы хотим использовать символ переноса строки для того, чтобы сделать наш код более читаемым, но мы не хотим чтобы символ переноса строки отображался в качестве части значения строки, то нам нужно использовать символ обратного слеша (\) в конце этих строк:

```
# Два предложения ниже будут выведены в одну строку из за \

let softWrappedQuotation = """
The White Rabbit put on his spectacles. "Where shall I begin, \
please your Majesty?" he asked.

"Begin at the beginning," the King said gravely, "and go on \
till you come to the end; then stop."
"""
```

Для того, чтобы создать литерал строки, который начинается и заканчивается символом возврата каретки (\r), пишем пустую строчку в самом начале и в конце литерала строки, например:

```
let lineBreaks = """

This string starts with a line break.
It also ends with a line break.

"""
```

Многострочная строка может иметь отступы для соответствия окружающему ее коду. Пробел до закрывающей группы двойных кавычек (""") сообщает Swift, сколько пробелов нужно игнорировать в начале каждой строки. Если нам не нравится что весь текст прижат к краю к нумерации строк кода то мы можем сдвинуть нижние кавычки в лево и весь текст будет отталкиваться от нижних ковычек:

```
let linesWithIndentation = """
   Эта строка начинается без пробелов в начале.
       Эта строка имеет 4 пробела.
   Эта строка так же начинается без пробелов.
   """
```

## Строка является типом значения

Тип String в Swift является типом значения. Когда мы создаем новое String значение, это значение копируется, когда оно передается функции или методу, или когда оно присваивается константе или переменной.

## Работа с символами

Тип String в Swift представляет собой коллекцию значений Character в указанном порядке. Мы можем получить доступ к отдельным значениям Character в строке с помощью итерации по этой строке в for-in цикле:

```
for character in "Dog!" {
print(character)
}
// D
// o
// g
// !
```

Кроме того, можно создать отдельную Character константу или переменную из односимвольного строкового литерала с помощью присвоения типа Character:

```
let exclamationMark: Character = "!"
```

Значения типа String могут быть созданы путем передачи массива типа [Character] в инициализатор:

```
let catCharacters: [Character] = ["C", "a", "t", "!"]
let catString = String(catCharacters)
print(catString)
// Выведет "Cat!"
```

## Конкатенация строк и символов

Значения типа String могут быть добавлены или конкатенированы с помощью оператора сложения (+) и мы также можем добавить значение типа String к другому, уже существующему значению String, с помощью комбинированного оператора сложения и присвоения (+=):

```
let string1 = "hello"
let string2 = " there"
var welcome = string1 + string2
// welcome равен "hello there"

var instruction = "look over"
instruction += string2
// instruction равен "look over there"
```

Мы можем добавить значение типа Character к переменной типа String, используя метод String append:

```
let exclamationMark: Character = "!"
welcome.append(exclamationMark)
// welcome равен "hello there!"
```

## Интерполяция строк

Интерполяция строк - способ создать новое значение типа String из разных констант, переменных, литералов и выражений, включая их значения в строковый литерал. Каждый элемент, который мы вставляем в строковый литерал, должен быть помещен в скобки и находиться внутри двойных кавычек литерала, а перед открывающей скобкой должен стоять знак обратного слэша.

```
let multiplier = 3
let message = "\(multiplier) times 2.5 is \(Double(multiplier) * 2.5)"
// message равен "3 times 2.5 is 7.5"
```

## Доступ и изменение строки

Мы получаем доступ и меняем строку через ее методы, свойства, или с помощью синтаксиса сабскрипта.

### Индексы строки 

Каждое String значение имеет связанный тип индекса: String.Index, что соответствует позиции каждого Character в строке. Различные символы в строке могут требовать различные объемы памяти для хранения, поэтому для того, чтобы определить, какой Character в определенной позиции, необходимо итерировать каждую скалярную величину Юникода, от начала или конца этой строки. По этой причине, Swift строки не могут быть проиндексированы целочисленными значениями.

Используем свойство startIndex для доступа позиции первого Character в String. Свойство endIndex — это позиция после последнего символа в String. В результате, endIndex свойство не является допустимым значением для сабскрипта строки. Если String пустая, то startIndex и endIndex равны:

```
let greeting = "Guten Tag!"
greeting[greeting.startIndex]
// G
# Если greeting была бы пустой то при получении greeting[greeting.startIndex] программа упадет
```

Мы получаем доступ к индексу до и после указанного индекса при помощи методов index(before:) и index(after:):

```
let greeting = "Guten Tag!"
greeting[greeting.index(before: greeting.endIndex)]
// !
greeting[greeting.index(after: greeting.startIndex)]
// u
```

Для доступа к индексу, расположенного не по соседству с указанным индексом, мы можем использовать метод index(_:offsetBy:)

```
let greeting = "Guten Tag!"
let index = greeting.index(greeting.startIndex, offsetBy: 7)
greeting[index]
// a
```

Используем свойство indices, чтобы создать Range всех индексов, используемых для доступа к отдельным символам строки:

```
for index in greeting.indices {
print("\(greeting[index]) ", terminator: " ")
}
// Выведет "G u t e n T a g !"
```

*Вы можем использовать свойства startIndex, endIndex и методы index(before:), index(after:) и index(_:offsetBy:) с любым типом, который соответствует протоколу Collection. Это включает в себя String, как и показано тут, различные типы коллекций, например Array, Dictionary и Set.*

### Добавление и удаление

Для того, чтобы вставить символ в строку по указанному индексу, используем insert(_:at:) метод, а для того, чтобы вставить содержимое другой строки по указанному индексу, используем метод insert(contentsOf:at:):

```
var welcome = "hello"
welcome.insert("!", at: welcome.endIndex)
// welcome теперь равен "hello!"
welcome.insert(contentsOf:" there", at: welcome.index(before: welcome.endIndex))
// welcome теперь равен "hello there!”
```

Для того, чтобы удалить символ из строки по указанному индексу используем remove(at:), если мы хотим удалить значения по указанному диапазону индексов, используем метод removeSubrange(_:):

```
welcome.remove(at: welcome.index(before: welcome.endIndex))
// welcome теперь равно "hello there"
let range = welcome.index(welcome.endIndex, offsetBy: -6)..<welcome.endIndex
welcome.removeSubrange(range)
// welcome теперь равно "hello”
```

*Мы можем использовать методы insert(_:at:), insert(contentsOf:at:), remove(at:) и removeSubrange(_:) с любыми типами, которые соответствуют протоколу RangeReplaceableCollection. Это включает в себя String, как показано тут, а так же коллекции, такие как Array, Dictionary и Set.*

## Подстроки

Мы можем получить подстроку из строки, например, используя сабскрипт или метод типа и prefix(:), результат которого возвращает экземпляр подстроки, а не другую строку. Подстроки в Swift имеют практически те же самые методы, что и строки, что означает, что мы можем работать с подстроками так же как и со строками. Однако, в отличие от строк, мы используем подстроки непродолжительное время, пока проводим какие-то манипуляции над строками. Когда мы готовы хранить результат более продолжительное время, то мы конвертируем подстроку в строку. Например:

```
let greeting = "Hello, world!"
let index = greeting.firstIndex(of: ",") ?? greeting.endIndex
let beginning = greeting[..<index]
// beginning is "Hello"
// Конвертируем в строку для хранения более продолжительное время.
let newString = String(beginning)
```

## Сравнение строк

Swift предусматривает три способа сравнения текстовых значений: равенство строк и символов, равенство префиксов, и равенство суффиксов.

### Равенство строк и символов

Равенство строк и символов проверяется оператором "равенства" (==) и оператором "неравенства" (!=):

```
let quotation = "Мы с тобой похожи"
let sameQuotation = "Мы с тобой похожи"
if quotation == sameQuotation {
print("Эти строки считаются равными")
}
// Выведет "Эти строки считаются равными"
```

Два String значения (или два Character значения) считаются равными, если их расширенные наборы графем канонически эквивалентны . Расширенные наборы графем канонически эквивалентны, если они имеют один и тот же языковой смысл и внешний вид, даже если они изначально состоят из разных скалярных величин Юникода.

Например, LATIN SMALL LETTER E WITH ACUTE (U+00E9) канонически эквивалентна LATIN SMALL LETTER E(U+0065) , если за ней следует COMBINING ACUTE ACCENT (U+0301) . Оба этих расширенных набора графем являются допустимыми вариантами представления символа é , и поэтому они считаются канонически эквивалентными:

```
// "Voulez-vous un café?" используем LATIN SMALL LETTER E WITH ACUTE
let eAcuteQuestion = "Voulez-vous un caf\u{E9}?"
// "Voulez-vous un café?" используем LATIN SMALL LETTER E и COMBINING ACUTE ACCENT
let combinedEAcuteQuestion = "Voulez-vous un caf\u{65}\u{301}?"
if eAcuteQuestion == combinedEAcuteQuestion {
print("Эти строки считаются равными")
}
// Выведет "Эти строки считаются равными"
```

Наоборот, LATIN CAPITAL LETTER A (U+0041 или "A") , используемый в английском языке, не является эквивалентом CYRILLIC CAPITAL LETTER A (U+0410, или "А"), используемой в русском языке. Символы визуально похожи, но имеют разный языковой смысл:

```
let latinCapitalLetterA: Character = "\u{41}"
let cyrillicCapitalLetterA: Character = "\u{0410}"
if latinCapitalLetterA != cyrillicCapitalLetterA {
print("Эти строки считаются не равными")
}
// Выведет "Эти строки считаются не равными"
```

*Сравнение строк и символов не зависит от локализации*

### Равенство префиксов и суффиксов

Чтобы проверить, имеет ли строка определенный строковый префикс или суффикс, вызываем hasPrefix(:) и hasSuffix(:) методы, оба из которых принимают единственный аргумент типа String, и возвращают логическое значение.
В приведенных ниже примерах рассмотрим массив строк, представляющих местоположение сцены в первых двух актах Ромео и Джульетты Шекспира:

```
let romeoAndJuliet = [
"Act 1 Scene 1: Verona, A public place",
"Act 1 Scene 2: Capulet's mansion",
"Act 1 Scene 3: A room in Capulet's mansion",
"Act 1 Scene 4: A street outside Capulet's mansion",
"Act 1 Scene 5: The Great Hall in Capulet's mansion",
"Act 2 Scene 1: Outside Capulet's mansion",
"Act 2 Scene 2: Capulet's orchard",
"Act 2 Scene 3: Outside Friar Lawrence's cell",
"Act 2 Scene 4: A street in Verona",
"Act 2 Scene 5: Capulet's mansion",
"Act 2 Scene 6: Friar Lawrence's cell"
]

#Используем hasPrefix(_:) метод с массивом romeoAndJuliet для подсчета количества сцен в первом акте пьесы:
var act1SceneCount = 0
for scene in romeoAndJuliet {
if scene.hasPrefix("Act 1 ") {
act1SceneCount += 1
}
}
print("Всего \(act1SceneCount) сцен в Акте 1")
// Выведет "Всего 5 сцен в Акте 1"

#Точно так же, использование hasSuffix(_:) метода для подсчета количества сцен, которые происходят внутри или вокруг особняка Капулетти и клетки монаха Лоренцо:
var mansionCount = 0
var cellCount = 0
for scene in romeoAndJuliet {
if scene.hasSuffix("Capulet's mansion") {
mansionCount += 1
} else if scene.hasSuffix("Friar Lawrence's cell") {
cellCount += 1
}
}
print("\(mansionCount) сцен в особняке; \(cellCount) тюремные сцены")
// выводит "6 сцен в особняке; 2 тюремные сцены"
```


