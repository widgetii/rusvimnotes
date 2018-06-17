## Пробелы и табы

Первым делом, что вы скорее всего захотите настроить в новом редакторе, будет
комфортная для вас конфигурация работы с пробелами и табуляцией. Исторически все
программисты делятся на любителей табуляции (привет Линус Торвальдс!) и
любителей заменить ее на четко заданное число символов пробелов (это, например,
я).

В качестве домашнего задания - исследуйте [стандарты кодирования ядра
Linux](https://github.com/torvalds/linux/blob/master/Documentation/process/coding-style.rst) и 

### Отображение невидимых символов

Настройка `list` редактора Vim используется для возможности отображения
символов, которые есть в файле, но обычно никак не выводятся на экран

По умолчанию listchars имеет следующие настройки:
* Vim: set listchars=eol:$
* NeoVim: set listchars=tab:> ,trail:-,nbsp:+

Примеры для поиска своей идеальной настройки:
* Если у вас ASCII терминал:
    `set listchars=tab:..,trail:_,extends:>,precedes:<,nbsp:~`
* TextMate: `set listchars=tab:▸\ ,eol:¬`
* Моя: `set listchars=tab:▶\ ,trail:·,extends:\#,nbsp:.`
* Еще одна с Reddit: `set list listchars=tab:▸\ ,trail:·,precedes:←,extends:→`
* Для бородатых дядек: `set listchars=eol:§,tab:¤›,extends:»,precedes:«,nbsp:‡`
* И бонусом: `set showbreak=↪\ `

И просто наборы юникодных символов отдельно:
* tab: ▸ » → ▶
* trail: · • ◥
* nbsp: · ␣ •
* precedes: ← ‹ ⟨ ❮ …
* extends: → › ⟩ ❯ …
* eol: ¬ ↲ ★

Не забудьте прочитать раздел справки **:help listchars** перед тем, как начнете
подбирать настройку под себя.

[Самый первый скринкаст от Дрю
Нейла](http://vimcasts.org/episodes/show-invisibles/)

### Делаем 

Первым делом давайте настроим "ширину" символа Tab, который будет отображаться
на экране при просмотре файлов:

```
set tabstop=4       " number of visual spaces per TAB
```

здесь я использую указание, что каждый TAB у меня будет визуально занимать ровно
4 знакоместа на моем экране.

### Разные настройки для разных языков

Можно воспользоваться автоматическими командами и выставить для каждого
определенного языка собственные оступы. Пример для Go:

```
au FileType go set noexpandtab
au FileType go set shiftwidth=4
au FileType go set softtabstop=4
au FileType go set tabstop=4
```

### Ссылки

1. [Эпичный срач](https://yarchive.net/comp/linux/coding_style.html)
1. vimcasts
Galore?

