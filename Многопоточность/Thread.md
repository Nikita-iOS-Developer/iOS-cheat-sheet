# Threads

Все операции, выполняемые в мобильном приложении, требуют некоторых ресурсов и имеют время на выполнение. Эти операции по умолчанию выполняются поочередно на главном потоке.

*Потоки - это одна из технологий, которые позволяют выполнять несколько операций внутри одного приложения одновременно. Хотя новые технологии, такие как Operations и Grand Central Dispatch(GCD), обеспечивают более современную и эффективную инфраструктуру для реализации параллелизма, OS X и iOS также предоставляют интерфейсы для создания потоков и управления ими.*

Главным потоком называется поток, в котором стартует приложение. Обработка событий связанных со взаимодействием с UI происходит на главном потоке (такой операцией может быть обработка тапа по экрану, нажатия на клавишу клавиатуры, движение мыши и т.д.). Помимо этого, любая операция, написанная нами, будь то выполнения алгоритма, запрос в сеть или обращение к базе данных так же будет выполняться на главном потоке, что может негативно сказываться на оотклике UI. Здесь к нам на помощь приходит многопоточность - возможность выполнять операции параллельно (одновременно) на разных потоках.

## Run Loop

Run Loop - своего рода бесконечный цикл, предназначенный для обработки и координации всех событий, поступающих к нему на вход. В первую очередь стоит отметить, что у каждого потока есть свой ассоциированный Run Loop. Run Loop для главного потока приложения (UIApplication) инициализирует и стартует автоматически, в то время как для созданных потоков запускать и конфигурировать Run Loop необходимо самостоятельно.
Run Loop главного потока отлавливает все системные события и запускает их обработку на главном потоке, будь то нажатия на клавиши клавиатуры, если это macOS, или тап по экрану iOS устройства. Также Run Loop умеет управлять своим потоком: будить для выполнения некоторой работы и переводить в спячку после ее выполнения.
По большому счету, Run Loop - это то, что отличает интерактивное мобильное приложение от обычной программы. Когда Run Loop получает сообщение о событии, он запускает обработчик, ассоциированный с этим событием на своем потоке, а после выполнения усыпляет поток до следующего события, именно таким образом приложение узнает о происходящих интерактивных событиях.
Разберемся, какие же события умеет обрабатывать Run Loop:

* Input sources - различные источники ввода(мышь, клавиатура, тачскрин и т.п), кастомные источники (необходимы для передачи сообщений между потоками), а так же вызов performSelector:onThread: (метод, необходимый для вызова события по селектору на определенном потоке)
* Timer sources - все таймеры в приложении всегда обрабатываются ранлупом

Как уже говорилось ранее, Run Loop'ом главного потока управляет приложение, в то время, как мы сами управляем Run Loop'ом созданных нами потоков, таким образом мы можем явно указать, какие источники ввода должен обрабатывать Run Loop. Рассмотрим режимы работы Run Loop:

* NSDefaultRunLoopMode - режим по умолчанию, который отслеживает все основные события

* NSConnectionReplyMode - режим для работы NSConnection (в документации написанно, что этот режим нам никогда не понадобится) 

* NSModalPanelRunLoopMode - режим, отслеживающий события в модальных окнах (используется только в macOS)

* NSEventTrackingRunLoopMode - режим, который отслеживает системные события связанные с UI (скроллинг, тап по экрану, движение мыши, нажатия на клавиатуре и т.п)

* NSRunLoopCommonModes - режим, объединяющий в себе 3 других: NSDefaultRunLoopMode, NSModalPanelRunLoopMode, NSEventTrackingRunLoopMode

В качестве примера использования режимов Run Loop может быть распространенная проблема, связанная с некорректной работой таймера:

```
Timer.scheduledTimer(timeInterval: 0.1,
    target: self,
    selector: #selector(onTimerUpdate),
    userInfo: nil,
    repeats: true)
```

Проблема данного кода заключается в том, что метод scheduledTimer создает таймер и планирует выполнение события в текущем Run Loop только для режима NSDefaultRunLoopMode (.default), в то время, как обработка UI событий (скролинг, тап по дисплею и т.п) происходит в режиме NSEventTrackingRunLoopMode (.tracking). Таким образом, когда пользователь тригерит UI события, Run Loop обрабатывает события только из режима .tracking, а наш таймер тем временем, имея более низкий приоритет, не обрабатывается. Для решения данной проблемы необходимо добавить таймер в текущий Run Loop для режима NSRunLoopCommonModes (.common), который в себя включает .tracking и .default:

```
let timer = Timer(timeInterval: 1,
    target: self, 
    selector: #selector(time), 
    userInfo: nil, 
    repeats: true)
        
RunLoop.main.add(timer, forMode: .common)

```

