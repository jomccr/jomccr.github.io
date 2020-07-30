### Wed, Jul 29, 2020 10:55:12 PM 
-----

I despise editing raw HTML and CSS (well, I don't mind as long as it doesn't have to look good...). So I've taken a liking to the plain vanilla markdown files you're able to shove on github pages and just write pretty much whatever you want. All you have to maintain is an index.md file that links to the various posts you've written. 

With this simple of a backend, I get to focus more on my writing. There are a few niceties I've ended up using in my `~/.vimrc` file though. One plugin I use for editing markdown files is Goyo. I'll include a screenshot below. 

![Goyo Screenshot](../../img/goyo_example.png)

The wide margins and minimal layout (no line numbers, status bars, etc) make it a nice distraction-free zone to write what I want and it even handles `:split` commands! In order to automatically start viewing a file with the Goyo extension toggled, I've added the following code to my `~/.vimrc`. 

```
call plug#begin('~/.vim/plugged')
    " ... other plugins here
    Plug 'junegunn/goyo.vim'
call plug#end()

" These are time savers!
nnoremap j gj
nnoremap k gk

if has("autocmd")
    " other filetype specific init commands 
    autocmd BufNewFile,BufRead *.md set wrap linebreak nolist
    autocmd BufNewFile,BufRead *.md Goyo
endif

" This macro just spits out the date time stamp
iab <expr> dts strftime("%c")
```

The first block is loading [Goyo](https://github.com/junegunn/goyo.vim) via the [vim-plug](https://github.com/junegunn/vim-plug) plugin manager. The two `nnoremap` commands let you move up and down through each visual line on your screen rather than by actual line number. These two have come in handy whenever I'm viewing long logfiles with wordwrap enabled. The `if` block contains logic to issue some various commands whenever openeing a markdown file. The last line is a macro I use to timestamp articles at the top of the page. 

I find it's nice to have a `/posts/staging` directory that's in your `.gitignore` file for holding posts-in-progress while not cluttering up the status of your working directory. 

