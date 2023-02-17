# Neovim安装
基于技术蛋老师的指导视频。

首先推荐安装0.8以上的版本，因为许多插件只能在这个版本上运行。

windows的init.lua文件在`C:\Users\47140\AppData\Local\nvim`这个位置，而linux的，在用户的home目录下的.config/nvim目录下就可以设置个人的配置了。

并且，因为引入了现代化的编程语言， Neovim的配置可以更加的模块化：例如：
在根目录（ init.lua文件的位置）可以再创建一个lua文件夹，专门用来放lua代码；

Neovim可以智能的识别文件夹：这个lua文件夹中就支持模块化。

接上，在lua文件夹中，创建core文件夹，然后创建options.lua文件，在这个文件里面配置选项功能。

此时把之前写在init里面的内容剪切到 options里面；

```lua
-- Create local var: use keyword "local".
local opt = vim.opt

-- Lines number
opt.relativenumber = true
opt.number = true

-- tab
opt.tabstop = 2
opt.shiftwidth = 2
opt.expandtab = true
opt.autoindent = true

-- Defending wrap
opt.wrap = false

-- highlight current line
opt.cursorline = true

-- mouse on
opt.mouse:append("a")

-- Use system clip board
-- But I don't think I really need it now...
-- opt.clipboard:append("unnameplus")
--
--
-- And lots of people are used to put split to right and below, set it
opt.splitbelow = true
opt.splitright = true

-- When use lower case, means to ignore cases; but if use upper case, means I really need to not ignore it.
opt.ignorecase = true
opt.smartcase = true

-- Appearance
opt.termguicolors = true
opt.signcolumn = "yes"



```

具体这些配置是什么意思都写在这个注释里面了。

因为模块化，所以还需要在init里面配置，用require进行导入就可以了。

```lua
require("core.options")
```

文件夹的分级用点号来分割即可。

然后就是添加熟练的改建了：
在core里面设置keymaps.lua

```lua
-- Set leader as <SPACE>
vim.g.mapleader = " "

local keymap = vim.keymap
--------visual mode-------------
keymap.set("v", "J", ":m '>+1<CR>gv=gv")
keymap.set("v", "K", ":m '<-2<CR>gv=gv")

--------insert mode-------------
keymap.set("i", "jk", "<ESC>")

```

还有两个添加新窗口和设置取消搜索高亮的命令，我没有加入，有需要就加入。

复杂的配置最好交给插件。

现在就来讲怎么添加插件：
首先 lua/plugins，这个文件夹创建好，然后创建一个叫做plugins-setup.lua的文件；

然后去github，好像使用packer来做插件管理器；
复制这段代码
```lua
local ensure_packer = function()
  local fn = vim.fn
  local install_path = fn.stdpath('data')..'/site/pack/packer/start/packer.nvim'
  if fn.empty(fn.glob(install_path)) > 0 then
    fn.system({'git', 'clone', '--depth', '1', 'https://github.com/wbthomason/packer.nvim', install_path})
    vim.cmd [[packadd packer.nvim]]
    return true
  end
  return false
end

local packer_bootstrap = ensure_packer()

return require('packer').startup(function(use)
  use 'wbthomason/packer.nvim'
  -- My plugins here
  -- use 'foo1/bar1.nvim'
  -- use 'foo2/bar2.nvim'

  -- Automatically set up your configuration after cloning packer.nvim
  -- Put this at the end after all plugins
  if packer_bootstrap then
    require('packer').sync()
  end
end)
```

然后这个玩意默认不带自动安装，需要运行命令sync来着，所以加了一段代码来实现我上面说的这个功能：
```lua
vim.cmd([[
  augroup packer_user_config
    autocmd!
    autocmd BufWritePost plugins-setup.lua source <afile> | PackerSync
  augroup end
]])

```

在init里面添加对应的require，然后回到plug的那个文件，:w保存就会自动下载插件了。

要安装新插件的话，在`return require('packer').startup(function(use)
  use 'wbthomason/packer.nvim'`这个位置添加依赖。
简单来说就是use加仓库名。

另外弹幕这里提到可以通过:checkhealth来启动packer自带的环境监测工具，只要你的packer确实安装成功了的话。。。


nvim里面通过colorscheme来设置主题，要设置默认主题的话，设置vim.cmd里面添加对应的代码；

这个推荐在options里面改，降低耦合度；

安装好了，赏心悦目。

现在开始安装状态栏：
有些状态栏不支持主题，所以这里可以安装一个支持的：lualine

安装方式同理，放在那个startup的位置，我之前也应该说了地方；
```lua
use {
  'nvim-lualine/lualine.nvim',
  requires = { 'kyazdani42/nvim-web-devicons', opt = true }
}
```

然后直接保存进行安装。

安装好之后需要进行一点配置：
同样的，对于lualine的配置，可以在plugins文件夹里面创建一个lualine.lua文件；模块化。


另外为了让状态栏和主题也配对，还需要加上option选项

