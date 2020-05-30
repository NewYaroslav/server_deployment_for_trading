# Инструкция по развертыванию сервера для алготрейдинга для ОС Ubuntu 18.04

Данная инстркукция подходит для работы с **Ubuntu 18.04 x86-64**

## Подключение к серверу

* Для подключения к серверу из среды Windows можно использовать [putty](https://www.putty.org/)
* После запуска putty указываем IP-адрес, открываем соединение.
* Если появляется всплывающее окно с предупреждением, нажимаем *да*
* В поле *login* указываем пользователя root, пароль указываем тот, который пришел на почтовый ящик от хостера, или который вы сами задали на сайте хостера. Пароль во время ввода в консоль pytty не видно, это нормально.
* Для копирования текста из консоли подходит сочетание клавиш *CTRL+V*

## Установка xdrp для удаленного подключения

* Обновляем список доступных пакетов:

```
sudo apt-get update
```

* Установите и включите утилиту xRDP:

```
sudo apt-get install xrdp
sudo systemctl enable xrdp
```

* Установите окружение xfce и разрешите xRDP его использовать:

```
sudo apt-get install xfce4 xfce4-terminal
sudo sed -i.bak '/fi/a #xrdp multiple users configuration n xfce-session n' /etc/xrdp/startwm.sh
```
FAQ по xfce: https://wiki.xfce.org/ru/faq

* Откройте порт RDP для возможности удаленного подключения:

```
sudo ufw allow 3389/tcp
```

* Перезагрузите xRDP сервер, чтобы изменения вступили в силу:

```
sudo /etc/init.d/xrdp restart
```

## Подключение к рабочему столку

* Для подключения откройте приложение Windows Подключение к удаленному рабочему столу. Введите IP-адрес сервера и имя пользователя и нажмите Подключить
* Если возникнет необходимость передавать файлы на сервер, можно указать это в настройках программы для подключения. 
* При подключении появится предупреждение безопасности, это связано с тем, что происходит соединение с ОС семейства Linux. Нажмите *Да*.
* В открывшемся окне в качестве сессии выборе Xorg, введите пароль для пользователя, нажмите *OK*.
* Для Windows можно скачать RDP-клиент: https://www.microsoft.com/en-us/download/details.aspx?id=50042

## Автологин

* Установка Display Manager

```
sudo apt-get install gdm3
```

* Как узнать какой используется менеджер отображения

```
systemctl status display-manager.service
```

* Для включения автоматического входа с GDM, добавьте в файл /etc/gdm/custom.conf

```
sudo nano /etc/gdm3/custom.conf
```

Следующие строки:

```
# Включение автоматического входа для пользователя
[daemon]
AutomaticLogin=имя_пользователя
AutomaticLoginEnable=True
```

* Настройка входа без пароля. добавьте следующую строку в начало файла /etc/pam.d/gdm-password

```
auth sufficient pam_succeed_if.so user ingroup nopasswdlogin
```

Затем добавьте группу nopasswdlogin в вашу систему. Для этого выполните

```
sudo groupadd nopasswdlogin
```

Теперь добавьте своего пользователя в группу nopasswdlogin:

```
usermod -a -G nopasswdlogin $USER
```

После этого вам будет достаточно кликнуть на вашем имени пользователя для входа.

Предупреждения:

Не делайте это с аккаунтом root.
Вы больше не сможете изменить тип вашей сессии при входе в GDM. Если вы хотите поменять ваш тип сессии по умолчанию, вам нужно сначало удалить вашего пользователя из группы nopasswdlogin.

## Установка Wine

Для работы Metatrader на Linux нужна такая полезная штука, как Wine. Wine позволяет запускать приложения Windows на Linux.

Проще установить старую версию Wine, ее вполне достаточно для работы.

```
sudo dpkg --add-architecture i386
wget -nc https://dl.winehq.org/wine-builds/Release.key
sudo apt-key add Release.key
sudo add-apt-repository "deb https://dl.winehq.org/wine-builds/ubuntu/ artful main"
sudo apt-get update
sudo apt-get install --install-recommends winehq-stable
```

При помощи Wine можно запускать **.exe** и даже **.bat** файлы. 
Пример c **.exe**:

```
wine /root/_repoz/example/bin/example.exe
```

Пример c **.bat**:

```
wine start example.bat
```

Также можно задать директорию выполнения 

```
wine start /d "/root/_repoz/" example.bat
```

Про Linux скрипты можно почитать здесь: https://linuxrussia.com/sh-ubuntu.html

## Установка chrome

* Первый вариант установки

```
sudo apt-get install chrome
sudo apt-get update
sudo apt-get upgrade
sudo google-chrome-stable --no-sandbox
```

* Второй вариант

```
wget -q -O - https://dl-ssl.google.com/linux/linux_signing_key.pub | sudo apt-key add - 
sudo sh -c 'echo "deb https://dl.google.com/linux/chrome/deb/ stable main" >> /etc/apt/sources.list.d/google.list'
sudo apt-get update
sudo apt-get install google-chrome-stable
sudo google-chrome-stable --no-sandbox
```

## Настройка переменных окружений

Глобальные переменные окружения для всей системы можно задать здесь: */etc/environment*.
Для этого можно открыть файл при помощи *nano*:

```
nano /etc/environment
```

Далее в файл можно занести свою переменную, к примеру:

```
EXAMPLE_DIR=/root/_repoz/example/
```

Проверить переменную окружения можно в терминале:

```
echo $EXAMPLE_DIR
```

## Настройка времени сервера

Есть множество статей на данную тему:
https://linuxconfig.org/how-to-change-timezone-on-ubuntu-18-04-bionic-beaver-linux
https://askubuntu.com/questions/323131/setting-timezone-from-terminal/524362#524362
https://linuxize.com/post/how-to-set-or-change-timezone-on-ubuntu-18-04/
https://askubuntu.com/questions/138423/how-do-i-change-my-timezone-to-utc-gmt
https://pingvinus.ru/note/timezone#tzChangeLocal

Например, можно сделать так:

```
sudo dpkg-reconfigure tzdata
```

Далее, чтбы выбрать время UTС, необходимо выбрать в первом окне *None of the above* или *Etc*, во втором окне *UTC*

Еще вариант:

```
timedatectl set-timezone UTC
```

## Установка Metatrader

Metatrader устанавливается как обычно. Есть лишь некоторые ньюансы:

* Папка песочницы Metatrader находится здесь: *home\.wine\drive_c\Program Files(x84)\MetaTrader 4*
* Терминал может глючить, подвисают котировки (https://www.mql5.com/ru/forum/116975) или слетают настройки (https://www.mql5.com/ru/forum/281434). Поэтому рекомендуется сохранять шаблоны и профили http://www.tevola.ru/trading/torgovye-platformy/metatrader/profiles-i-template-v-metatrader.html
* При работе с FXCM для потока котировок лучше использовать сервер FXCMUSD-Demo02 Австралия.

## Прочее

* [Пользователи и группы](https://help.ubuntu.ru/wiki/%D0%BF%D0%BE%D0%BB%D1%8C%D0%B7%D0%BE%D0%B2%D0%B0%D1%82%D0%B5%D0%BB%D0%B8_%D0%B8_%D0%B3%D1%80%D1%83%D0%BF%D0%BF%D1%8B)
* [Как посмотреть переменные окружения в Linux](https://webhamster.ru/mytetrashare/index/mtb0/131478862068iuw07f6q)
* [Как Дать Пользователю Права Root](https://www.shellhacks.com/ru/how-to-grant-root-access-user-root-privileges-linux/)
* [Запустить команду от другого пользователя](https://linux-notes.org/zapustit-komandu-ot-drugogo-pol-zovatelya-v-unix-linux/)
* [Автозапуск программы в xfce](https://www.linux.org.ru/forum/admin/5340361)
* [КАК ДАТЬ ПРАВА НА ПАПКУ ПОЛЬЗОВАТЕЛЮ LINUX](https://losst.ru/kak-dat-prava-na-papku-polzovatelyu-linux)
* [Установка RDP сервера](https://hostiq.ua/wiki/install-rdp-server/)
* [АВТОЗАГРУЗКА LINUX](https://losst.ru/avtozagruzka-linux#%D0%90%D0%B2%D1%82%D0%BE%D0%B7%D0%B0%D0%B3%D1%80%D1%83%D0%B7%D0%BA%D0%B0_%D0%BE%D0%BA%D1%80%D1%83%D0%B6%D0%B5%D0%BD%D0%B8%D1%8F_%D1%80%D0%B0%D0%B1%D0%BE%D1%87%D0%B5%D0%B3%D0%BE_%D1%81%D1%82%D0%BE%D0%BB%D0%B0)
* [XFCE - переключение пользователя](https://archlinux.org.ru/forum/topic/3584/)
* [Как удалить пользователя в Linux с помощью команды userdel](https://andreyex.ru/operacionnaya-sistema-linux/kak-udalit-polzovatelya-v-linux-s-pomoshhyu-komandy-userdel/)


