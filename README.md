## Tworzenie sieci lab12net

```sh
sudo docker network create --driver=bridge --subnet=10.20.96.0/20 lab12net
```

## Tworzenie folderów do logów

```
dev@dev-ms7e47 ~/s/c/zad_network> mkdir ~/lab12
dev@dev-ms7e47 ~/s/c/zad_network> mkdir ~/lab12/web1logs
dev@dev-ms7e47 ~/s/c/zad_network> mkdir ~/lab12/web2logs
dev@dev-ms7e47 ~/s/c/zad_network> mkdir ~/lab12/web3logs
```

## Tworzenie kontenerów

```
dev@dev-ms7e47 ~/s/c/zad_network> sudo docker run --rm -d --name web1 \
                                      --mount type=bind,src=/home/dev/lab12/web1logs,dst=/var/log/nginx/ \
                                      --mount type=bind,src=/home/dev/semestr6/chmura/zad_network/html,dst=/usr/share/nginx/html,ro \
                                      -p 5001:80 --network=lab12net nginx:1.31.1
4d96328e90930159efba5a3e9fe494897609badd45c79228338b9df95752030c

dev@dev-ms7e47 ~/s/c/zad_network> sudo docker run --rm -d --name web2 \
                                            --mount type=bind,src=/home/dev/lab12/web2logs,dst=/var/log/nginx/ \
                                            --mount type=bind,src=/home/dev/semestr6/chmura/zad_network/html,dst=/usr/share/nginx/html,ro \
                                            -p 5002:80 --network=lab12net nginx:1.31.1
7cb74a87146885862486cec39eb2815cee8bd8cf10b8dd8f446577496bd23656

dev@dev-ms7e47 ~/s/c/zad_network> sudo docker run --rm -d --name web3 \
                                      --mount type=bind,src=/home/dev/lab12/web3logs,dst=/var/log/nginx/ \
                                      --mount type=bind,src=/home/dev/semestr6/chmura/zad_network/html,dst=/usr/share/nginx/html,ro \
                                      -p 5003:80 --network=lab12net nginx:1.31.1
5d9983b067980c283e3124688e27fe88efe97690d671ecde7a8fe6886c286634

dev@dev-ms7e47 ~/s/c/zad_network> sudo docker ps
CONTAINER ID   IMAGE                           COMMAND                  CREATED          STATUS          PORTS                                     NAMES
5d9983b06798   nginx:1.31.1                    "/docker-entrypoint.…"   9 seconds ago    Up 9 seconds    0.0.0.0:5003->80/tcp, [::]:5003->80/tcp   web3
7cb74a871468   nginx:1.31.1                    "/docker-entrypoint.…"   25 seconds ago   Up 25 seconds   0.0.0.0:5002->80/tcp, [::]:5002->80/tcp   web2
4d96328e9093   nginx:1.31.1                    "/docker-entrypoint.…"   58 seconds ago   Up 58 seconds   0.0.0.0:5001->80/tcp, [::]:5001->80/tcp   web1
ddab615feca1   moby/buildkit:buildx-stable-1   "/usr/bin/buildkitd-…"   4 weeks ago      Up 2 hours                                                buildx_buildkit_smol-builder0
dev@dev-ms7e47 ~/s/c/zad_network>
```

## Dostępność strony z poziomu hosta

```
dev@dev-ms7e47 ~/s/c/zad_network> curl localhost:5001
<!DOCTYPE html>
<html lang="en">

<head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>LAB11</title>
</head>

<body>
        <h1>Lab 11 Sieci oraz wolumeny</h1>
        <h2>Bartłomiej Deska</h2>
        <h3>I1S 6.2 IO</h3>

</body>

</html>
dev@dev-ms7e47 ~/s/c/zad_network> curl localhost:5002
<!DOCTYPE html>
<html lang="en">

<head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>LAB11</title>
</head>

<body>
        <h1>Lab 11 Sieci oraz wolumeny</h1>
        <h2>Bartłomiej Deska</h2>
        <h3>I1S 6.2 IO</h3>

</body>

</html>
dev@dev-ms7e47 ~/s/c/zad_network> curl localhost:5003
<!DOCTYPE html>
<html lang="en">

<head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>LAB11</title>
</head>

<body>
        <h1>Lab 11 Sieci oraz wolumeny</h1>
        <h2>Bartłomiej Deska</h2>
        <h3>I1S 6.2 IO</h3>

</body>

</html>
```

## Dostępność logów z poziomu hosta

```
dev@dev-ms7e47 ~/s/c/zad_network> tree ~/lab12
/home/dev/lab12
├── web1logs
│   ├── access.log
│   └── error.log
├── web2logs
│   ├── access.log
│   └── error.log
└── web3logs
    ├── access.log
    └── error.log

4 directories, 6 files
```

```
dev@dev-ms7e47 ~/s/c/zad_network> cat ~/lab12/web1logs/access.log
10.20.96.1 - - [04/Jun/2026:10:02:32 +0000] "GET / HTTP/1.1" 403 555 "-" "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/148.0.0.0 Safari/537.36" "-"
10.20.96.1 - - [04/Jun/2026:10:02:32 +0000] "GET /favicon.ico HTTP/1.1" 404 555 "http://localhost:5001/" "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/148.0.0.0 Safari/537.36" "-"
10.20.96.1 - - [04/Jun/2026:10:06:31 +0000] "GET / HTTP/1.1" 200 278 "-" "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/148.0.0.0 Safari/537.36" "-"
10.20.96.1 - - [04/Jun/2026:10:09:57 +0000] "GET / HTTP/1.1" 200 278 "-" "curl/8.20.0" "-"

dev@dev-ms7e47 ~/s/c/zad_network> cat ~/lab12/web2logs/access.log
10.20.96.1 - - [04/Jun/2026:10:03:23 +0000] "GET / HTTP/1.1" 403 555 "-" "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/148.0.0.0 Safari/537.36" "-"
10.20.96.1 - - [04/Jun/2026:10:03:23 +0000] "GET /favicon.ico HTTP/1.1" 404 555 "http://localhost:5002/" "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/148.0.0.0 Safari/537.36" "-"
10.20.96.1 - - [04/Jun/2026:10:06:29 +0000] "GET / HTTP/1.1" 200 278 "-" "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/148.0.0.0 Safari/537.36" "-"
10.20.96.1 - - [04/Jun/2026:10:10:02 +0000] "GET / HTTP/1.1" 200 278 "-" "curl/8.20.0" "-"

dev@dev-ms7e47 ~/s/c/zad_network> cat ~/lab12/web3logs/access.log
10.20.96.1 - - [04/Jun/2026:10:03:26 +0000] "GET / HTTP/1.1" 403 555 "-" "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/148.0.0.0 Safari/537.36" "-"
10.20.96.1 - - [04/Jun/2026:10:03:26 +0000] "GET /favicon.ico HTTP/1.1" 404 555 "http://localhost:5003/" "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/148.0.0.0 Safari/537.36" "-"
10.20.96.1 - - [04/Jun/2026:10:06:26 +0000] "GET / HTTP/1.1" 200 278 "-" "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/148.0.0.0 Safari/537.36" "-"
10.20.96.1 - - [04/Jun/2026:10:10:04 +0000] "GET / HTTP/1.1" 200 278 "-" "curl/8.20.0" "-"
```
