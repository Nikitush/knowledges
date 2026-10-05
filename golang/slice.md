Слайс представляет из себя динамический массив, то есть массив переменной длины в отличие от обычного массива с константным количеством элементов
```go
type slice struct {
	array unsafe.Pointer -- ссылка на базоый массив
	len   int -- длина слайса
	cap   int -- капасити/максимальная емкость базового массива/слайса
}
```

Слайс может получиться, например, если мы из базового массива сделаем срез
При этом, если мы начинаем с 0-о элемента, то базовый массив слайса будет исходным массивом, с которого слайс нарезали
```go
package main

import (
	"fmt"
)

func main() {
	ms := [7]int{0, 1, 2, 14, 17, 21, 7}
	fmt.Println(ms)
	fmt.Println("len:", len(ms))
	fmt.Println("cap:", cap(ms))
	fmt.Println(fmt.Sprintf("%p", &ms))
	sl := ms[:4]
	fmt.Println(sl)
	fmt.Println("len:", len(sl))
	fmt.Println("cap:", cap(sl))
	fmt.Println(fmt.Sprintf("%p", sl))
}
```

```bash
go run ex.go 
[0 1 2 14 17 21 7]
len: 7
cap: 7
0xc00009a000
[0 1 2 14]
len: 4
cap: 7
0xc00009a000
```

И тут есть один трюк как ограничить капасити
В go в отличие от python, например, нет возможности в срезе задать шаг, вместо этого можно поменять капасити
`[0:4:4]` -- следует читать как: делаем слайс начиная с 0 элемента по 4(`[0:4]` обычный вариант записи), но при этом, ограничиваем капасити по следующей формуле, из записи `[0:4:4]` берем первый элемент(начало среза) и последний элемент(число из которого будет считаться капасити), получается 4-0 == 4
Теперь капасти нового слайса будет 4, при этом, капасити не может быть меньше чем число по средине, иначе слайс не влезет
```go
package main

import (
	"fmt"
)

func main() {
	ms := [7]int{0, 1, 2, 14, 17, 21, 7}
	fmt.Println(ms)
	fmt.Println("len:", len(ms))
	fmt.Println("cap:", cap(ms))
	fmt.Println(fmt.Sprintf("%p", &ms))
	sl := ms[0:4:4]
	fmt.Println(sl)
	fmt.Println("len:", len(sl))
	fmt.Println("cap:", cap(sl))
	fmt.Println(fmt.Sprintf("%p", sl))
}
```

```bash
go run ex.go 
[0 1 2 14 17 21 7]
len: 7
cap: 7
0xc00009a000
[0 1 2 14]
len: 4
cap: 4
0xc00009a000
```

Также мы можем начинать и не с 0-о элемента, тогда просто создасться слайс который не будет указывать на начало, а на тот элемент, с которого мы начали вырезать, но при этом капасити будет равна кол-во элементов исходного минус индекс начала
```go
package main

import (
	"fmt"
)
  
func main() {
	ms := [7]int{0, 1, 2, 14, 17, 21, 7}
	fmt.Println(ms)
	fmt.Println("len:", len(ms))
	fmt.Println("cap:", cap(ms))
	fmt.Println(fmt.Sprintf("%p", &ms))
	sl := ms[3:6]
	fmt.Println(sl)
	fmt.Println("len:", len(sl))
	fmt.Println("cap:", cap(sl))
	fmt.Println(fmt.Sprintf("%p", sl))
}
```

```bash
go run ex.go 
[0 1 2 14 17 21 7]
len: 7
cap: 7
0xc000020100
[14 17 21]
len: 3
cap: 4
0xc000020118
```

И можно просто взять и задать его изначально
```go
package main

import (
	"fmt"
)

func main() {
	sl := []int{0, 1, 2, 3}
	fmt.Println(sl)
	fmt.Println("len:", len(sl))
	fmt.Println("cap:", cap(sl))
	fmt.Println(fmt.Sprintf("%p", sl))
}
```

```bash
go run ex.go 
[0 1 2 3]
len: 4
cap: 4
0xc00001e140
```

