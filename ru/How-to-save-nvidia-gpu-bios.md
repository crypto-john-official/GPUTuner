
## Как сохранить биос Nvidia видеокарты через GPU Tuner

- Откройте GPU Tuner приложение

```bash
sudo ./GPUTuner
```

- Найдите `Bios` секцию

<img src="../images/nvidia-bios-backup-gputuner-01.png">

- Выберите директорию, где сохранить биос видеокарты

<img src="../images/nvidia-bios-backup-gputuner-02.png">

- Нажмите `Save BIOS` кнопку и подтвердите `OK` чтобы продолжить

<img src="../images/nvidia-bios-backup-gputuner-03.png">

- После этого экран временно погаснет примерно на минуту
- После включения экрана перезагрузите систему (по желанию)
- После перезагрузки вы можете найти файл биоса в директории которую выбирали
- Формат имени биоса `${BIOS_VERSION}-backup.rom`

## Полное видео как сохранить биос

[<img src="../images/youtube-02.png">](https://youtu.be/H2hF5Yhlu58)
