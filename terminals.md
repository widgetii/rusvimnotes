## Цвета в терминале (https://gist.github.com/XVilka/8346728)

Обычно начинающих пользователей проблемы с поддержкой цвета в терминале вводят в замешательство. В реальном мире у нас есть:

* старый добрый *голый* ASCII
* терминал с поддержкой ANSI Escape кодов (16 цветов букв и фона с полужирным/курсивным начертанием)
* 256-цветная палитра (216 цветов + 16 ANSI + 24 градации серого), сами доступные цвета для использования в палитре - 24-битные)
* полноценная 24-битная поддержка цвета (также известная как true color, "888" или "16 миллионов цветов")

Далее примеры по тексту у меня почему-то не работают:

```
printf "\x1b[${bg};2;${red};${green};${blue}m\n"
printf "\x1b[38;2;255;100;0mTRUECOLOR\x1b[0m\n"
```


https://github.com/iCyMind/NeoSolarized
Solirized with NeoVim and true color terminals
OR
https://github.com/lifepillar/vim-solarized8

https://github.com/airblade/vim-gitgutter
https://github.com/w0rp/ale





https://github.com/mhinz/neovim-remote

Еще скрипты для проверки 24 битного цвета:
https://raw.githubusercontent.com/JohnMorales/dotfiles/master/colors/24-bit-color.sh

