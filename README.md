# hs
Bash utilities for munging history into reusable scripts

Type a few commands and decide that you want to reuse them? Run `hs <name of your snip> 5` to save the last 5 lines from history as a snip in a git repo at `~/.hs/` that you can keep synced up with other machines.

Run it again with `hr <name of your snip> --any args you want`. It is run in the current shell, not a subshell, so you can do things in snips that persistently change your shell environment like setting env vars or changing the current directory.

## get it

```
cd ~
git clone https://github.com/mikemccracken/hs.git hsrc
# test:
. ~/hsrc/.hsrc
# persist:
echo ". ~/hsrc/.hsrc" >> .bashrc
```

## hs: history save

say you're editing some code in a git repo somewhere and building and testing it somewhere else.
You end up repeating a reset, pull, make cycle in your test location.

```
# git reset --hard HEAD^1
# git pull
# make clean build test
# notify "build done"
```

sometimes the `make clean build test` is much more involved.

So after you type all that once, use `hs` to capture that as a *snip*. (`hs` = history snip)

```
# hs repulltest 10
```

This will call `$EDITOR` on a file in your hs repo at `~/.hs/` named `repulltest` with the last 10 lines of history and let you write a script. When you save and exit, it'll add and commit it to the git repo.

## hr: history run
Then when you want to do the same thing over again, just use `hr`, "history run":

```
# hr repulltest
```

You can use tab completion if you forget the name. `hr rep<TAB>`.

You can pass args to 'hr', they'll be sent as args to your script.

*Note* that these aren't really full scripts, they're *snips*, so they're run in the same shell that you start from. They're run with `. $fn "$@"`. So if you `exit` in the snip, you will exit your shell when you `hr` that snip.

### hr run notes

For each run, `hr` adds a line that records when the snip was run, what its return value was and how long it took to a git note that is attached to the commit that was run. This might be useful some day? I thought git notes ought to be handy for something. I still think that.

## hl: history list

See a list of all the available snips with `hl`. It also prints the second line of the snip file, which you can use as a description message.

Filter the list with `hl <pat>`. It just pipes to grep.

Example:
```
$ hl
buildiso             # sudo: clean and run build_iso
hfind                # search all the separate screen window bash_history.N files
lookiso              # download and mount an ISO usage: lookiso URL/isofilename iso-name [LAYERNAME]
runimg               # run a VM on an installed disk.img
runiso               # run a VM that installs $1 on disk.img
tbrl                 # testbed remote login 'tbrl test1 host1[,host2] command'
```

## hd: history delete

remove a snip with `hd <name>`.

## he: history edit

edit an existing snip in `$EDITOR` with `he`.
Saving and exiting 0 will automatically commit your changes in git.

## hlog: history log

`hlog <name>` prints the git log of the snip `<name>` - this is where you'll see your edits and the notes with runtimes.

## hcat: history cat

you guessed it, this just `cat`s the snip file so you can remember what it actually does.


# h* commands in history

I hide some of the commands from being added to history, because they are for inspecting history and would pollute it. Specifically, `hs, hl, he, hd, hcat` are in `HISTIGNORE`. I left `hr` out of `HISTIGNORE` because you probably want to be able to re-run that, and see that you ran it when looking at history later.
