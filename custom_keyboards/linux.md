## Как разобраться в Xkb?

### Первые шаги (день первый)

Давайте запустим терминал и выведем команду

```
setxkbmap -print
```

которая отобразит нам текущие настройки клавиатуры. Я начал экспериментировать
на Raspberry Pi с практически заводскими настройками и получил следующее:

```
xkb_keymap {
        xkb_keycodes  { include "evdev+aliases(qwerty)" };
        xkb_types     { include "complete"      };
        xkb_compat    { include "complete+ledscroll(group_lock)"        };
        xkb_symbols   { include "pc+gb+inet(evdev)+capslock(grouplock)" };
        xkb_geometry  { include "pc(pc104)"     };
};
```

Теперь перейдем в каталог `/usr/share/X11/xkb/` и взглянем на его содержимое:

```
raspberrypi:/usr/share/X11/xkb$ ll
total 32
drwxr-xr-x  2 root  4096 Nov 22  2017 compat
drwxr-xr-x  4 root  4096 Nov 22  2017 geometry
drwxr-xr-x  4 root  4096 Nov 22  2017 keycodes
drwxr-xr-x  2 root  4096 Nov 22  2017 rules
drwxr-xr-x 14 root 12288 Jun 13 21:16 symbols
drwxr-xr-x  2 root  4096 Nov 22  2017 types
```

Примечательно, что правила, начинающиеся с префикса **xkb_** практически
совпадают со структурой каталога (за исключением `rules`). Давайте посмотрим на
первую группу `xkb_keycodes` и содержимое каталога `keycodes`:

```
raspberrypi:/usr/share/X11/xkb$ ls -l keycodes/
total 100
-rw-r--r-- 1 root root  416 Jul 18  2017 README
-rw-r--r-- 1 root root 2176 Jul 18  2017 aliases
-rw-r--r-- 1 root root 3401 Jul 18  2017 amiga
-rw-r--r-- 1 root root 1839 Jul 18  2017 ataritt
drwxr-xr-x 2 root root 4096 Nov 22  2017 digital_vndr
-rw-r--r-- 1 root root   68 Jul 18  2017 empty
-rw-r--r-- 1 root root 8656 Jul 18  2017 evdev
-rw-r--r-- 1 root root 3715 Jul 18  2017 fujitsu
-rw-r--r-- 1 root root 4971 Jul 18  2017 hp
-rw-r--r-- 1 root root 3146 Jul 18  2017 ibm
-rw-r--r-- 1 root root  283 Jul 18  2017 jolla
-rw-r--r-- 1 root root 4595 Jul 18  2017 macintosh
-rw-r--r-- 1 root root  762 Jul 18  2017 olpc
drwxr-xr-x 2 root root 4096 Nov 22  2017 sgi_vndr
-rw-r--r-- 1 root root 2954 Jul 18  2017 sony
-rw-r--r-- 1 root root 5076 Jul 18  2017 sun
-rw-r--r-- 1 root root 8634 Jul 18  2017 xfree86
-rw-r--r-- 1 root root 3204 Jul 18  2017 xfree98
```

Фактически команда в фигурных скобках `include "evdev+aliases(qwerty)"` говорит
системе прочитать и применить настройки из двух файлов `evdev` и `aliases`,
причем из второго взять секцию `qwerty`. Для первого файла система также
возьмет данные только из одной секции, та - которая будет отмечена ключевым
словом `default`.

