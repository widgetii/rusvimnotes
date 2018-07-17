http://web-techno.net/vim-php-ide/
http://web-techno.net/phpstorm-vs-vim/

http://kushellig.de/neovim-php-ide/

Bakhtiyar, [02.07.18 14:42]
[In reply to ∀lǝxǝʎ]
Тогда есть такая связка 
        " - LanguageServerPHP for NeoVim --- {{{
            " - Очень интересная реализация!
            " Plug 'roxma/LanguageServer-php-neovim', {'do': 'composer install && composer run-script parse-stubs'} " --- {{{
            " }}}

            " Plug 'autozimu/LanguageClient-neovim', {'branch': 'next', 'do': './install.sh'} " --- {{{
            " }}}

        " }}}

Bakhtiyar, [02.07.18 14:43]
Работает через LanguageServer

Bakhtiyar, [02.07.18 14:44]
Такую связку я использую для проектов где инклюды через жопу
