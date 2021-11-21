# telekasten.nvim

A Neovim (lua) plugin for working with a markdown zettelkasten, based on telescope.nvim

Find notes by name, daily notes by date, search within all notes, place and follow links to your notes.  Also, creates today's daily note if not present when searching for notes. At the moment, the daily note template is hardcoded though 😁.

## Install and setup

**MS Windows note:** At the moment, telekasten.nvim is unlikely to be able to run on Windows, because it relies on a bash script.  Just sayin.  Since telekasten.nvim is a project that scratches my own itch, I am not sure if I will add Windows support any time soon.  Should anyone read this: Pull requests are welcome 😄!  Replacing the daily finder by a proper lua version should do the trick.

### 1. Install the plugin
Install with your plugin manager of choice.  Mine is [Vundle](https://github.com/VundleVim/Vundle.vim).

```vimscript
Plugin 'renerocksai/telekasten.nvim'
```

### 2. Configure telekaste.nvimn
Somewhere in your vim config, put a snippet like this:

```vimscript
lua << END
require('telekasten').setup({
     home         = vim.fn.expand("~/zettelkasten"),
     dailies      = vim.fn.expand("~/zettelkasten/daily"),
     extension    = ".md",
     daily_finder = "daily_finder.sh",
     
     -- where to install the daily_finder, 
     -- (must be a dir in your PATH)
     my_bin       = vim.fn.expand('~/bin'),   

     -- download tool for daily_finder installation: curl or wget
     downloader = 'curl',
     -- downloader = 'wget',  -- wget is supported, too
})
END
```

### 3. Install the daily finder
Before using telekasten.nvim, a shell script needs to be installed.  It finds and sorts notes the way we want.  As long as I don't have a lua version for this functionality (this is literally the first time I use lua), we will have to stick with `daily_finder.sh`.

Luckily, the plugin can install the daily finder for you, directly from [GitHub](https://raw.githubusercontent.com/renerocksai/telekasten.nvim/main/ext_commands/daily_finder.sh):

```
:lua require('telekasten.nvim').install_daily_finder()
```

This will download the daily finder into the `bin/` folder of your home directory - or the directory you specified as `my_bin` in the step above.

## Use it

The plugin defines the following functions.

- `find_notes()` : find notes by file name (title), via Telescope
- `find_daily_notes()` : find daily notes by date (file names, sorted, most recent first), via Telescope.  If today's daily note is not present, it will be created with a hardcoded template.
- `search_notes()`: live grep for word under cursor in all notes (search in notes), via Telescope
- `insert_link()` : select a note by name, via Telescope, and place a `[[link]]` at the current cursor position
- `follow_link()`: take text between brackets (linked note) and open a Telescope file finder with it: selects note to open (incl. preview)
- `goto_today()` : pops up a Telescope window with today's daily note pre-selected. Today's note will be created if not present. 
- `install_daily_finder()` : installs the daily finder tool used by the plugin
- `setup(opts)`: used for configuring paths, file extension, etc.

To use one of the functions above, just run them with the `:lua ...` command.  

```vimscript
:lua require("telekasten").find_daily_notes()
```

## Bind it 
Usually, you would set up some key bindings, though:

```vimscript
nnoremap <leader>zf :lua require('telekasten').find_notes()<CR>
nnoremap <leader>zd :lua require('telekasten').find_daily_notes()<CR>
nnoremap <leader>zg :lua require('telekasten').search_notes()<CR>
nnoremap <leader>zz :lua require('telekasten').follow_link()<CR>
nnoremap <leader>zt :lua require('telekasten').goto_today()<CR>

" note: we define [[ in **insert mode** to call insert link
inoremap [[ <ESC>:lua require('telekasten').insert_link()<CR>
```

## The hardcoded stuff
Currently, many things are hardcoded: 
- the file format of the daily notes: YYYY-MM-DD.md
- the template for new daily notes

Here is an example of a newly created daily note:

```markdown
---
title: Saturday, November 20th, 2021
---
```
All the hardcoded stuff is in the `daily_finder.sh` script - which you can edit to your liking. I recommend making a copy, though. Otherwise your changes get lost with every plugin update. Don't forget to set `daily_finder = "my_edited_daily_finder.sh"` in the `setup()`, provided you named your  copy `my_edited_daily_finder.sh`.





