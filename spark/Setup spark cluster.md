# Setup Spark Cluster

### 1. Change hostname

```shell
sudo hostnamectl set-hostnmae sparkmaster
sudo reboot
```

### 2. Add /etc/hosts in all nodes

```shell
# sudo vi /etc/hosts

127.0.0.1       localhost
#127.0.1.1      hadoop1

192.168.38.50  hadoop1
192.168.38.51  hadoop2
192.168.38.54  hadoop3
192.168.38.56  sparkmaster



```

### 3. Copy ssh pub key to all nodes, make sure all nodes can ssh without password

```shell
# generate ssh key
ssh-keygen -t rsa

# copy to all nodes
ssh-copy-id hadoop1
ssh-copy-id hadoop2
ssh-copy-id hadoop3
ssh-copy-id sparkmaster

```



### 4. Install Scala

```shell
#download scala
wget https://downloads.lightbend.com/scala/2.11.12/scala-2.11.12.tgz
tar -zxvf scala-2.11.12.tgz
sudo mv scala-2.11.12 /usr/local/scala

sudo vi /etc/profile
#add below to it
export SCALA_HOME=/usr/local/scala
export PATH=$PATH:$SCALA_HOME/bin

source /etc/profile
#run scala to test
scala

```

