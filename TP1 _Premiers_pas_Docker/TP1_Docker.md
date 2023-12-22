# I. Init

```bash
# est-ce que le service Docker existe ?
systemctl status docker

# si oui, on le démarre alors
sudo systemctl start docker

# voyons si on peut taper une commande docker
sudo docker info
sudo docker ps
```

## 3. sudo c pa bo



🌞 **Ajouter votre utilisateur au groupe `docker`**

```
[alexandre@Docker ~]$ docker ps
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES

```


## 4. Un premier conteneur en vif


🌞 **Lancer un conteneur NGINX**
```
[alexandre@Docker ~]$ docker run -d -p 9999:80 nginx
Unable to find image 'nginx:latest' locally
latest: Pulling from library/nginx
af107e978371: Pull complete
336ba1f05c3e: Pull complete
8c37d2ff6efa: Pull complete
51d6357098de: Pull complete
782f1ecce57d: Pull complete
5e99d351b073: Pull complete
7b73345df136: Pull complete
Digest: sha256:bd30b8d47b230de52431cc71c5cce149b8d5d4c87c204902acf2504435d4b4c9
Status: Downloaded newer image for nginx:latest
f463547e8f7b114a08cf793c32cc5ea2ff26a534b16956fa51364565154f42fe
```

🌞 **Visitons**

```
[alexandre@Docker ~]$ docker ps
CONTAINER ID   IMAGE     COMMAND                  CREATED          STATUS          PORTS
   NAMES
f463547e8f7b   nginx     "/docker-entrypoint.…"   41 seconds ago   Up 39 seconds   0.0.0.0:9999->80/tcp, :::9999->80/tcp   jolly_poincare

[alexandre@Docker ~]$ docker logs f463547e8f7b
/docker-entrypoint.sh: /docker-entrypoint.d/ is not empty, will attempt to perform configuration
/docker-entrypoint.sh: Looking for shell scripts in /docker-entrypoint.d/
/docker-entrypoint.sh: Launching /docker-entrypoint.d/10-listen-on-ipv6-by-default.sh
10-listen-on-ipv6-by-default.sh: info: Getting the checksum of /etc/nginx/conf.d/default.conf
10-listen-on-ipv6-by-default.sh: info: Enabled listen on IPv6 in /etc/nginx/conf.d/default.conf
/docker-entrypoint.sh: Sourcing /docker-entrypoint.d/15-local-resolvers.envsh
/docker-entrypoint.sh: Launching /docker-entrypoint.d/20-envsubst-on-templates.sh
/docker-entrypoint.sh: Launching /docker-entrypoint.d/30-tune-worker-processes.sh
/docker-entrypoint.sh: Configuration complete; ready for start up
2023/12/21 14:10:38 [notice] 1#1: using the "epoll" event method
2023/12/21 14:10:38 [notice] 1#1: nginx/1.25.3
2023/12/21 14:10:38 [notice] 1#1: built by gcc 12.2.0 (Debian 12.2.0-14)
2023/12/21 14:10:38 [notice] 1#1: OS: Linux 5.14.0-284.30.1.el9_2.x86_64
2023/12/21 14:10:38 [notice] 1#1: getrlimit(RLIMIT_NOFILE): 1073741816:1073741816
2023/12/21 14:10:38 [notice] 1#1: start worker processes
2023/12/21 14:10:38 [notice] 1#1: start worker process 29


[alexandre@Docker ~]$ docker inspect f463547e8f7b
[
    {
        "Id": "f463547e8f7b114a08cf793c32cc5ea2ff26a534b16956fa51364565154f42fe",
        "Created": "2023-12-21T14:10:36.750818644Z",
        "Path": "/docker-entrypoint.sh",
        "Args": [
            "nginx",
            "-g",
            "daemon off;"
        ],
        "State": {
            "Status": "running",
            "Running": true,
            "Paused": false,
            "Restarting": false,
            "OOMKilled": false,
            "Dead": false,
            "Pid": 58293,
            "ExitCode": 0,
            "Error": "",
            "StartedAt": "2023-12-21T14:10:37.971717043Z",
            "FinishedAt": "0001-01-01T00:00:00Z"
   [......]
   
[alexandre@Docker ~]$ sudo ss -lnpt
[sudo] password for alexandre:
State    Recv-Q    Send-Q       Local Address:Port       Peer Address:Port   Process
LISTEN   0         128                0.0.0.0:22              0.0.0.0:*       users:(("sshd",pid=34614,fd=3))
LISTEN   0         4096               0.0.0.0:9999            0.0.0.0:*       users:(("docker-proxy",pid=58251,fd=4))
LISTEN   0         128                   [::]:22                 [::]:*       users:(("sshd",pid=34614,fd=4))
LISTEN   0         4096                  [::]:9999               [::]:*       users:(("docker-proxy",pid=58256,fd=4))



```

