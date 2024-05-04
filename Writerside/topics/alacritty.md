# alacritty

- 출처: [How To Make Your Boring macOS Terminal Amazing With Alacritty](https://www.youtube.com/watch?v=uOnL4fEnldA)

- `brew install --cask alacritty`
- alacritty 실행
- security에서 alacritty 허용
- 추가 설치
```bash
brew tap homebrew/cask-fonts
brew install font-meslo-lg-nerd-font
brew install powerlevel10k
echo "source $(brew --prefix) /share/powerlevel10k/powerlevel10k.zsh-theme" >>~/zshrc
```
- `p10k configure`
```
Lean
unicode
8 Colors 
24 hours
two lines
dotted
left
black
sparse
many icons
concise
no
verbose
yes
yes
```

~/config/alacritty
› git clone https://github.com/alacritty/alacritty-theme.git themes

~/.config/alacritty
`curl https://raw.githubusercontent.com/josean-dev/dev-environment-files/main/.config/alacritty/themes/themes/coolnight.toml --output ~/.config/alacritty/themes/themes/coolnight.toml`

~/.p10k.zsh에서
`typeset -g POWERLEVEL9K_DIR_BACKGROUND=4`
라인을 
`typeset -g POWERLEVEL9K_DIR_BACKGROUND=0`로 수정

source ~/.p10k.zsh
~/.zshrc에 다음 라인들을 추가
```bash
# history setup
HISTFILE=$HOME/.zhistory
SAVEHIST=1000
HISTSIZE=999
setopt share_history 
setopt hist_expire_dups_first
setopt hist_ignore_dups
setopt hist_verify
```

cat -v [enter]
↑↓ [ctlr+c]

^[[A^[[B^C]]

.zshrc에 다음 라인 추가
```
bindkey "^[[A" history-search-backward 
bindkey "^[[B" history-search-forward
```

vi 이후에 ↑ ↓ 등을 눌러서 이전에 작업했던 파일 선택 가능

`brew install zsh-autosuggestions`
`echo "source /opt/homebrew/share/zsh-autosuggestions/zsh-autosuggestions.zsh" > ~/.zshrc`

so만 입력해도 source ~/.zshrc를 제안해 줌
탭, → 등을 눌러 보면 ...

`brew install zsh-syntax-highlighting`
`echo "source $(brew --prefix)/share/zsh-syntax-highlighting/zsh-syntax-highlighting.zsh" >> ~/-zshrc`
so까지 입력하면 커맨드가 틀려서 붉은색으로 보이고
source까지 입력하면 초록색으로 변함

`brew install eza`
alias ls="eza --color=always --long --no-filesize --icons=always --no-time --no-user --no-permissions"

zoxide보다는 autojump가 더 나음