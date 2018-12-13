minimal-fzf :heart: vim
===============

Things you can do with [fzf][fzf] and Vim.

Rationale
---------

[fzf][fzf] in itself is not a Vim plugin, and the official repository only
provides the [basic wrapper function][run] for Vim and it's up to the users to
write their own Vim commands with it. However, I've learned that many users of
fzf are not familiar with Vimscript and are looking for the "default"
implementation of the features they can find in the alternative Vim plugins.

This repository is a bundle of fzf-based commands and mappings extracted from
my [.vimrc][vimrc] to address such needs. They are *not* designed to be
flexible or configurable, and there's no guarantee of backward-compatibility.

Why you should use fzf on Vim
-----------------------------

Because you can and you love fzf.

fzf runs asynchronously and can be orders of magnitude faster than similar Vim
plugins. However, the benefit may not be noticeable if the size of the input
is small, which is the case for many of the commands provided here.
Nevertheless I wrote them anyway since it's really easy to implement custom
selector with fzf.

Why fork and remove features?
----------------------------

For me fzf.vim has way too many commands.
* No need to have Ag i **Rg** together. This fork favourites the latter.
* Git commands naming could complement fugitive.vim better.
* A lot of what fzf.vim provides can be done from vim.
* It is possible that commands like :Buffers, :Marks will be removed later too.

*Nothing wrong in using one or the other version*

Installation
------------

FORK INFO:
`# this fork assumes you have this line in your .bashrc
export FZF_DEFAULT_COMMAND='rg --files --hidden --no-ignore-vcs'`

fzf.vim depends on the basic Vim plugin of [the main fzf
repository][fzf-main], which means you need to **set up both "fzf" and
"fzf.vim" on Vim**. To learn more about fzf/Vim integration, see
[README-VIM][README-VIM].

[fzf-main]: https://github.com/junegunn/fzf
[README-VIM]: https://github.com/junegunn/fzf/blob/master/README-VIM.md

### Using [vim-plug](https://github.com/junegunn/vim-plug)

