# openvpn-DCO-conf
Openvpn with DCO on VPS
Инструкция ориентирована на совсем новчиков, и систему ubuntu

OpenVPN презентовал модуль ядра, который обещает ускорить его до уровня wireguard. Но легкость настройки увы все также сложнее чем wireguard.
Чтобы воспользоваться данной фичей нужно будет все собирать из исходников. По умолчанию разработчики обещают включить поддержку в версии 2.6. Но пока в тестовых сборка поддержки DCO нет

1)И так начнем.
Нам нужно будет скачать исходжники openvpn и модуля ядра Openvpn-DCO
    - https://github.com/OpenVPN/openvpn
    - https://github.com/OpenVPN/ovpn-dco
    
Скачиваем любым удобным вам способом, Рекомендую скачивать ZIP архив и распаковывать это все в директорию /home

2) Открываем терминал и установим все необходимые пакеты для сборки
    sudo apt-get update
    sudo apt-get install build-essential intltool libtool m4 linux-headers cmake libuv1-dev libmicrohttpd-dev autoconf pkg-config libnl-genl-3-dev libcap-ng-dev libssl-dev libpam0g-dev automake
    
 Выпадет сообщение что linux-headers представляет вирьуальный линк на большое вариантов этого пакета под разные ядра. Нужно будет установить вариант который подходит под ваше ядро., узнать версию ядр можно в свою очередь командой uname -r
 
