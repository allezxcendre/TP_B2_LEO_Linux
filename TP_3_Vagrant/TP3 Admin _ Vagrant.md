# TP3 Admin : Vagrant



🌞 **Générer un `Vagrantfile`**

```
PS C:\Users\Alexandre\Vagrant\test>Vagrant init generic/ubuntu2204
```

🌞 **Modifier le `Vagrantfile`**



```ruby
Vagrant.configure("2") do |config|
  # The most common configuration options are documented and commented below.
  # For a complete reference, please see the online documentation at
  # https://docs.vagrantup.com.

  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://vagrantcloud.com/search.
  config.vm.box = "generic/ubuntu2204"

  # Disable automatic box update checking. If you disable this, then
  # boxes will only be checked for updates when the user runs
  # `vagrant box outdated`. This is not recommended.
  config.vm.box_check_update = false
  config.vm.synced_folder ".", "/vagrant", disabled: true
```

🌞 **Faire joujou avec une VM**

```
PS C:\Users\Alexandre\Vagrant\test> vagrant up


PS C:\Users\Alexandre\Vagrant\test> vagrant status
Current machine states:

default                   running (virtualbox)

The VM is running. To stop this VM, you can run `vagrant halt` to
shut it down forcefully, or you can run `vagrant suspend` to simply
suspend the virtual machine. In either case, to restart it again,
simply run `vagrant up`.

$ PS C:\Users\Alexandre\Vagrant\test> vagrant ssh
vagrant@ubuntu2204:~$


PS C:\Users\Alexandre\Vagrant\test> vagrant halt  
==> default: Attempting graceful shutdown of VM...

```


## 2. Repackaging


🌞 **Repackager la box que vous avez choisie**


```
PS C:\Users\Alexandre\Vagrant\test> vagrant package --output super_box.box

PS C:\Users\Alexandre\Vagrant\test> vagrant box add super_box super_box.box

PS C:\Users\Alexandre\Vagrant\test> vagrant box list
generic/ubuntu2204 (virtualbox, 4.3.2)
super_box          (virtualbox, 0)

dans mon vagrant file:
  config.vm.box = "super_box"
```

🌞 **Ecrivez un `Vagrantfile` qui lance une VM à partir de votre Box**
```

PS C:\Users\Alexandre\Vagrant\test> vagrant ssh                            
Last login: Thu Jan 11 13:16:51 2024 from 10.0.2.2
vagrant@ubuntu2204:~$ sudo ufw status
Status: active

To                         Action      From
--                         ------      ----
22                         ALLOW       Anywhere
22 (v6)                    ALLOW       Anywhere (v6)
```
## 3. Moult VMs



🌞 **Adaptez votre `Vagrantfile`** pour qu'il lance les VMs suivantes (en réutilisant votre box de la partie précédente)


📁 **`partie1/Vagrantfile-3A`** dans le dépôt git de rendu


🌞 **Adaptez votre `Vagrantfile`** pour qu'il lance les VMs suivantes (en réutilisant votre box de la partie précédente)


📁 **`partie1/Vagrantfile-3B`** dans le dépôt git de rendu
