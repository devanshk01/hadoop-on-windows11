



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
    C:\windows\system32\>hdfs namenode -format
    ```
- Now, in terminal, go to `C:\hadoop\sbin` and run these two commands,
    ```bash
    C:\hadoop\sbin\> start-dfs.cmd
    C:\hadoop\sbin\> start-yarn.cmd
    ```
- 4 new windows will pop-up. Now run
    ```bash
    C:\hadoop\sbin\>jps
    ```
    
- If it shows something like this, then your all your nodes and resource managers are working correctly.

- You can also check them in your browser by visiting [localhost:9870](localhost:9870) and [localhost:8088](localhost:8088).

---

### Installing `mrjob` and writing Python Map-Reduce program
- Go to `C:/` and create a new folder named `hadoop_jobs`. Open this folder in **VS Code**.

- Open terminal in VS Code and install `mrjob` and `setuptools` modules.
    ```bash
    C:\hadoop_jobs\>pip install mrjob
    C:\hadoop_jobs\>pip install setuptools
    ```
- Now, we can write a Map-Reduce job in python and run it using Hadoop cluster.
- I have given an example map-reduce program here. Save this code in `CatCount.py` file in `C:\hadoop_jobs` folder.
    ```python
    from mrjob.job import MRJob
        class CatCount(MRJob):
            def mapper(self, key, line):
                record = line.split(',')
                category = record[2]
                
                yield category, 1
                
            def reducer(self, category, counts):
                total_count = sum( counts )
                
                if total_count > 10:
                    yield category, total_count
                    
    if __name__ == '__main__':
    CatCount.run()
    ```
- You need to run this map-reduce job on `amazon-sales.csv` file. I have uploaded the `csv` file in this repository itself.
- To run this map-reduce job on hadoop, you need to upload the file to hdfs. For that run the following commands
    ```bash
    C:\hadoop_jobs\>hdfs dfs -mkdir input /
    C:\hadoop_jobs\>hdfs dfs -put C:\hadoop_jobs\amazon-sales.csv /input
    ```
- Now, to run the python program, you can run the given command
    ```bash
    C:\hadoop_jobs\>hadoop jar C:\hadoop\share\hadoop\tools\lib\hadoop-streaming-3.2.4.jar `
    -files CatCount.py `
    -input /input/amazon-sales.csv `
    -output /output/catcount `
    -mapper "python CatCount.py --step-num=0 --mapper" `
    -reducer "python CatCount.py --step-num=0 --reducer"
    ```
- Keep in mind that everytime you run a map-reduce job the output must be stored in a new directory on hdfs. That means, if you change something in the code and want to run the job again you have to change `-output /output/catcount` to something like `-output /output/catcount-2`.

- If your job is completed successfully, there must be new output folder created with two files `_SUCCESS` and `part-00000` inside. The desired output is inside `part-00000`.

- You can copy the output folder to device and open `part-00000` file using following command 
    ```bash
    C:\hadoop_jobs\>hdfs dfs -get /output/catcount C:\hadoop_jobs
    ```
- You'll be able to see the output in `part-00000` file.
