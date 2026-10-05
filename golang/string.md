Строки в golang -- это массив байт
Поэтому они являются иммутабельными, то есть при конкатенации, например, не записывается в тот же массив, а создается новая переменная с результатом
```go
package main

import (
	"fmt"
)

func main() {
	str := "Hello"
	strMult := "Hello\nGays"
	// raw string
	strMultOne := `Hello
	Gays`
	fmt.Println(str)
	fmt.Println(strMult)
	fmt.Println(strMultOne)
}
```

```bash
go run ex.go 
Hello
Hello
Gays
Hello
        Gays
```

Пример конкатенации
```go
package main

import (
	"fmt"
)

func main() {
	str := "Hello"
	str1 := "I"
	str2 := str + str1
	fmt.Println(&str)
	fmt.Println(&str1)
	fmt.Println(&str2)
	fmt.Println(str2)
}
```

```bash
go run ex.go 
0xc000014070
0xc000014080
0xc000014090
HelloI
```

Так как это массив байт, то длину строки посимвольно простым len не считать
Для этого можно воспользоваться несколькими хаками
А не считать, так как не все символы залезают в стандартную кодировку из 8 бит(1 байт)
Исходный код в Go использует кодировку UTF-8, поэтому строки обычно представлены в этой кодировке (если значения строк заданы в тексте программы)

```go
package main

import (
	"fmt"
	"unicode/utf8"
)

func main() {
	str := "Приветы"
	str1 := "Hello"
	fmt.Println(len(str))
	fmt.Println(len([]rune(str)))
	fmt.Println(utf8.RuneCountInString(str))
	fmt.Println(len(str1))
	fmt.Println(len([]rune(str1)))
	fmt.Println(utf8.RuneCountInString(str1))
}
```

```bash
go run ex.go 
14
7
7
5
5
5
```

Тут стоит упомянуть про такие типы как byte и rune, по факту это алиасы на другие типы
```go
// byte is an alias for uint8 and is equivalent to uint8 in all ways. It is
// used, by convention, to distinguish byte values from 8-bit unsigned
// integer values.
type byte = uint8

// rune is an alias for int32 and is equivalent to int32 in all ways. It is
// used, by convention, to distinguish character values from integer values.
type rune = int32
```

Так как строки это массив байт, то по нему можно итерироваться, но будут байты
```go
package main

import (
	"fmt"
) 

func main() {
	str := "Приветы"
	for _, v := range str {
		fmt.Println(v)
	}
}
```

```bash
go run ex.go 
1055
1088
1080
1074
1077
1090
1099
```

Это решается следующими способами
```go
package main

import (
	"fmt"
)

func main() {
	str := "Приветы"
	for _, v := range str {
		fmt.Println(string(v))
	}
}
```

```bash
go run ex.go 
П
р
и
в
е
т
ы
```

```go
package main

import (
	"fmt"
	"strconv"
)

func main() {
	str := "Приветы"
	for _, v := range str {
		fmt.Println(strconv.QuoteRune(v))
	}
}
```

```bash
go run ex.go 
'П'
'р'
'и'
'в'
'е'
'т'
'ы'
```

Конкатенация через `+` это хорошо, но не совсем быстро и если в это упираться, то стоит поглядеть на `strings.Builder`
```go
package main

import (
	"fmt"
	"strings"
)

func main() {
	str := "Приветы"
	str1 := "Hi"
	builder := strings.Builder{}
	builder.Grow(len(str) + len(str1))
	builder.WriteString(str)
	builder.WriteString(str1)
	fmt.Println(builder.String())
}
```

```bash
go run ex.go 
ПриветыHi
```

Либо еще через `bytes.Buffer`, но по факту все они просто оперируют слайсами байт и можно написать свое
```go
package main

import (
	"bytes"
	"fmt"
) 

func main() {
	str := "Приветы"
	str1 := "Hi"
	buffer := bytes.Buffer{}
	buffer.WriteString(str)
	buffer.WriteString(str1)
	fmt.Println(buffer.String())
}
```

```bash
go run ex.go 
ПриветыHi
```

И еще строки можно форматировать и получить либо новые строки, либо просто выводить форматированные
```go
package main

import (
	"fmt"
)

func main() {
	str := "Приветы"
	str1 := "Hi"
	fmt.Printf("%s %s\n", str, str1)
	newStr := fmt.Sprintf("%s %s", str, str1)
	fmt.Println(newStr)
}
```

```bash
go run ex.go 
Приветы Hi
Приветы Hi
```

Форматирование строк в int и наоборот, а также в разные системы счисления
```go
package main

import (
	"fmt"
	"strconv"
)

func main() {
	str := "1"
	num, err := strconv.Atoi(str)
	if err != nil {
		panic("Не перевели строку в int")
	}
	fmt.Println(num)
	str1 := strconv.Itoa(num)
	fmt.Println(str1)
	// Переводим в разные системы счисления
	str2 := "1101"
	num1, err := strconv.ParseInt(str2, 2, 64)
	if err != nil {
		panic("Не перевели строку в int64")
	}
	fmt.Println(num1)
	str3 := strconv.FormatInt(num1, 2)
	fmt.Println(str3)
}
```

```bash
go run ex.go 
1
1
13
1101
```