# TP4 : Vers une maîtrise des OS Linux


## 1. LVM dès l'installation

🌞 **Faites une install manuelle de Rocky Linux**


```
[alexandre@localhost ~]$ lsblk
NAME        MAJ:MIN RM  SIZE RO TYPE MOUNTPOINTS
sda           8:0    0 40.1G  0 disk
├─sda1        8:1    0    2G  0 part /boot
└─sda2        8:2    0   21G  0 part
  ├─rl-root 253:0    0   10G  0 lvm  /
  ├─rl-swap 253:1    0    1G  0 lvm  [SWAP]
  ├─rl-var  253:2    0    5G  0 lvm  /var
  └─rl-home 253:3    0    5G  0 lvm  /home
sr0          11:0    1 1024M  0 rom

[alexandre@localhost ~]$ df -h
Filesystem           Size  Used Avail Use% Mounted on
devtmpfs             4.0M     0  4.0M   0% /dev
tmpfs                1.1G     0  1.1G   0% /dev/shm
tmpfs                430M  5.9M  424M   2% /run
/dev/mapper/rl-root  9.8G  904M  8.4G  10% /
/dev/mapper/rl-home  4.9G   40K  4.6G   1% /home
/dev/mapper/rl-var   4.9G   98M  4.5G   3% /var
/dev/sda1            2.0G  228M  1.8G  12% /boot
tmpfs                215M     0  215M   0% /run/user/2000


[alexandre@localhost ~]$ sudo pvs
PV         VG Fmt  Attr PSize  PFree
/dev/sda2  rl lvm2 a--  21.00g 4.00m

[alexandre@localhost ~]$ sudo lvs
  LV   VG Attr       LSize  Pool Origin Data%  Meta%  Move Log Cpy%Sync Convert
  home rl -wi-ao----  5.00g
  root rl -wi-ao---- 10.00g
  swap rl -wi-ao----  1.00g
  var  rl -wi-ao----  5.00g
```

## 2. Scénario remplissage de partition

🌞 **Remplissez votre partition `/home`**



```
[alexandre@localhost home]$ dd if=/dev/zero of=/home/alexandre/bigfile bs=4M count=10000
dd: error writing '/home/alexandre/bigfile': No space left on device
1171+0 records in
1170+0 records out
4911124480 bytes (4.9 GB, 4.6 GiB) copied, 19.7989 s, 248 MB/s
```


🌞 **Constater que la partition est pleine**

```
[alexandre@localhost home]$ df -h
/dev/mapper/rl-home  4.9G  4.6G     0 100% /home
```
🌞 **Agrandir la partition**

```
[alexandre@localhost ~]$ sudo lvextend -l +100%FREE /dev/rl/home
[alexandre@localhost ~]$ sudo resize2fs /dev/mapper/rl-home
```

🌞 **Remplissez votre partition `/home`**


```
[alexandre@localhost ~]$dd if=/dev/zero of=/home/alexandre/bigfile bs=4M count=5000
```


🌞 **Utiliser ce nouveau disque pour étendre la partition `/home` de 40G**
```
[alexandre@localhost ~]$ sudo pvcreate /dev/sdb
[sudo] password for alexandre:
  Physical volume "/dev/sdb" successfully created.
  
[alexandre@localhost ~]$ sudo vgextend rl /dev/sdb
Volume group "rl" successfully extended

[alexandre@localhost ~]$ sudo resize2fs /dev/rl/home

[alexandre@localhost ~]$sudo lvextend -l +100%FREE /dev/rl/home

[alexandre@localhost ~]$ df -h
/dev/mapper/rl-home   45G  4.6G   38G  11% /home
```


# II. Gestion de users



🌞 **Gestion basique de users**


```
alice:x:2001:2001::/home/alice:/bin/bash
bob:x:2002:2002::/home/bob:/bin/bash
charlie:x:2003:2003::/home/charlie:/bin/bash
eve:x:2004:2004::/home/eve:/bin/bash
backup:x:2005:2005::/var/backup:/usr/sbin/nologin
```


🌞 **La conf `sudo` doit être la suivante***

