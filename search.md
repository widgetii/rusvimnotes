### vim-grepper

I really prefer vim-grepper because:

```
Use your favorite grep tool (ag, ack, git grep, ripgrep, pt, sift, findstr,
grep) to start an asynchronous search. All matches will be put in a quickfix or
location list.
```

I like that I can continue working while the search is happening.

yeah, my suggestion to flygrep would be to enable use of our favorite tools for the search

### fzf vs ctrl-p

is there any difference between fzf vim and ctrl p or do they solve the same problem?

1. fzf its faster
2. ctrlp is pretty fast if you set it up right, yet personally I use FZF becase you can toggle search by path and search by file name<Paste>
3. I think Fzf has more features
4. also, FZF is a standalone program that you can use outside vim

http://web-techno.net/vim-search/

http://stevelosh.com/blog/2010/09/coming-home-to-vim/
The next thing I do is tame searching/moving:

https://bluz71.github.io/2017/05/15/vim-tips-tricks.html
Set global replacement as the default
Substitute in a visual block
Count the number of pattern matches

## Замена длинных путей через :s

`s,/usr/local/bin,/opt/bin,g`
