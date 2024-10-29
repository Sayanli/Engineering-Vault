
#### Array

array - коллекция элементов одного типа. Длина _не_ может изменяться.

```go
arr := [4]int{3,2,5,4} или [...]int{3,2,5,4}
```

#### Slice

slice - обёртка над массивом

```go
s := make([]int, i, j)
i - len (заполняется пустыми элементами)
j - capacity
```


```go
type slice struct {
	array unsafe.Pointer
	len   int
	cap   int
}
```

array - указатель на нижестоящий массив
len - текущая длина слайса
cap - вместимость слайса

при len > cap происходит расширение слайса по формуле:
```go
const threshold = 256
	if oldCap < threshold {
		return doublecap
	}
	for {
		newcap += (newcap + 3*threshold) >> 2
		if uint(newcap) >= uint(newLen) {
			break
		}
	}
```

При cap < 256 слайс увеличивается в 2 раза, а потом примерно в 1,25 раза


Мы можем срезать часть массива. Срез не копирует элементы массива, он просто ссылается на них. Таким образом при изменении среза, изменится и массив, с которого мы брали срез:
```go
nameArray := [6]string{"D", "a", "n", "i", "i", "l"}	
nameSlice := nameArray[:3]	
nameSlice[len(nameSlice) - 1] = "m"	
fmt.Println(nameSlice) // [D a m]	
fmt.Println(nameArray) // [D a m i i l]
```

 Функция `append` добавляет элемент в конец слайса и при необходимости расширяет capacity. Сама функция возвращает новый слайс:

```go
foo := []int {}
foo = append(foo, 1)
```

В случае с `copy` мы просто копируем элементы из одного слайса в другой:

```go
foo := []int {1, 2}
bar := []int {}
copy(bar, foo)
```


## Задачи

```go
Расписать len и cap у каждого append и рассказать, что будет выведено в конце
func main() {
	x := []int{}
	x = append(x, 0)     // len=1 cap=1
	x = append(x, 1)     // len=2 cap=2
	x = append(x, 2)     // len=3 cap=4 x[0 1 2]
	y := append(x, 3)    // len=4 cap=4 x[0 1 2]
	z := append(x, 4)    // len=4 cap=4 x[0 1 2]
	fmt.Println(x, y, z) // x[0 1 2] y[0 1 2 4] z[0 1 2 4]
}
```

```go
вернуть квадраты значений слайса
func abs(x int) int {
    if x < 0 {
        return -x
    }
    return x
}

func sortedSquares(nums []int) []int {
    result := make([]int, len(nums))
    left, right := 0, len(nums)-1
    for i := len(nums) - 1; i >= 0; i-- {
        if abs(nums[left]) < abs(nums[right]) {
            result[i] = nums[right] * nums[right]
            right--
        } else {
            result[i] = nums[left] * nums[left]
            left++
        }
    }
    return result
}
```

```go
Написать функцию, которая удаляет нули из массива int
func removeZeros(arr []int) int { 
	writeIndex := 0 
	for _, num := range arr { 
		if num != 0 { 
			arr[writeIndex] = num writeIndex++ 
		} 
	} 
	return writeIndex 
}
```

```go
проверка на монотонность слайса
func isMonotonic(slice []int) bool { 
	if len(slice) <= 1 { 
		return true 
	} 
	increasing := true 
	decreasing := true 
	for i := 1; i < len(slice); i++ { 
		if slice[i] > slice[i-1] { 
			decreasing = false 
		} 
		if slice[i] < slice[i-1] { 
			increasing = false 
		} 
	} 
	return increasing || decreasing 
}
```





