# Aum.nvim

This config is a fork from [shaunsingh/nyoom.nvim](https://github.com/shaunsingh/nyoom.nvim) where I try to create my own config based on his.

Nyoom.nvim is a relatively small configuration that is heavily based on fennel, a lisp sibling of lua. Coming from Emacs, I see this as a feature.
Bare neovim (like bare Emacs) requires a lot of tweaking and package installing, so I usually turn to preconfigured configs such as Spacemacs and LunarVim for neovim. For various reasons, I have decided to ditch both Spacemacs and LunarVim and try other approaches such as this one.

As for the name, I borrowed the Sanskrit ligature "ॐ" which can be transliterated as "Om", "Aum" or "Ohm". I find the symbolism and meaning of ॐ very interesting and beautiful, so I have adopted it. Plus, there's a genre of music I like a lot that uses Hindu symbolism regularly.

## Install

### Dependencies

The only dependencies are `neovim-nightly` and `git`.

### Aum

I have found that the best way is to clone the repository (usually I do this using `ghq get`), then create a symlink between this repo and `~/.config/nvim` then using the nix flake (or installing the dependencies in [Regular](#regular)).

I will have to revise this section and update it accordingly, since my objective is to have nix install the config and its dependencies.

### Regular:

Install the following dependencies: 
- neovim-nightly (or neovim stable)
- rust/cargo (to build parinfer-rust)
- ripgrep (optional, for telescope)
- nodejs (optional, for copilot)
- fennel + fnlfmt (not required, but recommended)
- font with nerdfont icons 
```bash
git clone --depth 1 https://github.com/shaunsingh/nyoom.nvim.git ~/.config/nvim 
nvim
```
If you're using hotpot (default), enter neovim, exit neovim, enter it again, then run `:PackerSync`. (The restart is nessecary for neovim to load the newly compiled code). 
If you're using tangerine, run `:FnlCompile!`, then run `:PackerSync`

### Using nix: 

Requires nix version > 21.11, with experimental features `flakes` and `nix-commands` enabled
```bash
git clone --depth 1 https://github.com/shaunsingh/nyoom.nvim.git && cd nyoom.nvim
nix develop
```
Then run `nvim` as usual, and `:PackerSync` to update/install plugins

## Why fennel?
Fennel is a programming language that brings together the speed, simplicity, and reach of Lua with the flexibility of a lisp syntax and macro system. Macros are how lisps accomplish metaprogramming. You’ll see a lot of people treat lisp macros with a kind of mystical reverence. While several other languages have macro systems, the power of macros in lisps stem from allowance of lisps to you to write programs using the same notation you use for data structures. Remember: code is data, and we just need to manipulate data.

While people largely over exaggerate the usefulness of macros, there are a few places where macros shine, and configurations are one of them. Utilizing macros, we can essentially create our own syntax. For example, lets take a look at the `set!` macro I've used. `set!` is used for `vim.opt` options. For example, `(set! mouse :a)` expands to `vim.opt["mouse"]="a"`. If a string or number isn't passed to `set!`, it will assume true. e.g. `(set! list)` will expand to `vim.opt["list"]=true`. Similarly if the option starts with no, it will assume false e.g. `(set! noru)` will expand to `vim.opt["ru"]=false`.

With the macros provided, you can configure neovim just as easily, or dare I say easier than you can with Lua or vimscript, while retaining the performance benefits of LuaJIT.

All the magic happens in the `fnl/` folder. Some files to check out:
- `init.fnl`: Same as your init.lua would be, just in fennel! Disables some plugins and loads the core config
- `pack/`: This is where all your plugins go. The `pack.fnl` file is in charge of configuring packer, installing, as well as loading plugins
- `core/defs.fnl`: This is where neovim settings go. 
- `core/maps.fnl`: This is where mappings go. 
- `macros/`: In lisps, macros allow the user to define arbitrary functions that convert certain Lisp forms into different forms before evaluating or compiling them. This folder contains all the macros that I (and a few others, thanks David and Kat!) have written to help you out on your neovim journey. I don't recommend touching this file unless you know what you're doing

### Learning Fennel

For most people, chances are you haven't even heard of fennel before. So where should you start?
1. Read through the [Documentation](https://fennel-lang.org/)
2. [Install fennel](https://fennel-lang.org/setup#downloading-fennel) yourself! (Skip the part where it goes over adding fennel support to your editor, that's what this project is for :p)
3. [Learn lua](https://fennel-lang.org/lua-primer) first. I recommend reading through the [Neovim lua guide](https://github.com/nanotee/nvim-lua-guide) as well.
4. [Learn fennel](https://fennel-lang.org/tutorial)
5. Go over the [Style guide](https://fennel-lang.org/style).
6. [Learn macros](https://fennel-lang.org/macros). 

If you have trouble configuring neovim to your needs, check out [Antifennel](https://fennel-lang.org/see) to see how lua code compiles back to fennel! However, the generated code isn't always the cleanest, so its recommend you use it as a last resort. If you need any help, feel free to reach out to me via email or discord, and be sure to join the [Conjure Discord](https://conjure.fun/discord) too! 

### When Fiddling with Fennel Code

 While fiddling with the config, you can check if the things are not broken yet:
 1. evaluate form you just written (`<localleader>er`, by default `<space>mer`)
 2. evaluate buffer (`<localleader>eb`, by default `<space>meb`)
 3. start another neovim with a vim command: `:!neovim --headless +PlugSync`

## Design 
Nyoom.nvim is designed against the mantras of [doom-emacs](https://github.com/hlissner/doom-emacs): (shamelessly copy pasted)
- Gotta go fast. Startup and run-time performance are priorities.
- Close to metal. There's less between you and vanilla neovim by design. That's less to grok and less to work around when you tinker.
- Opinionated, but not stubborn. Nyoom (and Doom) are about reasonable defaults and curated opinions, but use as little or as much of it as you like.
- Your system, your rules. You know better. At least, Nyoom hopes so! There are no external dependencies (apart from rust), and never will be. 

It also aligns with many of Doom's features:
- Minimalistic good looks inspired by modern editors.
- A modular organizational structure for separating concerns in your config. 
- A standard library designed to simplify your fennel bike shedding.
- A declarative package management system (inspired by `use-package`, powered by [Packer.nvim](https://github.com/wbthomason/packer.nvim)). Install packages from anywhere, and pin them to any commit.
- A Space(vim)-esque keybinding scheme, centered around leader and localleader prefix keys (SPC and SPCm).
- Project search (and replace) utilities, powered by ripgrep, and telescope.

However, it also disagrees with some of those ideals
- Packages are not pinned to commits *by default*. Unlike Doom, this configuration includes fewer than 20 packages by default and breaking changes are usually few and far between. Everything is rolling release, and if a breaking change does occur I will push a fix within a day (pinky promise!). Of course, you are free to pin packages yourself (and use [:PackerSnapshot](https://github.com/wbthomason/packer.nvim/pull/370)). 

## Credits

- [David Guevara](https://github.com/datwaft) For getting me into fennel, and for some of his beautiful macros. Without him Nyoom wouldn't exist! 
- [Oliver Caldwell](https://github.com/Olical/) For his excellent work on Aniseed, Conjure, and making fennel feel like a first class language in neovim

## Changelog
v0.3.4 - Released 5/22/2022
- Colorscheme: Automatically changes based on `vim.opt.background`. Light palette added
- Macros: Added event/command macros. Minor adjustments to packer macros
- Bindings: Small fix for normal-mode bindings
- Loading: Fixes to bootstrapping and loading order. Defer lsp/lspinstaller. Add option to use tangerine.nvim
- Lsp: Add nvim-lsp-installer. Cleanups
- Packer: Automatically compile & require compiled file from within pack.fnl. 

v0.3.3 - Released 5/14/2022
- Colorscheme: Rewrite in fennel + macros using nvim api
- Macros: Rewrite of options macros. Replace keymap macros with which-key
- Bindings: Conjure/hotpot/fennel compilation and evalulation. Use Which-Key
- Treesitter/Loading: Fixes for ts-rainbow, telescope.nvim
- Lsp: Add lsp-signature, improve loading and fix fidget
- Packer: Cleaned up implementation

v0.3.2 - Released 5/08/2022
- removed nvim-parinfer and wrote fennel version based on ffi + parinfer-rust. (sidenote: I love how easy neovim makes this, back when I started vim learning enough vimscript to do this was a pipe dream. Long live lua! (and fennel))
- WIP Treesitter + matchparen speed improvements
- Properly configured nvimtree
- Nvim-bufferline integrations 
- Minor defs/fillchars/lsp/cmp updates
- Macro updates and optimizations

v0.3.1 - Released 5/03/2022
- More fennel! ~~Init is rewritten in fennel~~ (update: This caused the config to load after runtime files, which meant that `filetype.lua` stopped working. I've sinced reverted back to `init.lua` for now), and lua dependencies (md5 lib) were removed.
- Refactored the entire config. New directory structure, parted out macros, and cleaned up code. 
- Statusline has been added! Thin and light global statusline to align with nyoom's philosphies.
- Lsp/Cmp configs have been re-done. Lsp is now much quicker to load and is a much cleaner implementation. Cmp is much quicker, integrates properly with copilot, and everything was reworked from the ground up.
- Improved highlighting macros: Highlights are now applied via `nvim_set_hl`.
- Improved startuptime by 20-30ms: decreased lazy loading and optimized code. 
- Improved keybinding support: macros for buffer-local mappings and which-key documentation have been added. Nyoom default bindings are now documented within which-key
- Improved treesitter integration. Textobjects and support for conjure evaluation using treesitter have been added. 

## Showcase

Fast loading and package management using Nyoom! macros and [Packer.nvim](https://github.com/wbthomason/packer.nvim)

<img width="1450" alt="image" src="https://user-images.githubusercontent.com/71196912/166714568-8c2602b9-225b-417c-86bc-235dfbad11f5.png">

Lispy editing using [conjure](https://github.com/Olical/conjure) parinfer-rust

<img width="1388" alt="Screen Shot 2022-05-04 at 10 49 41 AM" src="https://user-images.githubusercontent.com/71196912/166711605-43887f56-eb05-46bc-9a27-95834bb06c32.png">

https://user-images.githubusercontent.com/71196912/153767688-a5e0cfc9-6437-43d1-9205-ed8ca7a07ae5.mov

Lightweight statusline written in fennel

<img width="1450" alt="image" src="https://user-images.githubusercontent.com/71196912/166715211-2abc94ba-6ead-4c06-9bfb-be15063ef069.png">

Note-taking and Getting Things Done with [neorg](https://github.com/nvim-neorg/neorg)

<img width="1450" alt="Screen Shot 2022-05-04 at 10 55 31 AM" src="https://user-images.githubusercontent.com/71196912/166711562-21c2f41d-f956-4baf-9055-59bccb021e43.png">

Syntax highlighting and Error checking with Neovim's builtin LSP, [lspconfig](https://github.com/neovim/nvim-lspconfig), [trouble.nvim](https://github.com/folke/trouble.nvim) and [nvim-treesitter](https://github.com/nvim-treesitter/nvim-treesitter).

![unknown-2](https://user-images.githubusercontent.com/71196912/166711742-2097f80e-0e46-4440-ac6c-edcdd0a19725.png)

<img width="1402" alt="lsp" src="https://user-images.githubusercontent.com/71196912/153767871-f3416e7a-93f9-4842-bfbd-16a3ff7b1d8f.png">

https://user-images.githubusercontent.com/71196912/153768142-b35ac2aa-eb3d-489d-918d-b11870758c8f.mov

Quick Completion and Wildmenu powered by [nvim-cmp](https://github.com/hrsh7th/nvim-cmp) and [copilot.lua](https://github.com/zbirenbaum/copilot.lua)

<img width="573" alt="Screen Shot 2022-05-04 at 11 03 19 AM" src="https://user-images.githubusercontent.com/71196912/166711916-4f25cacb-5219-42b5-9611-1a51f797ff76.png">
<img width="1399" alt="Screen Shot 2022-05-04 at 11 03 05 AM" src="https://user-images.githubusercontent.com/71196912/166711917-0941e87b-f734-416a-a88c-3bfe7aecc159.png">

Pretty notifications and focused editing using [TrueZen](https://github.com/pocco81/truezen.nvim) and [nvim-notify](https://github.com/rcarriga/nvim-notify)

<img width="563" alt="Screen Shot 2022-05-04 at 11 04 52 AM" src="https://user-images.githubusercontent.com/71196912/166712140-1d730080-669b-49a5-8ac1-67e9ceb92610.png">
<img width="1450" alt="Screen Shot 2022-05-04 at 11 04 38 AM" src="https://user-images.githubusercontent.com/71196912/166712142-10b3b286-32b5-4073-a817-700ab03a432a.png">

Informative Keybinds, Native Fuzzy Finding, and fast file history using [nvim-telescope](https://github.com/nvim-telescope/telescope.nvim), [telescope-fzf-native.nvim](https://github.com/nvim-telescope/telescope-fzf-native.nvim), [telescope-frecency.nvim](https://github.com/nvim-telescope/telescope-frecency.nvim), [sqlite.lua](https://github.com/tami5/sqlite.lua) and [which-key.nvim](https://github.com/folke/which-key.nvim)

<img width="1402" alt="image" src="https://user-images.githubusercontent.com/71196912/166712865-f584fb81-4dea-440e-b5bd-b98c0e4fcd71.png">

https://user-images.githubusercontent.com/71196912/153768858-7b3cd304-03ba-4958-b0d6-c743ebb4642b.mov

Themeing with [nvim-base16](https://github.com/RRethy/nvim-base16) and [base16-carbon-dark](https://github.com/shaunsingh/base16-carbon-dark)

<img width="1450" alt="image" src="https://user-images.githubusercontent.com/71196912/166713364-c54fbac6-8165-4d7f-8e7f-f6c492a19b91.png">

Magit-like commits with [Neogit](https://github.com/TimUntersberger/neogit) 

<img width="1450" alt="image" src="https://user-images.githubusercontent.com/71196912/166714003-f87b1ed0-93e3-4d4e-9767-0829d1611016.png">

## Notes

If you have an issue with a plugin in Nyoom.nvim, first you should report it to Nyoom.nvim to see if it's an issue with it. Please don't bother package maintainers with issues that are caused by my configs, and vice versa. I'm new to fennel, so don't hesitate to let me know my lisp-fu sucks! 

