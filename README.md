# openvpn-DCO-conf

Openvpn with DCO on VPS  
Инструкция ориентирована на новчиков, и систему ubuntu

OpenVPN презентовал модуль ядра, который обещает ускорить его до уровня wireguard. Но легкость настройки увы все также сложнее чем wireguard.
Чтобы воспользоваться данной фичей нужно будет все собирать из исходников. По умолчанию разработчики обещают включить поддержку DCO(Data Channel Offload) в версии 2.6. Но пока в тестовых сборках 2.6 поддержки DCO нет  
Ниже опишу весь процесс сборки и установки. На выходе мы получим установленный модуль ядра, установленый клиент openvpn с поддержкой DCO, запуском как в качествe службы, но без поддержки компрессии трафика. 

#### 1) И так начнем
Нам нужно будет скачать исходжники openvpn и модуля ядра Openvpn-DCO
    - https://github.com/OpenVPN/openvpn
    - https://github.com/OpenVPN/ovpn-dco
    
Скачиваем любым удобным вам способом. 
Рекомендую скачивать ZIP архив и распаковывать это все в директорию /home
Ну а если у вас редакция ubuntu без GUI то нужен пакет git(гуглим как установить пакет), переходим в директорию home

    cd /home

и клонируем весь репозиторий

    git clone <ссылка на репозиторий>

Вводим команду ls и видим что создалась новая папка, в которую скачались исходные коды

#### 2) Открываем терминал и установим все необходимые пакеты для сборки
    sudo apt-get update
    sudo apt-get install build-essential intltool libtool m4 linux-headers cmake libuv1-dev libmicrohttpd-dev autoconf pkg-config libnl-genl-3-dev libcap-ng-dev libssl-dev libpam0g-dev automake libsystemd-dev
    
 Выпадет сообщение что linux-headers представляет виртуальный линк на варианты этого пакета под разнличные версии ядра линукс. Нужно будет установить вариант который подходит под ваше ядро, узнать версию ядра можно в свою очередь командой `uname -r`. НО, может оказаться что ваше ядро устаревшее и тогда пакета linux-headers может не оказаться. Исправить это можно полным апдейтом всей системы. ПрЕДУПРЕЖУ, это может сломать совместимость некоторых пользовательских програм или настроек. Итак, апдейт

    sudo apt-get update
    sudo apt-get dist-upgrade

И теперь можете снова попытаться установить нужные пакеты для сборки

#### 3) приступим к сборке модуля ядра.
Переходим в папку с расспакованными исходниками ovpn-dco и открываем терминал. Если делаете это на ситеме без GUI то вводим команду cd <путь до папки с ихсодниками>

    make

Команда выполняет компиляцию кода, если все проходит без ошибок приступаем к установке

    sudo make install
    
#### 4) Теперь приступим к сборке openpvn
Переходим в директорию с расспакованными исходниками openvpn и открываем терминал. Если делаете это на ситеме без GUI то вводим команду cd <путь до папки с ихсодниками>

    autoreconf -vi

Создаем конфигурационные файлы для компиляции пакета с нужными нам параметрами

    ./configure --enable-dco --disable-lz4 --disable-lzo --enable-systemd

Если команда завершилась и в последних 10 строчках нет "configure: error:" то все успешно,иначе гуглим свою ошибку
Приступим к сборке

    make

Устанавливаем то что собралось

    sudo make install

#### 4) Проверяем, если у вас GUI то закрываем и открываем новый терминал и вводим команду

    openvpn --version

Должно вывести что-то типо этого, жирным выделил параметры которые должны строго совпадать с тем что у меня 


OpenVPN 2.6_git x86_64-pc-linux-gnu [SSL (OpenSSL)] [EPOLL] [MH/PKTINFO] [AEAD] **[DCO]**

library versions: OpenSSL 3.0.2 15 Mar 2022

Originally developed by James Yonan

Copyright (C) 2002-2022 OpenVPN Inc <sales@openvpn.net>

Compile time defines: enable_async_push=no enable_comp_stub=no enable_crypto_ofb_cfb=yes **enable_dco=yes** enable_debug=yes enable_dlopen=unknown enable_dlopen_self=unknown enable_dlopen_self_static=unknown enable_fast_install=needless enable_fragment=yes enable_iproute2=no enable_libtool_lock=yes enable_lz4=no enable_lzo=no enable_management=yes enable_pam_dlopen=no enable_pedantic=no enable_pkcs11=no enable_plugin_auth_pam=yes enable_plugin_down_root=yes enable_plugins=yes enable_port_share=yes enable_selinux=no enable_shared=yes enable_shared_with_static_runtimes=no enable_small=no enable_static=yes enable_strict=no enable_strict_options=no **enable_systemd=yes** enable_werror=no enable_win32_dll=yes enable_wolfssl_options_h=yes enable_x509_alt_username=no with_aix_soname=aix with_crypto_library=openssl with_gnu_ld=yes with_mem_check=no with_openssl_engine=auto with_sysroot=no  

#### 5) Конец
Поздравляю, модуль ядра и openvpn утсновлены. Осталось только создать инфраструктуру сетрификатов с ключами, правильные конфиги vpn и правильные конфиги firewall. Пример конфига который работает у меня на сервре лежит в репозитории, а настройка детально описана например тут  
[www.digitalocean.com/community/tutorials/how-to-set-up-and-configure-an-openvpn-server-on-ubuntu-20-04-ru](https://www.digitalocean.com/community/tutorials/how-to-set-up-and-configure-an-openvpn-server-on-ubuntu-20-04-ru)  
и тут  
[lithium.opennet.ru/articles/openvpn/openvpn-howto.html](http://lithium.opennet.ru/articles/openvpn/openvpn-howto.html)  
[adw0rd.com/2013/1/10/openvpn/#.Ue5cqFSKaa4](https://adw0rd.com/2013/1/10/openvpn/#.Ue5cqFSKaa4)  
<a href="https://github.com/purum-pum-pum/">github.com/purum-pum-pum/</a>  
[github.com/purum-pum-pum/AIOGRAM_Python_edu](https://github.com/purum-pum-pum/AIOGRAM_Python_edu)  