```
[alexandre@localhost ~]$ sudo visudo

# Groupe admins
%admins ALL=(ALL:ALL) NOPASSWD: ALL

# User eve
eve ALL=(backup) PASSWD: /bin/ls
🌞 **Le dossier `/var/backup`**

```
```
[root@localhost backup]# sudo mkdir /var/backup/
mkdir: cannot create directory ‘/var/backup/’: File exists
[root@localhost backup]# chown backup:backup /var/backup
[root@localhost backup]# chmod 700 /var/backup uniquement le lire

[alexandre@localhost ~]$ sudo touch /var/backup/precious_backup
 [alexandre@localhost ~]$ sudo chmod 640 /var/backup/precious_backup
 [alexandre@localhost ~]$ sudo chown backup:backup /var/backup/precious_backup
```

🌞 **Mots de passe des users, prouvez que**

```
alice:$6$c23OOAYrrfOpHFKm$5LTzFg7awJbSkPK/JJyNCKDC57tAO59BFbD7xH6NK3UszQ2agdxqc/iULZqpTq3kTwwuvSnf1DLCEkuT0MjmV0:19734:0:99999:7:::
bob:$6$nBHQl0nKtox4EILa$gNHzOlxFY8BZRC1/Gta.CSoUs3QX2Vy.1l/.lgLKNJ4aG0vrYxCByM2JwmnXPXfj60X1fHJcUdH6GMyD5QRF0.:19734:0:99999:7:::
charlie:$6$6wCWpQKR/LoZR27a$BMIYyhf3m4u4DwYgjRZo0lPtxEP10VctM2zgHvWJ6UdNlVUdhzImTyqOi04K/8pcqF0jcDT/jQ0OYqGUF7gqw/:19734:0:99999:7:::
eve:$6$jusnrC1ImA0KWzlO$n5t2O8t8STW5j6ai.XlsQElVMRQgf9mnY.bJ1gTpvpmmqNL40MzPTGRqrAbbokEpnAx5gmVjs.rM3q2hHq5mR0:19734:0:99999:7:::
backup:$6$Vtu885HooRbdCLgl$EUoZpsoXTUyKVvW39lx3Wa/XUACKkfvFHD4VEThvexPjjfhEBUTb7rYvRjndIlBtycbRfwKahGrNJ3qYRBvAG0:19734:0:99999:7:::
```

🌞 **User eve**

```
[eve@localhost ~]$ sudo -l
[sudo] password for eve:
Matching Defaults entries for eve on localhost:
    !visiblepw, always_set_home, match_group_by_gid,
    always_query_group_plugin, env_reset, env_keep="COLORS DISPLAY HOSTNAME
    HISTSIZE KDEDIR LS_COLORS", env_keep+="MAIL PS1 PS2 QTDIR USERNAME LANG
    LC_ADDRESS LC_CTYPE", env_keep+="LC_COLLATE LC_IDENTIFICATION
    LC_MEASUREMENT LC_MESSAGES", env_keep+="LC_MONETARY LC_NAME LC_NUMERIC
    LC_PAPER LC_TELEPHONE", env_keep+="LC_TIME LC_ALL LANGUAGE LINGUAS
    _XKB_CHARSET XAUTHORITY", secure_path=/sbin\:/bin\:/usr/sbin\:/usr/bin

User eve may run the following commands on localhost:
    (backup) PASSWD: /bin/ls
```
# III. Gestion du temps

![Timing](./img/timing.jpg)


🌞 **Je vous laisse gérer le bail vous-mêmes**
```
[alexandre@localhost ~]$ systemctl list-units -t service -a | grep ntp
  initrd-parse-etc.service                   loaded    inactive dead    Mountpoints Configured in the Real Root
● ntpd.service                               not-found inactive dead    ntpd.service
● ntpdate.service                            not-found inactive dead    ntpdate.service
● sntp.service                               not-found inactive dead    snt.serviceous êtes synchronisés sur l'heure de Paris


[alexandre@localhost ~]$ sudo cat /etc/chrony.conf
# Use public servers from the pool.ntp.org project.
# Please consider joining the pool (https://www.pool.ntp.org/join.html).
#pool 2.rocky.pool.ntp.org iburst
pool pool.ntp.org 
[alexandre@localhost ~]$ sudo systemctl restart chronyd
[alexandre@localhost ~]$ timedatectl 
               Local time: Fri 2024-01-12 16:09:50 CET
           Universal time: Fri 2024-01-12 15:09:50 UTC
                 RTC time: Fri 2024-01-12 15:09:50
                Time zone: Europe/Paris (CET, +0100)
System clock synchronized: no
              NTP service: active
          RTC in local TZ: no
```    
> systemd fournit un outil en ligne de commande `timedatectl` qui permet de voir des infos liées à la gestion du temps