🌞 **On va ajouter un site Web au conteneur NGINX**

```

[alexandre@Docker nginx]$ sudo nano index.html
[alexandre@Docker nginx]$ sudo nano site_nul.conf

[alexandre@Docker nginx]$ docker stop f463547e8f7b
f463547e8f7b
[alexandre@Docker nginx]$ docker run -d -p 9999:8080 -v /home/alexandre/nginx/index.html:/var/www/html/index.html -v /home/alexandre/nginx/site_nul.conf:/etc/nginx/conf.d/site_nul.conf nginx
c3af1b00711ed4a203a4614a53124f978d02115c7e82318b41c3c9a245462923
```

🌞 **Visitons**
```
[alexandre@Docker nginx]$ docker ps
CONTAINER ID   IMAGE     COMMAND                  CREATED         STATUS         PORTS
             NAMES
c3af1b00711e   nginx     "/docker-entrypoint.…"   2 minutes ago   Up 2 minutes   80/tcp, 0.0.0.0:9999->8080/tcp, :::9999->8080/tcp   ecstatic_engelbart
```

🌞 **Lance un conteneur Python, avec un shell**

```
[alexandre@Docker nginx]$ docker run -it python bash
Unable to find image 'python:latest' locally
latest: Pulling from library/python
bc0734b949dc: Pull complete
b5de22c0f5cd: Pull complete
917ee5330e73: Pull complete
b43bd898d5fb: Pull complete
7fad4bffde24: Pull complete
d685eb68699f: Pull complete
107007f161d0: Pull complete
02b85463d724: Pull complete
Digest: sha256:3733015cdd1bd7d9a0b9fe21a925b608de82131aa4f3d397e465a1fcb545d36f
Status: Downloaded newer image for python:latest
root@9a1e2e9c9e3e:/#
```


🌞 **Installe des libs Python**

```
root@9a1e2e9c9e3e:/# pip install aioconsole

root@9a1e2e9c9e3e:/# pip install aiohttp

root@9a1e2e9c9e3e:/# python
Python 3.12.1 (main, Dec 19 2023, 20:14:15) [GCC 12.2.0] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> import aiohttp
>>> import aiohttp
>>> import aioconsole
>>> ls
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
NameError: name 'ls' is not defined

```

# II. Images

## 1. Images publiques

🌞 **Récupérez des images**
```
[alexandre@Docker nginx]$ docker pull python:3.11

[alexandre@Docker nginx]$ docker pull mysql:5.7

[alexandre@Docker nginx]$ docker pull wordpress:latest

[alexandre@Docker nginx]$ docker pull linuxserver/wikijs:latest

[alexandre@Docker nginx]$ docker image ls
REPOSITORY           TAG       IMAGE ID       CREATED       SIZE
linuxserver/wikijs   latest    869729f6d3c5   6 days ago    441MB
mysql                5.7       5107333e08a8   8 days ago    501MB
python               latest    fc7a60e86bae   13 days ago   1.02GB
wordpress            latest    fd2f5a0c6fba   2 weeks ago   739MB
python               3.11      22140cbb3b0c   2 weeks ago   1.01GB
nginx                latest    d453dd892d93   8 weeks ago   187MB


```

🌞 **Lancez un conteneur à partir de l'image Python**
```
[alexandre@Docker nginx]$ sudo docker run -it python bash

root@66618a948c39:/# python --version
Python 3.12.1

```

## 2. Construire une image

🌞 **Ecrire un Dockerfile pour une image qui héberge une application Python**

```
[alexandre@Docker python_app_build]$ cat Dockerfile
FROM debian:buster
RUN apt update -y && apt install -y python3-pip
RUN python3 -m pip install emoji
COPY app.py .
ENTRYPOINT ["python3","app.py"]

[alexandre@Docker python_app_build]$ cat app.py
import emoji

print(emoji.emojize("Cet exemple d'application est vraiment naze :thumbs_down:"))
```

