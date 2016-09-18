+++
author = "RaviTezu"
date = "2014-04-14T16:20:02+05:30"
description = ""
tags = []
title = "Say NO to running `git status` and `git branch`"

+++
Get the output of `git status` and `git branch` at your command prompt.
The following steps will help you, to set this up on your machine.

**On OS X:**

1. Make sure you have `git` installed on your MAC machine or download it from [here](https://git-scm.com/download/mac) 
2. Download the files ([`git-prompt.sh`](https://raw.githubusercontent.com/RaviTezu/git-prompt/master/git-prompt.sh) and [`git-completion.bash`](https://raw.githubusercontent.com/RaviTezu/git-prompt/master/git-completion.bash)) to your home directory.
3. Now append the following content to your `~/.bash_profile` file. 

``` bash
source ~/.git-prompt.sh
source ~/.git-completion.bash
export GIT_PS1_SHOWUPSTREAM=auto
export GIT_PS1_SHOWCOLORHINTS=yes
export GIT_PS1_SHOWDIRTYSTATE=true
export GIT_PS1_SHOWUNTRACKEDFILES=true
export GIT_PS1_SHOWSTASHSTATE=true
export PS1='[\u@\h \w\[\033[01;32m\]$(__git_ps1)\[\033[00m\]]\$ '
```

**On Ubuntu:**

1. You should be having `/etc/bash_completion.d/git-prompt` file, if you have `git` installed on your machine.
2. Now create a file `.bash_aliases` in your home directory with the following content.

``` bash
os=`uname -s`

if [[ $os == "Linux" ]]; then
    git_completion="/etc/bash_completion.d/git-prompt"
else
    unset git_completion
fi

if [[ "$git_completion" ]]; then
    if [ -f $git_completion ]; then
           export GIT_PS1_SHOWDIRTYSTATE=true
           export GIT_PS1_SHOWUNTRACKEDFILES=true
           export GIT_PS1_SHOWSTASHSTATE=true          
           PS1="$PS1\$(__git_ps1 '[\[\e[31;1m\]%s\[\e[0m\]] ')"
    fi
fi
```

**On CentOS/Fedora/RedHat:**

1. You should be having `/etc/bash_completion.d/git` file, if you have git installed on your machine.
2. Now you have to append the same content(as above) in your `/etc/bashrc` file.

``` bash
os=`uname -s`

if [[ $os == "Linux" ]]; then
    git_completion="/etc/bash_completion.d/git-prompt"
else
    unset git_completion
fi

if [[ "$git_completion" ]]; then
    if [ -f $git_completion ]; then
           export GIT_PS1_SHOWDIRTYSTATE=true
           export GIT_PS1_SHOWUNTRACKEDFILES=true
           export GIT_PS1_SHOWSTASHSTATE=true
           PS1="$PS1\$(__git_ps1 '[\[\e[31;1m\]%s\[\e[0m\]] ')"
    fi
fi
```

Source the modified/added files or open a new terminal to see the Magic. 

**What's the Magic?:**
[![asciicast](https://asciinema.org/a/86150.png)](https://asciinema.org/a/86150/?autoplay=1)

**More details:**<br/>
You will see different symbol at the prompt for each status of your git repository. 
For example: 

- `=` - When your local branch is even with the remote branch. 
- `%` - When you have some new/untracked files in your git repository. 
- `>` - When your local branch is ahead of your remote branch. 
- `<branch-name>` - You will always see the `branch` name. 
- ... and more. 

If you don't like the prompt colors, you can change this by modifing the scripts or the PS1 environment variable.





