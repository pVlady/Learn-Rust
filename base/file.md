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

### Перебор объектов в каталоге
Итератор `WalkDir` рекурсивно проходит по всем объектам файловой системы указанного каталога.\
Функция `into_iter()` преобразует итератор `WalkDir` в регулярный итератор, что позволяет использовать функцию `filter_map()`, подавляя любые ошибки, которые могут возникнуть в процессе выполнения операции (напр., отсутствие необходимых прав доступа). С помощью `file_type()` проверяем, что текущий итерируемый объект является файлом, а не каталогом или ссылкой.
```toml
[dependencies]
walkdir = "2.3.1"
```
```rust
use std::path::Path;
use walkdir::WalkDir;
let root = Path::new("/path/to/directory");
for entry in WalkDir::new(root).into_iter().filter_map(|e| e.ok()) {
    if entry.file_type().is_file() {
        println!("{}", entry.path().display());
    }
}

// вывод содержимого каталога в виде дерева
let dir = "/path/to/directory";
for entry in WalkDir::new(dir) {
    let entry = entry.unwrap();
    let level = entry.depth();    // возвращает глубину вложения относительно корня просмотра
    println!("{}{}", "--".repeat(level), entry.file_name().to_string_lossy());
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
println!("{:x}", hasher.finalize());               // вывод md5-хэша в шестнадцатиричном виде
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

### SHA256
Manual version
```rust
let file = std::fs::File::open($path)?;
let mut reader = std::io::BufReader::new(file);
let mut sha256: sha2::Sha256 = sha2::Digest::new();
let mut buffer = [0; 1024];
loop {
    let count = std::io::Read::read(&mut reader, &mut buffer)?;
    if count == 0 { break; }
    sha2::Digest::update(&mut sha256, &buffer[..count]);
}
println!("{:x}", sha2::Digest::finalize(sha256));
```

```rust
use std::fs::File;
use std::io::prelude::*;
use sha2::{Sha256, Digest};

fn main() -> std::io::Result<()> {
    let mut file = File::open("path/to/file")?;
    let mut hasher = Sha256::new();
    let mut buffer = [0; 1024];  // последовательное чтение содержимого файла в буфер
    loop {
        let bytes_read = file.read(&mut buffer)?;
        if bytes_read == 0 { break; }        
        hasher.update(&buffer[..bytes_read]);  // обновить hasher данными из буфера
    }
    let result = hasher.finalize();  // конечный результат в виде byte array
    println!("{:x}", result);
    Ok(())
}
```

Сокращенная версия без детализации буферного чтения
```rust
let input = std::fs::File::open($path)?;
let mut reader = std::io::BufReader::new(input);
let mut sha256: sha2::Sha256 = sha2::Digest::new(); 
std::io::copy(&mut reader, &mut sha256)?; 
println!("{:x}", sha2::Digest::finalize(sha256));
```



