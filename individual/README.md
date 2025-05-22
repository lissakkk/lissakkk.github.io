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
*Далее для работы над проектов в этом руководстве будет использоваться Visual Studio 2022.*

В проекте создадим файл main.cpp: Правой кнопкой мыши по проекту в обозревателе решений — Добавить — Новый элемент — Файл C++ (.cpp) — Назовем его main.cpp

Начнем с необходимых библиотек:

#include <FL/Fl.H> //базовый заголовок FLTK (основные функции)

#include <FL/Fl_Window.H> //для создания окна

#include <FL/Fl_Menu_Bar.H> //для создания меню

#include <FL/Fl_Text_Editor.H> //виджет текстового редактора

#include <FL/Fl_Text_Buffer.H> //буфер для хранения текста

#include <FL/fl_ask.H> //для диалоговых окон

Создадим глобальные переменные (для упрощения):

Fl_Window *window;  //указатель на главное окно

Fl_Text_Editor *editor;        //указатель на текстовый редактор

Fl_Text_Buffer *textBuffer;    //указатель на буфер для хранения текста

bool is_modified = false;      //флаг изменения документа


Определим несколько важных функций для работы с документами, дадим им подходящие названия:

void modify_cb(int, int, int, int, const char*, void*);  //вызывается при изменении текста

void new_cb(Fl_Widget*, void*);    //создание нового документа

void open_cb(Fl_Widget*, void*);   //открытие файла

void save_cb(Fl_Widget*, void*);   //сохранение файла

void exit_cb(Fl_Widget*, void*);   //выход из программы


## Шаг 3: главное окно и меню

Функция *main()* — обязательный элемент любого кода на c++:

int main(int argc, char **argv) {

    window = new Fl_Window(800, 600, "Текстовый редактор"); //создание окна 800x600 с заголовком

    //создадим меню:

    Fl_Menu_Bar *menu = new Fl_Menu_Bar(0, 0, 800, 25); //создание шапки меню, определение размеров
    
    menu->add("Файл/Новый", FL_CTRL+'n', new_cb); //добавление кнопки "новый", горячие клавиши, привязка соответствующей функции
    
    menu->add("Файл/Открыть", FL_CTRL+'o', open_cb); //добавление кнопки "открыть", горячие клавиши, привязка соответствующей функции
    
    menu->add("Файл/Сохранить", FL_CTRL+'s', save_cb); //добавление кнопки "сохранить", горячие клавиши, привязка соответствующей функции
    
    menu->add("Файл/Выход", FL_CTRL+'q', exit_cb); //добавление кнопки "выход", горячие клавиши, привязка соответствующей функции


    textBuffer = new Fl_Text_Buffer(); //в этой переменной будет храниться содержимое буфера обмена

    textBuffer->callback(modify_cb, &is_modified); //установка callback-функции для отслеживания изменений

    //теперь создадим сам редактор текста:

    editor = new Fl_Text_Editor(0, 25, 800, 575); //создание редактора размером 0, 25, 800, 575

    editor->buffer(textBuffer); //привязка буфера к редактору

    window->end(); //завершение создания элементов окна

    window->show(argc, argv); //отображение окна
    
    return Fl::run(); //запуск главного цикла обработки событий
}


## Шаг 4: реализация главных функций

Теперь создадим все необходимые функции:

void modify_cb(int, int, int, int, const char*, void*) { //callback-функция, вызываемая при изменении текста

    is_modified = true;  //устанавливаем флаг изменения
    
}


void new_cb(Fl_Widget*, void*) {  //Создание нового документа
    
    if (is_modified) { //если документ изменен, спрашиваем о сохранении
        
        int choice = fl_choice("Сохранить изменения?", "Отмена", "Нет", "Да"); //Диалог с 3 кнопками: Отмена (0), Нет (1), Да (2)
        
        if (choice == 2) {  // Если нажали "Да"
        
            save_cb(nullptr, nullptr);  //Вызываем сохранение
            
        } else if (choice == 0) {  //Если "Отмена"
        
            return;  //прекращаем операцию
            
        }
        
    }
    
    textBuffer->text(""); // Очищаем буфер (удаляем весь текст)
    
    is_modified = false; // Сбрасываем флаг изменения
    
    window->label("Текстовый редактор"); // Устанавливаем заголовок окна по умолчанию
}


void open_cb(Fl_Widget*, void*) { //Открытие файла
    
    if (is_modified && fl_choice("Сохранить изменения?", "Отмена", "Нет", "Да") == 2) { //Проверка на несохраненные изменения (аналогично new_cb)
    
        save_cb(nullptr, nullptr); //сохранение
        
    }


  //Открываем диалог выбора файла (фильтр *.txt)
  
    const char *filename = fl_file_chooser("Открыть файл", "Текстовые файлы (*.txt)", NULL);
    
    if (filename) {
        
        if (textBuffer->loadfile(filename)) { //пытаемся загрузить файл в буфер
            
            fl_alert("Не удалось открыть файл"); //Если ошибка - показываем сообщение
        } else {
            
            window->label(filename); //Успешно - меняем заголовок окна на имя файла
            
            is_modified = false; //Сбрасываем флаг изменения
            
        }
        
    }
    
}


// Сохранение файла

void save_cb(Fl_Widget*, void*) {

    // Диалог выбора файла с предложением имени по умолчанию
    
    const char *filename = fl_file_chooser("Сохранить файл", "Текстовые файлы (*.txt)", "untitled.txt");
    
    if (filename) {
        
        if (textBuffer->savefile(filename)) { //Пытаемся сохранить буфер в файл
            
            fl_alert("Не удалось сохранить файл"); //Ошибка сохранения
            
        } else {
            
            window->label(filename); //Успешно - меняем заголовок окна
            
            is_modified = false; // Сбрасываем флаг изменения
            
        }
        
    }
    
}


// Выход из программы

void exit_cb(Fl_Widget*, void*) {

    // Проверяем, нужно ли сохранить изменения
    
    if (!is_modified || fl_choice("Сохранить изменения?", "Отмена", "Нет", "Да") != 0) {
    
        window->hide();  //Скрываем окно (программа завершится)
        
    }
    
}


## Шаг 5: как запускать и компилировать

Linux/macOS:

  *g++ text_editor.cpp -o editor -lfltk*

  *./editor*


Windows (MinGW):

  *g++ text_editor.cpp -o editor.exe -lfltk -mwindows*
  
  *start editor.exe*



![Диаграмма классов текстового редактора](class_diagram.png)












