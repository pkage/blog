---
layout: post
title: Neovim and SyncTeX on macOS
tags: vim, tooling
---

# Forward-reverse document synchronization with Neovim and SyncTeX on macOS

## Why?

It's nice to be able to author documents with a real text editor, and (imo)
there isn't a better editor than Neovim. I've [written about setting up Vim and
LaTeX before](https://ka.ge/blog/2020/10/31/vim-latex.html) but after writing my thesis in Neovim
I found myself wanting more from my latex editing experience. I had been using 
macOS's built-in Preview.app to view the compiled file, but this came with a few
downsides: Preview needed to be re-focused for every reload, it's geared
towards reading and not authoring, and of course it didn't support SyncTeX.
That's where [Skim.app](https://skim-app.sourceforge.io/) comes in. Skim
supports live reloading, nice annotations, and of course supports SyncTeX. This
of course prompted me to set up Neovim for SyncTeX.

### Sidebar: what's SyncTeX?

SyncTeX is a utility [written by Jérôme
Laurens](https://www.tug.org/TUGboat/tb29-3/tb93laurens.pdf) which provides a
bunch of information to your pdf reader allowing for synchronization between
your pdf reader and your text editor. Basically, it lets you select a location
from the latex source and show it in your pdf reader, and vice versa. This is
incredibly useful for quickly fixing issues with your documents, as you can
just click on the offending location and jump to it in your editor.

SyncTeX produces a `.synctex.gz` file in the directory with your compiled pdf
file, which your reader and editor look for to find the sync info. You can look
for it too, to ensure the compilation is working properly.

## Compiling with SyncTeX enabled

The first step is making sure that your TeX source is being compiled with
SyncTeX enabled. This can be achieved by adding the appropriate flag to your
latex compiler.

```sh
$ pdflatex -synctex=1 main.tex
```

Note that the `-synctex=1` precedes the file name, it won't work the other way
around. This is also supported by `xetex`.

A quick vimscript keybind mapping compilation to `\ll`:

```vimscript
map <leader>ll :!pdflatex -synctex=1 %<cr>
```

## Forward synchronization: Neovim to Skim

Forward synchronization from Neovim to Skim is also fairly simple. This
solution is taken [largely from the Skim
wiki](https://sourceforge.net/p/skim-app/wiki/TeX_and_PDF_Synchronization/#editor-script-for-vim):

```vimscript
map <leader>ls :w<CR>:silent !/Applications/Skim.app/Contents/SharedSupport/displayline <C-r>=line('.')<CR> %<.pdf<CR><CR>
```

This maps `\ls` to Skim's `displayline` script.

## Reverse synchronization: Skim to Neovim

This is unfortunately a bit more complicated, as Neovim runs a server in the
background but no easy tooling exists to run Vim commands from the command
line. Thankfully, the
[`neovim-remote`](https://pypi.org/project/neovim-remote/) project exists to
bridge this gap in functionality. Install it with:

```sh
$ pip3 install neovim-remote
```

This should add the `nvr` utility to your `$PATH`.

Next, we have to tell Neovim where to put it's server socket. By default, this
is in a private temporary file, which isn't great for outside control. We'll
have to set an environment variable to tell Neovim to put the socket somewhere
accessible: This should go in your `.zshrc` (or `.bashrc`):

```sh
export NVIM_LISTEN_ADDRESS=/tmp/nvimsocket
```

This location has the benefit of being the default for `nvr` already, so you
don't have to fiddle with it later. Next, you'll need to configure Skim to use
`nvr` as a SyncTeX synchronizer. Open Skim, and navigate to the preferences.
Set the preset to "Custom", set the command to the `nvr` path and the arguments
to the following:

![skim preferences]({{ "/img/skim_preferences.png" | relative_url }})

As copyable text:
```
command: /Library/Frameworks/Python.framework/Versions/3.8/bin/nvr
arguments: --remote +%line "%file"
```

Note that your `nvr` path might differ based on your Python configuration, you
can get the full path via:

```sh
$ which nvr
```

This should be all the config you need! To jump from Skim to Neovim,
command-shift-click somewhere in the pdf to select the relevant line in Neovim.
