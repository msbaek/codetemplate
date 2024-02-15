# Astronvim

## surround

- [delete ar*ound me!] ds] delete around me!
- remove <b>HTML t\*ags</b> dst remove HTML tags
- 'change quot\*es' cs'" "change quotes"
- <b>or tag\* types</b> csth1<CR> <h1>or tag types</h1>
- delete(functi\*on calls) dsf function calls

## move tabs in nerdtree

- `<`, `>`

## Fuzzy finding with Telescope

- find files: `<leader>+f+f`
- fild histories: `<leader>+f+o`
- find words: `<leader>+f+w`
- find for word under caret: `<leader>+f+c`

### Navigate buffers

- Move Buffer Right `>b`
- Move Buffer Left `<b`

### Manage Split

- Horizontal Split: `leader` + \
- Vertical Split: `leader` + |
- close split: leader + q

## terminal

- toggle: `F7`
- Floating Terminal `Leader + tf`
- Horizontal Split Terminal `Leader + th`
- Node: `Leader + tn`
- Python: `Leader + tp`

## Better Escape

`jj`,`jk`

## Telescope Mappings

- Buffers: `Leader + fb`
- Word at cursor `Leader + fc`
- Keymaps `Leader + fk`
- Colorschemes `Leader + ft`
- Live Grep `Leader + fw`
- Git Branches `Leader + gb`
- Git Commits `Leader + gc`
- Git Status `Leader + gt`

## Git Mappings

- status: `Leader`+`gt`
- Next Hunk `]g`
- Previous Hunk `[g`
- Blame Line `Leader + gl`
- Stage Hunk `Leader + gs`
- Stage Buffer `Leader + gS`
- Unstage Hunk `Leader + gu`
- Git Diff `Leader + gd`

---

## update

`:AstroUpdate`(`<leader>pA`)
`:AstroUpdatePackages`(`<leader>pa`)

## Package Management

- AstroNvim Packages Update: `Leader + pa`
- AstroNvim Updater: `Leader + pA`
- AstroNvim Changelog: `Leader + pl`
- AstroNvim Version: `Leader + pv`
- Mason Installer: `Leader + pm`
- Mason Updater: `Leader + pM`
- Plugins Install: `Leader + pi`
- Plugins Status: `Leader + ps`
- Plugins Sync: `Leader + pS`
- Plugins Check for Updates: `Leader + pu`
- Plugins Update: `Leader + pU`

## commands

:TodoQuickFix
:colorscheme = tokyonight

## Buffer

- new buffer: `<leader> n`
- close buffer: `<leader> c`

## neotree: `<leader>e`

- help: ?
- H: toggle hidden
- a: add file
- r: rename
- d: delete
- `l`: file open

### lua/astronvim/mappings.lua

```
maps.n["]b"] = maps.n["<Tab>"] =
{ function() require("astronvim.utils.buffer").nav(vim.v.count > 0 and vim.v.coun { function() require("
astronvim.utils.buffer").nav(vim.v.count > 0 and vim.v.count
t or 1) end, desc = "Next buffer" } or 1) end, desc = "Next buffer" }
maps.n["[b"] = { maps.n["<S-Tab>"] = {
function() require("astronvim.utils.buffer").nav(-(vim.v.count > 0 and vim.v.coun function() require("
astronvim.utils.buffer").nav(-(vim.v.count > 0 and vim.v.coun
t or 1)) end, t or 1)) end,
desc = "Previous buffer", desc = "Previous buffer",
```