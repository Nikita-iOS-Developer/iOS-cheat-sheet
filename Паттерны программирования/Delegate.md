# Delegate

Делегирование - это когда один объект передает задачи другому объекту на выполнение и другой объект может с этими задачами делать все что угодно. Делегирование часто используется для разделение ответственности, чтобы все делал не один клас, а часть какой либо функциональности он передал другому классу. Такое взаимодействие между объектами должно происходить по контракту, через протоколы. 
Таким образом у нас получается три основные сущности которые составляют паттерн delegate: 2 объекта которые будут взаимодействовать между собой и протокол.

## Делегат практика

Рассмотрим на простом примере как это выглидит:

```
// У нас есть два класса Engineer и ProjectManager. Когда у Engineer заканчиваются задачи он говорит своему ProjectManager чтобы он дал ему еще задачь через свойство tasks.

class Engineer {
    var tasks: Int = 0 {
        didSet {
            if tasks == 0 {
                projectManager?.taskHasEnded()
            }
        }
    }
    var projectManager: ProjectManager?
}

class ProjectManager {
    func taskHasEnded() {
        print("Give me tasks")
    }
}
```

В конструкции выше у нас есть недостатки. Мы напрямую работаем с определенным классом в свойстве projectManager. Если у нас изменится класс то нам придется везде переписывать код и тратить на это много времени. Чтобы недопустить такого нам поможет паттерн делегат.
Для того чтобы исправить ситуацию нам нужно вместо класса указать протокол и изменить название переменной:

```
protocol EngineerDelegate: AnyObject {
        func taskHasEnded()
}

class Engineer {
    var tasks: Int = 0 {
        didSet {
            if tasks == 0 {
                delegate?.taskHasEnded()
            }
        }
    }
    weak var delegate: EngineerDelegate?
    
    func taskDone() {
        tasks -= 1
    }
}

class ProjectManager {
    var engineer: Engineer?
    
    func addTasksToEngineer() {
        engineer?.tasks += 1
    }
}

extension ProjectManager : EngineerDelegate {
    func taskHasEnded() {
        print("Give me tasks")
    }
}

var engineer = Engineer()
var manager = ProjectManager()
engineer.delegate = manager
manager.engineer = engineer
manager.addTasksToEngineer()
engineer.taskDone()
```

В коде выше у нас есть два объекта Инженер и Проджект менеджер. Проджект менеджер знаект как работать с инженером (свойство var engineer) поэтому он подписан под протокол EngineerDelegate через который сам инженер уведомляет о своих проблемах и запросах. Мы указали инженеру его делегат (свойство weak var delegate: EngineerDelegate?) через который он уведомляет что у него нет задачь а у менеджера есть инженер которому он может давать задания (функция func addTasksToEngineer()). Когда у инженера количества заданий равняется 0 то в свойстве tasks в методе didSet вызывается свойсто delegate через которое инженер сообщает менеджеру что у него 0 заданий.
Два объекта знают друг о друге и взаимодействуют, что подразумевает собой паттерн делегат.
Расширим наше взаимодействие между инженером и менеджером. Для этого нам просто нужно добавить дополнительный метод в протокол:

```
protocol EngineerDelegate: AnyObject {
    func taskHasEnded()
    func didFinishResearch(data: String)
    func didAnalizeRequirements(results: [String])
}

class Engineer {
    var tasks: Int = 0 {
        didSet {
            if tasks == 0 {
                delegate?.taskHasEnded()
            }
        }
    }
    weak var delegate: EngineerDelegate?
    
    func taskDone() {
        tasks -= 1
    }
    
    func startResearch(_ data: String = "") {
        // use google
        // create the document
        //send the review doc
        delegate?.didFinishResearch(data: data)
    }
    
    func analize(requirements: [String]) {
        // read
        // questions
        // document
        delegate?.didAnalizeRequirements(results: requirements)
    }
}

class ProjectManager {
    var engineer: Engineer?
    
    func addTasksToEngineer(_ numberOfTasks: Int = 2) {
        engineer?.tasks = numberOfTasks
    }
}

extension ProjectManager : EngineerDelegate {
    func didFinishResearch(data: String) {
        addTasksToEngineer(1)
    }
    
    func didAnalizeRequirements(results: [String]) {
        print("Client: Clirify requirements")
    }
    
    func taskHasEnded() {
        print("Give me tasks")
    }
}

var engineer = Engineer()
var manager = ProjectManager()
engineer.delegate = manager
manager.engineer = engineer

manager.addTasksToEngineer()
engineer.taskDone()
engineer.analize(requirements: [])
engineer.startResearch()
engineer.taskDone()
```

В примере выше мы добавили новую функциональность менеджеру (делегату) и инженеру. Эти два объекта взаимодействуют друг с другом через свои методы.
Рассмотрим теперь случай когда менеджер нам больше не нужен, а нужен будет другой класс:

```
protocol EngineerDelegate: AnyObject {
    func taskHasEnded()
    func didFinishResearch(data: String)
    func didAnalizeRequirements(results: [String])
}

class Engineer {
    var tasks: Int = 0 {
        didSet {
            if tasks == 0 {
                delegate?.taskHasEnded()
            }
        }
    }
    weak var delegate: EngineerDelegate?
    
    func taskDone() {
        tasks -= 1
    }
    
    func startResearch(_ data: String = "") {
        // use google
        // create the document
        //send the review doc
        delegate?.didFinishResearch(data: data)
    }
    
    func analize(requirements: [String]) {
        // read
        // questions
        // document
        delegate?.didAnalizeRequirements(results: requirements)
    }
}

class ProjectManager {
    var engineer: Engineer?
    
    func addTasksToEngineer(_ numberOfTasks: Int = 2) {
        engineer?.tasks = numberOfTasks
    }
}

class Client {
    var engineer: Engineer?
}

extension Client : EngineerDelegate {
    func taskHasEnded() {
        print("Give me tasks")
        engineer?.tasks += 1
    }
    
    func didFinishResearch(data: String) {
        engineer?.tasks += 2
    }
    
    func didAnalizeRequirements(results: [String]) {
        print("Answers")
    }
}

extension ProjectManager : EngineerDelegate {
    func didFinishResearch(data: String) {
        addTasksToEngineer(1)
    }
    
    func didAnalizeRequirements(results: [String]) {
        print("Client: Clirify requirements")
    }
    
    func taskHasEnded() {
        print("Give me tasks")
    }
}

var engineer = Engineer()
var manager = ProjectManager()
engineer.delegate = manager
manager.engineer = engineer

manager.addTasksToEngineer()
engineer.taskDone()
engineer.analize(requirements: [])
engineer.startResearch()
engineer.taskDone()

manager.engineer = nil
engineer.delegate = nil

var client = Client()
client.engineer = engineer
engineer.delegate = client
engineer.tasks += 1
engineer.taskDone()
```

В примере выше мы дополнили наш код а не изменили его, добавили новые требования и новые реализации. Это одно из достоинств паттерна делегат.

## Делегат на примере UIKit

```
protocol PayViewDelegate: AnyObject {
    func didPressPayBtton()
}

class ViewController {
    var payView: PayView?
    
    // Делаем наш класс ViewController делегатом
    init(payView: PayView) {
        self.payView = payView
        payView.delegate = self
    }
}

class PayView {
    weak var delegate: PayViewDelegate?
    
    func action() {
        // some animation code
        delegate?.didPressPayBtton()
    }
}

extension ViewController: PayViewDelegate {
    func didPressPayBtton() {
        // show the pay screen
    }
}
```

### Один ко многим

Рассмотрим пример когда у нас много объектов которые имеют один делегат:

```
protocol PayViewDelegate: AnyObject {
    func didPressPayBtton()
}

class ViewController {
    var payView: [PayView]
    
    // Делаем наш класс ViewController делегатом
    init(payView: [PayView]) {
        self.payView = payView
        payView.forEach {$0.delegate = self}
    }
}

class PayView {
    var price: Double = 0.0
    
    weak var delegate: PayViewDelegate?
    
    func action() {
        // some animation code
        delegate?.didPressPayBtton()
    }
}

extension ViewController: PayViewDelegate {
    func didPressPayBtton() {
        // show the pay screen
        print("show pay screen")
    }
}

var monitor = PayView()
monitor.price = 500
var headset = PayView()
headset.price = 300
var mouse = PayView()
mouse.price = 50

var screen = ViewController(payView: [monitor, headset, mouse])
monitor.action()
```

В примере выше у нас у трех объектов один делегат. Но делегат непонимает какой определенный объект к нему обращается. Для этого когда мы понимаем что у нас может быть такая связь (один ко многим) мы должны передавать сам объект:

```
protocol PayViewDelegate: AnyObject {
    func didPressPayBtton(_ view: PayView) // В UIKit передаем самую верхнюю иерархию UIView
}

class PayView {
    var price: Double = 0.0
    
    weak var delegate: PayViewDelegate?
    
    func action() {
        // some animation code
        delegate?.didPressPayBtton(self)
    }
}

class ViewController {
    var payView: [PayView]
    
    // Делаем наш класс ViewController делегатом
    init(payView: [PayView]) {
        self.payView = payView
        payView.forEach {$0.delegate = self}
    }
}

extension ViewController: PayViewDelegate {
    func didPressPayBtton(_ view: PayView) {
        // show the pay screen
        print("show pay screen")
    }
}
```

Если у нас очень большой делегат то следует его разбивать на несколько логических делегатов.