## Синхронизация

При работе с многопоточностью, часто встает вопрос синхронизации. Существуют ситуации, в которых несколько потоков имеют одновременный доступ к ресурсу, например Thread1 читает ресурс в то время, как Thread2 изменяет его, что приводит к коллизии.
Во избежание таких ситуации существует несколько способов синхронизации, такие как mutex и semaphore. Синхронизация позволяет обеспечить безопасный доступ одного или нескольких потоков к ресурсу.

### Mutex

Mutex - примитив синхронизации, позволяющий захватить ресурс. Подразумевается, что как только поток обратиться к ресурсу, захваченному мьютексом, никакой другой поток не сможет с ним взаимодействовать до тех пор, пока текущий поток не освободит этот ресурс.
Стоит отметить, что mutex работает по принципу FIFO, то есть потоки будут захватывать ресурс по освобождению в том порядке, в котором данные потоки обратились к ресурсу.

### NSLock

NSLock - более удобная реализация базового фреймворка Foundation.
Рассмотрим пример использования NSLock:

```
    @IBAction func buttonOneAction(_ sender: UIButton) {
        // Создаем мьютекс
        let lock = NSLock()
        // Пример критической секции, которую мы хотим защитить
        var sharedResource = 0
        // Функция, которая будет вызываться из разных потоков
        func updateSharedResource() {
            lock.lock()
            // Критическая секция
            sharedResource += 1
            print("Shared resource updated: \(sharedResource)")
            lock.unlock()
        }
        // Создаем несколько потоков для демонстрации работы мьютекса
        for _ in 1...50 {
            DispatchQueue.global().async {
                updateSharedResource()
            }
        }
        // Ждем завершения всех потоков
        sleep(2)
    }
```

В этом примере мы используем NSLock для создания мьютекса, который блокирует доступ к критической секции кода, где изменяется общий ресурс sharedResource. Когда один поток заходит в критическую секцию, он блокирует мьютекс, чтобы другие потоки не могли одновременно изменять общий ресурс. После завершения работы в критической секции мьютекс разблокируется.

### Reqursive mutex

Reqursive mutex - разновидность базового mutex, которая позволяет потоку захватывать ресурс множество раз до тех пор пока, он не освободит его. Ядро операционной системы сохраняет след потока, который захватил ресурс и позволяет ему захватывать ресурс повторно. Рекурсивный мьютекс считает количество блокировок и разблокировок, таким образом ресурс будет захвачен до тех пор, пока их количество не станет равно друг другу. Чаще всего используется в рекурсивных функциях.

### NSRecursiveLock 

NSRecursiveLock - более удобная реализация reqursive mutex из фреймворка Foundation.
Рассмотрим пример использования NSRecursiveLock:

```
    @IBAction func buttonOneAction(_ sender: UIButton) {
        let recursiveLock = NSRecursiveLock()
        func recursiveFunction(_ counter: Int) {
            recursiveLock.lock()
            defer {
                recursiveLock.unlock()
            }

            if counter > 0 {
                print("Counter: \(counter)")
                recursiveFunction(counter - 1)
            }
        }
        // Создаем несколько потоков для демонстрации работы NSRecursiveLock
        let queue = DispatchQueue(label: "com.example.queue", attributes: .concurrent)
        for _ in 0..<5 {
            queue.async {
                recursiveFunction(3)
            }
        }
    }
```

В этом примере мы создаем NSRecursiveLock recursiveLock и определяем функцию recursiveFunction, которая рекурсивно вызывает саму себя. Внутри функции мы захватываем блокировку перед выполнением рекурсивного вызова и освобождаем ее после завершения.
Затем мы создаем очередь queue с атрибутами .concurrent и запускаем несколько потоков, каждый из которых вызывает recursiveFunction. NSRecursiveLock позволяет одному и тому же потоку многократно захватывать и освобождать блокировку, что делает его полезным для рекурсивных сценариев использования.


### Condition

Condition - еще один примитив синхронизации. Задача, закрытая condition, не начнет свое выполнение до тех пор, пока не получит сигнал из другого потока. Сигнал является неким триггером для condition, которые говорит о том, что поток должен выйти из состояния ожидания.

### NSCondition

NSCondition - более удобная реализация condition из фреймворка Foundation. Удобство заключается в том, что используя NSCondition у нас нет необходимости дополнительно создавать mutex, так как NSCondition самостояельно поддерживает методы lock() и unlock().
Рассмотрим пример использования NSCondition:

```
// Создаем булевый предикат
var boolPredicate = false
// Создаем condition
let condition = NSCondition()
func test1() {
    condition.lock()
    // Проверяем булевый предикат
    while (!boolPredicate) {
    //Переход в состояние ожидания
    condition.wait()
    }
    // Выполняем работу
    print("Hello World!")
    condition.unlock()
    }
func test2() {
    condition.lock()
    boolPredicate = true
    // Выпускаем сигнал в condition
    condition.signal()
    condition.unlock()
    }
```

