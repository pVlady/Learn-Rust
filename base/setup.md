# Установка rust

## Online установка

### Windows
C [официального сайта](https://www.rust-lang.org/tools/install) по ссылке `DOWNLOAD RUSTUP-INIT.EXE (64-BIT)` скачиваем и запускаем установщик rust.

### Linux
Выполняем с правами администратора:
```bash
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
```

## Оффлайн установка
Оффлайн установщики для различных платформ можно скачать [отсюда](https://forge.rust-lang.org/infra/other-installation-methods.html).

## Настройка редактора SublimeText
Последовательно устанавливаем следующие плагины:
 * _RustEnhanced_
 * _TOML_ — для подсветки синтаксиса _toml_-файлов
 * _LSP_ — language server
 * _LSP-rust-analyzer_
 * _Sublime Projects_ — для удобства организации проектов
 