Здесь под **[evdev](https://ru.wikipedia.org/wiki/Evdev)** мы подразумеваем
стандартный драйвер Xorg, который получает скан коды клавиш напрямую из модуля
ядра Linux (сокращенно `event device`).
**[QWERTY](https://ru.wikipedia.org/wiki/QWERTY)** же - самая популярная
латинская раскладка клавиатуры, включаемая в многих системах по-умолчанию.

Содержимое каталога `/usr/share/X11/xkb`, которое нам в дальнейшем понадобится,
условно можно разделить на 3 группы:

1. Файлы для генерации графического представления раскладки клавиатуры
2. Файлы для проведения сопоставления кодов клавиш и символов
3. Файлы для управления конфигурацией

```
├── geometry      # группа 1
├── keycodes      # группа 2
├── rules         # группа 3
├── symbols       # группа 2
```

Как помните у нас еще остались каталоги `compat` и `types`, но вы про них в
принципе пока можете забыть, их рассмотрим немного позже.

Итак, группа 1 описывает геометрические размеры используемой клавиатуры и
расположение на ней кнопок. Эта информация может использоваться другими
программами для вывода вашей клавиатуры в красивом графическом представлении на
экране (например, в Gnome).

Кроме того, в нашем распоряжении имеется утилита `xkbprint`, которая помогает
понять как X сервер "видит" описанные правила и генерирует графическое
отображение текущей клавиатуры. Пример с клавиатурой Microsoft Natural:

```
setxkbmap -model microsoft -layout us -variant intl -print | xkbcomp - - |  \
    xkbprint -label symbols -pict all -ll 1 -color -o - - | ps2pdf - > a.pdf
```

Поэкспериментируйте с параметром `-label`, поставив другие допустимые значения
`none, name, code, symbols`.

Вторая группа, состоящая из файлов в каталогах `keycodes` и `symbols` - это
источник нашего вдохновения для создания собственной раскладки.

В файле `keycodes/evdev` вы можете увидеть сопоставление между цифровым кодом
клавиши и ее символом, используемым в дальнейшем в раскладке. После этого символ
интерпретируется в таблице сопоставления, находящейся в файле
`symbols/us`.

Кусочек файла `keycodes/evdev`:

```
 <TLDE> = 49;
 <BKSP> = 22;
```

Здесь мы сопоставляем цифровой код клавиши `49` символической константе `TLDE`
(кнопка с символом тильды "~") и код `22` константе `BKSP` (кнопка Backspace).

А это кусочек из файла `symbols/us`:

```
key <TLDE> { [ grave, asciitilde ] };
key <BKSP> { [ BackSpace, BackSpace ] };
```

Здесь мы сопоставляем константе `TLDE` два символа "\`" and "~", а константе
`BKSP` два одинаковых символа "Backspace" and "Backspace". Последние символы из
каждой пары используются только тогда, когда клавиша нажимается непосредственно
с комбинацией `Shift` + клавиша. Это означает, что `Backscape` работает всегда
одинаково независимо от нажатия `Shift`.

В файле `symbols/us` содержится много разных раскладок. Каждая из них может быть
описана полностью своим сводом правил, либо будет включать правила из
родительской и слегка модифицировать их.

К примеру ниже описан вариант русской раскладки `winkeys`, совпадающей с русской
раскладкой, принятой в Windows:

```
default  partial alphanumeric_keys
xkb_symbols "winkeys" {

    include "ru(common)"
    name[Group1]= "Russian";

    key <AE03> { [           3,  numerosign  ] };
    key <AE04> { [           4,   semicolon  ] };
    key <AE05> { [           5,     percent  ] };
    key <AE06> { [           6,       colon  ] };
    key <AE07> { [           7,    question  ] };
    key <AE08> { [           8,    asterisk, U20BD  ] };

    key <AB10> { [      period,       comma  ] };
    key <BKSL> { [   backslash,       slash  ] };
};
```

Мы наследуемся от раскладки `common`, определенной в файле `ru`, и вносим
несколько своих изменений. При этом `common` в свою очередь может быть
наследником какой-то другой раскладки (и так далее). Описанные здесь правила
никак не влияют на своих родителей.

Следующая третья группа файлов - правила, находятся внутри каталога `rules` и
сообщают XKB о доступных раскладках и их вариантах. Вот они:

* base.lst
* base.xml
* evdev.lst
* evdev.xml

Файлы `base.lst` и `evdev.lst` идентичны, тоже самое можно сказать про файлы
`base.xml` и `evdev.xml`. Не знаю, почему так было задумано и почему не заменили
это симлинками.

Заглянем вглубь файла `base.lst` и найдем русскую раскадку `ru`:

```
! layout
...
  ru              Russian
...
! variant
  sah             ru: Yakut
```

в файле `base.xml`:

```
<layoutList>
    <layout>
      <configItem>
        <name>ru</name>

        <shortDescription>ru</shortDescription>
        <description>Russian</description>
        <languageList>
          <iso639Id>rus</iso639Id>
        </languageList>
      </configItem>
      <variantList>
        <variant>
          <configItem>
            <name>sah</name>
            <description>Yakut</description>
            <languageList>
              <iso639Id>sah</iso639Id>
            </languageList>
          </configItem>
        </variant>
        <variant>
        ...
        </variant>
```

и так далее с кучей других вариантов. Фактически файл `base.lst` включает список
всех раскладок и их дочерних вариантов, а `base.xml` добавляет информацию о их
родителях и вариантах. Если вы будете создавать свои варианты раскладок и
хотите, чтобы они отображались в системных утилитах по работе с клавиатурах
таких сред как KDE и Gnome, не забудьте добавить их сюда.

Теперь давайте все соединим вместе и посмотрим, что происходит, когда в
раскладке `us` вы нажмете клавишу "\`":
1. Клавиатура сгенерирует отправит скан-код нажатой клавиши `49`
2. XKB сопоставит полученному коду `49` символьный `<TLDE>`, который найден им в
   файле `keycodes/evdev`
3. XKB прочитает из правил раскладки `us`, записанных в файле `symbols/us`,
   сопоставление `<TLDE>` символьному коду "\`" (или `~`, если был нажат
   модификатор `Shift` и передат в систему этот символ.

В X-сах есть целый компилятор этих файлов, который обобщает все правила и строит
бинарное представление, используемое затем X сервером: `xkbcomp`. Используя эту
же программу мы можем в горячем режиме загрузить скомпилированный бинарник без
необходимости перезапускать X сервер:

```
setxkbmap -print >~/.config/xkb/map

    [... здесь мы делаем какие-то свои изменения выгруженного файла]

xkbcomp ~/.config/xkb/map $DISPLAY
```

Подробнее о назначении каталогов, синтаксисе и дебрях Xkb можно почитать у
[Ивана Паскаля](http://pascal.tsu.ru/other/xkb/setup.html).

Задание для самостоятельной работы: выведите на своей машине список действующих
на текущий момент правил и пробегитесь по файлам и секциям, которые составляют
вашу настройку клавиатуры. Выпишите непонятные моменты по синтаксису, которые у
вас возникнут при чтении файлов. К следующей секции предлагаю перейти по крайней
мере на следующий день, когда новая информация уложится в голове.

### Как изменять конфигурацию XKB? (день второй)

Не важно хотите ли вы изменить файлы, поставляемые с XKB, или просто попробовать
в деле другую раскладку - самым лучшим из доступных вариантом будет открыть
терминал и запустить `setxkbmap` для внесения изменений на лету.

Самая простая команда будет:

```
setxkbmap -layout us
```

но давайте представим что вы кроме стандартной QWERTY раскладки еще захотите
попробовать специально придуманную для программистов
[Colemak](https://ru.wikipedia.org/wiki/Colemak)?

```
setxkbmap -layout us,us -variant ,colemak
```

Будьте здесь очень внимательны с синтаксисом, используемым `setxkbmap`:
количество раскладок и их вариантов должно совпадать. Именно поэтому мы дважды
указываем `us`, а `colemak` предваряется запятой. Можно представить это как
*setxkbmap -layout us,us -variant "null",colemak*.

Используя этот подход вы можете загрузить любой вариант раскладки. Чтобы
посмотреть какие варианты доступны на вашей системе для раскладки `us`,
воспользуйтесь командой:

```
grep "xkb_symbols" /usr/share/X11/xkb/symbols/us
```

Посмотреть какая конфигурация сейчас загружена можно с помощью уже знакомой
команды, к которой добавим более подробный вывод:

```
setxkbmap -print -verbose 10
```

В результате вы увидите примерно следующее:

```
Setting verbose level to 10
locale is C
Trying to load rules file ./rules/evdev...
Trying to load rules file /usr/share/X11/xkb/rules/evdev...
Success.
Applied rules from evdev:
rules:      evdev
model:      pc104
layout:     us,us
variant:    ,colemak
options:    grp:caps_toggle,grp_led:scroll
Trying to build keymap using the following components:
keycodes:   evdev+aliases(qwerty)
types:      complete
compat:     complete+ledscroll(group_lock)
symbols:    pc+us+us(colemak):2+inet(evdev)+capslock(grouplock)
geometry:   pc(pc104)
xkb_keymap {
        xkb_keycodes  { include "evdev+aliases(qwerty)" };
        xkb_types     { include "complete"      };
        xkb_compat    { include "complete+ledscroll(group_lock)"        };
        xkb_symbols   { include "pc+us+us(colemak):2+inet(evdev)+capslock(grouplock)"   };
        xkb_geometry  { include "pc(pc104)"     };
};
```

Хочу Colemak+Qwerty!

Выводы по keycodes и symbols

Домашнее задание: исследовать grp:alt_space_toggle (переключатели раскладки и
swapespace)

### Изучаем свою клавиатуру (день третий)

Для следующих экспериментов я возьму старенькую экзотическую Bluetooth [клавиатуру от
Amazon для Android устройств](https://www.amazon.com/gp/product/B005OOKNP4/),
все что нужно знать о которой: она больше не продается. На ней отсутствуют кнопки
`Escape`, ряд функциональных клавиш, но вместе с тем есть куча неиспользуемых
мной кнопок, которым я и хочу присвоить полезные функции.

Вы в свою очередь можете использовать любую поделку с Aliexpress, любимую
навороченную клавиатуру с отдельными кнопками под Facebook и Twitter или просто
глубже изучить ту, которая верой и правдой служит много лет.

Когда вы нажимаете любую клавишу у себя на клавиатуре, операционная система
получает ее числовой код. Его можно посмотреть с помощью программы `xev`,
которая в некоторых дистрибутивах находится в пакете `x11-utils`. Запустите из
терминала команду `xev -event keyboard` и увидите ее в действии. К примеру, при
нажатии кнопки `a` можно будет получить:

```
KeyPress event, serial 25, synthetic NO, window 0x1200001,
    root 0x259, subw 0x0, time 312529, (-834,-450), root:(38,34),
    state 0x0, keycode 38 (keysym 0x61, a), same_screen YES,
    XLookupString gives 1 bytes: (61) "a"
    XmbLookupString gives 1 bytes: (61) "a"
    XFilterEvent returns: False

KeyRelease event, serial 28, synthetic NO, window 0x1200001,
    root 0x259, subw 0x0, time 312603, (-834,-450), root:(38,34),
    state 0x0, keycode 38 (keysym 0x61, a), same_screen YES,
    XLookupString gives 1 bytes: (61) "a"
    XFilterEvent returns: False
```

Код 38 представляет собой клавишу `a`.

Несколько слов об утилите `xev`. Как можно догадаться из приставки **x** она
будет работать только под X-ами и слушает event-ы. Причем мы специально
установили фильтр для событий клавиатуры, так как нам не нужны остальные
события. В качестве самостоятельной работы запустите ее без аргументов и
посмотрите как она ведет себя с другими событиями X Window: потыкайте мышкой,
поперемещайте окна и экспериментируйте.

Кстати, вы можете запускать ее удаленно с соседней машины и просматривать
события от локальной клавитуры, указав правильный `DISPLAY`:

```
DISPLAY=:0 xev
```

Как вы видели из предыдущего примера, операционная система получает два события:
одно, когда клавиша нажата, а другое - когда отпущена. Если вы зажмете одиночную
клавишу и будете ее держать, то получите бесконечный поток событий о нажатии и
отпускании клавиши.

Однако, если вы понажимаете *клавиши-модификаторы* типа `Ctrl`, `Shift`, `Alt`,
`Escape`, `Caps Lock`, `Insert`, `Num Lock`, то получите другое поведение.
К мы здесь видим - некоторые клавиши не такие как все.

Модификаторы используются вместе с другими клавишами для изменения их обычного
поведения:

* буква, нажатая вместе с `Shift`, временно меняет ее на заглавную
* клавиша `Caps Lock` переключает в режим постоянного ввода больших букв
* с **модификатором 3-го уровня** (произвольная клавиша, выбранная среди других
    модификаторов, обычно это `Alt` или `Windows`, позволяет вводить
    дополнительные символы, не меняя языковую раскладку. Обычно это используется
    в международных раскладках клавиатурах и также в европейских с обилием букв
    с диакритическими символами.

Пример использования модификатора 3-го уровня на международной расладке
setxkbmap -layout us,us -variant ,dvorak -option "lv3:
,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,,

Пришла пора заглянуть в ранее пропущенный каталог `/usr/share/X11/xkb/types` и
посмотреть на содержащиеся там файлы. Вспоминаем вывод `setxkbmap`:

```
xkb_keymap {
...
      xkb_types     { include "complete"      };
...
};
```

Начнем просмотр с файла `/usr/share/X11/xkb/types/complete`:

```
default xkb_types "complete" {
    include "basic"
    include "mousekeys"
    include "pc"
    include "iso9995"
    include "level5"
    include "extra"
    include "numpad"
};
```

К видно в файле нет отдельных правил и он включает в себя правила из множества
других. Перейдем к файлу `basic` в том же каталоге:

```
default xkb_types "basic" {

    // Fairly standard definitions for
    // three of the four required key types.
    // The fourth type "KEYPAD" is defined in the "numpad" file.

    virtual_modifiers NumLock;

    type "ONE_LEVEL" {
        modifiers = None;
        map[None] = Level1;
        level_name[Level1]= "Any";
    };

    type "TWO_LEVEL" {
        modifiers = Shift;
        map[Shift] = Level2;
        level_name[Level1] = "Base";
        level_name[Level2] = "Shift";
    };

    type "ALPHABETIC" {
        modifiers = Shift+Lock;
        map[Shift] = Level2;
        map[Lock] = Level2;
        level_name[Level1] = "Base";
        level_name[Level2] = "Caps";
    };

};
```

Нас встречает комментарий, объясняющий, что здесь мы описываем три из четырех
типов клавиш (четвертый из которых - это NumLock, содержащийся в одноименном
файле). `ONE_LEVEL` - это клавиши, к которым не применяются действия
модификаторов, например это `Enter`, `Пробел`, `Escape`, `F-клавиши` и сами
модификаторы `Shift`, `Alt`, `Ctrl` и так далее. Типы клавиш `TWO_LEVEL` и
`ALPHABETIC` выдают различные символы в зависимости от нажатия
клавиши-модификатора `Shift`. `ALPHABETIC` дополнительно зависят от статуса
переключения `CapsLock`. Синтаксис их определения довольно простой, строка:

```
modifiers = Shift+Lock;
```

указывает, что тип клавиш зависит только от этих клавиш-модификаторов.

Вообще история их клавиш довольно интересна и началась во времена рождения X
Window. Всего в протоколе обмена X предусмотрено 8 бит для отображения состояния
модификаторов:

* Shift
* Lock
* Control
* Mod1
* Mod2
* Mod3
* Mod4
* Mod5

Фактически модификатором может быть любая клавиша, но обычно, как можете
догадаться, левая и правая клавиши `Shift` сопоставлены модификатору *Shift*,
`Caps Lock` - на *Lock* и левый и правый `Ctrl` - на *Control*.

С другими модификаторами не все так очевидно: отсутствующуй в этом списке `Alt`
обычно сопоставлен *Mod1*. Тайловые оконнные менеджеры у себя в настройках
часто требуют указывать глобальную клавишу-модификатор, к которой
привязываются сочетания хоткеев именно в формате *Mod*.

На старых клавиатурах компьютеров
[Symbolics](http://en.wikipedia.org/wiki/Symbolics) присутствовали железные
кнопки `Meta`, `Super`, `Hyper` и некоторые другие, которые вошли в историю и
остались в исходниках X Window под этими именами (и до сих пор привязаны к
каким-то модификаторам). `Super` присутствует на современных клавиатурах, но
обычно называется как `Win`, `Cmd` или еще что-то там и располагается рядом с
кнопками 'Alt'. В современных системах `Super` привязана к *Mod4*.

Но а клавиши `Hyper` и `Meta` окончательно пропали, хотя в некоторых программах
`Alt` называют `Meta` и он заменяет ее функционал. Мы узнали что чем являются
модификаторы *Mod1* и *Mod4*, однако не рассмотрели какими сопоставлены другие и
привязаны ли они чему-нибудь в системе по умолчанию. Проверить это можно с
помощью команды:

```
xmodmap -pm 
```

и увидеть, что *Mod2* - это `Num Lock`, *Mod3* - исчезнувший `Hyper`, а *Mod5* -
присвоено каким-то значениям `Mode_switch` и `ISO_Level3_Shift`.

https://unix.stackexchange.com/questions/55076/what-is-the-mode-switch-modifier-for/55154#55154


https://wiki.archlinux.org/index.php/X_keyboard_extension#Editing_the_layout

https://habr.com/post/83223/

Домашнее задание: убрать у клавиши `Caps Lock` функцию *Lock* и добавить ей
функцию *Control*. Усложенное задание: убрать и у левой клавиши `Ctrl` функцию
*Control* и внедрить в рабочий процесс привычку пользоваться клавишей `Caps
Lock` вместо него.

### Утилита xcape

setxkbmap -option ctrl:swapcaps
xcape -e 'Control_L=Escape'&

### Сохраняем наши настройки

На Raspberry Pi в файле `/etc/default/keyboard` по умолчанию записано:

```
# KEYBOARD CONFIGURATION FILE

# Consult the keyboard(5) manual page.

XKBMODEL="pc104"
XKBLAYOUT=gb
XKBVARIANT=
XKBOPTIONS="grp:caps_toggle,grp_led:scroll"

BACKSPACE="guess"
```

а вот в Arch ничего нет!

https://aty.sdsu.edu/bibliog/latex/debian/keyboard.html
https://wiki.debian.org/XStrikeForce/InputHotplugGuide

### Отладка по xkbcomp $DISPLAY -

### Избегаем туннельного синдрома

### Simple X hotkey daemon https://github.com/baskerville/sxhkd

Shift работа с правильного ряда
Тренажеры и Ctrl-W
Переключалка языка
Space как Space, а удержание - временное включение раскладки (или HyperKey)
Caps -> Ctrl/Esc, Enter -> Ctrl/Enter
Shift+Caps Lock при этом обычно выполняет старую функцию Caps Lock
Backspace в центр
Правый! Cmd + hjkl -> стрелочки
Эмуляция мышиного клика по нажатию кнопки клавиатуры
https://habr.com/sandbox/21270/
Escape -> Lock, Eject - Sleep

https://github.com/LemmingAvalanche/keyboard

### Литература для чтения

[Еще один гайд по XKB](https://www.charvolant.org/doug/xkb/html/xkb.html)

[Описание протокола XKB](https://www.x.org/wiki/XKB/)


