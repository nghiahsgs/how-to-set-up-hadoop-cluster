# how-to-set-up-hadoop-cluster
how to set up hadoop cluster


## Step 1: Buy 3 vps (1 master, 2 slave)
After buy 3 vps (srv001,srv002,srv003) , install ubuntu 21 into 3 vps
```
149.28.156.113 srv001
45.76.159.224 srv002
207.148.122.206 srv003
```

## Step 2: For each vps : install java and hadoop
### Install java
```
apt install openjdk-8-jre-headless
apt install openjdk-8-jdk-headless
```

### Get java path
```
update-alternatives --display java
```

```
JAVA_HOME="/usr/lib/jvm/java-8-openjdk-amd64"
```

### Install hadoop
```
wget https://dlcdn.apache.org/hadoop/common/hadoop-3.2.2/hadoop-3.2.2.tar.gz
tar -xzvf hadoop-3.2.2.tar.gz
mv hadoop-3.2.2 /usr/local/hadoop
```

### Add 3 ip to hosts (For each vps), should call srv001 => srv001a
```
vi /etc/hosts
149.28.156.113 srv001a
45.76.159.224 srv002a
207.148.122.206 srv003a
```

### edit file /etc/environment
```
:/usr/local/hadoop/bin:/usr/local/hadoop/sbin
```
```
PATH="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin:/usr/local/hadoop/bin:/usr/local/hadoop/sbin"                           JAVA_HOME="/usr/lib/jvm/java-8-openjdk-amd64"
```

### create hadoop user for all vps
```
adduser hadoop
usermod -aG hadoop hadoop
chown hadoop:root -R /usr/local/hadoop
chmod g+rwx -R /usr/local/hadoop
```


## Step 3: In master vps : set up vps and copy to all vps
```
su - hadoop
ssh-keygen -t rsa
```

```
 su - hadoop
ssh-copy-id hadoop@srv001a
ssh-copy-id hadoop@srv002a
ssh-copy-id hadoop@srv003a
```

## Step 4: Configure master xml files
```
vi /usr/local/hadoop/etc/hadoop/core-site.xml
<configuration>
  <property>
    <name>fs.default.name</name>
    <value>hdfs://srv001a:9000</value>
  </property>
</configuration>
```

open firewall port 9000
```
sudo ufw allow 9000/tcp
```


```
<configuration>
  <property>
    <name>dfs.namenode.name.dir</name>
    <value>/usr/local/hadoop/data/nameNode</value>
  </property>
  <property>
    <name>dfs.datanode.data.dir</name>
    <value>/usr/local/hadoop/data/dataNode</value>
  </property>
  <property>
    <name>dfs.replication</name>
    <value>2</value>
  </property>
</configuration>
```

add workers name
```
vi /usr/local/hadoop/etc/hadoop/workers
srv002a
srv003a
```

## Step 5: Copy hadoop configurations to all slaves from master
```
scp /usr/local/hadoop/etc/hadoop/* srv002a:/usr/local/hadoop/etc/hadoop/
scp /usr/local/hadoop/etc/hadoop/* srv003a:/usr/local/hadoop/etc/hadoop/
```
## Step 6: In master: format HDFS and start all
```
source /etc/environment
hdfs namenode -format
start-dfs.sh (stop dfs command: stop-all.sh)
```
you can check jps on all server

## Step 7: HDFS Web UI
```
149.28.156.113 srv001
```
Open url to see hdfs UI
```
149.28.156.113:9870
```


open firewall port 9870
```
sudo ufw allow 9870/tcp
```
