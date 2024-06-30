# SSH Agent Linux

Add this to `~/.bashrc`:
```sh
#
# SSH start and add keys
#

# SSH Agent should be running, once
runcount=$(ps -ef | grep "ssh-agent" | grep -v "grep" | wc -l)
if [ $runcount -eq 0 ]; then
    echo Starting SSH Agent
    eval $(ssh-agent -s)
else
    echo SSH Agent already running
fi

# This runs ssh-add if there is not at least 1 key loaded and sets a key timeout of 1 day
ssh-add -l &>/dev/null
if ! [ "$?" == 0 ]; then
    echo No keys added. Adding keys...
    ssh-add -t 1d ~/.ssh/* &>/dev/null
else
    echo Keys already added
fi
```
