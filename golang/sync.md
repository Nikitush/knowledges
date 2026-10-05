В go существуют много механизмов синхронизации, например:
- Mutex(RWMutex)
- Atomic
- Channel
- WaitGroup
>[!info]
>По факту многие(вроде даже все) примитивы синхронизации в go построены на atomic'ах
## Мьютексы(+RW)
Необходимы для того, чтобы критическая секция секция кода выполнялась единственным исполнителем(в нашем случае горутиной)
Обычный отличается от RW тем, что RW имеет возможность блокировки так, что читать может общую память много горутин, т.к. при чтении фрагемент в памяти не подвержен изменениям, а значит мы не отстрелим себе ногу
Пример плохой
```go
package main

import (
	"fmt"
	"sync"
)

func main() {
	number := 0
	wg := &sync.WaitGroup{}
		for i := 0; i < 100; i++ {
			wg.Add(1)
			go func(i int) {
				defer wg.Done()
				number += 1
				}(i)
		}
	wg.Wait()
	fmt.Println(number)
}
```

Вывод 100 не гарантирован
```bash
go run ex.go 
95
go run ex.go 
87
go run ex.go 
94
go run ex.go 
100
```

Хороший пример с защитой критической секции
```go
package main

import (
	"fmt"
	"sync"
) 

func main() {
	number := 0
	mu := sync.Mutex{}
	wg := &sync.WaitGroup{}
	for i := 0; i < 100; i++ {
		wg.Add(1)
		go func(i int) {
			defer wg.Done()
			mu.Lock()
			defer mu.Unlock()
			number += 1
		}(i)
	}
	wg.Wait()
	fmt.Println(number)
}
```

```bash
go run ex.go 
100
```

## Atomic
Содержит в себе атомарные операции, потокобезопасные, т.к. выполнение занимает один такт процессора, в который ничего другого выполняться не может
Пример из mutex переписанный на atomic
```go
package main

import (
	"fmt"
	"sync"
	"sync/atomic"
)

func main() {
	//number := atomic.Int64{}
	number := int64(0)
	wg := &sync.WaitGroup{}
	for i := 0; i < 100; i++ {
		wg.Add(1)
		go func(i int) {
			defer wg.Done()
			//number.Add(1)
			atomic.AddInt64(&number, 1)
		}(i)
	}
	wg.Wait()
	fmt.Println(number) //number.Load()
}
```

```bash
go run ex.go 
100
```

## WaitGroup
Призвана для того, чтобы мочь ожидать выполнения необходимых нам горутин
```go
package main

import (
	"fmt"
	"sync"
	"sync/atomic"
)

func main() {
	number := int64(0)
	wg := &sync.WaitGroup{}
	for i := 0; i < 100; i++ {
		wg.Add(1)
		go func(i int) {
			defer wg.Done()
			atomic.AddInt64(&number, 1)
		}(i)
	}
	wg.Wait()
	fmt.Println(number)
}
```

```go
go run ex.go 
100
```

## Channel
Чисто гошная штука для общения между горутинами
Пример
```go
package main

import (
	"fmt"
)  

func work(ch chan int, done chan struct{}) {
	for {
		select {
		case v := <-ch:
			fmt.Println(v)
		case <-done:
			return
		}
	}
}

func main() {
	ch := make(chan int)
	done := make(chan struct{})
	go work(ch, done)
	for i := 0; i < 10; i++ {
		ch <- i
	}
	done <- struct{}{}
	close(ch)
	close(done)
}
```

```bash
go run ex.go
0
1
2
3
4
5
6
7
8
9
```