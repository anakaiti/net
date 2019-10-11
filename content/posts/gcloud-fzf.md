---
title: 'Switch between Google Cloud CLI configurations easily'
date: 2019-10-11T21:24:06+07:00
draft: false
---

When managing multiple Google Cloud environments, you will find yourself often
switching between them. This can be done easily using 
`gcloud config configurations list` and `gcloud config configurations activate`,
but those commands feels overly verbose & easy to forget. Here are some ways to
make it easier.

## Shell functions

``sh
gcl(){ gcloud config configurations list; }
gca(){ gcloud config configurations activate $1; }

``

This is already a lot better than the previous way, it's short and sweet. But
can we improve it further.

## Fzf

![fzf preview](https://raw.githubusercontent.com/junegunn/i/master/fzf-preview.png)

    fzf is a general-purpose command-line fuzzy finder.

With fzf, we can create a simple TUI with search. Using it, we can combine the
two commands into..

## One command to rule them all

``sh
gcp(){
    local readonly g=( "gcloud" "config" "configurations" )
    ${g} activate ${1:-$(${g} list |  fzf --header-lines 1 --reverse | cut -d ' ' -f 1)}
}

``

**Hol'up!** how did it get this complicated?  
Lets go through it line by line.  

``sh
local readonly g=( "gcloud" "config" "configurations" )

``

Here, we're setting the shell variable `g` with the first part of the
long command.

`local` is a shell keyword to make the variable locally scoped.  
`readonly` makes it constant/uneditable.  
It's not required here, but it makes things nicer, since I have a lot of other
functions defined elswhere in my shell profile.

``sh
${g} activate ${1:-$(...)}

``

`${g}` expands our last variable to shell commands & arguments.  
`activate` is one of the arguments from the long command.  
`${1:-...}` expands the variable `1`, which is the first argument to the
function call, this makes `gcp {configuration-name}` possible.  
The `:-` part is used to set a default value in case `1` is unset/empty, here
the default value is not set directly, but instead..
`$(...)` runs the command(s) listed inside it in a subshell and returns it as
an argument.  

``sh
${g} list |  fzf --header-lines 1 --reverse | cut -d ' ' -f 1

``

You can see that there's actually 3 commands here. This is called a pipeline.
The output of the previous command is passed to the input of the next command.

`${g} list` calls `gcloud config configurations list`
`fzf` creates a simple TUI selection from the list.  
`--header-lines 1` option makes the first line inputted as the header of the TUI
which, which make it easier to navigate.  
`--reverse` is used because, by default, fzf outputs your selection in reverse
alphabetical order. I prefer it alpabetical. so I use this.
`cut` is a command to trim your input line by line.
`-d` enables the `cut` command's table mode, which splits the output based on a
set delimiter.  
`-f 1` will trim to only the first field of the input.  

## Final results

<script id="asciicast-iCMA6snE19cBuwDIY1oBjPhy6" src="https://asciinema.org/a/iCMA6snE19cBuwDIY1oBjPhy6.js" async></script>

It's pretty nice, isn't it?  
Thanks for reading, it's my first post so sorry if I'm unclear.  
