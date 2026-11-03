# firmware

Прошивки устройств для продукта `oneir`.

Прошивки — двоичные файлы производителей, которые драйверы ядра загружают в устройства. Они не входят в сборку ядра и берутся из репозитория [linux-firmware](https://git.kernel.org/pub/scm/linux/kernel/git/firmware/linux-firmware.git) (kernel.org). Этот репозиторий хранит только описание: какую версию `linux-firmware` скачать и какие файлы поставить в образ. Сами прошивки в нём не хранятся.

## Содержимое

| Файл | Назначение |
|---|---|
| `linux-firmware.xml` | Манифест `repo`: скачивает `linux-firmware` зафиксированной версии в `linux-firmware/` |
| `BUILD.gn` | Цели `ohos_prebuilt_etc`, ставящие нужные прошивки в `/lib/firmware`, группа `oneir_firmware` |
| `.gitignore` | Исключает скачанный каталог `linux-firmware/` |

## Подключение

`repo` читает локальные манифесты только из `.repo/local_manifests/` в корне дерева, поэтому манифест подключается ссылкой (один раз на машину сборки):

```bash
cd <корень дерева OpenHarmony>
mkdir -p .repo/local_manifests
ln -sf ../../oneir/firmware/linux-firmware.xml .repo/local_manifests/linux-firmware.xml
repo sync -c oneir/firmware/linux-firmware
```

Путь `path` в манифесте должен совпадать с расположением этого репозитория в дереве (`oneir/firmware`) плюс `/linux-firmware`.

## Сборка

```bash
./oneir/make.sh oneir/firmware:oneir_firmware
```

Прошивки ставятся в `/lib/firmware` — стандартный путь, где ядро ищет их само, без дополнительных параметров. Подкаталог задаётся так, как его ожидает драйвер: например, `ath10k` ищет файлы в `ath10k/QCA9377/hw1.0/`, а `rt2800usb` — прямо в `/lib/firmware`.


## Как добавить прошивку

1. Найти нужный файл в `linux-firmware/` (имя файла драйвер пишет в журнал ядра при попытке загрузки).
2. Добавить цель `ohos_prebuilt_etc` в `BUILD.gn` с правильным `module_install_dir` и включить её в группу `oneir_firmware`.
3. Добавить имя цели в список прошивок скрипта сборки образа.

## Как обновить версию

Изменить `revision` в `linux-firmware.xml` на новый тег `linux-firmware` и выполнить `repo sync -c oneir/firmware/linux-firmware`.

## Лицензии

Прошивки распространяются на условиях лицензий их производителей. Тексты лицензий (`LICENCE.*`) и соответствие файлов лицензиям (`WHENCE`) находятся в корне `linux-firmware/`.