If you already installed fzf using [Homebrew](https://brew.sh/), the following
should suffice:

```vim
Plug '/usr/local/opt/fzf'
Plug 'drozdowsky/minimal-fzf.vim'
```

But if you want to install fzf as well using vim-plug:

```vim
Plug 'junegunn/fzf', { 'dir': '~/.fzf', 'do': './install --all' }
Plug 'drozdowsky/minimal-fzf.vim'
```

- `dir` and `do` options are not mandatory
- Use `./install --bin` instead if you don't need fzf outside of Vim
- Make sure to use Vim 7.4 or above

Commands
--------

| Command           | List                                                                    |
| ---               | ---                                                                     |
| `Files [PATH]`    | Files (similar to `:FZF`)                                               |
| `Gfiles [OPTS]`   | Git files (`git ls-files`)                                              |
| `Gfiles?`         | Git files (`git status`)                                                |
| `Buffers`         | Open buffers                                                            |
| `Rg [PATTERN]`    | [rg][rg] search result (`ALT-A` to select all, `ALT-D` to deselect all) |
| `Marks`           | Marks                                                                   |
| `Locate PATTERN`  | `locate` command output                                                 |
| `History`         | `v:oldfiles` and open buffers                                           |
| `History:`        | Command history                                                         |
| `History/`        | Search history                                                          |
| `Commits`         | Git commits (requires [fugitive.vim][f])                                |
| `Gcommits`        | Git commits for the current buffer                                      |
| `Commands`        | Commands                                                                |

- Most commands support `CTRL-T` / `CTRL-X` / `CTRL-V` key
  bindings to open in a new tab, a new split, or in a new vertical split
- Bang-versions of the commands (e.g. `Ag!`) will open fzf in fullscreen
- You can set `g:fzf_command_prefix` to give the same prefix to the commands
    - e.g. `let g:fzf_command_prefix = 'Fzf'` and you have `FzfFiles`, etc.

(<a name="helptags">1</a>: `Helptags` will shadow the command of the same name
from [pathogen][pat]. But its functionality is still available via `call
pathogen#helptags()`. [↩](#a1))

[pat]: https://github.com/tpope/vim-pathogen
[f]:   https://github.com/tpope/vim-fugitive

### Customization

#### Global options

See [README-VIM.md][readme-vim] of the main fzf repository for details.

[readme-vim]: https://github.com/junegunn/fzf/blob/master/README-VIM.md#configuration

```vim
" This is the default extra key bindings
let g:fzf_action = {
  \ 'ctrl-t': 'tab split',
  \ 'ctrl-x': 'split',
  \ 'ctrl-v': 'vsplit' }

" Default fzf layout
" - down / up / left / right
let g:fzf_layout = { 'down': '~40%' }

" In Neovim, you can set up fzf window using a Vim command
let g:fzf_layout = { 'window': 'enew' }
let g:fzf_layout = { 'window': '-tabnew' }
let g:fzf_layout = { 'window': '10split enew' }

" Customize fzf colors to match your color scheme
let g:fzf_colors =
\ { 'fg':      ['fg', 'Normal'],
  \ 'bg':      ['bg', 'Normal'],
  \ 'hl':      ['fg', 'Comment'],
  \ 'fg+':     ['fg', 'CursorLine', 'CursorColumn', 'Normal'],
  \ 'bg+':     ['bg', 'CursorLine', 'CursorColumn'],
  \ 'hl+':     ['fg', 'Statement'],
  \ 'info':    ['fg', 'PreProc'],
  \ 'border':  ['fg', 'Ignore'],
  \ 'prompt':  ['fg', 'Conditional'],
  \ 'pointer': ['fg', 'Exception'],
  \ 'marker':  ['fg', 'Keyword'],
  \ 'spinner': ['fg', 'Label'],
  \ 'header':  ['fg', 'Comment'] }

" Enable per-command history.
" CTRL-N and CTRL-P will be automatically bound to next-history and
" previous-history instead of down and up. If you don't like the change,
" explicitly bind the keys to down and up in your $FZF_DEFAULT_OPTS.
let g:fzf_history_dir = '~/.local/share/fzf-history'
```

#### Command-local options

```vim
" [Buffers] Jump to the existing window if possible
let g:fzf_buffers_jump = 1

" [[B]Commits] Customize the options used by 'git log':
let g:fzf_commits_log_options = '--graph --color=always --format="%C(auto)%h%d %s %C(black)%C(bold)%cr"'

" [Tags] Command to generate tags file
let g:fzf_tags_command = 'ctags -R'

" [Commands] --expect expression for directly executing the command
let g:fzf_commands_expect = 'alt-enter,ctrl-x'
```

#### Advanced customization

You can use autoload functions to define your own commands.

```vim
" Command for git grep
" - fzf#vim#grep(command, with_column, [options], [fullscreen])
command! -bang -nargs=* GGrep
  \ call fzf#vim#grep(
  \   'git grep --line-number '.shellescape(<q-args>), 0,
  \   { 'dir': systemlist('git rev-parse --show-toplevel')[0] }, <bang>0)

" Override Colors command. You can safely do this in your .vimrc as fzf.vim
" will not override existing commands.
command! -bang Colors
  \ call fzf#vim#colors({'left': '15%', 'options': '--reverse --margin 30%,0'}, <bang>0)

" Augmenting Rg command using fzf#vim#with_preview function
command! -bang -nargs=* Rg
  \ call fzf#vim#grep(
  \   'rg --column --line-number --no-heading --color=always --smart-case '.shellescape(<q-args>), 1,
  \   <bang>0 ? fzf#vim#with_preview('up:60%')
  \           : fzf#vim#with_preview('right:50%:hidden', '?'),
  \   <bang>0)

" Likewise, Files command with preview window
command! -bang -nargs=? -complete=dir Files
  \ call fzf#vim#files(<q-args>, fzf#vim#with_preview(), <bang>0)
```

### Completion helper

`fzf#vim#complete` is a helper function for creating custom fuzzy completion
using fzf. If the first parameter is a command string or a Vim list, it will
be used as the source.

```vim
" Replace the default dictionary completion with fzf-based fuzzy completion
inoremap <expr> <c-x><c-k> fzf#vim#complete('cat /usr/share/dict/words')
```

For advanced uses, you can pass an options dictionary to the function. The set
of options is pretty much identical to that for `fzf#run` only with the
following exceptions:

- `reducer` (funcref)
    - Reducer transforms the output lines of fzf into a single string value
- `prefix` (string or funcref; default: `\k*$`)
    - Regular expression pattern to extract the completion prefix
    - Or a function to extract completion prefix
- Both `source` and `options` can be given as funcrefs that take the
  completion prefix as the argument and return the final value
- `sink` or `sink*` are ignored

```vim
" Global line completion (not just open buffers. ripgrep required.)
inoremap <expr> <c-x><c-l> fzf#vim#complete(fzf#wrap({
  \ 'prefix': '^.*$',
  \ 'source': 'rg -n ^ --color always',
  \ 'options': '--ansi --delimiter : --nth 3..',
  \ 'reducer': { lines -> join(split(lines[0], ':\zs')[2:], '') }}))
```

#### Reducer example

```vim
function! s:make_sentence(lines)
  return substitute(join(a:lines), '^.', '\=toupper(submatch(0))', '').'.'
endfunction

inoremap <expr> <c-x><c-s> fzf#vim#complete({
  \ 'source':  'cat /usr/share/dict/words',
  \ 'reducer': function('<sid>make_sentence'),
  \ 'options': '--multi --reverse --margin 15%,0',
  \ 'left':    20})
```

Status line of terminal buffer
------------------------------

When fzf starts in a terminal buffer (see [fzf/README-VIM.md][termbuf]), you
may want to customize the statusline of the containing buffer.

[termbuf]: https://github.com/junegunn/fzf/blob/master/README-VIM.md#fzf-inside-terminal-buffer

### Hide statusline

```vim
autocmd! FileType fzf
autocmd  FileType fzf set laststatus=0 noshowmode noruler
  \| autocmd BufLeave <buffer> set laststatus=2 showmode ruler
```

### Custom statusline

```vim
function! s:fzf_statusline()
  " Override statusline as you like
  highlight fzf1 ctermfg=161 ctermbg=251
  highlight fzf2 ctermfg=23 ctermbg=251
  highlight fzf3 ctermfg=237 ctermbg=251
  setlocal statusline=%#fzf1#\ >\ %#fzf2#fz%#fzf3#f
endfunction

autocmd! User FzfStatusLine call <SID>fzf_statusline()
```

License
-------

MIT

[fzf]:   https://github.com/junegunn/fzf
[run]:   https://github.com/junegunn/fzf#usage-as-vim-plugin
[vimrc]: https://github.com/junegunn/dotfiles/blob/master/vimrc
[rg]:    https://github.com/BurntSushi/ripgrep
