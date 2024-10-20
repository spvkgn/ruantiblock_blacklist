[![build status](https://github.com/spvkgn/ruantiblock_blacklist/actions/workflows/update_blacklist.yml/badge.svg)](https://github.com/spvkgn/ruantiblock_blacklist/actions/workflows/update_blacklist.yml)

# Создание конфигов ipset и dnsmasq со списком блокировок для [ruantiblock_openwrt](https://github.com/gSpotx2f/ruantiblock_openwrt).

Дальнейшие действия, кроме последнего пункта, должны производиться на той машине где будет выполняться создание конфигов!

## Зависимости

Для работы парсера нужен Python3 (версии >=3.6). Обычно, во всех современных дистрибутивах Linux он уже установлен по умолчанию.

## Установка скриптов для запуска модуля-парсера

Создайте локальную копию этого репозитория в `/opt/ruantiblock_blacklist`:

    git clone https://github.com/gSpotx2f/ruantiblock_blacklist_test /opt/ruantiblock_blacklist

## Запуск

Создание конфигов ipset и dnsmasq (эту команду можно добавить в cron для регулярного обновления списка блокировок):

    /opt/ruantiblock_blacklist/0.9/start.sh

По умолчанию, созданные файлы будут находиться в директориях `blacklist/ip` и `blacklist/fqdn` (в корне проекта) для конфигураций `ip` и `fqdn` соответственно. В скрипте `start.sh` можно изменить директорию для вывода готовых конфигов (`OUTPUT_DIR`) и поместить их, например, на ваш веб-сервер, чтобы роутер (ruantiblock) забирал их оттуда при обновлении блэклиста.

## Настройка [ruantiblock_openwrt](https://github.com/gSpotx2f/ruantiblock_openwrt) на роутере для получения созданных конфигов с вашего веб-сервера

В конфигурационном файле `/etc/ruantiblock/ruantiblock.conf` измените ссылки на файлы в следующих переменных:

    RA_IP_IPSET_URL="https://<USERNAME>.github.io/ruantiblock_blacklist/blacklist-0.9/ip/ruantiblock.ip"
    RA_IP_DMASK_URL="https://<USERNAME>.github.io/ruantiblock_blacklist/blacklist-0.9/ip/ruantiblock.dnsmasq"
    RA_IP_STAT_URL="https://<USERNAME>.github.io/ruantiblock_blacklist/blacklist-0.9/ip/update_status"
    RA_FQDN_IPSET_URL="https://<USERNAME>.github.io/ruantiblock_blacklist/blacklist-0.9/fqdn/ruantiblock.ip"
    RA_FQDN_DMASK_URL="https://<USERNAME>.github.io/ruantiblock_blacklist/blacklist-0.9/fqdn/ruantiblock.dnsmasq"
    RA_FQDN_STAT_URL="https://<USERNAME>.github.io/ruantiblock_blacklist/blacklist-0.9/fqdn/update_status"

Включение режима обновления блэклиста `ruantiblock-fqdn`:

    uci set ruantiblock.config.bllist_preset="ruantiblock-fqdn"
    uci commit ruantiblock

Обновление блэклиста:

    /usr/bin/ruantiblock update
