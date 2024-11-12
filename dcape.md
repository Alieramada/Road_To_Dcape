# Dcape

DCAPE - аббревиатура от “**D**ocker **c**omposed **ap**plication **e**nvironment”.

[Dcape](https://github.com/dopos/dcape) - это инструмент создания окружения (среды) для развёртывания [docker](https://www.docker.com/)-приложений по технологии [GitOps](https://www.gitops.tech/). Такое развёртывание состоит из нескольких этапов, для каждого из которых уже существуют opensource-решения (сервисы), но и их, в свою очередь, необходимо сконфигурировать и развернуть. **Dcape**, с помощью [make](https://www.gnu.org/software/make/) и [docker-compose](https://docs.docker.com/compose/), позволяет решить следующие задачи:

* сконфигурировать и развернуть [сервисы](https://dopos.github.io/dcape/coreapps/) для всех этапов развёртывания приложений
* сформулировать [правила адаптации приложений](https://dopos.github.io/dcape/usage/apps/) к стеку сервисов **dcape**

Цель создания **dcape** - получить относительно удобный способ запуска docker-приложений простой короткой командой, например

* `make up` - локально
* `git push` - удаленно

Главная проблема была в том, как повесить несколько контейнеров на порт 80, чтобы запросы к ним проксировались по имени хоста не на основании заранее руками созданного конфига, а по факту старта контейнера - эту задачу решил [traefik](https://traefik.io/).\
Бонусом он добавил TLS, т.е. возможность получения сертификата для хоста или, когда хостов много - для домена (т.е. wildcard-домена)

При использовании `traefik` приложение может управлять своей доступностью извне посредством меток (label) docker-контейнера, задавать которые можно в файле `docker-compose.yml`

Проект dcape - это попытка достичь требуемых целей с помощью `traefik`, `docker-compose` и минимального количества дополнительного кода.

## Настройка DNS

Dcape разворачивается и используется на локальном компьютере, в т.ч. если у этого компьютера нет сетевых интерфейсов.

Т.к. сервисы **dcape** общаются между собой, их hostname нельзя привязать к `loopback`- интерфейсу. В качестве ip-адреса можно использовать шлюз подсети dcape, которая задается параметром `DCAPE_SUBNET` (по умолчанию, подсеть - 100.127.0.0/24 и шлюз - 100.127.0.1):

```bash
grep -q " dev.lan" /etc/hosts || \
sudo bash -c 'for n in "" git drone port ns ; do [ "$n" ] && n="$n." ; echo "100.127.0.1 ${n}dev.lan" >> /etc/hosts ; done'

```

## Установка

```bash
cd /opt
sudo mkdir dcape && sudo chown $USER dcape
git clone --depth 1 https://github.com/dopos/dcape.git
cd dcape
```

## Настройка и запуск <a href="#nastroika-i-zapusk" id="nastroika-i-zapusk"></a>

## Локальный сервер <a href="#lokalnyi-server" id="lokalnyi-server"></a>

Вариант без поддержки SSL, но с установкой gitea. Выполняется в 3 шага, т.к. на шаге 2 необходимо использовать браузер для

* завершения установки gitea
* создания API TOKEN

**Шаг 1. Подготовка к запуску gitea**

```bash
make init DCAPE_DOMAIN=your_server.ltd
make apply
make up
```

<mark style="color:red;">make: \*\*\* Нет правила для сборки цели «init». Останов</mark>

