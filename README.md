# Ntrt_infra
Ntrt Infra repository

### HW 4 Cloud-testapp

Адрес инстанса:
```
testapp_IP = 35.204.9.219
testapp_port = 9292
```


===========================

### HW 3 Cloud-bastion

Подключение в одну команду к Someinternalhost пользователем c32exe:
`ssh -i .ssh/c32exe -At c32exe@35.204.73.227 ssh c32exe@10.164.0.3`

Для удобного подключения к хостам можно создать файл  `~/.ssh/config` и прописать в нем хосты и
параметры подключения к ним:

```
Host bastion
    Hostname 35.204.73.227
    User c32exe
    IdentityFile ~/.ssh/c32exe
    ForwardAgent yes

Host someinternalhost
    Hostname 10.164.0.3
    User c32exe
    ProxyCommand ssh -W %h:%p bastion
```
Подключиться к Someinternalhost теперь можно командой: `ssh someinternalhost`

Адреса инстансов:
```
bastion_IP = 35.204.73.227
someinternalhost_IP = 10.164.0.3
```

После настройки Pritunl подключиться к нему можно командой:
`openvpn --config cloud-bastion.ovpn`
или используя vpn-клиент типа Tunnelblick.

Так же Pritunl автоматически выдает SSL сертификат на указанный в настройках домен.
Указываем домен `35.204.73.227.sslip.io`, который будет разрезовлен во внешний ip Bastionhost.
