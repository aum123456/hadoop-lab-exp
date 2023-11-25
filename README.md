# Exp-4

Objective: setup Hadoop in **Standalone mode** and **Pseudo-distributed mode**.

### 1. Standalone mode

Import the [ova file](https://drive.google.com/file/d/1KaMTTNwGTd5OuCUMF3y9EQvhZyUV_O8D/view?usp=sharing) into **VMWare** *as Open a virtual machine* or into **Virtual box** *as an appliance*.

The `hadoop` user account does not have any password. However, if you need to use `sudo` for commands, try entering the password as `hadoop` in the shell.

Starting all daemons:

```bash
startCDH.sh
```

Verification:

```bash
jps
```

### 2. Pseudo-distributed mode

Creating 2 directories in `/home/hadoop`:

```bash
cd /home/hadoop
mkdir hadooptmpdata
mkdir hdfs
cd ./hdfs
mkdir namenode
mkdir datanode
```

👆 These directories are where we want Hadoop to find Name node, Data node, HDFS and temporary data. (We will specify the paths of these directories in the config files in a few minutes.)

Navigate to the directory which has all the config files:

```bash
cd $HADOOP_HOME/etc/hadoop
ls
```

NOTE:

> Hadoop mode can be changed by editing some of these XML files. If any XML does not exist, create a new one with the name mentioned. Don't forget to include **XML Header** (aka **XML Declaration**) (copy from some other XML file).

We will edit `core-site.xml` to specify which port to run HDFS on and specify the path of the temp directory:

```bash
gedit core-site.xml
```

Replace the given `<configuration></configuration>` tags with the following text:

```xml
<configuration>
	<property>
		<name>fs.defaultFS</name>
		<value>hdfs://localhost:9000</value>
	</property>
	<property>
		<name>hadoop.tmp.dir</name>
		<value>/home/hadoop/hadooptmpdata</value>
	</property>
</configuration>
```

Save the file and exit gedit.

Editing the following file will set the replication factor and set the path for Name node and Data node.

```bash
gedit hdfs-site.xml
```

Replace the given `<configuration></configuration>` tags with the following text:

```xml
<configuration>
	<property>
		<name>dfs.replication</name>
		<value>1</value>
		<name>dfs.name.dir</name>
		<value>file:///home/hadoop/hdfs/namenode</value>
		<name>dfs.data.dir</name>
		<value>file:///home/hadoop/hdfs/datanode</value>
	</property>
</configuration>
```

Edit the following file:

```bash
gedit mapred-site.xml
```

Replace the configuration tags with the following text:

```xml
<configuration>

	<property>
		<name>mapreduce.framework.name</name>
		<value>yarn</value>
	</property>

	<property>
		<name>yarn.app.mapreduce.am.env</name>
		<value>HADOOP_MAPRED_HOME=$HADOOP_HOME</value>
	</property>

	<property>
		<name>mapreduce.map.env</name>
		<value>HADOOP_MAPRED_HOME=$HADOOP_HOME</value>
	</property>

	<property>
		<name>mapreduce.reduce.env</name>
		<value>HADOOP_MAPRED_HOME=$HADOOP_HOME</value>
	</property>
</configuration>
```

Save the file and exit Gedit.

We have now set up YARN for MapReduce.

```bash
gedit yarn-site.xml
```

Replace the configuration tags with the following text:

```xml
<configuration>
	<property>
		<name>yarn.nodemanager.aux-services</name>
		<value>mapreduce_shuffle</value>
	</property>
</configuration>
```

Save the file and exit Gedit.

```bash
hdfs namenode -format
```

Check the last 2 lines of the output. You should get an `FSImage` and a `SHUTDOWN_MSG` for the name node. You have setup Hadoop in pseudo-distributed mode.

If not, recheck the xml files for manual errors.

```bash
startCDH.sh
jps
```

# Exp-5

Objective: File management in Hadoop: adding files, retrieving files, deleting files to/from/from HDFS

NOTE: Reset hadoop to single-node mode by resetting xml files.

TIP: Viewing the GUI for visual convenience in browser:

```
http://localhost:50070/
```

### 1. Adding files to HDFS

```bash
sudo touch /home/local_file.txt
```

```bash
ls /home
```

```bash
$HADOOP_HOME/bin/hadoop fs -mkdir /user/input
```

```bash
$HADOOP_HOME/bin/hadoop fs -put /home/local_file.txt /user/input
```

### 2. Retrieving files from HDFS

```bash
$HADOOP_HOME/bin/hadoop fs -mkdir /user/output
```
```bash
$HADOOP_HOME/bin/hadoop fs -touchz /user/output/outfile.txt
```
```bash
$HADOOP_HOME/bin/hadoop fs -get /user/output/outfile.txt /home/hadoop/Desktop
```

### 3. Deleting files from HDFS

```bash
$HADOOP_HOME/bin/hadoop fs -rm /user/output/outfile.txt
```

```bash
$HADOOP_HOME/bin/hadoop fs -rm -r /user/output
```

```bash
$HADOOP_HOME/bin/hadoop fs -rm -r /user/input
```

Additional command for the purpose of writing in the exam:

```bash
hdfs dfs -rm -r hdfs:///user/output/outfile.txt
```
***OR***

```bash
hdfs dfs -rm -r hdfs:/user/output/outfile.txt
```