Иначе
```go
package main

import (
	"fmt"
)

func main() {
	var sl []int
	fmt.Println(sl)
	fmt.Println("len:", len(sl))
	fmt.Println("cap:", cap(sl))
	fmt.Println(fmt.Sprintf("%p", sl))
	sl = append(sl, 1)
	fmt.Println(sl)
	fmt.Println("len:", len(sl))
	fmt.Println("cap:", cap(sl))
	fmt.Println(fmt.Sprintf("%p", sl))
}
```

```bash
go run ex.go
[]
len: 0
cap: 0
0x0
[1]
len: 1
cap: 1
0xc0000120f0
```

Но есть более интересный способ через make
Для слайса у него есть 3 аргумента: тип, длина исходная, капасити
```go
package main

import (
	"fmt"
)

func main() {
	sl := make([]int, 2, 5)
	fmt.Println(sl)
	fmt.Println("len:", len(sl))
	fmt.Println("cap:", cap(sl))
	fmt.Println(fmt.Sprintf("%p", sl))
}
```

```bash
go run ex.go
[0 0] -- дефолтные значения типа int, а 2 их, так как длин 2
len: 2
cap: 5
0xc00001c210
```

Так как слайс это динамический массив, то в него можно добавлять элементы
Для этого есть функция `append`
```go
package main

import (
	"fmt"
)

func main() {
	sl := []int{0, 2, 4, 6, 8}
	fmt.Println(sl)
	fmt.Println("len:", len(sl))
	fmt.Println("cap:", cap(sl))
	fmt.Println(fmt.Sprintf("%p", sl))
	sl = append(sl, 1, 2, 3)
	fmt.Println(sl)
	fmt.Println("len:", len(sl))
	fmt.Println("cap:", cap(sl))
	fmt.Println(fmt.Sprintf("%p", sl))
}
```

```bash
go run ex.go
[0 2 4 6 8]
len: 5
cap: 5
0xc00001c210
[0 2 4 6 8 1 2 3]
len: 8
cap: 10
0xc000024050
```

И в `append` есть интересное поведение -- это процесс роста базового массива
Как видно из примера капасити увеличилось в 2 раза, но так происходит не всегда
Далее будет приведена функция роста слайса
```go
func growslice(oldPtr unsafe.Pointer, newLen, oldCap, num int, et *_type) slice {
	...

	newcap := oldCap
	doublecap := newcap + newcap
	if newLen > doublecap {
		newcap = newLen
	} else {
		const threshold = 256
		if oldCap < threshold {
			newcap = doublecap
		} else {
			// Check 0 < newcap to detect overflow
			// and prevent an infinite loop.
			for 0 < newcap && newcap < newLen {
				// Transition from growing 2x for small slices
				// to growing 1.25x for large slices. This formula
				// gives a smooth-ish transition between the two.
				newcap += (newcap + 3*threshold) / 4
			}
			// Set newcap to the requested cap when
			// the newcap calculation overflowed.
			if newcap <= 0 {
				newcap = newLen
			}
		}
	}

	...
} -- для go 1.21.13
```

Как растет слайс по шагам:
1. Если новая len больше, чем двойная старая cap, то новая cap будет равна новой len
2. Если новая len меньше, чем двойная старя cap, и старая cap меньше определенного размера(в 1.21.13 это 256), то новая cap будет равна двойной старой cap
3. Если предыдущие шаги не подходят, то новая cap будет равна сумме старой cap и одной четвертой от суммы старой cap и трёх пороговых значений(в 1.21.13 это 256)
Также срезы можно копировать
Это нужно, если мы хотим аналогичный слайс, но при этом ссылающийся на другое место в памяти и мы могли его воротить как хотим, при этом не убивая первый, а мы помним, что без специальных средств дочерний слайс будет ссылаться и на массив родителя, так что можно выстрелить себе в ногу
Пример проблемы
```go
package main

import (
	"fmt"
)

func main() {
	sl := []int{0, 2, 4, 6, 8}
	fmt.Println(sl)
	fmt.Println("len:", len(sl))
	fmt.Println("cap:", cap(sl))
	fmt.Println(fmt.Sprintf("%p", sl))
	sl1 := sl[:] // или sl1 := sl
	fmt.Println(sl1)
	fmt.Println("len:", len(sl1))
	fmt.Println("cap:", cap(sl1))
	fmt.Println(fmt.Sprintf("%p", sl1))
	sl1[2] = 15
	fmt.Println(sl)
	fmt.Println(sl1)
}
```

