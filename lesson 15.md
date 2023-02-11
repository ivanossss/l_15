# Lesson 15.


## 1 создать файл config в котором перечислены значения вида key:value, создать скрипт который будет читать этот файл и выводить на экран в виде key=value

```sh
ivan@ubuntu:~$ cat config
my_name:ivan
yo:25
some_quote:"hoping is a bad strategy"

ivan@ubuntu:~$ ./my_script.sh
my_name=ivan
yo=25
some_quote="hoping is a bad strategy"

ivan@ubuntu:~$ cat my_script.sh

#!/bin/bash
exec 0< config
while read line
do
echo "$line" | sed "s/:/=/g"
done

```
## 2 создать файл commands в котором перечислены значения вида name:command, создать скрипт который будет читать этот файл и запускать команды из него

```sh
ivan@ubuntu:~$ cat commands
print_work_directory:pwd
time:date
name_our_release:uname -a
who_is_in_our_VM:who

ivan@ubuntu:~$ cat second_script.sh
#!/bin/bash
exec 0< commands
awk -F: '{print $2}' commands > 3
exec 0< 3
count=1
while read line
do
name=$(grep $line commands | awk -F: '{print $1}')
echo -e "We are watching $name \n \n`$line` \n"
done

ivan@ubuntu:~$ ./second_script.sh
We are watching print_work_directory

/home/ivan

We are watching time

Sat Feb  4 15:33:07 UTC 2023

We are watching name_our_release

Linux ubuntu 5.15.0-58-generic #64-Ubuntu SMP Thu Jan 5 11:43:13 UTC 2023 x86_64 x86_64 x86_64 GNU/Linux

We are watching who_is_in_our_VM

ivan     tty1         2023-01-27 09:37
ivan     pts/0        2023-01-27 09:38
ivan     pts/2        2023-01-27 09:39 (192.168.56.1)

```
## 3 открыть консоль и узнать pid текущего процесса оболочки, открыть вторую консоль и отправить в первую консоль любое сообщение с помощью дескрипторов, тем же способом послать вывод команды lsblk

```sh
ivan@ubuntu:~$ $$
1069: command not found

На другой консоли
ivan@ubuntu:~$ "hello" > /proc/1069/fd/1

На первой
ivan@ubuntu:~$ hello

На второй 
ivan@ubuntu:~$ lsblk > /proc/1069/fd/1

На первой
ivan@ubuntu:~$ NAME   MAJ:MIN RM   SIZE RO TYPE MOUNTPOINTS
loop1    7:1    0    62M  1 loop /snap/core20/1587
loop2    7:2    0  79.9M  1 loop /snap/lxd/22923
loop3    7:3    0  49.8M  1 loop /snap/snapd/17950
loop4    7:4    0  63.3M  1 loop /snap/core20/1778
loop5    7:5    0 111.9M  1 loop /snap/lxd/24322
sda      8:0    0  15.1G  0 disk
├─sda1   8:1    0     1M  0 part
└─sda2   8:2    0  15.1G  0 part /
sr0     11:0    1  1024M  0 rom

```
## 4 создать приглашение консоли в виде “полный путь - перенос строки - зеленый знак “$” если не root, а если root то тоже самое но красный цвет и знак “#”

```sh
ivan@ubuntu:~$ echo 'PS1="\w\n\e[32m$\e[m"' >> /home/ivan/.bashrc
~
$sudo su
root@ubuntu:/home/ivan# echo 'PS1="\w\n\e[31m#\e[m"' > /root/.bashrc
/home/ivan
#

```
## 5 создать пользователя со стандартным расположением домашней директории и сменить ему домашнюю директорию на /tmp на постоянной основе

```sh
ivan@ubuntu:~$ sudo adduser task5
Adding user `task5' ...
Adding new group `task5' (1001) ...
Adding new user `task5' (1001) with group `task5' ...
...
Is the information correct? [Y/n]
ivan@ubuntu:~$
ivan@ubuntu:~$ sudo usermod -d /tmp task5
ivan@ubuntu:~$ cat /etc/passwd |grep 1001
task5:x:1001:1001:,,,:/tmp:/bin/bash


```
## 6 создать задание в CRON, запускающее скрипт, выполняющий lsblk с выводом в файл, с подавлением вывода stdout и stderr в самом кроне

```sh
ivan@ubuntu:~$ cat task6.sh
#!/bin/bash
lsblk > lsblk.txt

ivan@ubuntu:~$ chmod ugo+x task6.sh

Добавляем в крон 
*/1 * * * * /bin/bash /home/ivan/task6.sh &> /dev/null

ivan@ubuntu:~$ journalctl -e
Feb 05 10:01:01 ubuntu CRON[1174]: pam_unix(cron:session): session opened for user ivan(uid=1000) by (uid=0)
Feb 05 10:01:01 ubuntu CRON[1175]: (ivan) CMD (/bin/bash /home/ivan/task6.sh &> /dev/null)
Feb 05 10:01:01 ubuntu CRON[1174]: pam_unix(cron:session): session closed for user ivan

```
## 7 скопировать свои скрипты в /tmp и добавить в PATH путь до /tmp, попробовать запускать просто вводя имя скрипта находясь в домашней директории.

```sh
ivan@ubuntu:~$ cp my_script.sh /tmp/
ivan@ubuntu:~$ cp second_script.sh /tmp/
ivan@ubuntu:~$ cp task6.sh /tmp/
ivan@ubuntu:~$ rm my_script.sh
ivan@ubuntu:~$ rm second_script.sh
ivan@ubuntu:~$ rm task6.sh

ivan@ubuntu:~$ export PATH=$PATH:/tmp
ivan@ubuntu:~$ echo $PATH
/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin:/tmp
ivan@ubuntu:~$ my_script.sh
my_name=ivan
yo=25
some_quote="hoping is a bad strategy"

ivan@ubuntu:~$ ls
3  commands  config  lsblk.txt

```
