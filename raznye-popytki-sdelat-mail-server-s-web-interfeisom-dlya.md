# Разные попытки сделать mail server с Web интерфейсом для

## Разные попытки сделать mail server с Web интерфейсом для пользователей

Разные попытки сделать mail server с Web интерфейсом для пользователей

### Шаг 0 <a href="#shag-0" id="shag-0"></a>

Установим portainer

cd /opt

mkdir portainer

cd portainer

touch docker-compose.yaml

vi docker-compose.yaml

```yaml
version: "3.3"
services:
  twportainer:
    image: portainer/portainer-ce:latest
    container_name: portainer
    environment:
      - TZ=Europe/Moscow
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
      - /opt/portainer/portainer_data:/data
    ports:
      - "8000:8000"
      - "9443:9443"
    restart: always
```

up -d



### 1. docker-mailserver + roundcube <a href="#id-1.-docker-mailserver--roundcube" id="id-1.-docker-mailserver--roundcube"></a>

## 1. docker-mailserver + roundcube

cd /opt

git clone [https://github.com/docker-mailserver/docker-mailserver.git](https://github.com/docker-mailserver/docker-mailserver.git)

cd  /docker-mailserver

docker-compose up -d

./setup.sh - список команд для настройки

Добавил пару пользователей

Поднимаем контйнер с roundcube

```
version: 'latest'

services:
  roundcubemail:
    image: roundcube/roundcubemail:latest
    container_name: roundcubemail
#    restart: unless-stopped
    volumes:
      - ./www:/var/www/html
      - ./db/sqlite:/var/roundcube/db
    ports:
      - 9002:80
    environment:
      ROUNDCUBEMAIL_DB_TYPE: sqlite
      ROUNDCUBEMAIL_SKIN: elastic
      ROUNDCUBEMAIL_DEFAULT_HOST: tls://mailserver
      ROUNDCUBEMAIL_SMTP_SERVER: tls://mailserver
      ROUNDCUBEMAIL_UPLOAD_MAX_FILESIZE: 5000M
      ROUNDCUBEMAIL_COMPOSER_PLUGINS: "weird-birds/thunderbird_labels,jfcherng-roundcube/show-folder-size,germancoding/tls_icon:^1.2"
      ROUNDCUBEMAIL_PLUGINS: thunderbird_labels, show_folder_size, tls_icon

```

В целом подрубилось и через веб можно отправлять почту

```
ROUNDCUBEMAIL_UPLOAD_MAX_FILESIZE: 5000M
```

Не подошёл синтаксис с гигабайтами

Не подрузились плагины

`ROUNDCUBEMAIL_USERNAME_DOMAIN не отрабатывает.`

Почему-то не импортируются файлы с почтой (в mailu  с roundcube отрабатывает)

Список плагинов в mailu попробовать установить

`ROUNDCUBE_PLUGINS` is not set the following plugins are enabled by default:

* archive
* carddav
* enigma
* mailu
* managesieve
* markasjunk
* zipdownload

Удалось подцепить файлы с письмами

**Почитать про плагины rouncube**

**Попробовать в dcape**

