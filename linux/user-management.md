# User management

## Table of content
- [Common](#common)
- [CRUD Users](#crud-users)
- [CRUD User Groups](#crud-user-groups)
- [Add root access](#add-root-access)
- [User Passwords](#user-passwords)
- [Блокировка пользователей](#блокировка-пользователей)

## Common
1. **`whoami`** - показывает имя учетной записи
1. **`who`** - предоставляет информацию о том, какие пользователи осуществили вход в систему
1. **`whoami`** - выводит информацию о сессии текущего пользователя
1. **`w`** - показывает информацию о пользователях, которые осуществили вход в систему, а так же о том, чем они занимаются
1. **`id`** - показывает идентификатор текущего пользователя, основной идентификатор группы, а так же список групп, в которых состоит текущий пользователя.
    ```bash
    $ id
    uid=1000(max) gid=1000(max) groups=1000(max),4(adm),24(cdrom),27(sudo),30(dip),46(plugdev),108(lpadmin),124(sambashare),999(docker)
    ```
1. **`su`** - позволяет работать от другого пользователя системы
    ```bash
    su tania
    su root
    ```

    * по умолчанию при смене пользователя утилита `su` оставляет сохранение переменных окружения командной оболочик. Для того, чтобы работать от лица другого пользователя в окружении командной оболочки целевого пользователя, следует применять команду **`su -`**

        ```bash
        su - laura
        ```

    * если после `su` не указанно имя пользователя то считает что целевым пользователем является `root`.

1. **`sudo`** - позволяет пользователю осуществлять запуск программ с привелегиями других пользователей. Для того, чтобы данная утилита работала, системный администратор должен отредактировать (с помощью команды **`visudo`**) файл `/etc/sudoers`. Утилита `sudo` может оказаться полезной в случае возникновения необходимости делегирования административных задач другому пользователю (без передачи этому пользователю пароля пользователя root).

1. В некоторых дистрибутивах Linux, таких, как Ubuntu и Xubuntu пароль пользователя `root` изначально не установлен. Это значит, что не имеется возможности войти в систему под именем пользователя `root`. Для выполнения задач от лица пользователя `root` **_первому_** пользователю системы предоставляется возможность использования утилиты `sudo` благодаря добавлению специальной записи в файл `/etc/sudoers`.

    * Фактически все пользователи, являющиеся членами группы `admin`, также могут использовать утилиту `sudo` для исполнения команд от лица пользователя `root`.
        ```bash
        $ sudo grep admin /etc/sudoers
        # Members of the admin group may gain root privileges
        %admin ALL=(ALL) ALL
        ```
    * В результате **_первый_** пользователь работать от лица пользователя `root`
        ```bash
        sudo su -
        ```

## CRUD Users
1. Files with default settings
    * `/etc/default/useradd`
    * `/etc/login.defs`
    * `/etc/skel` - content of this directory will be copied to new user's home

1. Локальная база данных учетных записей пользователей расположена в файле **`/etc/passwd`**.
    ```bash
    $ cat /etc/passwd | grep -E 'max|root'
    root:x:0:0:root:/root:/bin/bash
    max:x:1000:1000:max,,,:/home/max:/bin/bash
    ```

    * В столбцах (разделённых двоеточикем) содержится следующая информация:
        * имя пользователя
        * симол х
        * идентификатор пользователя
        * идентификатор основной группы пользователя
        * описание учетной записи пользователя
        * пусть к домашней директории пользователя
        * пусть к исполняемому файлу командной оболочки.

    * `root` всегда получает идентификатор 0
    * дополнительная информация может быть взята из справки:
        ```bash
        man 5 passwd
        ```
1. **`useradd`** - добавляет пользователя в систему
    ```bash
    $ useradd -m -d /home/yanina -c "yanina wickmayer" yanina
    $ tail -1 /etc/passwd
    yanina:x:529:529:yanina wickmayer:/home/yanina:/bin/bash
    ```

    * **`/etc/default/useradd`** - файл содержащий параметры по умолчанию для новых добавляемых в систему пользователей.

    * **`useradd -D`** - просмотр файла с дефолтными настройками для новых пользователей.

1. **`adduser`** - shortcut for adding user interactively

1. **`userdel`** - удаляет пользователя из системы
    ```bash
    userdel -r yanina
    ```

    * **`-r`** - удаляет так же и домашнюю директорию пользователя

1. **`usermod`** - модифицирует параметры указанной записи
    ```bash
    $ tail -1 /etc/passwd
    harry:x:516:520:harry potter:/home/harry:/bin/bash
    $ usermod -c 'wizard' harry
    $ tail -1 /etc/passwd
    harry:x:516:520:wizard:/home/harry:/bin/bash
    ```

1. **`/etc/skel`** - в случае использования параметра `-m` утилиты `useradd` содержимое директории `/etc/skell` копируется в создаваемую домашнюю директорию пользователя. Обычно в `/etc/skell` находятся скрыте файлы, которые содержат стандартные параметры профиля пользвователя и значения параметров приложений.
    ```bash
    $ ls -la /etc/skel/
    total 40
    drwxr-xr-x   2 root root  4096 май 20 19:20 .
    drwxr-xr-x 144 root root 12288 авг 27 15:40 ..
    -rw-r--r--   1 root root   220 сен  1  2015 .bash_logout
    -rw-r--r--   1 root root  3771 сен  1  2015 .bashrc
    -rw-r--r--   1 root root  8980 апр 20  2016 examples.desktop
    -rw-r--r--   1 root root   655 июн 24  2016 .profile
    ```

## CRUD User Groups
1. **`/etc/group`** - хранит информацию о членстве пользователях в группах
    ```bash
    $ tail -5 /etc/group
    max:x:1000:
    sambashare:x:124:max
    docker:x:999:max
    mysql:x:125:
    debian-tor:x:126:
    ```

    * имя группы : зашифрованный пароль группы : идентификатор группы : список членов группы

1. **`groupadd`** -  добавить группу
1. **`groups username`** - список групп в которых состоит пользователь
1. **`usermod -a -G groupname username`** - добавляет пользователя в группу
1. **`groupmod`** - изменить параметры группы (`-n` изменить имя)
1. **`groupdel`** - удалить группу
1. **`gpasswd -A`** - делегирует функции контроля над группой другому пользователю

    ```bash
    root@$ gpasswd -A serena sports # user serena now have rights to edit sports user group
    root@$ su - serena
    serena@$ gpasswd -a harry sports # add user hary to group sports
    ```

    * **`/etc/gshadow`** - содержит информацию об администраторах групп пользователей
    * **`gpasswd -A "" groupname`** - очищает список администраторов группы пользователей
1. **`newgrp groupname`** - создаёт _дочернюю командную оболочку_ с новой _основной группой пользователей_
    ```bash
    $ touch st.txt
    $ ls -l
    total 0
    -rw-r--r-- 1 root root 0 Sep  1 05:46 st.txt
    $ newgrp test-gr
    $ echo $SHLVL
    2
    $ touch st2.txt
    $ ls -l
    total 0
    -rw-r--r-- 1 root root    0 Sep  1 05:46 st.txt
    -rw-r--r-- 1 root test-gr 0 Sep  1 05:47 st2.tx
    ```


## Add root access
1. Information about users with root access is stored in `/etc/sudoers`
1. Add file with the following content inside `/etc/sudoers.d/`
    ```
    username ALL=(ALL) NOPASSWD:ALL
    ```

## User Passwords
1. **`passwd`** - изменяет пароль текущего пользователя.
    * Применяет несколько стандартных проверок на качество пароля.
    * Пользователь `root` не обязан следовать правилам проверки.
1. **`passwd username`** - изменяет пароль пользователя с именем username
1. **`/etc/shadow`** - хранит зашифрованные пароли пользователей. Содержит в себе
    * имя пользователя
    * зашифрованный пароль
    * время последнего изменения пароля (с 01.01.1970)
    * количество дней, в течение которых пароль должен оставаться неизменным
    * день истечения срока действия пароля
    * количество дней перед истечением срока действия пароля, по прошествии которых должно выводиться предупреждение
    * количество дней после истечения пароля, по прошествии которых учетная запись должна быть отключена
    * день отключения учетной записи (с 01.01.1970)
1. Возможно создать пользователя и сразу задать ему пароль с помощью утилиты `openssl`
    * Создание пользователя user1 с паролем hunter2
        ```bash
        useradd -m -p $(openssl passwd hunter2) user1
        ```

    * Нужно помнить что при задании пароля таким образом, он останется в истории командной оболочки

1. **`/etc/login.defs`** - содержит некоторые стандартные значения параметров паролей пользователей.
    ```bash
    $ grep PASS /etc/login.defs
    #	PASS_MAX_DAYS	Maximum number of days a password may be used.
    #	PASS_MIN_DAYS	Minimum number of days allowed between password changes.
    #	PASS_WARN_AGE	Number of days warning given before a password expires.
    PASS_MAX_DAYS	99999
    PASS_MIN_DAYS	0
    PASS_WARN_AGE	7
    #PASS_CHANGE_TRIES
    #PASS_ALWAYS_WARN
    #PASS_MIN_LEN
    #PASS_MAX_LEN
    ```

1. **`chage`** - устанавливает дату истечения срока действия пользовательской учетной записи (`-E`), установка минимального (`-m`), максимального (`-M`) срока действия пароля и других параметров паролей.
    * **`-l`** - позволяет ознакомиться с текущими параметрами пароля для указанной записи
        ```bash
        ~$ chage -l $USER
        Last password change					: окт. 29, 2016
        Password expires					: never
        Password inactive					: never
        Account expires						: never
        Minimum number of days between password change		: 0
        Maximum number of days between password change		: 99999
        Number of days of warning before password expires	: 7
        ```

## Блокировка учетных записей
1. Если пароль в `/etc/shadow` начинается с `!` то учетная запись не может использоваться.
1. **`usermod -L username`** - блокировка записи
1. **`usermod -U username`** - разблокировка записи
