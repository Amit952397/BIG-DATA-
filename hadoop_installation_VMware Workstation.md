# 🚀 Complete Guide to Installing Hadoop 3.3.6 on Ubuntu 24.04 (VMware)

This guide provides step-by-step instructions to install and configure **Hadoop 3.3.6** in **pseudo-distributed (single-node)** mode on **Ubuntu 24.04 running in VMware**.  
It includes setup, SSH configuration, environment variables, verification, and troubleshooting tips.

---

## 📘 This Guide Covers:
✅ Installing Hadoop 3.3.6 on Ubuntu 24.04  
✅ Configuring HDFS, YARN, and MapReduce  
✅ Setting up passwordless SSH  
✅ Ensuring proper Java installation  
✅ Troubleshooting common issues  

---

## 🧩 1️⃣ Prerequisites

Before starting, make sure:
- Ubuntu 24.04 is running in VMware Workstation  
- At least **4 GB RAM**, **50 GB disk space**, and **4 CPU cores** are allocated  
- **Java 8 (JDK 1.8.0_368)** is installed at:

```bash
/home/amitkumar/Downloads/jdk1.8.0_368/
🧭 2️⃣ Update Ubuntu Packages
Update all system packages to avoid dependency issues:

bash
Copy code
sudo apt update && sudo apt upgrade -y
☕ 3️⃣ Verify Java Installation
Check Java version:

bash
Copy code
java -version
Expected output:

scss
Copy code
java version "1.8.0_368"
Java(TM) SE Runtime Environment (build 1.8.0_368-bXX)
Java HotSpot(TM) 64-Bit Server VM
If not detected, set JAVA_HOME globally:

bash
Copy code
sudo nano /etc/environment
Add the line:

bash
Copy code
JAVA_HOME="/home/amitkumar/Downloads/jdk1.8.0_368/"
Apply the changes:

bash
Copy code
source /etc/environment
echo $JAVA_HOME
🧑‍💻 4️⃣ (Optional) Create Hadoop User
To isolate the Hadoop environment:

bash
Copy code
sudo adduser hadoop
sudo usermod -aG sudo hadoop
su - hadoop
You can skip this step and use your existing user (amitkumar).

📦 5️⃣ Download & Install Hadoop 3.3.6
Navigate to the Apache Hadoop downloads page:
🔗 https://hadoop.apache.org/releases.html

Download Hadoop:

bash
Copy code
wget https://downloads.apache.org/hadoop/common/hadoop-3.3.6/hadoop-3.3.6.tar.gz
Verify file integrity (optional):

bash
Copy code
sha512sum hadoop-3.3.6.tar.gz
Extract Hadoop:

bash
Copy code
tar -xvzf hadoop-3.3.6.tar.gz
Move to installation directory:

bash
Copy code
sudo mv hadoop-3.3.6 /usr/local/hadoop
Set ownership:

bash
Copy code
sudo chown -R $USER:$USER /usr/local/hadoop
⚙️ 6️⃣ Configure Hadoop Environment Variables
Edit .bashrc:

bash
Copy code
nano ~/.bashrc
Add the following lines at the end:

bash
Copy code
# Hadoop Environment Variables
export HADOOP_HOME=/usr/local/hadoop
export HADOOP_INSTALL=$HADOOP_HOME
export HADOOP_MAPRED_HOME=$HADOOP_HOME
export HADOOP_COMMON_HOME=$HADOOP_HOME
export HADOOP_HDFS_HOME=$HADOOP_HOME
export HADOOP_YARN_HOME=$HADOOP_HOME
export HADOOP_CONF_DIR=$HADOOP_HOME/etc/hadoop
export JAVA_HOME=/home/amitkumar/Downloads/jdk1.8.0_368/
export PATH=$PATH:$HADOOP_HOME/bin:$HADOOP_HOME/sbin
Apply changes:

bash
Copy code
source ~/.bashrc
Verify:

bash
Copy code
echo $HADOOP_HOME
Expected output:

bash
Copy code
/usr/local/hadoop
🧩 7️⃣ Configure Hadoop Core Files
🔹 (a) hadoop-env.sh
bash
Copy code
nano $HADOOP_HOME/etc/hadoop/hadoop-env.sh
Find:

arduino
Copy code
export JAVA_HOME=
Replace with:

bash
Copy code
export JAVA_HOME=/home/amitkumar/Downloads/jdk1.8.0_368/
🔹 (b) core-site.xml
bash
Copy code
nano $HADOOP_HOME/etc/hadoop/core-site.xml
Replace the existing content with:

xml
Copy code
<configuration>
    <property>
        <name>fs.defaultFS</name>
        <value>hdfs://localhost:9000</value>
    </property>
</configuration>
🔹 (c) hdfs-site.xml
bash
Copy code
nano $HADOOP_HOME/etc/hadoop/hdfs-site.xml
Add:

xml
Copy code
<configuration>
    <property>
        <name>dfs.replication</name>
        <value>1</value>
    </property>
    <property>
        <name>dfs.name.dir</name>
        <value>file:///usr/local/hadoop/hdfs/namenode</value>
    </property>
    <property>
        <name>dfs.data.dir</name>
        <value>file:///usr/local/hadoop/hdfs/datanode</value>
    </property>
</configuration>
Create directories:

bash
Copy code
mkdir -p /usr/local/hadoop/hdfs/namenode
mkdir -p /usr/local/hadoop/hdfs/datanode
sudo chown -R $USER:$USER /usr/local/hadoop/hdfs
🔹 (d) mapred-site.xml
Copy the template and edit:

bash
Copy code
cp $HADOOP_HOME/etc/hadoop/mapred-site.xml.template $HADOOP_HOME/etc/hadoop/mapred-site.xml
nano $HADOOP_HOME/etc/hadoop/mapred-site.xml
Add:

xml
Copy code
<configuration>
    <property>
        <name>mapreduce.framework.name</name>
        <value>yarn</value>
    </property>
    <property>
        <name>yarn.app.mapreduce.am.env</name>
        <value>HADOOP_MAPRED_HOME=/usr/local/hadoop</value>
    </property>
    <property>
        <name>mapreduce.map.env</name>
        <value>HADOOP_MAPRED_HOME=/usr/local/hadoop</value>
    </property>
    <property>
        <name>mapreduce.reduce.env</name>
        <value>HADOOP_MAPRED_HOME=/usr/local/hadoop</value>
    </property>
</configuration>
🔹 (e) yarn-site.xml
bash
Copy code
nano $HADOOP_HOME/etc/hadoop/yarn-site.xml
Add:

xml
Copy code
<configuration>
    <property>
        <name>yarn.nodemanager.aux-services</name>
        <value>mapreduce_shuffle</value>
    </property>
</configuration>
🔐 8️⃣ Configure Passwordless SSH
Install SSH and generate key pair:

bash
Copy code
sudo apt install ssh -y
ssh-keygen -t rsa -P ""
cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
chmod 600 ~/.ssh/authorized_keys
Test SSH:

bash
Copy code
ssh localhost
If it logs in without a password → ✅ SSH configured successfully.

🧱 9️⃣ Format Namenode & Start Hadoop
Format HDFS:
bash
Copy code
hdfs namenode -format
Start HDFS:
bash
Copy code
start-dfs.sh
Start YARN:
bash
Copy code
start-yarn.sh
Verify running daemons:
bash
Copy code
jps
Expected output:

nginx
Copy code
NameNode
DataNode
ResourceManager
NodeManager
✅ 🔟 Verify Installation in Browser
Service	URL
HDFS NameNode Web UI	http://localhost:9870/
YARN ResourceManager	http://localhost:8088/

🧩 11️⃣ Troubleshooting Tips
Problem	Cause	Fix
NameNode not starting	Incorrect JAVA_HOME	Verify hadoop-env.sh path
ssh: connect to host localhost port 22	SSH not installed	Run sudo apt install ssh
Permission denied	Wrong file ownership	sudo chown -R $USER:$USER /usr/local/hadoop
DataNode refused connection	Port conflict	Stop and restart all Hadoop daemons

🎉 Congratulations!
You have successfully installed Hadoop 3.3.6 on Ubuntu 24.04 (VMware) using your custom JDK path:
/home/amitkumar/Downloads/jdk1.8.0_368/

Your single-node pseudo-distributed Hadoop cluster is now fully operational with HDFS, YARN, and MapReduce!
