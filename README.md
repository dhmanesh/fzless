# fzless
A zero-resource, terminal-based micro-IDE that combines fzf and less into a seamless loop for rapid file discovery, viewing, editing, and command harvesting
 
This tool establishes a seamless loop between macro-searching (`fzf`), micro-searching/reviewing (`less`), and editing (`your $EDITOR`), allowing you to navigate and modify your entire file system without leaving your terminal workflow.

## The Workflow 

1. **Macro Search (`fzf`)**: Instantly search and preview any file or symlink in your home directory.
2. **Micro Search (`less`)**: Press `Enter` to open the file in `less` with line numbers for deep review and searching.
3. **Seamless Editing (`v`)**: Press `v` inside `less` to launch your favorite editor. Saving and exiting drops you straight back into `less` with the updated file contents.
4. **The Endless Loop**: Quitting `less` (`q`) returns you to the active `fzf` search loop to continue your work. Press `Esc` to exit entirely.
5. **Command Snippet Harvesting**: Extract specific lines from any file directly into your terminal command history for immediate execution.

## Features

- **Resource Efficient**: Uses almost zero system overhead compared to heavy modern IDEs.
- **Toggleable Previews**: Keep your terminal clean. Press `?` inside the search window to hide or show file previews.
- **Dynamically Updated**: File previews update automatically after you edit them.
- **Snippet to History**: Press `Ctrl+X` to cherry-pick specific lines from a file and inject them directly into your shell's history (accessible via the `Up` arrow).

## Installation & Setup

### Prerequisites
Ensure you have the following tools installed:
- `fzf`
- `less`
- Any terminal editor (configured via your `$EDITOR` environment variable, e.g., `export EDITOR=vim`)

### Configuration
Add the function to your shell configuration file (e.g., `~/.bashrc` or `~/.zshrc`):

```bash
fzless() { 
    find ~ -type f,l | fzf \
        --preview-window=up:50% \
        --preview-window '~1' \
        --preview-window 'hidden' \
        --bind '?:toggle-preview' \
        --bind "enter:execute(less -N {})" \
        --bind "focus:transform-header(echo -ne 'press ENTER to launch less; Ctrl+x for snip-to-history\n' && wc -l {})" \
        --bind "ctrl-x:execute(cat -n {} | fzf -m --preview-window=:hidden >> history -s)"
}
```

*Note: Reload your shell (`source ~/.bashrc`) after saving.*

## Keybindings Matrix

| Context | Key | Action |
| :--- | :--- | :--- |
| **fzf** | `?` | Toggle file preview window |
| **fzf** | `Ctrl + X` | Open line selector to push snippets to history |
| **fzf** | `Enter` | Open selected file in `less` |
| **fzf** | `Esc` | Break the loop and exit to shell |
| **less** | `v` | Open file in your `$EDITOR` |
| **less** | `q` | Quit file review and return to `fzf` search |

Tip: Feel free to change the search scope of the find command in the script to match your workflow. You can replace ~ with specific directories (like ~/Projects) or add flags like -not -path '*/.*' to skip hidden files, binary assets, or heavy .git folders.
