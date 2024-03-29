# Введение в rust
## Переменные
* Переменные разделяются на изменяемые и неизменяемые (по умолчанию).
* Каждая переменная должна быть инициализирована допустимым для ее типа значением. Компилятор запрещает использование неинициализированных переменных.
* Неявное приведение типов запрещено. Явное приведение типов осуществляется с помощью оператора `as`:
  ```rust
  let decimal = 65.4321_f32;
  let integer = decimal as u8;
  ```
* В debug-режиме программа компилируется с проверкой _integer overflow_.

## Работа с памятью
* У любого заданного объекта на все время его жизни существует ровно один владелец, который может меняться (передача владения).
* В любой момент времени можно иметь либо одну изменяемую ссылку, либо любое количество неизменяемых ссылок.
* Ссылки всегда должны быть актуальными.
* Осуществляется проверка границ массивов (небольшая цена производительности для исключения более серьезных проблем).
* При копировании переменной, _которая ссылается на данные в куче_, всегда создается _поверхностная_ копия объекта (из стека копируются указатель, емкость и фактически занятый переменной размер памяти). При этом происходит смена владельца, а старая переменная прекращает свое существование. Такие типы данных всегда реализует типаж `Drop`. Когда переменная, содержащая данные в куче, выходит из области видимости, содержимое в куче будет очищено функцией `drop()`, если только данные не были перемещены во владение другой переменной.
* Для создания _глубокой_ копии объекта нужно использовать метод `clone()`.
* При копировании переменной, _которая находится в стеке_, всегда создается полная копия переменной. При этом старая переменная остается актуальной. Такие типы данных всегда реализует типаж `Copy` -- переменные, которые его используют, не перемещаются, а тривиально копируются. Это все целочисленные типы, логический тип, все типы с плавающей запятой, символьный тип `char` и кортежи, которые содержат только типы, реализующие типаж `Copy`.
* Передача переменной в функцию приводит к ее перемещению или копированию, как и присваивание.
* Ссылки позволяют использовать значение без передачи права владения.
 
## Особенности синтаксиса
* В числовых литералах тип может быть аннотирован добавлением суффикса типа (кроме `uint` и `int`, которые используют суффиксы `u` и `i`).
* Выражение `type NanoSecond = u64;` определяет алиас типа. Используется для сокращиения названий типов, в т.ч. обобщенных.
* `&x` ссылка на переменную `x` — ограничений по количеству таких ссылок нет (они неименяемы); `fun(&foo)` — передача аргумента по ссылке.
* `&mut x` изменяемая (исключительная) ссылка на переменную `x` — она может существовать только в единственном экземпляре, а переменная `x` должна быть мутабельной.
* оператор `&` применяется к переменной и возвращает ссылку на нее: `&x` – это ссылка на переменную `x`
* оператор `*` применяется к ссылке и возвращает объект, на который она ссылается: `*y` означает переход от ссылки `y` к самой переменной (`*(&x) `даст `x`)

## Простые типы данных

## Составные типы данных
### Тип `Option`
* Может быть либо `Some` (содержащее значение любого типа), либо `None`.
* Переменная `x` типа `Option` имеет логические методы `x.is_some()`, `x.is_none()`
* Метод `x.unwrap()` вызовет панику, если `x.is_none() == true`, либо вернет значение конкретного типа, если `x.is_some() == true `.
* Метод `x.unwrap_or(&-1)` вернет знчение, если `x.is_some() == true `, либо `-1`, если `x.is_none() == true`.

### Массивы (Array)
* Содержат значения только одного типа, расположенные в памяти друг за другом
* Размер массива должен быть известен во время компиляции
* Можно организовывать массивы массивов
```rust
let arr = [1, 2, 3, 4, 5];             // массив
let arr: [i32; 5] = [1, 2, 3, 4, 5];   // указание размера массива и типа его элементов
let numbers: [i32; 5] = [3; 5];        // массив из 5 элементов со значениями по умолчанию равными 3
let arr = [3; 5];                      // тоже с автоопределением типа элементов
let mut arr = [3; 5];                  // изменяемый массив
arr[0];                                // индексация начинается с нуля (обращение к первому элементу)

for index in 0..3 {
    println!("Index: {} |> value: {}", index, arr[index]);   // цикл по элементам массива
}
```
* По умолчанию массивы неизменяемы.

### Срезы (Slice)
* Срез является порцией данных из коллекций (массивов, векторов, символьного значения)
* Срез не копирует данные, а сохраняет связь с коллекцией
* Состоит из указателя на данные и указателя на размер блока памяти, занимаемой срезом
* Размер среза в памяти равен `usize ` и зависит от архитектуры процессора
* Фактический размер среза не известен во время компиляции
* Имеет сигнатуру типа `&[T]`
```rust
let slice = &arr[1..3];      // 2-ой и 3-ий элементы массива (&arr - ссылка на массив)
let slice = &arr[..3];       // все элементы с первого по третий
let slice = &arr[2..];       // все элементы с третьего и до конца массива
let slice = &arr[2..];       // получим все элементы массива
let slice = &mut arr[1..4];  // изменяемый срез (можно изменить значение внутри среза - массив должен быть изменяемым)
slice.get(5);                // получение элемента по индексу без вызова исключения (вернет объект типа `Option`)
```

