In fact, the home row has only around ⅓ of keystrokes in QWERTY while in Colemak it has ¾ in English.
http://blog.erw.dk/2015/02/27/4-years-with-colemak/ 

https://forum.colemak.com/topic/1858-learn-colemak-in-steps-with-the-tarmak-layouts/
https://forum.colemak.com/topic/1438-dreymars-big-bag-of-keyboard-tricks-linuxxkb-files-included/

https://github.com/dhardy/keyboard

http://hcoder.org/2010/10/16/a-month-of-colemak/

I worked with this layout for a few weeks and then even went a bit further by adding the “tn” insert mode escape sequence in vim. On colemak, t is on the left index finder and n is on the right index finger.

I found tn to be a good shortcut, and rarely used in english. I also liked the feel of using both index fingers to escape to normal mode.

https://upload.wikimedia.org/wikipedia/commons/a/a0/KB_Terminal_ADM3A.svg

https://forum.colemak.com/topic/1808-colemak-and-vimplus-zsh-tmux-less-etc/ 

Of course, Colemak is a hard barrier to other people using my computer now. I see it as a security feature, hahaha. I secretly have a keybind set to toggle between Qwerty and Colemak, but I only tell my best friends about that one


Shift-Spacebar for me. Very little movement.

""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""
" Colemak-Vim Mappings
"
" The idea is to use HNEI as arrows – keeping the traditional Vim homerow style
– and changing as
" little else as possible. This means JKL are free to use and NEI need new keys.
" - k/K is the new n/N.
" - s/S is the new i/I ["inSert"].
" - j/J is the new e/E ["Jump" to EOW].
" - l/L skip to the beginning and end of lines. Much more intuitive than ^/$.
" - Ctrl-l joins lines, making l/L the veritable "Line" key.
" - r replaces i as the "inneR" modifier [e.g. "diw" becomes "drw"].
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""
" HNEI arrows. Swap 'gn'/'ge' and 'n'/'e'.
  noremap n gj|noremap e gk|noremap i l|noremap gn j|noremap ge k
  " In(s)ert. The default s/S is synonymous with cl/cc and is not very useful.
    noremap s i|noremap S I
    " Last search.
      noremap k n|noremap K N
      " BOL/EOL/Join Lines.
        noremap l ^|noremap L $|noremap <C-l> J
        " _r_ = inneR text objects.
          onoremap r i
          " EOW.
          " TODO: I never use this. Use for something else?
            noremap j e|noremap J E
            "noremap j e|noremap J E|noremap gj ge|noremap gJ gE


""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""
" Other Colemak Arrow-Based Mappings
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""
" Switch tabs.
  nnoremap <C-i> <C-PageDown>|nnoremap <C-h> <C-PageUp>
  " Switch panes.
    noremap H <C-w>h|noremap I <C-w>l|noremap N <C-w>j|noremap E <C-w>k
    " Moving windows around.
      noremap <C-w>N <C-w>J|noremap <C-w>E <C-w>K|noremap <C-w>I <C-w>L
      " High/Low/Mid.
        noremap <C-e> H|noremap <C-n> L|noremap <C-m> M
        " Scroll up/down.
          noremap zn <C-y>|noremap ze <C-e>
          " Back and forth in jump and changelist.
            nnoremap gh <C-o>|nnoremap gi <C-i>|nnoremap gH g;|nnoremap gI g,
            Also,

            " Sane redo.
            noremap U <C-r>


Tap your Z key, and it sends the letter Z as expected. Press and hold it down...
and it becomes Ctrl.

https://gist.github.com/benyarb/4060552
