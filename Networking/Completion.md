# Completion

Для того чтобы понять что такое completion разберем простую функцию:

```
func wordPecker(phrase: String) {
    for letter in phrase {
        print(letter)
    }
}

wordPecker(phrase: "Word pecker")
```

В коде выше у нас простая функция, которая печатает каждый символ строки в первом параметре.
Усложним нашу функцию добавив *последним* параметром completion: () -> Void. В Swift принято замыкания в функциях писать последним параметром:

```
func wordPecker(phrase: String, completion: () -> Void) {
    for letter in phrase {
        print(letter)
    }
    completion()
}

wordPecker(phrase: "Word pecker") {
    print("finish")
}
```

В коде выше completion позволяет нам добавить свой блок кода в методе, даже если этот метод из другого модуля то разработчик который писал такую функцию дает нам возможность написать свой код в теле completion. Стоит отметить что completion можно запускать не один раз в теле функции. Completion - это название, указывающее на то, что выполнение этого участка кода следует находиться в конце тела функции.
Добавим параметр в наш completion и немного добавим кода в теле функции:

```
func wordPecker(phrase: String, completion: (String) -> Void) {
    let words = phrase.split(separator: " ")
    
    for word in words {
        for letter in word {
            print(letter)
        }
        completion(String(word))
    }
}

wordPecker(phrase: "Word pecker") { someWord in
    print("--> \(someWord.uppercased()) completed")
}
```

В примере выше показанно как completion работает с параметрами и что вызывается он не в конце функции.

## @escaping

Если функция выполняется ассинхронно, то нам нужно принудить функцию отработать до конца, когда весь остальной код уже отработал. Для этого замыкание помечается @excaping:

```
func wordPecker(phrase: String, completion: @escaping (String) -> Void) {
    let words = phrase.split(separator: " ")
    
    DispatchQueue.main.asyncAfter(deadline: .now() + 2.0) {
        completion("finish")
    }
    for word in words {
        for letter in word {
            print(letter)
        }
        completion(String(word))
    }
}

wordPecker(phrase: "Word pecker") { someWord in
    print("--> \(someWord.uppercased()) completed")
}
```

