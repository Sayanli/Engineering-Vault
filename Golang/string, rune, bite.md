
### bite

это псевдоним для типа `uint8`. Он представляет собой 8-битное целое число без знака, которое может принимать значения от 0 до 255.
Тип данных `byte` часто используется для работы с бинарными данными, такими как байты в файлах, сетевых пакетах и других бинарных структурах. Он также используется для представления символов ASCII, так как каждый символ ASCII занимает один байт.
### rune

это псевдоним для типа `int32`. Он представляет собой 32-битное целое число со знаком, которое используется для представления символов Unicode. Символы Unicode могут занимать от 1 до 4 байт, и тип `rune` позволяет работать с этими символами в их естественном виде.
Тип данных `rune` используется для работы с текстом, который может содержать символы из различных языков и наборов символов. Он обеспечивает поддержку многобайтовых символов Unicode.

### string

Строка является неизменяемой последовательностью байтов.

Строки неизменяемы, что означает, что их содержимое не может быть изменено после создания. Это важное свойство обеспечивает несколько преимуществ, включая безопасность при передаче строк между функциями и горутинами без необходимости блокировок или других форм синхронизации.

```go
type stringStruct struct {
	str unsafe.Pointer
	len int
}
```

Длина - количество байт в строке, а не количество рун или символов. Это важное различие, поскольку в UTF-8 один символ может занимать от 1 до 4 байт.

Go использует UTF-8 как стандартную кодировку для строк.  Операции, такие как получение длины строки в рунах (символах) или доступ к отдельному символу, могут потребовать дополнительных вычислений для обработки многобайтовых символов.


### Задачи

```go
func main() {
	s := "test"
	println(s[0]) // выведет байты, а не сам символ
	s[0] = "R" // не сработает
	//нужно сделать слайс рун runeSlice := []rune(s)
	//далее runeSlice[0] = 'R'
	//s = string(runeSlice)
	println(s)
}
```







