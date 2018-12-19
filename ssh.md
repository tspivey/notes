## Windows 10 ssh-agent

### Notes
* Git clone with Windows ssh seems to work.
* rsync doesn't work.

ssh -V shows OpenSSH for Windows 7.7P1, and the project is on GitHub.
Documentation: https://github.com/PowerShell/Win32-OpenSSH/wiki

### Starting ssh-agent
* services.msc
* Find OpenSSH Authentication Agent, set it to automatic
* Right click, start

### Add key
ssh-add

Should scan for your key in %userprofile%\.ssh and add it

From then on, ssh should just work without a passphrase.

### Getting git to use windows ssh
    git config --global core.sshCommand "c:/windows/system32/OpenSSH/ssh.exe"

To reset:
    git config --global --unset core.sshCommand

## KeeAgent

### Setup
* Put the plugin in C:\Program Files (x86)\KeePass Password Safe 2\Plugins
* Quick start: https://keeagent.readthedocs.io/en/stable/usage/quick-start.html
* Add entry, give it a name, password is ssh key passphrase, attach key
* KeeAgent tab, check allow
* right click entry, load key
* Tools, Options, KeeAgent, check cygwin socket, set path
