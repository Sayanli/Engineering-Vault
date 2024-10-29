

```go
merge channels

func merge(cs ...<-chan int) <-chan int {
	out := make(chan int)
	var wg sync.WaitGroup
	wg.Add(len(cs))
	for _, c := range cs {
		go func(c <-chan int) {
			for v := range c {
				out <- v
			}
			wg.Done()
		}(c)
	}
	go func() {
		wg.Wait()
		close(out)
	}()
	return out
}
```

```go
func main() {
	ch := make(chan int)

	for i := 0; i < 10; i++ {
		select {
		case x := <-ch:
			print(x)
		case ch <- i:
		}
	}
}
тут надо сделать канал с буфером, чтоб не заблочилось, потом закрыть канал. Вывод будет рандомный
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
type MyError struct{}

func (err *MyError) errorHandler() {
	if err != nil {
		fmt.Println("Error Method:", err)
	}
}

func (MyError) Error() string { return "MyError!" }

func errorHandler(err error) {
	if err != nil {
		fmt.Println("errorHandler function:", err)
	}
}

func main() {
	var err *MyError
	var p error
	err.errorHandler()  // вопрос: что выведет? Ничего
	
	errorHandler(err) // вопрос: что выведет? Сообщение с ошибкой, т.к. err не будет равнять nil
	errorHandler(p)  // вопрос: что выведет? Ничего
}
```

```go
func main() {
	s := "test"
	println(s[0])
	//нужно сделать слайс рун runeSlice := []rune(s)
	//далее runeSlice[0] = 'R'
	//s = string(runeSlice)
	s[0] = "R"
	println(s)
}
```

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
	y := f.(BC) // сработает ли такой type-assertion? да, сработает, т.к. реализует BC
	y.A() // а этот вызов? Не сработает, т.к. в интерфейсе нет A()
	_ = y
}

```

![[Pasted image 20240921172344.png]]

![[Pasted image 20240921121853.png]]

```go
Дано натуральное число (например 123). Нужно написать метод, который принимает это число и возвращает число наоборот - (в данном случае 321).
func reverseNumber(n int) int { 
	reversed := 0 
	for n > 0 { 
		reversed = reversed*10 + n%10 
		n /= 10 
	} 
	return reversed 
}
```


-----

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

![[Pasted image 20240921124448.png]]
```go
что выведет
Ответ: рандом
myMap := map[int]string{1: "a", 2: "b", 3: "c"}

