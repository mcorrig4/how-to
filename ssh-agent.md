# SSH Agent Linux

### Create Keys
```sh
mkdir -p ~/.ssh
chmod 700 ~/.ssh

ssh-keygen -t ed25519 -C "my-name@gmail.com"
chmod 600 ~/.ssh/new_key_ed25519
chmod 644 ~/.ssh/new_key_ed25519.pub
```
> [!NOTE]
> you cannot use `~` when creating key. Use full path `/home/username/.ssh/new_key_ed25519`

### Run SSH Agent & Add Keys
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

#### BETTER
The above seems to suck with VS Code remote development

```sh
SSH_ENV="$HOME/.ssh/agent-environment"

function start_agent {
    echo "Initialising new SSH agent..."
    /usr/bin/ssh-agent | sed 's/^echo/#echo/' > "${SSH_ENV}"
    echo succeeded
    chmod 600 "${SSH_ENV}"
    . "${SSH_ENV}" > /dev/null
    /usr/bin/ssh-add;
}

# Source SSH settings, if applicable

if [ -f "${SSH_ENV}" ]; then
    . "${SSH_ENV}" > /dev/null
    #ps ${SSH_AGENT_PID} doesn't work under cywgin
    ps -ef | grep ${SSH_AGENT_PID} | grep ssh-agent$ > /dev/null || {
        start_agent;
    }
else
    start_agent;
fi
```

Reload `.bashrc`
```sh
source ~/.bashrc
```
