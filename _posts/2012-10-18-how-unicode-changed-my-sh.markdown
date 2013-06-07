---
layout: post
title: "how unicode changed my sh"
date: 2012-10-18 08:56
categories: shell
image: http://cl.ly/image/163P0j1k3L3f/content
---

Hey look how I integrated UTF-8 to my zsh theme.
{% highlight bash %}
PROMPT='%{$fg[red]%}‹$(rbenv version-name)› %{$fg[yellow]%}${PWD/#$HOME/~}  %{$reset_color%}'
RPROMPT='$(git_prompt_info)'


ZSH_THEME_GIT_PROMPT_PREFIX="%{$reset_color%}[ "
ZSH_THEME_GIT_PROMPT_SUFFIX="%{$reset_color%} ]"
ZSH_THEME_GIT_PROMPT_DIRTY=" %{$fg[red]%}%B☁%b%F{154}%f%{$reset_color%}"
ZSH_THEME_GIT_PROMPT_CLEAN=" %{$fg[green]%}☀%F{154}"
{% endhighlight %}

The great part is of course the ☁ and the ☀ for my git status.
If you don't use zsh, switch! [Old article from 2007 on why!](http://friedcpu.wordpress.com/2007/07/24/zsh-the-last-shell-youll-ever-need/)

If you don't use oh-my-zsh you probably should have a look.
[github: oh-my-zsh](https://github.com/robbyrussell/oh-my-zsh)
