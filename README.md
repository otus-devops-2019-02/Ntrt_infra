# Ntrt_infra
Ntrt Infra repository

### HW 4 Cloud-testapp

Команда для создания инстанса с выполнением скрипта из локального файла при загрузке инстанса:
```
gcloud compute instances create reddit-app\
  --boot-disk-size=10GB \
  --image-family ubuntu-1604-lts \
  --image-project=ubuntu-os-cloud \
  --machine-type=g1-small \
  --tags puma-server \
  --restart-on-failure \
  --metadata-from-file startup-script=/Users/mike/Otus/Ntrt_infra/startup_script.sh
```
Файл копируется на созданный инстанс, получает атрибут исполняемого файла и выполняется.
Все шаги выполнения видны в syslog инстанса.
Для передачи скрипта через URL создаем GC бакет и заливаем туда скрипт.
```
gcloud compute instances create reddit-app \
  --boot-disk-size=10GB \
  --image-family ubuntu-1604-lts \
  --image-project=ubuntu-os-cloud \
  --machine-type=g1-small \
  --tags puma-server \
  --restart-on-failure \
  --scopes storage-ro \
  --metadata startup-script-url=https://storage.googleapis.com/otus-hw4/startup_script.sh
```
Здесь использован доступ по https, так же можно использовать gs.
Права на файл в бакете оставлены дефолтные, из публичной сети файл недоступен.
Команда для создания правила firewall:
```
gcloud compute firewall-rules create default-puma-server\
  --direction=INGRESS \
  --priority=1000 \
  --network=default \
  --action=ALLOW \
  --rules=tcp:9292 \
  --source-ranges=0.0.0.0/0 \
  --target-tags=puma-server
```


Адрес инстанса:
```
testapp_IP = 35.204.191.29
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
