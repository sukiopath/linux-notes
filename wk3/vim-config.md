
# Vim config

Copy and paste the code block below into `~/.vimrc` 

```

" Comments in Vimscript start with a `"`.

" create a new file ~/.vimrc and copy this code block into it.
" you can create the file by entering the command 'vim ~/.vimrc'

" Vim is based on Vi. Setting `nocompatible` switches from the default
" Vi-compatibility mode and enables useful Vim functionality.
set nocompatible

" Turn on syntax highlighting.
syntax on

" Disable the default Vim startup message.
set shortmess+=I

" Show line numbers.
set number

" This enables relative line numbering mode.
" with relative and regular the current line will show the actual line number
set relativenumber

" Always show the status line at the bottom, even if you only have one window open.
set laststatus=2

" use the system clipboard instead of buffer
" depending on the distro you may need to install gvim, or vim-gtk to get this feature
" you can check that you have a version of vim capable of this by running this command 'vim --version | grep clipboard'
" if you see +clipboard it will work, if not it won't.
set clipboard=unnamedplus

" The backspace key has slightly unintuitive behavior by default. For example,
" by default, you can't backspace before the insertion point set with 'i'.
" This configuration makes backspace behave more reasonably, in that you can
" backspace over anything.
set backspace=indent,eol,start

" replace tabs with spaces
set expandtab
" 1 tab = 2 spaces
set tabstop=2 shiftwidth=2

" By default, Vim doesn't let you hide a buffer (i.e. have a buffer that isn't
" shown in any window) that has unsaved changes. This is to prevent you from "
" forgetting about unsaved changes and then quitting e.g. via `:qa!`. I find
" hidden buffers helpful enough to disable this protection. See `:help hidden`
" for more information on this.
set hidden

" Do not save backup files. and swap files
" This might save you a lot of headache later.
" If you quit vim incorrectly it leaves a swap file, 
" like a backup of unsaved changes. This is a good thing, but more often than
" not these swapfiles cause problems for peopel new to vim.
set nobackup
set noswapfile

" alternatively you could save them in a different location, 
" just uncomment the lines  below and comment out the nobackup and noswapfile above 
"set backupdir=~/.vim/backup//
"set directory=~/.vim/swap//
"set undodir=~/.vim/undo//

" This setting makes search case-insensitive when all characters in the string
" being searched are lowercase. However, the search becomes case-sensitive if
" it contains any capital letters. This makes searching more convenient.
set ignorecase
set smartcase

" search
set hlsearch     " Highlight all search results
set incsearch    " Searches for strings incrementally

" Use the mouse, you actually usually don't want this.
" You are better off learning to use Vim without using the mouse
" You will be faster and more productive without it.
" If you really want to use the mouse uncomment the line below.
"set mouse=a

" enable wildmenu, a little menu that displays files and commands in the button left corner
set wildmenu
set wildmode=list:longest,full

" Unbind some useless/annoying default key bindings.
nmap Q <Nop> " 'Q' in normal mode enters Ex mode. You almost never want this.

" Disable audible bell because it's annoying.
set noerrorbells visualbell t_vb=

" Try to prevent bad habits like using the arrow keys for movement. 
nnoremap <Left>  :echo "Use h"<CR>
nnoremap <Right> :echo "Use l"<CR>
nnoremap <Up>    :echo "Use k"<CR>
nnoremap <Down>  :echo "Use j"<CR>
```
