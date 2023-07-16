# Установка rust

## Online установка

### Windows
C [официального сайта](https://www.rust-lang.org/tools/install) по ссылке `DOWNLOAD RUSTUP-INIT.EXE (64-BIT)` скачиваем и запускаем установщик rust.
Программа проверит наличие необходимых компонентов и при их отсутствии предложит установить с помощью Microsoft Visual Studio Installer:
  * Desktop Development with C++ (блок Разработка классических приложений на C++ - все дополнительные компоненты в блоке отключаем)
  * The Windows 10 or 11 SDK
  * The English language pack component, along with any other language pack of your choosing

### Linux
Выполняем с правами администратора:
```bash
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
```

## Оффлайн установка
Оффлайн установщики для различных платформ можно скачать [отсюда](https://forge.rust-lang.org/infra/other-installation-methods.html).

## Обновление версии
Если Rust установлен с помощью rustup, то для обновления достаточно выполнить в консоли:
```bash
$ rustup update stable
```

## Настройка редактора SublimeText
Последовательно устанавливаем следующие плагины:
 * _RustEnhanced_ — замена встроенного пакета поддержки языка _Rust_ (добавляет улучшенную подсветку синтаксиса, вывода Cargo, кастомизацию сборки приложения + автосохранение файлов, см. _[Rust Enhanced User Guide](https://rust-lang.github.io/rust-enhanced/index.html))_
 * _TOML_ — обеспечивает подсветку синтаксиса файла _Cargo.toml_
 * _LSP_ — language server
 * _LSP-rust-analyzer_ — см. _[User Manual](https://rust-analyzer.github.io/manual.html)_
 * _Sublime Projects_ — для удобства организации проектов
 * для форматирования кода можно установить _[RustFmt](https://packagecontrol.io/packages/RustFmt)_

_Clippy_ — это набор правил для анализа rust-кода. Установка:
```bash
$ rustup component add clippy
```
Запуск проверок Clippy’s для проекта Cargo:
```bash
$ cargo clippy
```
Для испльзования _Сlippy_ указываем в настройках _Rust Enhanced_:
```json
{
  "rust_syntax_checking": true,
  "rust_syntax_checking_method": "clippy",
}
```

 ## Настройка VS Code
 [Пишем с комфортом на Rust в VS Code](https://habr.com/ru/articles/645797/)
 * Со [страницы загрузки Microsoft](https://visualstudio.microsoft.com/ru/downloads/) из раздела "Инструменты для Visual Studio" скачиваем "Инструменты сборки для Visual Studio 2022". Запускаем инсталлятор и выбираем пункт "Разработка классических приложений на C++".
 * Для подсветки синтаксиса и автодополнений устанавливаем расширение _rust-analyzer_ (алльтернатива _Rust_ работает чуть хуже и они конфликтуют между собой).
 * Для отладки устанавливаем расширение _CodeLLDB_.
 * Для работы с _toml_ ставим плагины _[Even Better TOML](https://marketplace.visualstudio.com/items?itemName=tamasfe.even-better-toml)_ (для подсветки), _[crates](https://marketplace.visualstudio.com/items?itemName=serayuzgur.crates)_ для удобного обновления зависимостей, и _[crates completer](https://marketplace.visualstudio.com/items?itemName=jedeop.crates-completer)_ для автодополнения имён пакетов и версий.
 * Устанавливаем планины _[Test Explorer UI](https://marketplace.visualstudio.com/items?itemName=hbenl.vscode-test-explorer)_ и _[Rust Test Explorer](https://marketplace.visualstudio.com/items?itemName=swellaby.vscode-rust-test-adapter)_, чтобы запускать тесты из боковой панели, а не из консоли.
