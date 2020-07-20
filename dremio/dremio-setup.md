# SetUp dremio docker cluster

```shell
# add docker group and add current user to docker group, so you can use docker ps instead of sudo docker ps
sudo groupadd docker
sudo usermod -aG docker $USER
newgrp docker


docker swarm init
docker swarm join --token SWMTKN-1-1yi6wosldfkyuf47376xkbv4cixkaeh54gr2r2mi5p56acrcb0-6ubnqvovyyr0gfex7wqmh8z4v 192.168.38.50:2377

docker network create --driver=overlay --attachable dremio-net

docker build -t dremio-coordinator -f coordinator/Dockerfile .
docker build -t dremio-executor -f executor/Dockerfile .

docker run --name zookeeper.host -h zookeeper.host --network dremio-net -d -p 80:9047 -p 31010:31010 -v $PWD/data:/opt/dremio/data dremio-coordinator

# https://download.dremio.com/community-server/4.2.2-202004211133290458-b550b6fa/dremio-community-4.2.2-202004211133290458-b550b6fa.tar.gz

docker run --name zookeeper.host -h zookeeper.host --network dremio-net -d -p 80:9047 -v $PWD/data:/opt/dremio/data dremio-coordinator

docker run --name executor1 -h executor1 --network dremio-net -d dremio-executor
docker run --name executor2 -h executor2 --network dremio-net -d dremio-executor
docker run --name executor3 -h executor3 --network dremio-net -d dremio-executor

# To run python scripts, need install dremio odbc driver and unixodbc, pyodbc, pandas 
# install pandas, pyodbc 
pip3 install -i https://pypi.tuna.tsinghua.edu.cn/simple pandas

# install unixodbc-dev before install pyodbc
sudo apt-get install unixodbc unixodbc-dev -y

pip3 install -i https://pypi.tuna.tsinghua.edu.cn/simple pyodbc

# install dremio-odbc driver

sudo dpkg -i dremio-odbc_1.4.2.1003-2_amd64.deb
# if you want to remove dremio you can use below command
sudo dpkg -r dremio

#if has dependencies problem, can fix up
sudo apt-get install -f


```



## Dremio Backup & Restore

```shell
# Run this command at coordinator node and the cluster is running.

<dremio_home>/bin/dremio-admin backup
    -u <DREMIO_ADMIN_USER> \
    -p <DREMIO_ADMIN_PASS> \
    -d <BACKUP_PATH>
    
chmod 777 -R ./data/backup

docker exec -it dremio-coordinator bash

# use -j option to export json format
/opt/dremio/bin/dremio-admin backup -u dremio -p abc1234_ -j -d /opt/dremio/data/backup

# Make sure all cluster nodes are completely shut down before running restore command. 
# Run the restore command at coordinator node
<dremio_home>/bin/dremio-admin restore -d <BACKUP_PATH>

docker run  -h zookeeper.host --network dremio-net -d -p 80:9047 -p 31010:31010 -v $PWD/data:/opt/dremio/data dremio-coordinator /bin/bash -c '/opt/dremio/bin/dremio-admin restore -d /opt/dremio/data/backup '

```