```lua
-- Configuration for "lualine"
require('lualine').setup(
  options = {
    theme = 'tokyonight'
  }
)
```

保存后通过so命令，这样就可以发现状态栏出来了。

nvim也可以安装树形结构目录，这里推荐nvim-tree：
同样的安装方法；

```lua
use {
  'nvim-tree/nvim-tree.lua',
  requires = {
  -- 下面这个图标好像涉及到一些固定的字体还是什么，如果报错记得看看
    'nvim-tree/nvim-web-devicons', -- optional, for file icons
  },
  -- 下面这个是可选的，我的配置文件把它删除了。
  tag = 'nightly' -- optional, updated every week. (see issue #1193)
}
```

和lualine一样，插件需要对应的配置，之后安装其他插件的时候就不啰嗦了。

这里我同意技术蛋老师的说法，这个插件需要的时候使用就可以了，所以没有采用github里面的推荐的配置；

创建同名文件，然后在里面添加：
```lua
-- Default not launch nvim-tree
vim.g.loaded_netrw = 1
vim.g.loaded_netrwPlugin = 1

require("nvim-tree").setup()
```

这里刚刚踩了个坑：每次在 startup里面编写了新的插件之后记得马上保存来同步插件；

另外因为 nvim-tree的命令非常的长，可以在keymap里面添加键盘映射来快捷打开；

```lua
keymap.set("n", "<leader>e", ":NvimTreeToggle<CR>")
```

另外在写这个配置文件的时候要细心一点，出了这些报错的话比较麻烦。

另外，你可能发现： 在文档树和代码编辑区之间切换并不方便，此时就需要使用 vim-tmux-navigator这款插件；

老样子，在 setup那里添加插件即可；
此时就可以通过ctrl+hjkl来方便快捷的切换窗口了，这也是通用的，切换窗口的快捷键。
这个插件也适用于tmux（虽然我不知道这个东西到底是什么）

继续安装实用的插件
```lua
use "nvim-treesitter/nvim-treesitter" -- Syntax highlight
use "p00f/nvim-ts-rainbow" -- Different colors in each brackets
```

提醒一下，这里的彩虹括号插件依赖于treesitter，所以第一安装的时候会失败（仅这个插件），然后再试一次就可以成功了。

然后这个 treesitter插件也是需要配置的，同样的道理。
并且在文档树安装完成后，可以直接在对应的位置按a(ppend)，此时光标就会转移到底下的命令栏位置，输入新的文件名就可以了。

这里的话，我会把技术蛋老师提到的语言的设置记录下来，并且因为我使用的语言是csharp，所以这里我也会记录这个语言。
```lua
require'nvim-treesitter.configs'.setup {
  -- Add different language
  ensure_installed = { "vim", "help", "bash", "c", "cpp", "javascript", "json", "lua", "python","c_sharp"}

  highlight = { enable = true },
  indent = { enable = true },

  -- Rainbow brackets
  rainbow = {
    enable = true,
    extended_mode = true,
    max_file_lines = nil,
  }
}
```

目前位置，一个具有完整功能的编辑器已经基本诞生了。 但是还少了一个最重要的东西....

外网一般把这个东西成为lsp，我的话直接理解成代码补全的东西。
这个东西涉及的知识点非常的多，所以现在技术蛋老师怎么说，我就怎么做，不恋战。

配置lsp需要三个插件，先把他们安装好：
```lua
  use {
    "williamboman/mason.nvim",
    "williamboman/mason-lspconfig.nvim", -- 这个相当于上面那个和下面那个之间的桥梁
    "neovim/nvim-lspconfig"
  }
```

需要特别说明的是这个mason插件是用来统一管理 lsp服务的，里面涉及的知识点非常的多，

然后主要是在lsp.lua文件中配置与 lsp相关的配置。

```lua
-- lsp.lua
require("mason").setup({
  ui = {
      icons = {
          package_installed = "✓",
          package_pending = "➜",
          package_uninstalled = "✗"
      }
  }
})
require("mason-lspconfig").setup({
  -- 确保安装，根据需要填写
  ensure_installed = {
    "lua_ls",
  },
})
```

在Config的那个位置，暂时只安装了 lua的相关配置，有csharp的配置的话我也会考虑加进去；

然后在init里面进行引入；
并且安装lsp的话，最好把自动补全也引入进来，这样在编辑方面的体验就和vscode差不多了；

这里的话需要的自动化的配置似乎比较多... 我很担心我不能安装好 csharp的自动补全； 其实主流语言应该都有的，只不过问题是...


算了先不管那些，在对应的位置安装代码片段的插件：
```lua
  -- 自动补全
  use "hrsh7th/nvim-cmp"
  use "hrsh7th/cmp-nvim-lsp"
  use "L3MON4D3/LuaSnip" -- snippets引擎，不装这个自动补全会出问题
  use "saadparwaiz1/cmp_luasnip"
  use "rafamadriz/friendly-snippets"
  use "hrsh7th/cmp-path" -- 文件路径
```