Для успешной работы condition всегда необходима дополнительная проверка в виде предиката.

### Read Write Lock

Read Write Lock - примитив синхронизации, который предоставляет потоку доступ к ресурсу на чтение, в это время закрывая возможность записи в ресурс из других потоков.
Необходимость использовать wrlock появляется тогда, когда много потоков читают данные, и только один поток их пишет (Reader-writes problem). На первый взгляд кажется, что данную проблему можно решить простым mutex, однако этот подход будет требовать больше ресурсов, нежели простой wrlock, так как фактически нет необходимости локировать доступ к ресурсу полностью.

*Существует только unix вариация rwlock, во фреймворке Foundation нет альтернативы.*

### Spin Lock

Spin Lock - наиболее быстродействующий, но в то же время энергозатратный и ресурсотребовательный mutex. Быстродействие достигается за счет непрерывного опрашивания, освобожден ресурс в данный момент времени или нет.
Рекомендуется использовать spin lock лишь в редких случаях, когда к ресурсу обращается небольшое количество потоков непродолжительное время.

### Unfair Lock

Unfair Lock(iOS 10+) - примитив многопоточности, позволяющий наиболее эффективно захватывать ресурс. По большому счету, unfair lock является более производительной заменой spin lock. Производительность достигается путем максимального сокращения возможных context switch.

*Context switch - процесс переключения между потоками. Для того, чтобы переключаться между потоками, необходимо прекратить работу на текущем потоке, сохранив при этом состояние и всю необходимую информацию, а далее восстановить и загрузить состояние задачи, к выполнению которой переходит процессор. Является энергозатратной и ресурсотребовательной операцией.*

Обычный mutex работает по принципу FIFO, в то время, как unfair lock отдаст предпочтение тому потоку, который чаще обращается к ресурсу, таким образом и достигается сокращение context switch.

## Проблемы

Многопоточность предназначена для решения проблем, но как и любые другие технологии может порождать новые. В большинстве случаев проблемы связанны с доступом к ресурсам. Самые распространенные из них:

* Deadlock - ситуация, в которой поток бесконечно ожидает доступ к ресурсу, который никогда не будет освобожден.
* Priority inversion - ситуация, в которой высокоприоритетная задача ожидает выполнения низкоприоритетной задачи.
* Race condition - ситуация, в которой ожидаемый порядок выполнения операций становится непредсказуемым, в результате чего страдает закладываемая логика.

Deadlock - это ситуация в многозадачной среде, при которой несколько потоков находятся в состоянии ожидания ресурса, занятого друг другом, и ни один из них не может продолжать свое выполнение. Таким образом оба потока бесконечно ожидая друг друга никогда не выполнят задачу, что может привести к неожиданному поведению приложения.

Воспроизведем самый примитивный кейс бесконечной блокировки ресурса:

```
class Deadlock {
    let lock = NSLock()
    var boolPredicate = true
    var counter = 0
    
    func test() {
        lock.lock()
        
        counter += 1
        boolPredicate = counter < 10
        
        if boolPredicate { test() }
        
        lock.unlock()
    }
}
```

Проблема легко решается, если мы будем использовать NSRecursiveLock вместо NSLock. Таким образом поток сможет множество раз захватывать ресурс и не будет ожидать, пока сам же не освободит его.

Воспроизведем deadlock с использованием вложенных блокировок ресурсов:

```
class DeadLock {
    let lock1 = NSLock()
    let lock2 = NSLock()

    var resource1 = 0
    var resource2 = 0
    
    func test() {
        let thread1 = Thread(block: {
            self.lock1.lock()
            
            self.resource1 = 1
            
            self.lock2.lock()
            
            self.resource2 = 1
            
            self.lock2.unlock()
            
            self.lock1.unlock()
        })

        let thread2 = Thread(block: {
            self.lock2.lock()
            
            self.resource2 = 1
            
            self.lock1.lock()
            
            self.resource1 = 1
            
            self.lock1.unlock()
            
            self.lock2.unlock()
        })
        
        thread1.start()
        thread2.start()
    }
}


let deadLock = DeadLock()
deadLock.test()
```

Потоки thread1 и thread2 будут бесконечно ожидать освобождения ресурсов resource1 и resource2, захваченных мьютексами lock1 и lock2. Deadlock возможен только тогда, когда используется вложенный многопоточный доступ к ресурсам. Избегать подобные ситуации давольно просто, старайемся не проектировать заведомо сложные решения там, где можно обойтись и избегаем вложенные доступы к ресурсам как в примере выше.
