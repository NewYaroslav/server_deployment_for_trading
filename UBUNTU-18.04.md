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












