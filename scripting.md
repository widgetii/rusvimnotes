https://devhints.io/vimscript

### Префиксы переменных в VimScript

`:help internal-variables:`

```
There are several name spaces for variables.  Which one is to be used is
specified by what is prepended:

        (nothing) In a function: local to a function; otherwise: global
        |buffer-variable|    b:   Local to the current buffer.
        |window-variable|    w:   Local to the current window.
        |tabpage-variable|   t:   Local to the current tab page.
        |global-variable|    g:   Global.
        |local-variable|     l:   Local to a function.
        |script-variable|    s:   Local to a |:source|'ed Vim script.
        |function-argument|  a:   Function argument (only inside a function).
        |vim-variable|       v:   Global, predefined by Vim.

        The scope name by itself can be used as a |Dictionary|.  For example, to
        delete all script-local variables: >
            :for k in keys(s:)
            :    unlet s:[k]
            :endfor
```

http://learnvimscriptthehardway.stevelosh.com/chapters/17.html

https://gabri.me/blog/diy-vim-statusline/