然后对应的创建一个cmp.lua来管理与自动补全有关的配置；这里的配置也是比较复杂；所以先是只把代码呈现上来，然后再说什么考虑细节或者个性化；

```lua
----- cmp.lua ------
local cmp_status_ok, cmp = pcall(require, "cmp")
if not cmp_status_ok then
  return
end

local snip_status_ok, luasnip = pcall(require, "luasnip")
if not snip_status_ok then
  return
end

require("luasnip.loaders.from_vscode").lazy_load()

-- 下面会用到这个函数
local check_backspace = function()
  local col = vim.fn.col "." - 1
  return col == 0 or vim.fn.getline("."):sub(col, col):match "%s"
end


cmp.setup({
  snippet = {
    expand = function(args)
      require('luasnip').lsp_expand(args.body)
    end,
  },
  mapping = cmp.mapping.preset.insert({
    ['<C-b>'] = cmp.mapping.scroll_docs(-4),
    ['<C-f>'] = cmp.mapping.scroll_docs(4),
    ['<C-e>'] = cmp.mapping.abort(),  -- 取消补全，esc也可以退出
    ['<CR>'] = cmp.mapping.confirm({ select = true }),

    ["<Tab>"] = cmp.mapping(function(fallback)
      if cmp.visible() then
        cmp.select_next_item()
      elseif luasnip.expandable() then
        luasnip.expand()
      elseif luasnip.expand_or_jumpable() then
        luasnip.expand_or_jump()
      elseif check_backspace() then
        fallback()
      else
        fallback()
      end
    end, {
      "i",
      "s",
    }),

    ["<S-Tab>"] = cmp.mapping(function(fallback)
      if cmp.visible() then
        cmp.select_prev_item()
      elseif luasnip.jumpable(-1) then
        luasnip.jump(-1)
      else
        fallback()
      end
    end, {
      "i",
      "s",
    }),
  }),

  -- 这里重要
  sources = cmp.config.sources({
    { name = 'nvim_lsp' },
    { name = 'luasnip' },
    { name = 'path' },
  }, {
    { name = 'buffer' },
  })
})
```

这里lsp的安装似乎要花一点时间，我的是安装成功了，可以正常出现代码提示。现在我需要花一点时间去熟悉他。

就是这个代码片段的问题，好像可以通过快捷键来进行跳转的。
tab和shift tab可以跳转

并且当出现自动补全的提示框，里面的信息比较多的时候，可以用C-f和C-b来上下移动信息。

要取消补全按C-e就可以了。

此时功能基本就和vscode就一致了。

如果要添加更多的补全插件，记得在cmp的source位置来进行引用
```lua
------ cmp ---------
  -- 这里重要
  -- Seems like here is where to add new code_snippets;
  sources = cmp.config.sources({
    { name = 'nvim_lsp' },
    { name = 'luasnip' },
    { name = 'path' },
  }, {
    { name = 'buffer' },
  })
})

```

然后需要回到lsp文件，需要在最底下加上下面的代码，让lsp和代码补全相结合：
```lua
-- 这里好像还和之前用到的和具体的语言有关的插件有联系；
local capabilities = require('cmp_nvim_lsp').default_capabilities()

-- 比如这里就是 什么什么neko，这里同步换成 lua_ls了
require("lspconfig").lua_ls.setup {
  capabilities = capabilities,
}
```

然后此时重启 Neovim，这是下边可能会显示安装信息；

然后进入vim的命令模式，输入Mason，可以进入lsp（由Mason进行管理）的管理页面，就可以看到它提示某个东西已经安装好了。

如果你需要安装其他语言的lsp服务，可以直接搜索这门语言；

然后按i（stall）就可以安装新的lsp服务了。 记住Mason。


因为前面的东西都是以lua进行的距离，所以随便回到一个lua文件，就可以发现语法提示已经起作用了。

如果没有起作用注意检查treesitter、lsp和cmp这几个东西。 主要是这几个。
+ [ ] 我这里lsp仍然没有生效。 看样子似乎是treesitter这个插件因为环境的问题失败了。 说实话我已经配置很长的时间了。 现在却没有成功。 

关于这个问题， 作者（提出这个问题的人，不能在windows上运行成功的人）在issue上回复在linux上运行正常。

我想我可能发现问题在哪了。。。可是怎么解决呢？
错误提示的是 .so不是有效的，而这个其实是linux的动态链接库文件。
如果要在windows上可用的话需要把对应的.so替换成.dll。 这里有一个办法时进行交叉编译，不过...
这个是我的想法。 可以看看github里面是怎么说的。





现在在neovim里面进行注释有点不方便，所以可以通过两个新的插件进行安装：
```lua
  use "numToStr/Comment.nvim" -- gcc和gc注释
  use "windwp/nvim-autopairs" -- 自动补全括号
```

同样的:w进行保存。

接着创建comment.lua和autopairs.lua这两个文件来进行配置。

