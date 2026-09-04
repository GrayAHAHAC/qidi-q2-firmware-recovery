🌍 [English version](#english) | 🇷🇺 [Русская версия](#russian)

---

<a id="english"></a>
# 🌍 English Version

> [!CAUTION]
> **Warning! All actions described below are performed at your own risk!!!**

# Qidi Q2 Firmware Recovery

This procedure allows you to completely restore your Q2 to factory settings by flashing the eMMC memory with the standard OS image. This is necessary if the system doesn't boot, the network stack is broken, or a factory reset via the display interface doesn't help (the display reset only clears print statistics, not the OS).

## When this guide can help:
* Network (Wi-Fi and LAN) stopped working after making system changes.
* Klipper or the OS is in an unrecoverable state.
* The printer boots but does not function properly.

## Requirements:
* **A Windows PC.** *Important:* The USB-C cable must be connected directly to the rear USB ports of the PC's motherboard. Using front panel ports can lead to connection drops during flashing due to voltage drops and interference.
* **USB-A to USB-C cable** (must support data transfer, not just charging).
* **RKDevTool v3.19** and **DriverAssistant v5.12** (Rockchip flashing tools).
* **Q2 OS Firmware image** (`KLP_IMG_QD_Q2_V1.0.1_20250505_Release.img`).

All files are available on Google Drive: 👉 https://drive.google.com/drive/folders/1DSWuDp-SBexRj-f-FzzQahNxTc9IDZGr?usp=sharing
Alternative link: 👉 https://drive.google.com/drive/folders/1WKg1bVOYq7t6M3MXozvj9jvNutjHJRGt?usp=drive_link

Extract the archives with the firmware image and the tool. Ensure there are no Cyrillic (or non-Latin) characters in the file path, this is important!

To change RKDevTool language to English, open `config.ini` in the RKDevTool folder, set `Language=2`, and restart the program.

<img width="550" height="490" alt="image1" src="https://github.com/user-attachments/assets/8575c8bc-48ee-41d4-9b76-9fe252af348f" />

## Motherboard Location
The motherboard is located in the top left part of the printer, behind a removable panel. The USB-C port becomes accessible after removing this panel.

<img width="688" height="424" alt="image2" src="https://github.com/user-attachments/assets/6730c7f3-4658-4b85-81db-5d62b1e56c3c" />
<img width="597" height="455" alt="image3" src="https://github.com/user-attachments/assets/d3976f8e-c5ad-4d52-8a43-8dee069f7ff3" />

## Procedure

### 1. Install Drivers:
* Extract `DriverAssistant_v5.12.zip` (located in the tool folder) and run `DriverInstall.exe`.
* Click **Uninstall Driver** first (to remove old drivers and avoid conflicts), then click **Install Driver**.

<img width="319" height="342" alt="image4" src="https://github.com/user-attachments/assets/efa0e23a-40b5-46f7-9aaa-f8753b442852" />
<img width="448" height="191" alt="image5" src="https://github.com/user-attachments/assets/eb6b7db4-88c6-4f51-b57f-2b2292744cd8" />
<img width="108" height="109" alt="image6" src="https://github.com/user-attachments/assets/d9d8383f-b8c1-4cde-acb9-75dc61b8f163" />

### 2. Connect the printer and run RKDevTool:
* Connect the cable to the USB-C port on the motherboard and turn on the printer (the original English instructions suggest a different order, but I recommend plugging the cable into the powered-off printer first to avoid accidental shorts on the host board).
* Information about the connection will appear at the bottom. If it looks like the screenshot (numbers before ADB may vary), the drivers are installed correctly, and the printer is ready.
* Go to the **[Upgrade Firmware]** tab, click the **[Firmware]** button, and select the firmware image path in the Firmware field.

<img width="916" height="430" alt="image7" src="https://github.com/user-attachments/assets/b58b3479-ebe0-48d2-99fa-1b0b9e550866" />

### 3. Switch the host to Loader mode:
* Click the **Switch** button. The printer will disconnect briefly and reconnect as a Loader device. The connection mode at the bottom will change from ADB to LOADER. The console on the right will display a success message: `Switch Rockusb Success`.

<img width="916" height="430" alt="image8" src="https://github.com/user-attachments/assets/382b986b-d6ab-4e0e-9ee9-433ed6e05fd0" />

### 4. Flashing:
* Click the **Upgrade** button. Watch the flashing process in the console on the right. Wait until the progress reaches 100% and a success message appears: `Download Firmware Success`.
<img width="1940" height="912" alt="image9" src="https://github.com/user-attachments/assets/63e0417d-8a97-40e4-b98d-adf5e8f08fc2" />

### 5. First boot after flashing:
* The printer will boot with firmware version 1.0.1 and a Chinese interface. Note that there is no Russian in this specific firmware build.
* Change the language to English (or your preferred language) in the display settings.
* Connect to a Wi-Fi network.
* Perform an online update via the display menu or an offline update via USB drive to update the firmware to the latest version.

## Troubleshooting
If something goes wrong and the data transfer is interrupted (e.g., USB disconnection or power loss), the host may enter MASKROM mode instead of the normal LOADER mode.

* In this situation, RKDevTool shows **MASKROM device** instead of LOADER device.
* The Q2 display remains completely black (no Qidi logo, no boot animation).

<img width="1940" height="912" alt="image10" src="https://github.com/user-attachments/assets/56af2a7e-aeaa-491f-8fa3-12beefc8151c" />

MASKROM is a low-level recovery mode built directly into the Rockchip SoC. Unlike Loader mode, it doesn't depend on a working bootloader — the chip accepts USB commands directly from the PC. This means you can recover the device even if the previous flash was incomplete.

**How to flash from Maskrom mode:**
1. Do not turn off the printer or disconnect the USB-C cable.
2. RKDevTool will display **MASKROM device** — the Switch step is not needed.
3. Make sure the correct `.img` file is selected.
4. Click the **Upgrade** button.
5. Wait for the progress to reach 100%.

The flashing process will complete normally, and the printer will reboot with the newly installed firmware.

## Forced (Hardware) MASKROM Mode
As described above, the host can enter MASKROM automatically if a flash fails. However, if the system is so corrupted that the printer freezes at boot and the PC doesn't recognize it at all (neither as ADB nor Loader), a forced hardware switch to MASKROM is required. This is an "unbricking" procedure performed at your own risk:

1. Power off the printer completely.
2. Connect the USB-C cable to the PC (motherboard port) and to the host port.
3. Locate the **MODE** header on the board (it consists of two pins and is located near the host USB-C port).
4. Securely short these two pins together (you can use a motherboard jumper, tweezers, or a paperclip).
5. While keeping the pins shorted, turn on the printer power. The processor, unable to detect memory due to the short, will force itself into emergency boot mode.
6. Check RKDevTool: the text **Found One MASKROM Device** should appear at the bottom.
7. **CRITICAL:** Now remove the short/jumper from the MODE pins to free the memory for writing.
8. Ensure the correct firmware file is selected and click **Upgrade** (do not click Switch).
9. Wait for the process to reach 100%.

*Based on qidi-wiki, translated and adapted by: @AHAHAC*

---

<a id="russian"></a>
# 🇷🇺 Русская версия

> [!CAUTION]
> **Внимание! Все действия которые описаны ниже, вы делаете на свой страх и риск!!!*

# Прошивка Qidi Q2

С помощью этой процедуры можно полностью вернуть Q2 к заводским настройкам, перепрошив eMMC-память стандартным образом ОС. Это необходимо, если система не загружается, сетевой стек не работает или сброс к заводским настройкам через интерфейс дисплея не дает результата (сброс через дисплей очищает только статистику печати, но не ОС).

## Некоторые ситуации когда данный способ может помочь:
* Сеть (Wi-Fi и LAN) перестала работать после внесения изменений в систему
* Klipper или операционная система находятся в состоянии, из которого невозможно выйти
* Принтер загружается, но не работает

## Что потребуется:
* **ПК с Windows.** *Важно:* USB-C кабель необходимо подключать к USB-портам, расположенным непосредственно на задней панели материнской платы ПК. Использование портов на передней панели корпуса может привести к обрыву связи в процессе прошивки из-за просадок напряжения и помех.
* **Кабель USB-A — USB-C** (кабель для передачи данных, а не только для зарядки)
* **RKDevTool v3.19** и **DriverAssistant v5.12** (инструменты для перепрошивки Rockchip)
* **Образ прошивки ОС Q2** (`KLP_IMG_QD_Q2_V1.0.1_20250505_Release.img`)

Все файлы доступны на Google Диске: 👉 https://drive.google.com/drive/folders/1DSWuDp-SBexRj-f-FzzQahNxTc9IDZGr?usp=sharing
Альтернативная ссылка: 👉 https://drive.google.com/drive/folders/1WKg1bVOYq7t6M3MXozvj9jvNutjHJRGt?usp=drive_link

Разархивируем архивы с образом прошивки и программой так, чтобы в пути к ним не было кириллицы, это важно!

Чтобы перевести RKDevTool на английский язык, откройте `config.ini` в каталоге RKDevTool и выберите `Language=2`, затем перезапустите программу.

<img width="550" height="490" alt="image1" src="https://github.com/user-attachments/assets/8575c8bc-48ee-41d4-9b76-9fe252af348f" />

## Расположение материнской платы
Материнская плата расположена в верхней левой части принтера, за съемной панелью. Порт USB-C доступен после снятия этой панели.

<img width="688" height="424" alt="image2" src="https://github.com/user-attachments/assets/6730c7f3-4658-4b85-81db-5d62b1e56c3c" />
<img width="597" height="455" alt="image3" src="https://github.com/user-attachments/assets/d3976f8e-c5ad-4d52-8a43-8dee069f7ff3" />

## Процедура

### 1. Устанавливаем драйвера:
* Извлеките `DriverAssistant_v5.12.zip` (архив с драйверами расположен в папке с программой) и запустите `DriverInstall.exe`.
* Сначала нажмите **Uninstall Driver** (чтобы удалить драйвер для избежания конфликтов), затем нажмите **Install Driver** (чтобы установить).

<img width="319" height="342" alt="image4" src="https://github.com/user-attachments/assets/efa0e23a-40b5-46f7-9aaa-f8753b442852" />
<img width="448" height="191" alt="image5" src="https://github.com/user-attachments/assets/eb6b7db4-88c6-4f51-b57f-2b2292744cd8" />
<img width="108" height="109" alt="image6" src="https://github.com/user-attachments/assets/d9d8383f-b8c1-4cde-acb9-75dc61b8f163" />

### 2. Подключаем принтер и запускаем RKDevTool:
* Подключите кабель к USB-C на материнской плате и включите принтер (в англоязычной инструкции эти действия описаны в другом порядке, но я рекомендую сначала подключить кабель в обесточенный принтер, дабы избежать более серьезных последствий, вызванных случайным замыканием чего-нибудь на плате хоста).
* В нижнем поле появится информация о подключении, если всё как на скриншоте (цифры перед ADB могут отличаться), то значит драйвера установились корректно и принтер готов к дальнейшим действиям.
* Переходим во вкладку **[Upgrade Firmware]**, нажимаем кнопку **[Firmware]** и указываем путь к образу прошивки в поле Firmware.

<img width="916" height="430" alt="image7" src="https://github.com/user-attachments/assets/b58b3479-ebe0-48d2-99fa-1b0b9e550866" />

### 3. Переводим хост в режим загрузки образа прошивки:
* Нажмите кнопку **Switch**. Принтер на короткое время отключится и снова подключится в качестве устройства Loader. В нижнем поле режим подключения сменится с ADB на LOADER. В консоли справа отобразится информация об успешном переключении – `Switch Rockusb Success`.

<img width="916" height="430" alt="image8" src="https://github.com/user-attachments/assets/382b986b-d6ab-4e0e-9ee9-433ed6e05fd0" />

### 4. Прошивка:
* Нажимаем кнопку **Upgrade**. И наблюдаем за процессом прошивки слева в консоли. Дождитесь, пока индикатор выполнения дойдет до 100% и не появится информация об успешном выполнении операции по прошивке – `Download Firmware Success`.

<img width="1940" height="912" alt="image9" src="https://github.com/user-attachments/assets/63e0417d-8a97-40e4-b98d-adf5e8f08fc2" />

### 5. Первая загрузка после перепрошивки:
* Принтер загрузится с прошивкой версии 1.0.1 с интерфейсом на китайском языке. Учтите, что русского в этой прошивке нет.
* Установите английский (или другой предпочитаемый вами язык) в настройках дисплея.
* Подключитесь к сети Wi-Fi.
* Выполните онлайн-обновление через меню на дисплее или офлайн-обновление с флэшки через USB, чтобы обновить прошивку до последней версии.

## Устранение неполадок
Если вдруг что-то пошло не так и передача данных прерывается (например, при отключении USB или потере питания), хост может перейти в режим MASKROM вместо обычного режима LOADER.

* В этой ситуации RKDevTool показывает **MASKROM device** вместо LOADER device.
* Дисплей Q2 остается полностью черным (без логотипа Qidi, без анимации загрузки).

<img width="1940" height="912" alt="image10" src="https://github.com/user-attachments/assets/56af2a7e-aeaa-491f-8fa3-12beefc8151c" />

MASKROM — это низкоуровневый режим восстановления, встроенный в сам чип Rockchip. В отличие от режима Loader, он не зависит от работающего загрузчика — чип напрямую принимает USB-команды от ПК. Это значит, что вы сможете восстановить данные, даже если предыдущая прошивка была неполной.

**Процедура выхода из режима Maskrom:**
1. Не выключайте принтер и не отсоединяйте кабель USB-C.
2. RKDevTool отобразит **MASKROM device** — шаг переключения (Switch) не требуется.
3. Убедитесь, что выбран правильный `.img` файл.
4. Нажмите кнопку **Upgrade**.
5. Дождитесь, пока индикатор выполнения достигнет 100%.

Процесс перепрошивки завершится в обычном режиме, и принтер перезагрузится с установленной прошивкой.

## Принудительный (аппаратный) перевод в режим MASKROM
Как описано выше, хост может перейти в MASKROM автоматически, если процесс прошивки прервался. Однако, если система повреждена настолько серьезно, что принтер зависает при старте, а ПК его вообще не распознает (ни как ADB, ни как Loader устройство), потребуется принудительный аппаратный переход в режим MASKROM. Эту процедуру мы делаем на свой страх и риск, она предназначена для "раскирпичивания" устройства:

1. Полностью выключите принтер.
2. Подключите USB-C кабель к ПК (в порт на материнской плате) и к порту хоста.
3. Найдите на плате разъем **MODE** (он состоит из двух пинов и расположен рядом с портом USB-C хоста).
4. Надежно замкните эти два пина между собой (подойдет перемычка от материнской платы, пинцет или скрепка).
5. Не убирая перемычку, включите питание принтера. Процессор, не обнаружив память из-за замыкания, принудительно перейдет в аварийный режим загрузки.
6. Проверьте RKDevTool: внизу должна появиться надпись **Found One MASKROM Device**.
7. **КРИТИЧНО:** Теперь обязательно уберите перемычку с контактов MODE, чтобы освободить память для записи.
8. Убедитесь, что выбран правильный файл прошивки и нажмите кнопку **Upgrade** (кнопку Switch нажимать не нужно).
9. Дождитесь завершения процесса на 100%.

*Списал с qidi-wiki, перевёл и адаптировал: @AHAHAC*