for key, value := range myMap {
    fmt.Println("Key:", key, "Value:", value)
```

```go
func main() {
	x := []int{}
	x = append(x, 0)     // l1 c1
	x = append(x, 1)     // l2 c2
	x = append(x, 2)     // l3 c4 x[0 1 2]
	y := append(x, 3)    // l4 c4 x[0 1 2]
	z := append(x, 4)    // l4 c4 x[0 1 2]
	fmt.Println(x, y, z) // x[0 1 2] y[0 1 2 4] z[0 1 2 4]
}
```

```go
что выведет программа
Ответ: рандом
x := make(map[int]int, 1)
go func() { x[1] = 2 }()
go func() { x[1] = 7 }()
go func() { x[1] = 10 }()
time.Sleep(100 * time.Millisecond)
fmt.Println("x[1] =", x[1])
```

```go
Написать функцию, которая удаляет нули из массива int'ов - внимательно инициализируете слайс для результатов, доп вопрос, как сделать задачу in-place
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

---

МОЯ КОМАНДА

```GO
// Требуется реализовать функцию uniqRandn, которая генерирует слайс длины n уникальных, рандомных чисел.
func uniqRand(n int) []int {
 rand.Seed(time.Now().UnixNano()) 
 numbers := make(map[int]bool)
 res := make([]int, 0, n)

 for len(res) < n {
  randNum := rand.Intn(n) 
  if !numbers[randNum] {
   numbers[randNum] = true
   res = append(res, randNum)
  }
 }
 return res
}
```

второй этап
```GO
1) У нас есть база данных с паролями пользователей, пароли захешированы (функция hashPassword),
а так же известен набор символов которые могут быть использованы в паролях (переменная alphabet).
Наша задача реализовать функцию RecoverPassword так,
чтобы она восстанавливала пароль по известному хэшу и тест TestRecoverPassword завершился успешно

# Базовый код на Go
var alphabet = []rune{'a', 'b', 'c', 'd', '1', '2', '3'}

func RecoverPassword(h []byte) string {
    // TODO: implement me
    return ""
}

func hashPassword(in string) []byte {
    h := md5.Sum([]byte(in))
    return h[:]
}


# Тут ниже код на Python
...

alphabet = ['a', 'b', 'c', 'd', '1', '2', '3']
alphabet_len = len(alphabet)


def recovery_password(hashed_password: bytes) -> str:
    attempt = 0
    while True:
        current_password = []
        
        d, m = divmod(attempt)
        while d > alphabet_len:
            d, m = divmod(d)
            symbol = alphabet[m]
            current_password.append(symbol)
        symbol = alphabet[d]
        current_password.append(symbol)
        
        str_password = "".join(current_password)
        
        if hashPassword(str_password) == hashed_password:
            return str_password
        else:
            attempt += 1    



4) На примере создания заказа.
Есть запрос на сервис и есть ответ, между этими двумя действиями мы сладываем в аналитику товары которые заказали (например для подсчета популярности товаров).
Сервис аналитики переодически работает медленно или вовсе таймаутит, и мы не успеваем ответить, теряем заказы.
Что делать, что бы перестать терять заказы, и деньги соответсвенно?

+-------+         +---------------+    +-------------------+
| User  |         | OrderService  |    | AnalyticsService  |
+-------+         +---------------+    +-------------------+
    |                     |                      |
    | CreateOrder         |                      |
    |-------------------->|                      |
    |                     |                      |
    |                     | TrackOrder           |
    |                     |--------------------->|
    |                     |                      |
    |                     |                      | Too long action
    |                     |                      |----------------
    |                     |                      |               |
    |                     |                      |<---------------
    |                     |                      |
    |                     |                      |
    |                     |<---------------------|
    |                     |                      |
    |                     |                      |
    |<--------------------|                      |
    |                     |                      |



5) Фронт -->  А ---http-->  Б
```

Алгоритмическая секция
1. как распараллелить исполнение recovery_password?
2. как можно защититься взлома пароля? (медленный алгоритм хэширования, константный ответ от сервера)
3. как ещё можно оптимизировать взлом пароля? (rainbow tables)
4. допустим, что функция hashPassword всегда возвращает пароли длины до 10, как с помощью этой информации можно оптимизировать функцию? (хотел услышать как взломать MD5 алгоритм хэширования)

Архитектурная секция I
1. как называется этот паттерн? (transactional outbox)
2. опиши на псевдокоде алгоритм работы job-ы, которая разгребает outbox
```go
job
    select id, payload from outbox where now() > now() + treshhold 
    push to analytics
    if !response.ok
        retry
    else
        delete from outbox where id = id
```
4. допустим, мы расширились и получили много job которые обрабатывают outbox. Как сделать так, чтобы они не читали дважды одни и те же сообщения?
5. какие есть виды гарантий доставки сообщений?
6. как реализовать exactly once?

Архитектурная секция II
6. сервис Б не справляется с нагрузкой, что делать?
7. ок, мы подняли 2 сервиса Б, как реализовать балансировку нагрузки по http между ними?
8. какие алгоритмы балансировки нагрузок ты знаешь?
9. какие есть недостатки у серверной балансировки?
10. ок, мы подняли 2 балансировщика нагрузок, как клиент узнаёт к кому из них обращаться?
11. как клиент узнаёт адреса серверных балансировщиков?


---
```go
func main() {
	timeStart := time.Now()
	_, _ = <-worker1(), <-worker1()

	// для вывода 3 нужно сделать так
	ch1 := worker1()
	ch2 := worker1()

	var wg sync.WaitGroup
	wg.Add(2)

	go func() {
		defer wg.Done()
		<-ch1
	}()
	go func() {
		defer wg.Done()
		<-ch2
	}()
	wg.Wait()
	//
	
	fmt.Println(int(time.Since(timeStart).Seconds())) // выведет 6
}

func worker1() chan int {
	ch := make(chan int)
	go func() {
		time.Sleep(3 * time.Second)
		ch <- 1
	}()
	return ch
}
Доп вопрос - как сделать что бы напечаталось 3

```
---

1 часть: даны 2 слайса разной длины, надо вернуть слайс слайсов, у которого длина будет равна длине кратчайшего из двух, а элементы будут получаться из соответствующих по индексу  элементов начальных слайсов.
Input: [1, 2, 3], [4, 5, 6, 7]
Output: [[1, 4], [2, 5], [3, 6]]
Объяснение: берём сначала элементы по индексам 0, затем по индексам 1 и 2. Индекс 3 уже не берём, так как кратчайший слайс из 3-х элементов.
Я написал вот так:

```go
func makeSlice(sl1 []int, sl2 []int) [][]int {
    if len(sl1) > len(sl2) {
        sl1, sl2 = sl2, sl1
    }

    res := make([][]int, 0, len(sl1))
    
    for i := range sl1 {
        res = append(res, []int{sl1[i], sl2[i]})
    }

    return res
}

теперь на входе может быть произвольное количество слайсов. Я решил так:
func mult2(args ...[]int) [][]int {
    if len(args) <= 0 {
        return nil
    }

    min_n := len(args[0])
    for i := range args {
        if len(args[i]) < min_n {
            min_n = len(args[i])
        }
    }

    res := make([][]int, 0, min_n)
    
    for i := range min_n {
        buf := make([]int, 0, len(args))
        for j := range args {
            buf = append(buf, args[j][i])
        }
        res = append(res, buf)
    }

    return res
}
```

На понимание горутин и каналов. Спрашивают, что будет выводиться, как избежать блокировок и так далее. Дают код:
```go
func main() {

    ch := make(chan int)

    go func() {
        for i := 0; i < 5; i++ {
            ch <- i
        }
        // сюда close(ch), чтоб закрыть канал. Иначе range будет вечно ждать и дедлок
    }()

    for n := range ch {
        fmt.Println(n)
    }
}
```

```go
func main() {
// тут var wg sync.WaitGroup , wg.Add(1)
    go func() {
    // defer wg.Done()
        for i := 0; i < 5; i++ {
            fmt.Println(n)
        }
    }()
    wg.Wait()
}
```

2 тех собес

Дан массив meetings, в котором каждый элемент meeting[i] - это пара двух чисел [startTime, endTime]
Необходимо объединить все накладывающиеся друг на друга встречи и вернуть массив с объединенными встречами, покрывающих те же временные слоты.
Input: [ [1,3], [2,6], [8,10], [15,18] ]
Output: [ [1,6], [8,10], [15,18] ]

```go
type Meeting struct { 
	startTime int 
	endTime int 
} 
func mergeMeetings(meetings [][]int) [][]int { 
	// Преобразуем встречи в структуру Meeting для удобства сортировки 
	var meetingsList []Meeting 
	for _, meeting := range meetings { 
		meetingsList = append(meetingsList, Meeting{startTime: meeting[0], endTime: meeting[1]}) 
	} 
	// Сортируем встречи по времени начала, а затем по времени окончания 
	sort.Slice(meetingsList, func(i, j int) bool { 
	if meetingsList[i].startTime == meetingsList[j].startTime { 
		return meetingsList[i].endTime < meetingsList[j].endTime 
	} 
	return meetingsList[i].startTime < meetingsList[j].startTime 
	}) 
	// Объединяем пересекающиеся встречи 
	var mergedMeetings [][]int 
	currentMeeting := meetingsList[0] 
	for i := 1; i < len(meetingsList); i++ { 
	if meetingsList[i].startTime <= currentMeeting.endTime { 
		// Объединяем пересекающиеся встречи 
		currentMeeting.endTime = max(currentMeeting.endTime, meetingsList[i].endTime) 
	} else { 
	// Добавляем текущую встречу в результат и начинаем новую встречу 
		mergedMeetings = append(mergedMeetings, []int{currentMeeting.startTime, currentMeeting.endTime}) 
	currentMeeting = meetingsList[i] } } 
	// Добавляем последнюю встречу 
		mergedMeetings = append(mergedMeetings, []int{currentMeeting.startTime, currentMeeting.endTime}) 
	return mergedMeetings 
} 

func max(a, b int) int { 
	if a > b { 
		return a 
	} 
	return b 
} 
func main() { 
	meetings := [][]int{{1, 3}, {2, 6}, {8, 10}, {15, 18}} 
	mergedMeetings := mergeMeetings(meetings) 
	fmt.Println(mergedMeetings) // Output: [[1 6] [8 10] [15 18]] 
}
```

---

