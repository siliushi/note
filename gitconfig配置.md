注意：name和email替换下

```
[alias]
        co = checkout
        ci = commit
        br = branch
        st = status
        unstage = reset HEAD --
        last = log -1 HEAD
        mg = merge --no-ff
        glog = log --pretty=oneline --graph
        df = difftool
        sh = stash
        pl = pull --rebase
        ph = push
        sp = stash pop
        lg = log --color --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset'
        rv = revert --soft HEAD^
        pr  = "!f() { git fetch -fu ${2:-$(git remote |grep ^upstream || echo origin)} refs/pull/$1/head:pr/$1 && git checkout pr/$1; }; f"
        pr-clean = "!git for-each-ref refs/heads/pr/* --format='%(refname)' | while read ref ; do branch=${ref#refs/heads/} ; git branch -D $branch ; done"
        # for bitbucket/stash remotes
        spr  = "!f() { git fetch -fu ${2:-$(git remote |grep ^upstream || echo origin)} refs/pull-requests/$1/from:pr/$1 && git checkout pr/$1; }; f"
[color]
        ui = true
[format]
        pretty = oneline·
[user]
        name = **
        email = **
[core]
        autocrlf = false
        eol = lf
[http]
        postBuffer = 924288000
[credential]
        helper = osxkeychain
[push]
        default = simple
```