🌞 **Build l'image**
```
[alexandre@Docker python_app_build]$ docker build . -t python_app:version_de_ouf
[+] Building 354.6s (9/9) FINISHED                                                                                                           docker:default
 => [internal] load build definition from Dockerfile                                                                                                   0.0s
 => => transferring dockerfile: 247B                                                                                                                   0.0s
 => [internal] load .dockerignore                                                                                                                      0.0s
 => => transferring context: 2B                                                                                                                        0.0s
 => [internal] load metadata for docker.io/library/debian:buster                                                                                      22.5s
 => [1/4] FROM docker.io/library/debian:buster@sha256:cd3bbe339eeb538f4a1a6b04e290690658c119cd50b73677d29b80e3cda8f1dd                                69.5s
 => => resolve docker.io/library/debian:buster@sha256:cd3bbe339eeb538f4a1a6b04e290690658c119cd50b73677d29b80e3cda8f1dd                                 0.1s
 => => sha256:c95792f4821670a66dc2812d4efc0b3af63b97aea8270b0d63d3b5f99665ff9e 1.46kB / 1.46kB                                                         0.0s
 => => sha256:30b73a2ffaab3c39355a8bcc5eae8ba1465bd7d9467f197b91f7635816b16bc9 50.50MB / 50.50MB                                                      66.2s
 => => sha256:cd3bbe339eeb538f4a1a6b04e290690658c119cd50b73677d29b80e3cda8f1dd 984B / 984B                                                             0.0s
 => => sha256:42bafe476d336a07ed8a455b9f3419894242909a948bafc8f706eadb5a9c9bde 529B / 529B                                                             0.0s
 => => extracting sha256:30b73a2ffaab3c39355a8bcc5eae8ba1465bd7d9467f197b91f7635816b16bc9                                                              3.1s
 => [internal] load build context                                                                                                                      0.0s
 => => transferring context: 86B                                                                                                                       0.0s
 => [2/4] RUN apt update -y && apt install -y python3-pip                                                                                            245.7s
 => [3/4] RUN python3 -m pip install emoji                                                                                                            12.5s
 => [4/4] COPY app.py .                                                                                                                                0.1s
 => exporting to image                                                                                                                                 4.0s
 => => exporting layers                                                                                                                                4.0s
 => => writing image sha256:102840d2d7734ac5bb0fdd2165e2d2ae0615751e407faf5924dafc78a081ac3e                                                           0.0s
 => => naming to docker.io/library/python_app:version_de_ouf
```


🌞 **Lancer l'image**

```
[alexandre@Docker python_app_build]$ docker run python_app:version_de_ouf
Cet exemple d'application est vraiment naze 👎
```

# III. Docker compose

Pour la fin de ce TP on va manipuler un peu `docker compose`.

🌞 **Créez un fichier `docker-compose.yml`**
```

[alexandre@Docker ~]$ mkdir compose_test
[alexandre@Docker compose_test]$ sudo nano docker-compose.yml
[alexandre@Docker compose_test]$ cat docker-compose.yml
```
```
version: "3"

services:
  conteneur_nul:
    image: debian
    entrypoint: sleep 9999
  conteneur_flopesque:
    image: debian
    entrypoint: sleep 9999
```

🌞 **Lancez les deux conteneurs** avec `docker compose`

```
[alexandre@Docker compose_test]$ docker compose up -d
[+] Running 3/3
 ✔ conteneur_flopesque 1 layers [⣿]      0B/0B      Pulled                                                                                            53.3s
   ✔ bc0734b949dc Already exists                                                                                                                       0.0s
 ✔ conteneur_nul Pulled                                                                                                                               53.3s
[+] Running 3/3
 ✔ Network compose_test_default                  Created                                                                                               0.3s
 ✔ Container compose_test-conteneur_nul-1        Started                                                                                               0.1s
 ✔ Container compose_test-conteneur_flopesque-1  Started                                                                                               0.1s
```

🌞 **Vérifier que les deux conteneurs tournent**
```
[alexandre@Docker compose_test]$ docker compose ps
NAME                                 IMAGE     COMMAND        SERVICE               CREATED         STATUS         PORTS
compose_test-conteneur_flopesque-1   debian    "sleep 9999"   conteneur_flopesque   6 minutes ago   Up 6 minutes
compose_test-conteneur_nul-1         debian    "sleep 9999"   conteneur_nul         6 minutes ago   Up 6 minutes
```
🌞 **Pop un shell dans le conteneur `conteneur_nul`**


```
root@418e3ee14e76:/# apt-get update

root@418e3ee14e76:/# apt-get install -y iputils-ping

root@418e3ee14e76:/# ping conteneur_flopesque
PING conteneur_flopesque (172.18.0.2) 56(84) bytes of data.
64 bytes from compose_test-conteneur_flopesque-1.compose_test_default (172.18.0.2): icmp_seq=1 ttl=64 time=0.106 ms
64 bytes from compose_test-conteneur_flopesque-1.compose_test_default (172.18.0.2): icmp_seq=2 ttl=64 time=0.106 ms
64 bytes from compose_test-conteneur_flopesque-1.compose_test_default (172.18.0.2): icmp_seq=3 ttl=64 time=0.111 ms
^C
--- conteneur_flopesque ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2060ms
rtt min/avg/max/mdev = 0.106/0.107/0.111/0.002 ms
```