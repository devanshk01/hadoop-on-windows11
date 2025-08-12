


# Hadoop on Windows 11 — Full end-to-end installation, usage, and running Python MapReduce jobs with `mrjob`

### About this repository
This repository provides a **step-by-step guide** to installing and running **Hadoop 3.2.4** on **Windows 11**, including configuration for Python **MapReduce** programs using the `mrjob` module.  
It’s designed to document **exactly** the same process I followed on my system so anyone can replicate it without confusion.

---

### Install Java 8 and set `JAVA_HOME`
- Download Java 8 from [here](https://www.oracle.com/in/java/technologies/downloads/#java8-windows).
  - Go to the webpage and scroll down.
  - Locate Java 8 and click on it.
  - Then click on Windows.
  - Download x64 installer in .exe format.
- Go to the C: drive of your computer and create a folder named Java8
- Now, run the installer. Make sure that you change the installation path to `"C:\Java8"`.
- After the installation is complete, go to `"C:\Program Files\Java"` and **cut** the folder named `jdk1.8.*` and paste it to the `Java8` folder that we created, then delete the `Java` folder from the `Program Files` folder.
- Now, click `win`+`s` and type **Environment Variables**.
- Set user variables 
	1. `JAVA_HOME` = `C:\Java8\jdk-1.8.*\bin`
- In System Variables, select **path** variable and click edit.
	1. Add new path = `C:\Java8\jdk-1.8.*\bin`
- Open **Command Prompt** and check java version using following command,
	```powershell
	C:\>java -version
	```
- If it says `java` is not identified as internal or external command, then is some mistake in Java installation.

---

### Install Hadoop
- Download hadoop 3.2.4 from [here](https://hadoop.apache.org/release/3.2.4.html) (tar.gz format).
- Extract the file to `"C:\"` folder and rename the **extracted** folder as `hadoop`.
- Now, go to `C:\hadoop\etc\hadoop` and right click on `hadoop-env.cmd` and select *Edit in Notepad*.
- In the file `hadoop-env.cmd` edit the following part
	```bash
	@rem The java implementation to use.  Required.
	set JAVA_HOME=%JAVA_HOME%
	```
	to 
	```bash
	@rem The java implementation to use.  Required.
	set JAVA_HOME=C:\Java8\jdk-1.8.*
	```
- Now, we need to set another environment variable as follows,
	1. In User variables, set `HADOOP_HOME` = `C:\hadoop\bin`
	2. In  System variables, edit **path** variables by adding two new paths `C:\hadoop\bin` and `C:\hadoop\sbin`

- Now, in the `C:\hadoop` folder create a new folder named as `data` and inside `data` folder create another two new folders named as `namenode` and `datanode`.	

- Now we will need to edit the following files just like the above one. These files are in the folder `C:\hadoop\etc\hadoop`. In all files we need to replace `<configuartion> </configuration>` blocks with following code.
	- In `core-site.xml`,
		```xml
			<configuration>
				<property>
					  <name>fs.defaultFS</name>
					  <value>hdfs://localhost:9000</value>
				</property>
			</configuration>
		```
	- In `hdfs-site.xml`,
		```xml
			<configuration>
				<property>
					  <name>dfs.replication</name>
					  <value>1</value>
				</property>
				<property>
					  <name>dfs.namenode.name.dir</name>
					  <value>C:\hadoop\data\namenode</value>
				</property>
				<property>
					  <name>dfs.datanode.data.dir</name>
					  <value>C:\hadoop\data\datanode</value>
				</property>
			</configuration>
		```
	- In `mapred-site.xml`,
		```xml
			<configuration>
				<property>
					  <name>mapreduce.framework.name</name>
					  <value>yarn</value>
				</property>
			</configuration>
		```
	- In `yarn-site.xml`,
		```xml
			<configuration>
				<property>
					  <name>yarn.nodemanager.aux-services</name>
					  <value>mapreduce_shuffle</value>
				</property>
				<property>
					  <name>yarn.nodemanager.auxservices.mapreduce.shuffle.class</name>
					  <value>org.apache.hadoop.mapred.ShuffleHandler</value>
				</property>
			</configuration>
		```
- Now, first go to the `C:\hadoop` and delete the `bin` folder.
- Now download the new bin folder from [here](https://drive.google.com/file/d/1nCN_jK7EJF2DmPUUxgOggnvJ6k6tksYz/view) and paste/extract it to `C:\hadoop\`.

- Now, go to `C:\hadoop\bin` and run `winutils.exe`. If any error pops up saying that there is some `.dll` file is missing then check the name of that file and download it online and paste it to `C:\Windows\System32` folder.

- If a terminal pop-up and disappears instantly without any error message when you run `winutils.exe`, then you are done.

- Now your Hadoop set-up should be complete. To check if the set-up is successful, go to **Command Prompt** and run,
    ```bash
    C:\>hadoop version
    ```

- If it says, 'hadoop' is not recognised as an internal or external command, then there is some mistake in set-up. 

- Now, after the successful set-up, run **Command Prompt** as administrator. Run the following command to format the namenode,
    ```bash
    C:\windows\system32>hdfs namenode -format
    ```
- Now, in terminal, go to `C:\hadoop\sbin` and run these two commands,
    ```bash
    C:\hadoop\sbin> start-dfs.cmd
    C:\hadoop\sbin> start-yarn.cmd
    ```
- 4 new windows will pop-up. Now run
    ```bash
    C:\hadoop\sbin>jps
    ```
    
- If it shows something like this, then your all your nodes and resource managers are working correctly.

- You can also check them in your browser by visiting  and localhost:8088