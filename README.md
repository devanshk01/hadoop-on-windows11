
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
	```bash
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
- Now 
