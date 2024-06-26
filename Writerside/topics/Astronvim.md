# Astronvim

## Most Useful

| Action              | Mappings    |
| ------------------- | ----------- |
| **Comment**         | Leader + /  |
| **Format Document** | Leader + lf |
| Symbols Outline     | Leader + lS |
| Document Symbols    | Leader + ls |
| Save Session        | Leader + Ss |

## 📦 Setup

- [Getting Started](https://docs.astronvim.com/)
  ```
  git clone --depth 1 https://github.com/AstroNvim/template ~/.config/nvim
  # remove template's git connection to set up your own later
  rm -rf ~/.config/nvim/.git
  nvim
  ```
- Install LSP
  - Enter `:LspInstall` followed by the name of the server you want to install
  - Example: `:LspInstall pyright`
- Install language parser
  - Enter `:TSInstall` followed by the name of the language you want to install
  - Example: `:TSInstall python`

## General Mappings

| Action           | Mappings   |
| ---------------- | ---------- |
| Comment          | Leader + / |
| Horizontal Split | \          |
| Vertical Split   | \|         |

## Buffers

| Action                               | Mappings     |
| ------------------------------------ | ------------ |
| Move Buffer Right                    | >b           |
| Move Buffer Left                     | <b           |
| Close all buffers except the current | Leader + bc  |
| Close all buffers                    | Leader + bC  |
| Sort buffers by extension            | Leader + bse |
| Sort buffers by buffer number        | Leader + bsi |
| Sort buffers by last modification    | Leader + bsm |
| Sort buffers by full path            | Leader + bsp |
| Sort buffers by relative path        | Leader + bsr |

## Better Escape

| Action     | Mappings |
| ---------- | -------- |
| Escape key | jj, jk   |

## Neo-Tree

| Action                | Mappings   |
| --------------------- | ---------- |
| help                  | ?          |
| Neotree toggle        | Leader + e |
| Neotree focus         | Leader + o |
| toggle hidden         | H          |
| file open             | `l`        |
| move tabs in nerdtree | `<`, `>`   |

## Session Manager Mappings

| Action                         | Mappings    |
| ------------------------------ | ----------- |
| Save Session                   | Leader + Ss |
| Last Session                   | Leader + Sl |
| Delete Session                 | Leader + Sd |
| Search Sessions                | Leader + Sf |
| Load Current Directory Session | Leader + S. |

## Package Management Mappings

| Action                    | Mappings    |
| ------------------------- | ----------- |
| AstroNvim Packages Update | Leader + pa |
| AstroNvim Updater         | Leader + pA |
| Mason Installer           | Leader + pm |
| Mason Updater             | Leader + pM |
| Plugins Install           | Leader + pi |
| Plugins Update            | Leader + pU |

## LSP Mappings

| Action           | Mappings    |
| ---------------- | ----------- |
| Format Document  | Leader + lf |
| Symbols Outline  | Leader + lS |
| Document Symbols | Leader + ls |
| Declaration      | gD          |
| Definition       | gd          |

## Telescope Mappings

| Action                            | Mappings    |
| --------------------------------- | ----------- |
| Live Grep                         | Leader + fw |
| Find files                        | Leader + ff |
| Word at cursor                    | Leader + fc |
| Keymaps                           | Leader + fk |
| Man Pages                         | Leader + fm |
| Notifications                     | Leader + fn |
| Old Files                         | Leader + fo |
| Marks                             | Leader + f' |
| Buffers                           | Leader + fb |
| Commands                          | Leader + fC |
| Find files (include hidden files) | Leader + fF |
| Help Tags                         | Leader + fh |
| Registers                         | Leader + fr |
| Live Grep (include hidden files)  | Leader + fW |
|                                   |             |
| Git Branches                      | Leader + gb |
| Git Commits (repository)          | Leader + gc |
| Git Commits (current file)        | Leader + gC |
| Git Status                        | Leader + gt |
| LSP Symbols                       | Leader + ls |

## Terminal Mappings

| Action                   | Mappings            |
| ------------------------ | ------------------- |
| Open Floating Terminal   | Leader + tf or <F7> |
| Open Horizontal Terminal | Leader + th         |
| Open Vertical Terminal   | Leader + tv         |
| Open Toggle btm          | Leader + tt         |

## surround

- [kylechui/nvim-surround: Add/change/delete surrounding delimiter pairs with ease. Written with in Lua.](https://github.com/kylechui/nvim-surround)
- `ys{motion}{char}`, `ds{char}`, and `cs{target}{replacement}`

## commands

:TodoQuickFix
:colorscheme = tokyonight

## git
- lazy-git: [leader + g + g]
- commit
  - [leader + e]로 Expore로 이동
  - '<', '>'로 git 탭으로 이동
  - ? 누르면 help
