## DRY
don’t repeat yourself(не повторяй себя)
Тут все просто, не плодим повторы, все разносим в переиспользуемые блоки
## KISS
Keep it simple, stupid/Keep it stupid simple(Сделай это проще, тупица/Пусть всё будет простым до безобразия)
Аналогично предыдущему, делать все проще и все\
## YAGNI
You ain’t gonna need it(Вам это не понадобится)
Просто не плодить лишнего в коде
## SOLID
вот тут подробнее
### S
Single Responsibility Principle/принцип единственной ответственности
Каждый класс должен отвечать только за одну зону ответственности (действий), чтобы его было проще дополнять и менять. Изменение должно минимально затрагивать код. Следует разделить функциональность большого класса на более мелкие, отвечающие за конкретные аспекты, части.
Короче говоря - каждый класс или функция должны решать лишь одну задачу, не более. Если у вас есть функция или класс, который меняется по нескольким причинам, это первый звоночек, что вы нарушаете SRP.
Хороший пример
```go
package main

import (
	"fmt"
)

// Объект для суммирования чего-то
type Summator struct{}

func (s Summator) sum(a, b int) int {
	return a + b
}

func main() {
	summator := Summator{}
	fmt.Println(summator.sum(1, 5))
}
```

Плохой пример
```go
package main

import (
	"fmt"
)

// Объект для суммирования чего-то
type Summator struct{}

func (s Summator) sum(a, b int) int {
	return a + b
}

func (s Summator) del(a, b int) int {
	return a / b
}

func main() {
	summator := Summator{}
	fmt.Println(summator.sum(1, 5))
	fmt.Println(summator.del(1, 5))
}
```

### O
Open Closed Principle/принцип открытости-закрытости
Класс должен быть закрыт для изменения, но открыт для расширения. Пишем код так, чтобы другие могли легко расширить функционал, не меняя написанный (оттестированный, понравившийся твоему начальнику) код, чтобы для добавления новой функциональности не требовалось менять существующий код. Соблюдение этого уменьшает вероятность возникновения багов, т.к вам не нужно трогать уже работающий код
Хороший пример
```go
package main

import "fmt"

type Printer interface {
	Print(data string)
}

type TextPrinter struct{}

func (p *TextPrinter) Print(data string) {
	fmt.Println("Data: ", data)
}

type HTMLPrinter struct{}
  
func (h *HTMLPrinter) Print(data string) {
	fmt.Println("<html>" + data + "</html>")
}

func main() {
	var printer Printer
	
	printer = &TextPrinter{}
	printer.Print("Hello, World!")
	  
	printer = &HTMLPrinter{}
	printer.Print("Hello, HTML World!")
}
```

Плохой пример
```go
package main

import "fmt"

type Printer struct{}
  
func (p *Printer) Print(data string) {
	fmt.Println("Data: ", data)
}

// Допустим, нам нужно добавить функционал печати в HTML
// придется измнить класс Printer, что нарушает OCP
func (p *Printer) PrintHTML(data string) {
	fmt.Println("<habr>" + data + "</habr>")
}

func main() {
	printer := Printer{}
	printer.Print("Hello, World!")
	printer.PrintHTML("Hello, HABR World!")
}
```

### L
Liskov Substitution Principle/принцип подстановки Барбары Лисков
Если в коде программы Базовый класс заменить на его Наследника, то программа должна работать, так как в Наследнике есть все операции, которые были в Базовом. В Базовый класс нужно выносить только общую логику, которую наследники будут реализовывать. Наследников создаем только тогда, когда они правильно собираются реализовать логику Базового класса без проблем.
В Go особо не работает, так как нет классов в классическом их понимании
### I
Interface Segregation Principle/принцип разделения интерфейса
Клиенты не должны зависеть от интерфейсов, которые они не используют. Большие интерфейсы следует разбивать на интерфейсы поменьше. Так клиенты смогут использовать только те интерфейсы, которые им нужны. Это делает менее связанный код, уменьшает зависимости между элементами системы, упрощает изменения в коде.
Хороший пример
```go
package main

type Printer interface {
	Print(document string)
}

type Scanner interface {
	Scan(document string)
}

// MultiFunctionDevice наследует от обоих интерфейсов
type MultiFunctionDevice interface {
	Printer
	Scanner
}

// класс, реализующий только функцию печати
type SimplePrinter struct{}

func (p *SimplePrinter) Print(document string) {
// реализация печати
}

// класс, реализующий только функцию печати
type SimpleScanner struct{}
  
func (p *SimpleScanner) Scan(document string) {
// реализация скана
}

// класс, реализующий обе функции
type AdvancedPrinter struct{}

func (p *AdvancedPrinter) Print(document string) {
}

func (p *AdvancedPrinter) Scan(document string) {
}
```

Плохой пример
```go
package main

// MultiFunctionDevice наследует от обоих интерфейсов
type MultiFunctionDevice interface {
	Print(document string)
	Scan(document string)
}

// класс, реализующий только функцию печати
type SimplePrinter struct{}

func (p *SimplePrinter) Print(document string) {
// реализация печати
}

// нужна просто для соотвествия интерфейса, хотя в структуре не нужна
func (p *SimplePrinter) Scan(document string) {
// реализация скана
}

// класс, реализующий только функцию печати
type SimpleScanner struct{}

// нужна просто для соотвествия интерфейса, хотя в структуре не нужна
func (p *SimpleScanner) Print(document string) {
// реализация печати
}

func (p *SimpleScanner) Scan(document string) {
// реализация скана
}
```

### D
Dependency Inversion Principle/принцип инверсии зависимостей
Модули верхних уровней не должны зависеть от модулей нижних уровней. Оба типа модулей должны зависеть от абстракций. Абстракции не должны зависеть от реализации. Реализация должна зависеть от абстракции.
Хороший пример
```go
package main

import "fmt"

// Интерфейс для абстракции хранения данных
type DataStorage interface {
	Save(data string)
}

// Низкоуровневый модуль для хранения данных в файле
type FileStorage struct{}

func (fs *FileStorage) Save(data string) {
	fmt.Println("Сохранение данных в файл:", data)
}

// Высокоуровневый модуль, не зависит напрямую от FileStorage
type DataManager struct {
	storage DataStorage // зависит от абстракции
}

func (dm *DataManager) SaveData(data string) {
	dm.storage.Save(data) // делегирование сохранения
}

func main() {
	fs := &FileStorage{}
	dm := DataManager{storage: fs}
	dm.SaveData("Тестовые данные")
}
```

Плохой пример
```go
package main

import "fmt"

// Низкоуровневый модуль для хранения данных в файле
type FileStorage struct{}

func (fs *FileStorage) Save(data string) {
	fmt.Println("Сохранение данных в файл:", data)
}

// Высокоуровневый модуль, зависит напрямую от FileStorage
type DataManager struct {
	storage *FileStorage // зависит от FileStorage
}

func (dm *DataManager) SaveData(data string) {
	dm.storage.Save(data) // делегирование сохранения
}

func main() {
	fs := &FileStorage{}
	dm := DataManager{storage: fs}
	dm.SaveData("Тестовые данные")
}
```