```bash
go run ex.go
[0 2 4 6 8]
len: 5
cap: 5
0xc00001c210
[0 2 4 6 8]
len: 5
cap: 5
0xc00001c210
[0 2 15 6 8]
[0 2 15 6 8]
```

Копированием избегаем данную проблему
```go
package main

import (
	"fmt"
)

func main() {
	sl := []int{0, 2, 4, 6, 8}
	fmt.Println(sl)
	fmt.Println("len:", len(sl))
	fmt.Println("cap:", cap(sl))
	fmt.Println(fmt.Sprintf("%p", sl))
	sl1 := make([]int, len(sl), cap(sl)) // следует сделать через make и соблюсти размерность
	copy(sl1, sl)
	fmt.Println(sl1)
	fmt.Println("len:", len(sl1))
	fmt.Println("cap:", cap(sl1))
	fmt.Println(fmt.Sprintf("%p", sl1))
	sl1[2] = 15
	fmt.Println(sl)
	fmt.Println(sl1)
}
```

```bash
go run ex.go
[0 2 4 6 8]
len: 5
cap: 5
0xc00001c210
[0 2 4 6 8]
len: 5
cap: 5
0xc00001c240
[0 2 4 6 8]
[0 2 15 6 8]
```

Один из самых распространенных приколов -- передача слайса в функцию
Как мы знаем все передается по значению(копируется), но копируется и указатель на базовый массив
```go
package main

import (
	"fmt"
)

func changeSl(sl []int) {
	sl[0] = 115
	sl = append(sl, 1)
	fmt.Println("\nStart in func")
	fmt.Println(sl)
	fmt.Println("len:", len(sl))
	fmt.Println("cap:", cap(sl))
	fmt.Println(fmt.Sprintf("%p", sl))
	fmt.Println("End in func\n")
}

func main() {
	sl := []int{0, 2, 4, 6, 8}
	fmt.Println(sl)
	fmt.Println("len:", len(sl))
	fmt.Println("cap:", cap(sl))
	fmt.Println(fmt.Sprintf("%p", sl))
	changeSl(sl)
	fmt.Println(sl)
	fmt.Println("len:", len(sl))
	fmt.Println("cap:", cap(sl))
	fmt.Println(fmt.Sprintf("%p", sl))
}
```

```bash
go run ex.go
[0 2 4 6 8]
len: 5
cap: 5
0xc00001c210

Start in func
[115 2 4 6 8 1]
len: 6
cap: 10
0xc000024050
End in func

[115 2 4 6 8]
len: 5
cap: 5
0xc00001c210
```

Происходит так по нескольким причинам
Первая -- это слайсы, не смотря на передачу по значению, структура слайса содержит указатель на место в памяти, где хранится массив, и он копируется в принимающую переменную, которая с ним в последствии работает, поэтому 0-й элемент сменил значение на 115
Вторая -- область видимости переменной и передача по значению, sl в функции это копия sl в main и при увеличении len/cap sl в функции эти изменения будут видны только в функции, sl в main ничего об этом знать не будет, хотя на самом деле, массовый массив пополнится после append, при условии, что хватило cap и не создавался новый базовый массив, но sl в main из-за len/cap не увидит его, ведь они увеличились только у sl в функции

И вот еще один фокус, который показывает как достучаться до тех элементов, которые есть в памяти, но len официально 0
```go
package main

import (
	"fmt"
)

func main() {
	sl := []int{0, 2, 4, 6, 8}
	fmt.Println(sl)
	fmt.Println("len:", len(sl))
	fmt.Println("cap:", cap(sl))
	fmt.Println(fmt.Sprintf("%p", sl))
	sl = sl[:3]
	fmt.Println(sl)
	fmt.Println("len:", len(sl))
	fmt.Println("cap:", cap(sl))
	fmt.Println(fmt.Sprintf("%p", sl))
	sl1 := sl[3:]
	fmt.Println(sl1[:2])
	fmt.Println("len:", len(sl1))
	fmt.Println("cap:", cap(sl1))
	fmt.Println(fmt.Sprintf("%p", sl1))
}
```

```bash
go run ex.go
[0 2 4 6 8]
len: 5
cap: 5
0xc00001c210
[0 2 4]
len: 3
cap: 5
0xc00001c210
[6 8]
len: 0
cap: 2
0xc00001c228
```