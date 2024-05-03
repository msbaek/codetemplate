# tmux.md

## my conf

```
set -g default-terminal "screen-256color"

unbind C-b
set -g prefix C-a
bind C-a send-prefix

# split panes using | and -
bind | split-window -h
bind - split-window -v
unbind '"'
unbind %

unbind r
bind r source-file ~/.tmux.conf\; display-message "Config reloaded."

# easily resizing tmux panes
# not work. 어떤 키를 눌러야 하는지 모르겠음
bind -r j resize-pane -D 5
bind -r k resize-pane -U 5
bind -r l resize-pane -R 5
bind -r h resize-pane -L 5

# maximize pane
bind -r m resize-pane -Z

set -g mouse on

set-window-option -g mode-keys vi # use vi keys in buffer
bind-key -T copy-mode-vi 'v' send -X begin-selection # start selecting text with "v"
bind-key -T copy-mode-vi 'y' send -X copy-selection # copy text with "y"

set-option -g status-position top

unbind -T copy-mode-vi MouseDragEnd1Pane # don't exit copy mode after dragging with mouse

# tpm plugins
set -g @plugin 'tmux-plugins/tpm'

# List of plugins
set -g @plugin 'christoomey/vim-tmux-navigator' # for navigating panes and vim/nvim with Ctrl-hjkl
set -g @plugin 'catppuccin/tmux'
# set -g @plugin 'jimeh/tmux-themepack' # to configure tmux th↓eme [jimeh/tmux-themepack: A pack of various Tmux themes.](https://github.com/jimeh/tmux-themepack)
set -g @plugin 'tmux-plugins/tmux-resurrect' # persist tmux sessions after computer restart
set -g @plugin 'tmux-plugins/tmux-continuum' # automatically saves sessions for you every 15 minutes

# set -g @themepack 'powerline/default/cyan' # use this theme for tmux

set -g @resurrect-capture-pane-contents 'on' # allow tmux-ressurect to capture pane contents
set -g @continuum-restore 'on' # enable tmux-continuum functionality

run '~/.tmux/plugins/tpm/tpm'
# --

set -g @catppuccin_window_left_separator "█"
set -g @catppuccin_window_right_separator "█ "
st -g @catppuccin_window_number_position "right"
set -g @catppuccin_window_middle_separator "  █"

set -g @catppuccin_window_default_fill "number"
set -g @catppuccin_window_current_fill "number"
set -g @catppuccin_window_current_text "#{pane_current_path}"

# set -g @catppuccin_status_modules_right "application session date_time"
set -g @catppuccin_status_modules_right "directory session"
set -g @catppuccin_status_left_separator  ""
set -g @catppuccin_status_right_separator " "
set -g @catppuccin_status_fill "all"
set -g @catppuccin_status_connect_separator "yes"

set -g @plugin 'tmux-plugins/tmux-resurrect'
set -g @plugin 'tmux-plugins/tmux-continuum'

set -g @continuum-restore 'on'
set -g @continuum-boot 'on'
```

## commands

- [Tmux Cheat Sheet & Quick Reference | Session, window, pane and more](https://tmuxcheatsheet.com/#)

### Sessions

| 설명                                        | 명령                             |
| ------------------------------------------- | -------------------------------- |
| Start a new session                         | `:new`                           |
| Start a new session with the name mysession | `tmux new -s mysession`          |
|                                             | - `:new -s mysession`            |
| kill/delete session mysession               | `tmux kill-ses -t mysession`     |
|                                             | `tmux kill-session -t mysession` |
| kill/delete all sessions but the current    | `tmux kill-session -a`           |
| Detach from session                         | leader + d                       |
| Show all sessions                           | `tmux ls`                        |
| Attach to last session                      | `tmux attach`                    |
| Attach to a session with the name mysession | `tmux attach -t mysession`       |
| Session and Window Preview                  | leader + w                       |
| Move to previous session                    | leader + (                       |
| Move to next session                        | leader + )                       |

### Windows

| 설명                                            | 명령                     |
| ----------------------------------------------- | ------------------------ |
| Create window                                   | leader + c               |
| Rename current window                           | leader + ,               |
| Close current window                            | leader + &               |
| List windows                                    | leader + w               |
| Previous window                                 | leader + p               |
| Next window                                     | leader + n               |
| Switch/select window by number                  | leader + 0..9            |
| Toggle last active window                       | leader + l               |
| swap window number 2(src) and 1(dst)            | `:swap-window -s 2 -t 1` |
| Move current window to the left by one position | `:swap-window -t -1`     |
| Move window from source to target               | ...                      |
| Reposition window in the current session        | `:movew -s 0:9`          |
| Renumber windows to remove gap in the sequence  | `:move-window -r`        |

### Panes

| 설명                         | 명령                       |
| ---------------------------- | -------------------------- |
| Toggle last active pane      | leader + ;                 |
| Split to a horizontal layout | `:split-window -h`         |
| Split to a vertical layout   | `:split-window -v`         |
| Join two windows as panes    | `:join-pane -s 2 -t 1`     |
| Move pane to another window  | `:join-pane -s 2.1 -t 1.0` |
| Toggle pane zoom             | leader + z                 |
| Convert pane into a window   | leader + !                 |
| Close current pane           | leader + x                 |
| Toggle between pane layouts  | leader + sp                |

### Copy Mode

- scroll하면서 텍스트를 선택 복붙할 때 유용
- copy mode가 실행 중이면 우상단에 [0/280] 등이 보임

| 설명            | 명령       |
| --------------- | ---------- |
| Enter copy mode | leader + [ |
| begin selection | v, V       |
| Copy selection  | y          |
| quit mode       | C-c        |

### Misc

| 설명                        | 명령              |
| --------------------------- | ----------------- |
| Enter command mode          | leader + :        |
| Set OPTION for all sessions | `:set -g OPTION`  |
| Set OPTION for all windows  | `:setw -g OPTION` |
| Enable mouse mode           | `:set mouse on`   |

### Help

| 설명                                     | 명령         |
| ---------------------------------------- | ------------ |
| List key bindings(shortcuts)             | `:list-keys` |
| Show every session, window, pane, etc... | tmux info    |

## References

### [How To Use and Configure Tmux Alongside Neovim](https://www.josean.com/posts/tmux-setup)

### [Ep.1 Tmux will SKYROCKET your productivity - here’s how](https://www.youtube.com/watch?v=niuOc02Rvrc)

### [Ep.2 Making Tmux Better AND Beautiful -- here’s how](https://www.youtube.com/watch?v=jaI3Hcw-ZaA)

- [TPM](http://github.com/tmux-plugins/tpm)
- `git clone https://github.com/tmux-plugins/tpm ~/.tmux/plugins/tpm`
- `tmux source ~/.tmux.conf`
- install plugins: `leader + I`
- [catppuccin/tmux: 💽 Soothing pastel theme for Tmux!](https://github.com/catppuccin/tmux)

### [Ep3. My Forever Dev Workflow](https://www.youtube.com/watch?v=_YaI2vDbk0o)

- vim-tmux-navigator
