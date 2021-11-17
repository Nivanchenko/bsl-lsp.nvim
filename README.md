# bsl-lsp.nvim
Lsp-config extension, that make bsl and oscript lsp server work in neovim

Данный плагин расширяет lspconfig и позволяет запускать и подключаться к bsl-language-server (https://github.com/1c-syntax/bsl-language-server)

На данный момент уже не актуально, мой pr приняли в репо Lsp-config. Теперь он из коробки подерживает bsl ls. 

Подключить можно вот так:

```lua
local util = require 'lspconfig/util'

-- Любым удобным способом указываем путь к бинарю
local bslbinary = vim.fn.stdpath('data') .. "/bsl-language-server/bin/bsl-language-server"

-- Подключаем модуль lsp конфига, и запускаем анализатор bsl
require'lspconfig'.bsl_ls.setup {
    root_dir = function(fname)
                   return util.find_git_ancestor(fname) or util.path.dirname(fname)
               end,
    -- Определяем команду, которой запускается lsp 
    cmd = {bslbinary, "lsp"}
}
```

## Использование

Скачиваем бинарник bsl-language-server, и кладем в какое-нибудь комфортное место

Если вы знакомы с установкой плагинов и настройкой lsp-клиентов. то вот краткий гайд, как настраивать

```lua
USER = vim.fn.expand('$USER')
-- Путь до бинаря
local bslbinary = "/home/" .. USER .. "/.config/nvim/bsl-language-server/bin/bsl-language-server"
require'lspconfig'.bsllsp.setup {
-- Команда запуска лсп сервера
      cmd = {bslbinary, "lsp"}
}
```

Более подробно, если для кого-то это в новинку. 

Допустим конфиг неовима, находится вот тут **~/.config/nvim/init.vim**

Дополним его, добавив текущий рантайм, если этого не сделано

```vim
set rtp+=~/.config/nvim
```

Подключим модуль, который мы далее добавим. Назовем его например **main**

```vim
lua require('main')
```

Теперь добавим каталог **lua/** и разместим в нем **main.lua** 

```lua
local cmd = vim.cmd  -- to execute Vim commands e.g. cmd('pwd')
local fn = vim.fn    -- to call Vim functions e.g. fn.bufnr()
local g = vim.g      -- a table to access global variables
local opt = vim.opt  -- to set options
local execute = vim.api.nvim_command

-- Установка менеджера плагинов
local install_path = fn.stdpath('data')..'/site/pack/packer/opt/packer.nvim'

if fn.empty(fn.glob(install_path)) > 0 then
  execute('!git clone https://github.com/wbthomason/packer.nvim '..install_path)
end
vim.cmd [[packadd packer.nvim]]
vim.cmd 'autocmd BufWritePost plugins.lua PackerCompile' -- Auto compile when there are changes in plugins.lua

-- Ининциализация плагинов
require('packer').startup(function()
	use {'wbthomason/packer.nvim', opt = true}
	use { 'neovim/nvim-lspconfig' }
	use { 'Nivanchenko/bsl-lsp.nvim' }
end)

-- Непосредственно подключение к bsl lsp
require('bsl-lsp')
USER = vim.fn.expand('$USER')
-- Любым удомным способом указываем путь к бинарю
local bslbinary = "/home/" .. USER .. "/.config/nvim/bsl-language-server/bin/bsl-language-server"
require'lspconfig'.bsllsp.setup {
-- Определяем команду, которой запускается lsp 
      cmd = {bslbinary, "lsp"}
}
```

Перезапускаем неовим и вызываем команду
```vim
:PackerInstall
```

Снова перезапускаем, теперь для файлов с расширением .bsl и .os будет работать линтер.

Форматирование по команде
```vim
:lua vim.lsp.buf.formatting()
```

Если 1с и vim  для вас не пустой звук. Можно заглянуть вот сюда https://github.com/ava57r/vim-language-1c-bsl

Для полного удовлетворения можно используя плагин **nvim-telescope/telescope.nvim** вызывать разные команды lsp сервера и получать результат.

![telescope_example](https://user-images.githubusercontent.com/36507839/126916579-9770d138-513a-41a3-9b72-459e3041a4cc.gif)
