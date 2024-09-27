# 前言
在使用 `nvim` 的过程中，我们往往需要写一些文档性质的文件，而很多文档性质的文件需要中英文混合输入，
而如果我们使用系统的输入法进行操作会面临频繁切换中英文输入模式，或者需要频繁地使用回车或者上档键将
输入法中的英文字符上屏。

如果可以直接在 `nvim` 中使用输入法，那么将会大幅度提升我们的体验。在完成了相关的设置之后，当我使用
这套配置写下这篇文章的时候，总体的体验还是非常不错的。

本文将会介绍如何使用 `nvim-cmp + rime-ls` 完成 `nvim` 中实现中英文混合输入，以及过程中遇到的问题和解决
思路。

NOTE：目前我使用的这一套配置是我体验的很多配置中最好的，最符合日常输入法使用习惯的。

# 安装 `rime` 输入法
首先需要安装 `rime` 输入法，我使用的是 `ubuntu` ，因此我使用以下的命令进行安装：

```bash
sudo apt-get install -y ibus-rime
```

其他系统的安装方法可以参考 `rime` 的官方文档，这里不进行赘述。

由于我平时使用的是小鹤双拼输入法，因此我需要安装小鹤双拼的配置文件，可以使用以下的命令进行安装：

```bash
sudo apt-get install -y librime-data-double-pinyin
```

# 安装 `rime-ls`
`rime-ls` 是一个 `rime` 的后台服务，可以帮助我们在 `nvim` 中使用 `rime` 输入法。我们可以使用以下的命令进行安装：

```bash
sudo apt-get install -y clang librime-dev cargo
cd || exit 1
git clone https://github.com/wlh320/rime-ls.git
cd - || exit 1
cd ~/rime-ls || exit 1
cargo build --release || exit 1
cd - || exit 1
```

其中安装的 `clang, librime-dev, cargo` 是安装 `rime-ls` 所需要的依赖。上面的代码会将 `rime-ls` 安装
在家目录下，如果需要更改可以自行调整。

NOTE：因为使用的是 `nvim-cmp + rime-ls` 所以当然需要安装 `nvim-cmp` 了。除此之外，还需要
安装 `lspconfig` 用于配置 `rime-ls`。既然安装了 `nvim-cmp` 和 `lspconfig` 那么 `cmp-nvim-lsp` 也是需要
安装的。由于这几个是非常常用的插件，这里不详细介绍如何安装。

# `rime-ls` 配置
如果你查看了 `rime-ls` 的官方文档，你会获得一份完成了大部分设置的配置。但是在使用官方文档中的配置的时候，
我遇到了许多困难，这里首先给出你能从官方文档中得到的代码：

```lua
local M = {}
-- 设置哪些文件开启 `rime-ls`
local rime_ls_filetypes = { 'markdown', 'vimwiki', 'copilot-chat' }

function M.setup_rime()
    vim.g.rime_enabled = true

    -- 修改 `lualine` 的样式，如果没有使用 `lualine` 可以不用这一段
    local function rime_status()
        if vim.g.rime_enabled then
            return 'ㄓ'
        else
            return ''
        end
    end
    require('lualine').setup({
        sections = {
            lualine_x = { rime_status, 'copilot', 'encoding', 'fileformat', 'filetype' },
        }
    })

    local lspconfig = require('lspconfig')
    local configs = require('lspconfig.configs')
    if not configs.rime_ls then
        configs.rime_ls = {
            default_config = {
                name = "rime_ls",
                -- 这里放置 `rime-ls` 的路径
                cmd = { vim.fn.expand'~/rime-ls/target/release/rime_ls' },
                filetypes = rime_ls_filetypes,
                single_file_support = true,
            },
            settings = {},
            docs = {
                description = [[
https://www.github.com/wlh320/rime-ls

A language server for librime
]],
            }
        }
    end

    local rime_on_attach = function(client, _)
        local toggle_rime = function()
            client.request('workspace/executeCommand',
                { command = "rime-ls.toggle-rime" },
                function(_, result, ctx, _)
                    if ctx.client_id == client.id then
                        vim.g.rime_enabled = result
                    end
                    -- 关闭或者开启的时候重新触发一次补全
                    if cmp.visible() then
                        if not vim.g.rime_enabled then
                            cmp.close()
                        end
                        cmp.complete()
                    end
                end
            )
        end
        -- 通过 `<c-space>` 来切换 `rime-ls` 的开关
        vim.keymap.set({ 'i', 'n' }, '<c-space>', toggle_rime)
    end

    local capabilities = vim.lsp.protocol.make_client_capabilities()
    -- `cmp-nvim-lsp` 支持额外的功能，如果没有这个插件，这一行可以删除。
    capabilities = require('cmp_nvim_lsp').default_capabilities(capabilities)

    lspconfig.rime_ls.setup {
        init_options = {
            enabled = vim.g.rime_enabled,
            -- `rime` 安装后方案所在目录
            shared_data_dir = "/usr/share/rime-data",
            -- `rime-ls` 的配置目录
            user_data_dir = vim.fn.expand"~/.local/share/rime-ls",
            log_dir = vim.fn.expand"~/.local/share/rime-ls",
            paging_characters = {"-", "="},
            trigger_characters = {},
            schema_trigger_character = "&"
        },
        on_attach = rime_on_attach,
        capabilities = capabilities,
    }
end
return M
```

