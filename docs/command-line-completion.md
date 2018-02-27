---
id: command-line-completion
title: Command Line Tab Completion
sidebar_label: Command Line Tab Completion
---

We have provided two tab completion scripts to make it easier to use the Zapier Platform CLI, for zsh and bash.

### Zsh Completion Script

To use the zsh completion script, first setup support for completion, if you haven't already done so. This example assumes your completion scripts are in `~/.zsh/completion`. Adjust accordingly if you put them somewhere else:

```zsh
# add custom completion scripts
fpath=(~/.zsh/completion $fpath)

# compsys initialization
autoload -U compinit
compinit
```

Next download our completion script to your completions directory:

```zsh
cd ~/.zsh/completion
curl https://raw.githubusercontent.com/zapier/zapier-platform-cli/master/goodies/zsh/_zapier -O
```

Finally, restart your shell and start hitting TAB with the `zapier` command!

### Bash Completion Script

To use the bash completion script, first download the completion script. The example assumes your completion scripts are in `~/.bash_completion.d` directory. Adjust accordingly if you put them somewhere else.

```bash
cd ~/.bash_completion.d
curl https://raw.githubusercontent.com/zapier/zapier-platform-cli/master/goodies/bash/_zapier -O
```

Next source the script from your `~/.bash_profile`:

```bash
source ~/.bash_completion.d/_zapier
```

Finally, restart your shell and start hitting TAB with the `zapier` command!
