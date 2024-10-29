
`Defer` — это ключевое слово в Go, которое позволяет отложить выполнение функции до момента завершения выполнения текущей функции.

В Go каждый `defer` помещает вызов функции в **стек отложенных вызовов**. Этот стек работает по принципу **LIFO**, то есть последний добавленный вызов выполняется первым.

`Defer` также хорош для обработки ситуаций, когда программа неожиданно "_паникует_".

```go
 defer func() {        
	 if r := recover(); r != nil {            
		 fmt.Println("Восстановление после паники:", r)        
	 }    
 }()
```

Подсчёт времени работы функции:
```go
func logFunctionTime() func() {
	start := time.Now()
	return func() {
		fmt.Printf("Время выполнения: %v\n", time.Since(start))
	}
}

func funcA() {
	defer logFunctionTime()()
	time.Sleep(1 * time.Second)
	fmt.Println("Выполнение функции A")
}
```

Оператор `defer` сразу захватывает копию значения. Если значение потом будет изменено, то `defer` вернёт самое первое значение.

```go
func pushAnalytic(a int) {
  fmt.Println(a) // будет выведено 10, а не 20
}
func main() {
  a := 10
  defer pushAnalytic(a)

  a = 20
}
```

Чтобы это исправить, нужно использовать замыкание(анонимную функцию). таким образом, будет захват переменной по ссылке, а не по значению:
```go
defer func() {
    pushAnalytic(a)
  }()
```

Или можно сразу передавать адрес значения:
```go
func pushAnalytic(a *int) {
  fmt.Println(*a)
}
func main() {
  a := 10
  defer pushAnalytic(&a)
  a = 20
}
```

Вторая ловушка со структурами:
```go
type Data struct {
  a int
}

func (d Data) pushAnalytic() {
  fmt.Println(d.a)
}

func main() {
  d := Data{a: 10}
  defer d.pushAnalytic() // выведет 10

  d.a = 20
}
```

Это происходит из-за того, что `func(d Data) pushAnalytic(... args)` под капотом работает как `func pushAnalytic(d Data, ... args)`. Т.е. сам язык в начало любого метода просто добавляет структуру, как переменную, но как мы знаем, будет передавать копия. 

Мы точно также можем использовать замыкание, но просто передача адреса уже не сработает, нам потребуется передавать в функцию адрес структуры, например, вот так:
```go
func (d *Data) pushAnalytic() {
  fmt.Println(d.a)
}
```


При работе с ошибками, например, когда мы закрываем файл, при обычном `defer f.Close()` ошибка при закрытии будет потеряна. Для получения ошибки можно делать так:
```go
func doSomething() (err error) {
  f, err := os.Open("phuong-secrets.txt")
  if err != nil {
    return err
  }
  defer func() {
    err = errors.Join(err, f.Close())
  }()
}
```


При переборе объекты будут изменяться в рантайме, из-за чего `defer` будет выделять на `heap`. Но если мы будем перебирать объекты через `if`, то `defer` будет выделять на `stack`:
```go
func testDefer(a int) {
	if a == unpredictableNumber {
		defer println("Defer in if") // stack-allocated defer
	}
	if a == unpredictableNumber+1 {
		defer println("Defer in if") // stack-allocated defer
	}

  for range a {
    defer println("Defer in for") // heap-allocated defer
  }
}
```


## Задачи

```go
type X struct {
	V int
}

func (x X) S() {
	fmt.Println(x.V)
}

func main() {
	x := X{123}
	defer x.S() // тут выведет 123, т.к. захватило копию, можно сделать (x *X)
	defer func() {
		x.S() // тут выведет 456, т.к. захватило из области видимости после изменения
	}()
	x.V = 456
}
```

```go
func test() (x int) {
	defer func(n int) {
		fmt.Printf("x as parameter: %d ", n) // выведет 0
		fmt.Printf("x after return: %d ", x) // выведет 9
		x *= 10
	}(x)

	x = 7
	return 9
}

func main() {
	fmt.Printf("result: %d", test()) // выведет 90
}
```