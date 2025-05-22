# Разработка простого текстового редактора на C++ с помощью FLTK

## Исследование предметной области
1. Перед началом работы нужно определить, какие основные функции мы хотим видеть в текстовом редакторе:
   * Открытие текстовых файлов
   * Сохранение файлов
   * Редактирование текста
   * Простой интерфейс с меню

2. Для разработки дизайна простого текстового редактора можно воспользоваться FLTK.
   **FLTK** (Fast Light Toolkit) — это кроссплатформенная библиотека для создания графических интерфейсов. В этом руководстве мы создадим простой текстовый редактор с базовыми функциями: открытие, сохранение и редактирование файлов.

   В FLTK существует несколько классов:
   * Fl_Text_Editor — виджет для редактирования текста
   * Fl_Text_Buffer — буфер для хранения текста
   * Fl_Menu_Bar — создание меню
   * Вспомогательные функции для работы с файлами

## Техническое руководство
### Шаг 1: настройка среды разработки
Установка FLTK


#### На Windows:
  1. Скачайте FLTK с официального сайта
  2. Распакуйте архив
  3. Добавьте пути к библиотекам в настройки компилятора

#### На Linux (Ubuntu/Debian): 
  Ввести в терминал команду: *sudo apt-get install libfltk1.3-dev*

#### На macOS: 
  Ввести в терминал команду: *brew install fltk*

Так как FLTK — это библиотека, для ее использования потребуется компилятор. Вот подборка компиляторов для каждой из операционных систем:

ОС |	Рекомендуемый компилятор |	Альтернативы  |
---|---------------------------|----------------|
Windows |	MinGW (GCC) |	MSVC (Visual Studio) |
Linux	| GCC (g++)	| Clang |
macOS	| Clang (Xcode)	| GCC (через Homebrew) |


### Как подключить FLTK к компилятору?

**1. Windows (MinGW/MSVC)**
  1.1. MinGW
  
  Установите FLTK через MSYS2 или вручную с помощью терминала: 
    
  *pacman -S mingw-w64-x86_64-fltk*
    
  *g++ main.cpp -o app -I/path/to/fltk/include -L/path/to/fltk/lib -lfltk -lfltk_images -lfltk_gl -lfltk_forms -mwindows* 

  1.2. Visual Studio
  
    * Скачайте FLTK с официального сайта.
    * Настройте пути в Properties → C/C++ → Additional Include Directories.
    * Добавьте .lib-файлы в Linker → Input → Additional Dependencies.


  **2. Linux (GCC/Clang)**
  
  Установите FLTK через пакетный менеджер в терминале:
  
  *sudo apt install fltk1.3-dev  # Ubuntu/Debian*
  
  *sudo dnf install fltk-devel    # Fedora*
  
  *g++ main.cpp -o app `fltk-config --cxxflags --ldflags`*


**3. macOS (Clang/Xcode)**

Установите FLTK через Homebrew в терминале:

*clang++ main.cpp -o app -I/opt/homebrew/include -L/opt/homebrew/lib -lfltk -lfltk_images*

## Шаг 2: каркас приложения
В проекте создадим файл main.cpp: Правой кнопкой мыши по проекту в обозревателе решений — Добавить — Новый элемент — Файл C++ (.cpp) — Назовем его main.cpp

Начнем с необходимых библиотек:

#include <FL/Fl.H> — базовый заголовок FLTK (основные функции)
#include <FL/Fl_Window.H> — для создания окна
#include <FL/Fl_Menu_Bar.H> — для создания меню
#include <FL/Fl_Text_Editor.H> — виджет текстового редактора
#include <FL/Fl_Text_Buffer.H> — буфер для хранения текста
#include <FL/fl_ask.H> — для диалоговых окон
