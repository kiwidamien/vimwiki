# Aliases in zshell

The article https://linuxhint.com/configure-use-aliases-zsh/ was really good at describing using aliases in zshell.

* `alias -s EXT=COMMAND` means typing in a file with a particular extension launches the `COMMAND filename`.
  e.g. `alias -s py=nvim` will make typing `$ something.py` at the prompt will open the file in neovim
* `function thing(){  (cd DIR && COMMAND $@) }` means that `thing arg1 arg2 ... argN` will execute `COMMAND arg1 arg2 ... argN` _in_ the directory DIR, but will not change your current directory

