# Docker에서 MariaDB 사용하기

## Docker를 사용해서 MariaDB 설치하기

[Docker를 사용해서 MariaDB을 설치하기 – Epistemology](http://www.epistemology.pe.kr/2020/09/26/1293)를 참고하여 설치합니다.

## MariaDB 사용하기

docker에서 실행중인 MariaDB에 접속하려면 다음과 같이 하면 됩니다.

```bash
docker exec -it mariadb /bin/bash
```

윗 명령어를 실행하면 다음과 같이 설치한 mariadb 도커로 들어가게 됩니다.

```bash
❯ docker exec -it mariadb /bin/bash
root@ad0bb5de3d3f:/#
```

여기서 아래와 같이 `mysql -u root -p`을 입력하시면 `mariadb`로 들어갈 수 있습니다. password는 앞에서 도커로 설치할 때 사용한 것을 그대로 사용했습니다. 만약 password를 다른 것으로 바꾼 분들은 본인이 입력한 password를 입력하시면 됩니다.

```bash
❯ docker exec -it mariadb /bin/bash
root@ad0bb5de3d3f:/#
root@e144fdbab9be:/# mysql -u root -p
# MYSQL_ROOT_PASSWORD=RT27hDosK
Enter password:
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MariaDB connection id is 4
Server version: 10.5.5-MariaDB-1:10.5.5+maria~focal mariadb.org binary distribution

Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

MariaDB [(none)]>
```

이제 여러분이 설치하신 mariadb에서 `SQL`을 실행할 수 있게 되었습니다. 만약 위와 같이 되지 않는다면, 이미 mariadb CONTAINER가 실행하고 있어서 안 될 수 있습니다. 이때는 아래와 같이 `docker ps -a`을 입력해 보시면 이미 작동하고 있는 경우를 확인할 수 있습니다. 이때는 아래와 같이 강제 종료를 시킨 다음에 다시 mariadb CONTAINER를 실행해주세요! 그러신 다음에 윗 과정을 따라가시면 됩니다.

```bash
❯ docker ps -a
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS                  PORTS                    NAMES
e144fdbab9be        mariadb             "docker-entrypoint.s…"   2 weeks ago         Exited (0) 7 days ago   0.0.0.0:3306->3306/tcp   mariadb
# 작동이 안 되면, 다 멈추고 재 시작합니다.
❯ docker rm $(docker ps -a -q) -f
❯ docker run --name mariadb -d -p 3306:3306 -e MYSQL_ROOT_PASSWORD=RT27hDosK mariadb
```