上面的代码的使用方法很简单，只需要将内容拷贝到一个文件中，然后在你的 `init.lua` 中调用
`require'filename'.setup_rime()` 即可。

这里讲一下如何配置 `rime-ls`。在上面的代码中，存在一个
`user_data_dir = vim.fn.expand"~/.local/share/rime-ls"` 因此配置文件需要放置在这个目录下，而配置方法
与配置 `rime` 输入法是一致的，例如下面是我添加的一些简单配置：

![](https://raw.githubusercontent.com/Kaiser-Yang/image-hosting-site/main/20240421-20250421/20240808220342.png){: .img-fluid}

如果需要切换输入方案只需要打开一个启动了 `rime-ls` 的文件，然后输入 `&` 并手动触发一次 `cmp.complete()` 即可
(默认的按键是 `<c-n>`) 。通过数字键选择对应的方案即可，选择后需要确认补全。

# `auto-pairs` 与空格自动上屏
官方文档中还提供了设置空格自动选择第一个的方法，虽然可以使用但是我在使用的过程中遇到了一些问题：
由于我使用了 `jiangmiao/auto-pairs` 这一款插件，这款插件重新绑定了空格键，因此按照官方的方法设置空格键
的时候，在正常使用空格的时候，命令行中会一直出现 `=AutoPairsSpace()` 这段文字，这样会影响体验。
因此采用了以下的方式解决：
1. 首先关闭 `jiangmiao/auto-pairs` 的空格键绑定，这个通过 `vim.g.AutoPairsMapSpace = 0` 可以关闭。
2. 将一个一直不会使用的按键绑定到 `AutoPairsSpace` 上，这里我选择了 `<f30>` 这个按键。
3. 将空格绑定到 `<f30>` 这个按键上，启用递归绑定。
4. 使用 `autocmd` 为启用了 `rime-ls` 的文件重新绑定空格以实现空格上屏的操作，在不能上屏的时候通过触发
`<f30>` 来达到静默调用的目的。

那么我们可以写出以下的代码：

```lua
-- 这行代码需要在初始化 `jiangmiao/auto-pairs` 之前执行
vim.g.AutoPairsMapSpace = 0
map.set({ 'i' }, '<f30>', '<c-]><c-r>=AutoPairsSpace()<cr>', { remap = false, silent = true })
map.set({ 'i' }, '<space>', '<f30>', { remap = true, silent = true })
local cmp = require("cmp")
local function is_rime_entry(entry)
  return vim.tbl_get(entry, "source", "name") == "nvim_lsp"
    and vim.tbl_get(entry, "source", "source", "client", "name") == "rime_ls"
end
vim.api.nvim_create_autocmd('FileType', {
    pattern = rime_ls_filetypes,
    callback = function ()
        vim.keymap.set({ 'i', 's' }, '<space>', function()
            if not vim.g.rime_enabled then
                vim.api.nvim_feedkeys(vim.api.nvim_replace_termcodes('<f30>', true, true, true),
                    'm', false)
            else
                local entry = cmp.get_selected_entry()
                if entry ~= nil then
                    vim.api.nvim_feedkeys(vim.api.nvim_replace_termcodes('<f30>', true, true, true),
                        'm', false)
                    return
                end
                entry = cmp.core.view:get_first_entry()
                if is_rime_entry(entry) then
                    cmp.confirm({
                        behavior = cmp.ConfirmBehavior.Replace,
                        select = true,
                    })
                else
                    vim.api.nvim_feedkeys(vim.api.nvim_replace_termcodes('<f30>', true, true, true),
                        'm', false)
                end
            end
        end, {
            noremap = true,
            silent = true,
            buffer = true
        })
    end
})
```

# 数字键自动上屏
在 `rime-ls` 的官方仓库中的 `issues` 部分提到了数字键不能自动上屏，在按了数字键之后还需要一次空格
(需要绑定空格自动上屏) 操作才能进行上屏的操作。这里参考了 `issues` 中的
[解决方案](https://github.com/TwIStOy/dotvim/blob/df9e00b6376c62a285b8c8fe37e61c4ffed251e8/lua/dotvim/pkgs/extra/misc/rime.lua#L46-L87)
，这个方案在输入的时候会有明显的卡顿，因为其每次输入一个字符都会去检查是否需要触发自动上屏，我对其方案
进行了一定的改进，通过自动命令只为数字键绑定了上屏操作，这样操作后使用基本不会感觉到卡顿：

```lua
local cmp = require("cmp")
local function is_rime_entry(entry)
  return entry ~= nil and vim.tbl_get(entry, "source", "name") == "nvim_lsp"
    and vim.tbl_get(entry, "source", "source", "client", "name") == "rime_ls"
end
local function auto_upload_rime()
    if not cmp.visible() then
        return
    end
    local entries = cmp.core.view:get_entries()
    if entries == nil or #entries == 0 then
        return
    end
    local first_entry = cmp.get_selected_entry()
    if first_entry == nil then
        first_entry = cmp.core.view:get_first_entry()
    end
    if first_entry ~= nil and is_rime_entry(first_entry) then
        local rime_ls_entry_occur = false
        for _, entry in ipairs(entries) do
            if is_rime_entry(entry) then
                if rime_ls_entry_occur then
                    return
                end
                rime_ls_entry_occur= true
            end
        end
        if rime_ls_entry_occur then
            cmp.confirm {
                behavior = cmp.ConfirmBehavior.Insert,
                select = true,
            }
        end
    end
end
vim.api.nvim_create_autocmd('FileType', {
    pattern = rime_ls_filetypes,
    callback = function ()
        for numkey = 1, 9 do
            local numkey_str = tostring(numkey)
            vim.keymap.set({ 'i', 's' }, numkey_str, function()
                local visible = cmp.visible()
                vim.fn.feedkeys(numkey_str, 'n')
                -- 使用 `vim.schedule` 可以保证 `auto_upload_rime` 在 `feedkeys` 之后执行
                if visible then
                    vim.schedule(auto_upload_rime)
                end
            end, {
                noremap = true,
                silent = true,
                buffer = true,
            })
        end
    end
})
```

# 中文标点
中文标点的使用一直是一个痛点，而如果使用 `rime-ls` 进行标点的频繁切换的话又会影响工作的效率，为了解决
这个问题，我想到了一个简单的方法。这个方法的灵感来源于我平常写英文文档的习惯。众所周知，在书写英文的时候
如果输入了一个标点符号后，我们是需要输入一个空格与下一个英文隔开的，因为英文的标点只占用一个宽度，如果
不隔开会导致非常的拥挤。正是因为这个习惯我想到了可以在 `rime-ls` 启动的时候通过标点加空格的形式插入
中文标点，而在输入法关闭的时候，标点加空格则是正常的添加英文标点与空格。我于是有了以下的代码
(如果使用这种方式的话，需要将 `rime-ls` 标点输入设置为英文标点，可以通过方案选择的方式设置)：

```lua
local punc_en = { ',', '.', ':', ';', '?', '\\' }
local punc_zh = { '，', '。', '：', '；', '？', '、' }
vim.api.nvim_create_autocmd('FileType', {
    pattern = rime_ls_filetypes,
    callback = function ()
        for i = 1, #punc_en do
            local src = punc_en[i] .. '<space>'
            local dst = 'rime_enabled ? "' .. punc_zh[i] .. '" : "' .. punc_en[i] .. ' "'
            vim.keymap.set({ 'i', 's' }, src, dst, {
                noremap = true,
                silent = false,
                expr = true,
                buffer = true,
            })
        end
    end
})
```

如果需要更多的中文标点只需要更改 `punc_en` 和 `punc_zh` 的内容即可。

# `copilot-chat` 与 `rime-ls`
在我完成了所有配置后，我发现在 `copilot-chat` 的窗口中不能在触发 `rime-ls` 的补全，在我提了 `issue`
后我根据网友的回答得到了以下的代码：

```lua
vim.api.nvim_create_autocmd('FileType', {
    pattern = rime_ls_filetypes,
    callback = function (env)
        local rime_ls_client = vim.lsp.get_clients({ name = 'rime_ls' })
        -- 如果没有启动 `rime-ls` 就手动启动
        if #rime_ls_client == 0 then
            vim.cmd('LspStart rime_ls')
            rime_ls_client = vim.lsp.get_clients({ name = 'rime_ls' })
        end
        -- `attach` 到 `buffer`
        if #rime_ls_client > 0 then
            vim.lsp.buf_attach_client(env.buf, rime_ls_client[1].id)
        end
    end
})
```

我估计这个不能够自动启动的原因是 `copilot-chat` 窗口并不是一个有效的 `buffer`，而通过上面的方式启动后
会创建一个新的 `buffer` 这个 `buffer` 与 `copilot-chat` 中的内容相同。

# 最后
我们将上面所有的配置整合起来便可以得到一个完整的文件：
[rime-ls.lua](https://github.com/Kaiser-Yang/dotfiles/blob/main/.config/nvim/lua/rime-ls.lua)

NOTE：本文出现的所有代码没有全部放置在这个文件中。空格的重新绑定放置在
[key_mapping.lua](https://github.com/Kaiser-Yang/dotfiles/blob/main/.config/nvim/lua/key_mapping.lua) 中，
`auto-pairs` 的变量设置放置在
[autopairs_config.lua](https://github.com/Kaiser-Yang/dotfiles/blob/main/.config/nvim/lua/plugin_config/autopairs_config.lua)
中，而 `require'rime-ls'.setup_rime()` 则放置在了
[nvimlspconfig_config.lua](https://github.com/Kaiser-Yang/dotfiles/blob/main/.config/nvim/lua/plugin_config/nvimlspconfig_config.lua)
中。
