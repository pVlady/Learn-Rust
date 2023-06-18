# Работа в файлами в Rust
Модуль `std::io::prelude` определяет набор общеупотребительных трейтов, обеспечивающих единый интерфейс для операций ввода-вывода при работе с различными I/O-потоками:
* `Read` : обеспечивает стандартный интерфейс для чтения байтов из потока
* `Write` : обеспечивает стандартный интерфейс для записи байтов в поток
* `BufRead` : обеспечивает стандартный интерфейс для буферизованного чтения байтов из потока

Модуль `std::fs::File` реализует примитивы для работы с файловой системой.

## Операции с файлами
```rust
use std::fs::File;
use std::io::prelude::*;
use std::io::{BufReader, prelude::*};

fs::create_dir(folder_name)?;               // создание каталога (если каталог уже существует, вернет std::io::Error)
let file = File::open("filename.txt")?:     // открытие файла
let file = File::create("filename.txt")?;   //создание файла
fs::remove_file("filename.txt")?;           // удаление файла

# чтение содержимого файла в текстовую переменную
let file = File::open("filename.txt")?;
let reader = BufReader::new(file);
let mut contents = String::new();
reader.read_to_string(&mut contents)?;

# запись в новый файл
let mut file = File::create("filename.txt")?;
file.write(b"Hello, world!")?;

# добавление данных в файл
let mut file = OpenOptions::new()
                 .append(true)
                 .open("filename.txt")?;
file.write(b"Hello again!")?;


fn main() -> std::io::Result<()> {
    let mut file = File::create("new_file.txt")?;
    file.write_all(b"Hello, world!")?;
    Ok(())
}
```

## Дополнительные операции с файловой системой
```rust
use dirs;
use std::path::PathBuf;
let mut home = PathBuf::from(dirs::home_dir().unwrap());   // получить путь к домашнему каталогу пользователя
fs::create_dir(home.push(folder_name))?;                   // добавить в путь папку в домашний каталог
```

## Вычисление хеша файла
[Тест производительности расчета](https://github.com/ETKNeil/sha_speed)

### md5
```toml
[dependencies]
md5 = "0.9"
```
```rust
use md5::{Digest, Md5};
let message = "Hello, world!";
let mut hasher = Md5::new();
hasher.update(message.as_bytes());
let result = hasher.finalize();
println!("{:x}", result);               // вывод md5-хэша в шестнадцатиричном виде
```

### SHA1
```rust
use std::{fs, io};
use sha1::{Sha1, Digest};

let mut file = fs::File::open(&path)?;
let mut hasher = Sha1::new();
let n = io::copy(&mut file, &mut hasher)?;
let hash = hasher.finalize();
```
