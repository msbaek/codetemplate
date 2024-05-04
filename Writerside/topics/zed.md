# zed

- [Porting Neovim Keymaps To Zed](https://www.youtube.com/watch?v=IHPgokWisYE&t=215s)
- 이 영상에서 있는 설정을 기본으로 약간 수정해서 사용([dotfiles/.config/zed at main · msbaek/dotfiles](https://github.com/msbaek/dotfiles/tree/main/.config/zed))

### Keybindings

cmd+R: toggle right dock

```
-- terminal
"ctrl-w z": "workspace::ToggleZoom",
"ctrl-w t": "terminal_panel::ToggleFocus",

"cmd-/":  "editor::ToggleComments",
"ctrl-space": "editor::ShowCompletions",

"cmd-.": "editor::ToggleCodeActions",
"space l a": "editor::ToggleCodeActions",

"alt-cmd-r": "editor::RevealInFinder"
"alt-cmd-[": "editor::Fold",
"alt-cmd-]": "editor::UnfoldLines",


-- visual mode
"shift-k": "editor::MoveLineUp"
"shift-j": "editor::MoveLineDown",

- multiple cursors
"cmd-d":  "editor::SelectNext",
"cmd-alt-up": "editor::AddSelectionAbove",
"cmd-ctrl-p": "editor::AddSelectionAbove",
"cmd-alt-down": "editor::AddSelectionBelow",
"cmd-ctrl-n": "editor::AddSelectionBelow",

"g ]": "editor::GoToDiagnostic",
"g [": "editor::GoToPrevDiagnostic",
"g r": "editor::FindAllReferences",
"shift-k": "editor::Hover",
"space l l": "diagnostics::Deploy",
"space v": "editor::GoToDefinitionSplit"

"f8": "editor::GoToDiagnostic",
"shift-f8": "editor::GoToPrevDiagnostic",
"f2": "editor::Rename",
"f12": "editor::GoToDefinition",
"alt-f12": "editor::GoToDefinitionSplit",
"cmd-f12": "editor::GoToTypeDefinition",
"alt-cmd-f13": "editor::GoToTypeDefinitionSplit",
"alt-shift-f12": "editor::FindAllReferences",

"space f": "file_finder::Toggle",
"space o": "tab_switcher::Toggle",
"space e": "workspace::ToggleLeftDock",
"space p": "outline::Toggle",
"space l r": "editor::Rename",
"space l f": "editor::Format",
"g c c": "editor::ToggleComments",
```

### [From Vim to Zed - by Thorsten Ball - Register Spill](https://registerspill.thorstenball.com/p/from-vim-to-zed)

- 다중 커서, 다중 선택, 선택 실행 취소/다시 실행 스택

### [Zed - Code at the speed of thought](https://zed.dev/)

- AI
    - code 블록을 선택하고 ctrl-enter
    - you can use GPT-4 to generate or refactor code by pressing ctrl-enter and typing a natural language prompt.