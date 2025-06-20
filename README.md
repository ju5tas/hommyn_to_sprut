# Интеграция обогревателя Rusklimat через MQTT с TLS

## Общее описание
Обогреватель Electrolux Air Gate взаимодействует с сервером по протоколу MQTT через TLS. Для корректной работы с локальным MQTT-брокером необходимо, чтобы DNS-имя mqtt.cloud.rusklimat.ru резолвилось в локальный IP-адрес, по которому доступен брокер.

### Проблема с версиями MQTT-брокера
Обогреватель не поддерживает последние версии Mosquitto, но успешно подключается к более старым (в частности, версии 1.5.11). Также возможно настроить ретрансляцию сообщений между двумя брокерами MQTT, что позволяет использовать промежуточный брокер в Docker-контейнере.

**Важно**: новые версии Mosquitto несовместимы с обогревателем, рекомендуется использовать Mosquitto версии 1.5.11.

## Настройка системы

### 1. DNS-запись
Добавьте статическую DNS-запись на роутере: `mqtt.cloud.rusklimat.ru → 192.168.1.4`

_(по данному адресу работает Mosquitto в Docker-контейнере)._

### 2. Разверните Mosquitto (v1.5.11) в Docker с поддержкой TLS и настройкой ретрансляции:
> Важно: необходимо сгенерировать TLS-сертификаты и разместить их в директории ./config/certs.

### 3. Перезапустите обогреватель.
После включения он подключится к локальному брокеру по адресу `mqtt.cloud.rusklimat.ru`, который теперь указывает на Docker-контейнер. Этот брокер пересылает сообщения основному брокеру, работающему на контроллере (например, `spruthub` или `wirenboard`).

### 4. Добавление в SprutHub
Добавьте в SprutHub MQTT-шаблон `AirGateDi4.json` (для WirenBoard шаблон скопировать в директорию `/mnt/data/makesimple/.SprutHub/data/Templates/MQTT/`).

### Результат
После обновления шаблонов и сканирования новых устройств, обогреватель будет автоматически обнаружен в интерфейсе SprutHub и станет доступен для управления.
