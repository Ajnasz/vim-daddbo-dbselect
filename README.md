# vim-dadbod DBSelect

Extension for [vim-dadbod](https://github.com/tpope/vim-dadbod) to enable selection of database configurations from your preconfigured list.

## Setup

### Configuration

`g:dadbods`

A dictionary where the key represents the text displayed on the UI, and the value is either a connection string or a function that returns a connection string.

`g:dadbod_loaders`

A list of functions, each returning a dictionary to extend `g:dadbods`. This is useful for dynamically loading configurations from files.

### FZF.vim

Install https://github.com/junegunn/fzf.vim plugin

```vim
command! DBSelect :call dadbod_dbselect#db_select_fzf()
```

### Neovim vim.ui

It has a built in function to use neovim's [vim.ui.select()](https://neovim.io/doc/user/lua.html#vim.ui) function

```vim
:call dadbod_dbselect#db_select_vim_ui_select()
```

### Telescope extension

The plugin also provides an extension for the [telescope](https://github.com/nvim-telescope/telescope.nvim) plugin:

```vim
:Telescope vim-dadbod-db-select
```

### Example configuration

```vim
" static configurations
let g:dadbods = {
  \"mysql:localhost": "mysql://root:password@127.0.0.1:3306/?login-path=localhost",
  \"mysql:secret": {-> systemd("gpg -d ~/.secretdb.gpg")}, " call an external tool to get the connection string
  \"mongo:localhost": "mongodb://localhost",
\}

function! FileToDict(filename, separator)
  return reduce(
        \ map(readfile(a:filename), {_, v -> split(v, a:separator)}),
        \ {d, v -> extend(d, {v[0]: v[1]})},
        \ {})
endfunction

" load configuration from `/path/to/file` where each line contains one db
" entry, the display name separated from the connection string with a tab
" character
let g:dadbod_loaders = [ {-> FileToDict("/path/to/file", "\t")}]
```
