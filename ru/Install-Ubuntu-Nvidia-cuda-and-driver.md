
## Nvidia CUDA и Драйвер установка

- Откройте `Показать приложения` окно

<img src="./images/Ubuntu-Applications-Button.png" width="80%"/>

- Найдите `Software Updater` и запустите. Эта программа предложит вам обновить одни пакеты и удалить другие ненужные. Вам следует согласиться со всеми обновлениями и удалениями пакетов, иначе CUDA установка может конфликтовать с пакетами, которые были установлены ранее Ubuntu установщиком (ISO файл). После того как необходимые пакеты обновятся или удалятся `Sowftware Updater` приложение предложит вам перезагрузить систему, чтобы все изменения применились. После перезагрузки мы готовы к установке Nvidia CUDA и драйверов.

<img src="../images/Ubuntu-Software-updater-01.png" width="60%"/>
<img src="../images/Ubuntu-Software-Updater-Partial-update.png" width="60%"/>
<img src="../images/Ubuntu-Software-updater-ask-start-partial-upgrade.png" width="80%"/>
<img src="../images/Ubuntu-Software-updater-remove-packages.png" width="80%"/>
<img src="../images/Ubuntu-Software-updater-02.png" width="60%"/>
<img src="../images/Ubuntu-Software-updater-upgrade-completed.png" width="60%"/>

- Теперь в `Показать приложения` найдите `Терминал` и запустите его

<img src="./images/Ubuntu-Find-Terminal-App.png" width="80%"/>

- Затем откройте Nvidia CUDA сайт по адресу https://developer.nvidia.com/cuda-downloads и выберите платформу `Ubuntu 20.04` -> `deb (network)`.

<img src="../en/images/Ubuntu-Cuda-Select.png" width="80%"/>

- Для установки Nvidia драйвера и Cuda вам нужно выполнить все команды из `Installation Instructions` секции на скриншоте выше (там описано 6 команд)

<img src="../en/images/Cuda-installation-deb.png" width="80%"/>

- Перед выполнением последней команды `sudo apt-get -y install cuda` рекомендуем закрыть `терминал` и запустить эту команду в новом терминале.

- После последней команды `sudo apt-get -y install cuda` перезагрузите систему. После этого Driver и Cuda должны быть установлены.
