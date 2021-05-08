---
title: "Search in Vim"
date: 2021-05-09T11:33:39-07:00
draft: true
---

# `CocSearch`

With `coc-nvim` plugin, `CocSearch` is available. It uses ripgrep under the 
hood and is super fast.

It opens a window with results, and just press `<CR>` to open the file under
cursor. The file is opened in a new split, and subsequent file opens in the 
same split.

Example command: `:CocSearch STM32F411 ./keyboards/`

Arguments for ripgrep (can be added by using `-A` in `CocSearch` comman)

  * -g {GLOB}: Include or exclude files/directories that matches the glob. 
      Precede glob with `!` to exlude.
  * -w --word-regexp: Only show matches surrounded by word boundaries.
  * -x --line-regexp: Only show matches surrounded by line boundaries.

Example: search a string but ignore `keyboards/` folder and exlude `.mk` files.

```
:CocSearch STM32F411 ../../.. -g !keyboards/ -g !*.mk
```

## Mappings

I created the following mappings to use cocsearch

* `<Leader>gg`: In normal mode, it enters `:CocSearch ` and waits for input.
In visual mode, it fills the argument with the visual selection. (spaces are 
  escaped)
* `<Leader>g{w|W}`: Search the word/WORD under cursor.

Combine this with the shortcut `%%` (expands to current file's folder), we can
easily start searching.

# Other options

* `git grep`: Can search from non-current version of the source. 
