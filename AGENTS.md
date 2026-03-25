# AGENTS.md

Neovim configuration based on [LazyVim](https://github.com/LazyVim/LazyVim) starter template.

## Structure

```
init.lua                  # Entry point — bootstraps lazy.nvim and loads config/lazy.lua
lua/
  config/
    lazy.lua              # Plugin specs, extras, and lazy.nvim settings
    options.lua           # vim options and global vars (currently holds PHP LSP choice + buffer keymaps)
    keymaps.lua           # Custom keymaps (loaded on VeryLazy)
    autocmds.lua          # Custom autocmds (loaded on VeryLazy)
  plugins/
    *.lua                 # One file per plugin or logical group; each returns a lazy.nvim spec table
```

## Adding / Modifying Plugins

Create or edit a file under `lua/plugins/`. Each file must return a lazy.nvim spec:

```lua
return {
  "author/plugin-name",
  dependencies = { ... },
  opts = { ... },           -- merged with defaults via tbl_deep_extend
  config = function(_, opts)
    require("plugin").setup(opts)
  end,
}
```

- To override a LazyVim default plugin, return a spec with the same plugin name and supply `opts` or `keys`.
- To disable a LazyVim plugin: `{ "author/plugin", enabled = false }`.
- Use `opts = function(_, opts) ... return opts end` when you need to extend (not replace) list fields.

## Enabling LazyVim Extras

Add lines to the `spec` table in `lua/config/lazy.lua`:

```lua
{ import = "lazyvim.plugins.extras.<category>.<name>" },
```

Currently enabled extras: json, php, rust, toml, markdown, tailwind, typescript, vue, yaml, dot, supermaven, mini-comment, mini-surround, fzf, leap, black, prettier, project.

## Formatting

**StyLua** is used for Lua formatting (`stylua.toml`):
- Indent: 2 spaces
- Column width: 120

Format a file: `stylua <file>.lua`  
Format all: `stylua lua/`

## LSP / Language Config

- PHP LSP is set to **intelephense** via `vim.g.lazyvim_php_lsp` in `lua/config/options.lua`.
- LSP servers are managed by **Mason** (auto-installed).
- To add an LSP server, override `nvim-lspconfig` opts in a plugin file:
  ```lua
  { "neovim/nvim-lspconfig", opts = { servers = { myserver = {} } } }
  ```

## Custom Keymaps

Defined in `lua/config/options.lua` (global) and `lua/config/keymaps.lua` (VeryLazy):

| Key | Mode | Action |
|-----|------|--------|
| `<leader>q` | n | Close buffer (`:bd`) |
| `<leader>Q` | n | Force close buffer (`:bd!`) |
| `-` | n | Open parent directory via oil.nvim |

## Conventions

- Plugin files are named after the plugin (e.g., `oil.lua` for `stevearc/oil.nvim`).
- The `example.lua` file has `if true then return {} end` at the top — it is intentionally inert and serves as a reference/template. Keep it that way.
- `lua/config/keymaps.lua` and `lua/config/autocmds.lua` are intentionally sparse — LazyVim's defaults are loaded automatically; only add overrides or additions here.
- `lazyvim.json` tracks installed version and extras state — do not edit manually.
- `lazy-lock.json` is the lockfile — commit changes to it when updating plugins.

## Gotchas

- `opts` tables are **deep-merged** by lazy.nvim; list fields (arrays) are **overwritten**, not extended. Use `opts = function(_, opts) vim.list_extend(opts.some_list, {...}) end` to append to lists.
- `version = false` in `lazy.lua` means all plugins track latest git commit, not semver tags.
- Plugin update checker runs periodically but notifications are disabled (`notify = false`).
- `lua/config/options.lua` currently contains both vim options AND some keymaps — this is intentional per the current setup.