### Векторы (Vector)
* По сути являются массивами с изменяемым размером, т.е. если вектор объявлен изменяемым, в него могут быть добавлены элементы.
* Оператор `&` приводит вектор к типу `Slice`.
* Метод `x.push(value)` добавляет элемент со значением `value` в конец вектора; метод `x.pop()` извлекает последний элемент вектора, укарачивая его.
* Метод  `x.sort()` сортирует вектор, метод `x.dedup()` удаляет дубликаты. Оба метода _in-place_.
* Добавление и удаление элементов вектора в определенной позиции производится методами `insert()` и `remove()` (менее эффективны, чем `push()` и `pop()`).
```rust
let mut x = Vec::new();
x.push(10);
x.size;                  // текущий размер вектора
x.capacity;              // текущая емкость вектора
x.clear();               // очистка содержимого (емкость вектора не изменяется)
x.clone();               // копирование вектора
dump(&x;)
```

### Структуры (Struct)
* Может иметь элементы различных типов
* Могут быть использованы в качестве полей другой структуры
```rust
struct Person {name: String, age: u8, height: u8}                // объявление структуры
let person1 = Person {name: String::from("John Doe"), age: 18};  // создание экзмепляра структуры  
person1.age;                                                     // доступ к полю структуры
let Person { name1, age1, height1 } = person1;                   // распаковка структуры в указанные переменные
println!("{:?}", person1);                                       // печать структуры
```
Существует три типа структур, которые можно создать с помощью ключевого слова `struct`:
* Кортежная структура, которая на самом деле является именованным кортежем
* Классическая C-структура
* Единичная структура, которая не имеет полей, но может быть полезна для обобщённых типов
```rust
struct Point { x: f32, y: f32, }          // структура с двумя полями
struct Unit;                              // unit-структура
struct Pair(i32, f32);                    // Кортежная структура
let pair = Pair(1, 0.1);
println!("pair содержит {:?} и {:?}", pair.0, pair.1);  // доступ к полям кортежной структуры
let Pair(var1i, var2) = pair;       // деструктурируем кортежную структуру
println!("pair содержит {:?} и {:?}", var1, var2);  // доступ к полям кортежной структуры
```

### Строки (String)
Разделяют символьные значения статические (по сути срезы символьной строки) и размещенные в памяти — независимые текстовые значения.
* строки аллоцируют память динамически и можно изменять их размер.
* выражение вида "hello" не является типом _string_, а представляет строковый срез, который может быть размещен в памяти методом `to_string()` и превращен в тип _string_.
Под капотом _String_ представляется типом `Vec<u8>`, в то время как срез &str представляется как &[u8] (при этом сами байты должны быть вылидными занчениями для UTF-8).
```rust
let text = "hello dolly";     // string slice
let s = text.to_string();     // string, размещенный в памяти 

let mut s = String::new();    // изначально пустой! (как вектор)    
s.push('H');                  // добавление символа в конец строки
```

### Кортежи (Tuple)
Коллекция, которая хранит переменные разных типов.\
Кортежи создаются с помощью круглых скобок `()`, и каждый кортеж является переменной с сигнатурой типов `(T1, T2, ...)`.
```rust
let tuple1 = (1, "привет", 4.5, true);
let tuple2 = (1, );                        // кортеж из одного элемента
let tuple3 = ((1u8, 2u16), (4u64, -1i8));  // кортеж из кортежей

println!(tuple1.0);                        // обращение к элементу кортежа по индексу
println!("Кортеж: {:?}", tuple1);          // печать кортежа
let (a, b, c, d) = tuple1;                 // кортежи можно деструктурировать
```

### Перечисления (Enum)
```rust
enum WebEvent {    
    PageLoad,                    // `enum` может быть как `unit-подобным`
    PageUnload,    
    KeyPress(char),              // так и кортежной структурой
    Paste(String),  
    Click { x: i64, y: i64 },    // или С-подобной структурой    
}
enum Number { Zero, One, Two, }  // C-подобный enum с неявным дискриминатором (начинается с 0)
enum Color { Red = 0xff0000,     // C-подобный enum с явным дискриминатором
             Green = 0x00ff00,
             Blue = 0x0000ff,
}
```
[Пример использования `enums` для создания связанного списка](https://doc.rust-lang.ru/stable/rust-by-example/custom_types/enum/testcase_linked_list.html)

## Ветвление и циклы
### Условные операторы
```rust
if <condition> {...} else {... }           // скообки вокруг condition не обязхательны
```

### Циклы
```rust
for j in 0..5 { тело_цикла }
```
## Функции
```rust
fn main() { тело функции }
fn by_ref(x: &i32) -> f32 { ... }          // передача аргумента по ссылке и возврат f32-значения
```
* Возвращаемое значение устанавливается оператором `return` либо последним выражением в теле функции, после которого НЕ СТАВИТСЯ точка с запятой.

  

## References
* [Rust | Programiz](https://www.programiz.com/rust/getting-started)
* [A Gentle Introduction To Rust](https://stevedonovan.github.io/rust-gentle-intro/readme.html)
* [Язык программирования Rust](https://doc.rust-lang.ru/book/title-page.html)

