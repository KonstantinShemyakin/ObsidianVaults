При установке git вместе с ним ставится специальная утилита для генерации ssh-ключей - ssh.exe . Чтобы сгенерировать ssh-ключ на Windows нужно открыть командную строку и написать следующую команду:
```cmd
ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
```
После этого в указанной при генерации папке (по стандарту C:/Users/User/.ssh) будет создано два файла *название*.pub - открытая часть ключа, и *название* - закрытая/приватная часть ключа.
Если работа идет с GitHub, то для работы с репозиториями открытый ключ нужно добавить туда через **settings -> ssh and gpg keys**:
![[Pasted image 20240212164835.png]]
Также для работы с удаленным репозиторием в GitHub нужно подключить приватный ключ в Git, для автоматического подключения нужно прописать в файле **profile** или **bashrc** в папке с установкой Git следующий код в самом конце:
```file
env=~/.ssh/agent.env

agent_load_env () { test -f "$env" && . "$env" >| /dev/null ; }

agent_start () {
    (umask 077; ssh-agent >| "$env")
    . "$env" >| /dev/null ; }

agent_load_env

# agent_run_state: 0=agent running w/ key; 1=agent w/o key; 2=agent not running
agent_run_state=$(ssh-add -l >| /dev/null 2>&1; echo $?)

if [ ! "$SSH_AUTH_SOCK" ] || [ $agent_run_state = 2 ]; then
    agent_start
    ssh-add
elif [ "$SSH_AUTH_SOCK" ] && [ $agent_run_state = 1 ]; then
    ssh-add
fi

unset env
```
Если ключ находится в нестандартной локации ( .ssh/id_rsa ), то необходимо запустить ssh-agent и добавить туда ключ:
***Shell with admin***
```powershell
# start the ssh-agent in the background
Get-Service -Name ssh-agent | Set-Service -StartupType Manual
Start-Service ssh-agent
```
***cmd without admin***
```cmd
ssh-add c:/Users/YOU/.ssh/id_ed25519
```
