это тип, который определяет набор методов, которые должны быть реализованы структурой или другим типом, чтобы удовлетворять этому интерфейсу. Интерфейсы позволяют писать код, который может работать с различными типами данных, если эти типы данных реализуют определенные методы.

```go
type iface struct {	
	tab  *itab	
	data unsafe.Pointer
}
```

`tab` - таблица, где хранится вся информация по методам, типам и прочему
`data` - данные, которые хранит данный тип
Например, у `var a int := 5` `int` хранится в таблице `tab`, а `5` хранится в `data`


### Пустой интерфейс inteface{}

Пустой интерфейс `interface{}` не содержит никаких методов и может быть реализован любым типом. Это полезно для создания обобщенных функций и структур данных.

```go
func Add(a interface{}, b interface{}) (interface{}, error) { 
	switch v := a.(type) { 
		case int: 
		switch w := b.(type) { 
			case int: 
				return v + w, nil 
			case float64: 
				return float64(v) + w, nil 
			default: 
				return nil, fmt.Errorf("unsupported type for b: %T", b) 
		} 
	case float64: 
		switch w := b.(type) { 
			case int: 
				return v + float64(w), nil 
			case float64: 
				return v + w, nil 
			default: 
				return nil, fmt.Errorf("unsupported type for b: %T", b) 
		} 
	default: 
		return nil, fmt.Errorf("unsupported type for a: %T", a) 
	} 
}
```


### Обычный интерфейс

Это тип, который определяет набор методов, которые должны быть реализованы структурой, чтобы удовлетворять этому интерфейсу.

```go
package main

import "fmt"

// Определение интерфейса
type Shape interface {
    Area() float64
}

// Структура, реализующая интерфейс Shape
type Rectangle struct {
    Width  float64
    Height float64
}

func (r Rectangle) Area() float64 {
    return r.Width * r.Height
}

// Другая структура, реализующая интерфейс Shape
type Circle struct {
    Radius float64
}

func (c Circle) Area() float64 {
    return 3.14 * c.Radius * c.Radius
}

// Функция, принимающая интерфейс Shape
func PrintShapeInfo(s Shape) {
    fmt.Printf("Area: %.2f\n", s.Area())
}

func main() {
    rect := Rectangle{Width: 5, Height: 3}
    circ := Circle{Radius: 4}

    fmt.Println("Rectangle:")
    PrintShapeInfo(rect)
    
    fmt.Println("Circle:")
    PrintShapeInfo(circ)
}
```


### Задачи

```go
type Foo struct{}

func (f *Foo) A() {}
func (f *Foo) B() {}
func (f *Foo) C() {}

type AB interface {
	A()
	B()
}

type BC interface {
	B()
	C()
}

func main() {
	var f AB = &Foo{}
	y := f.(BC) // сработает ли такой type-assertion? 
	//да, сработает, т.к. реализует BC
	
	y.A() // а этот вызов? 
	//Не сработает, т.к. интерфейс скастовался в BC, где нет A()
	_ = y
}
```