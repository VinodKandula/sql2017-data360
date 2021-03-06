# Platform of your choice: Freedom to choose Windows, Linux or Docker Containers for SQL Server

## What's here?
This page has the ***demo scripts*** and ***talking points*** for the ***SQL 2017 on Linux*** demos presented at the Data & AI Summit, Sept 2017.

Jump to:
- [Main README.md](https://github.com/sanagama/sql2017-data360)
- [Demo Setup](https://github.com/sanagama/sql2017-data360/blob/master/demo-setup.md)
- [Demo Script - SQL on Linux](https://github.com/sanagama/sql2017-data360/blob/master/demo-script-sql-linux.md)
- [Demo Script - SQL in Docker](https://github.com/sanagama/sql2017-data360/blob/master/demo-script-docker.md)

## Demo Script - SQL 2017 on Linux
There are 3 demos below. I recommend you perform these demos in sequence if possible.

Jump to:
1. [Install SQL 2017 on Linux](#1-install-sql-2017-on-linux)
1. [SQL Server on Linux Backup Restore](#2-sql-server-on-linux-backup-restore)
1. [Use VS Code with SQL on Linux](#3-use-vs-code-with-sql-on-linux)

### 1. Install SQL 2017 on Linux

(**Talking Points**)
- I have 2 VMs running on my MacBook: one is a Windows VM with SQL 2017 installed and another is a fresh Ubuntu VM.
- Let me go over to my Ubuntu VM.

Switch over to the Ubuntu VM.

(**Talking Points**)
- Let’s install SQL Server on Ubuntu.
- One of our key goals with SQL Server on Linux is to meet developers where they are.
- We wanted to make sure that we had an easy & platform-appropriate install experience for customers. I’ll use ```apt``` on Ubuntu.
- For this demo, I have ```Bash``` scripts to save me some typing.  I’ll show you what each Bash script does before I run it.

Launch ```Terminal``` and type the following:
```
cd ~/sql2017-data360/demos/sql-on-ubuntu
ls -al
cat ./1-install-sql2017.sh
```

(**Talking Points**)
- As you would expect, we have a standard ```apt``` experience for Ubuntu.
- We also support Redhat (yum) and SUSE (zypper) and we also have a Docker image that you can use on Linux/macOS/Windows, and we have a VM template in the Azure gallery.
- Let me run the Bash script

Type the following in the ```Terminal``` window:
```
sudo ./1-install-sql2017.sh
```

(**Talking Points**)
- Let me follow the prompts here…. And install done!
- SQL is installed and it tells me to run ```sudo /opt/mssql/bin/mssql-conf setup```
- Let me do that now.

Type the following in the ```Terminal``` window:
```
sudo /opt/mssql/bin/mssql-conf setup
```

(**Talking Points**)
- I’ll accept the EULA and specify my SA password ```Yukon900```
- Done. SQL is configured and is running
- Now let me check if SQL is running - I have a simple ```Bash``` script to do that

Type the following in the ```Terminal``` window:
```
cat ./2-check-status.sh
./2-check-status.sh
```

(**Talking Points**)
- Yep, all good.
- We’ve ported the standard SQL command line tools (sqlcmd and bcp) to Linux & macOS and packaged them up in a separate package called mssql-tools that you can install separately from SQL Server.
- Let me install mssql-tools - you guessed it, I have a ```Bash``` script to do that too.

Type the following in the ```Terminal``` window:
```
cat ./3-install-mssql-tools.sh
sudo ./3-install-mssql-tools.sh
```

As you would expect, another standard ```apt``` experience.

(**Talking Points**)
- Tools are installed, so I’ll run sqlcmd to connect to SQL running locally and run a simple query

Type the following in the ```Terminal``` window:
```
sqlcmd -S . -U sa
```

Type in the password (```Yukon900```) when prompted.
At the ```sqlcmd``` prompt, type:
```
sqlcmd -S . -U sa
select @@version
GO
```

(**Talking Points**)
- Proof that SQL Server is indeed running on Ubuntu ;-)
- You’re familiar with SSMS, right? Cool, let’s connect to SQL on Linux with SSMS running on Windows.

Switch to the Windows VM and launch SSMS
Connect to ```pbox-ubuntu``` with SQL Server authentication (User: ```sa``` Password: ```Yukon900```)

Right-click the ```server``` node and click ```Properties```. Show the ```platform``` and ```version``` information.

(**Talking Points**)
- The tools you use today work nicely with SQL Server on Linux.

### 2. SQL Server on Linux Backup Restore

(**Talking Points**)
- Let’s do something more fun.
- I have a database called ```BollywoodDB``` on my SQL Server on Windows

Expand *tables* and do a *select top 1000* from the *Actors* table.

(**Talking Points**)
- Let’s try and migrate this database over to SQL on Ubuntu.
- I have a backup of this database here ```c:\sql2017-data360\backups\BollywoodDB.bak``` that I’d like to migrate to SQL on Ubuntu.
- Migrating a database from SQL on Windows to SQL on Linux is easy: simply restore a backup
- The first step is to copy the ```.bak``` file from Windows to Ubuntu
- There are several ways you can do this (ssh, scp, etc.)
- For this demo, I’ll just use WinSCP to copy the ```.bak``` file from my Windows VM to my Ubuntu VM.

Switch to the Windows VM and run WinSCP in the copy the file ```c:\sql2017-data360\backups\BollywoodDB.bak``` to the ```HOME``` folder in the Ubuntu VM.

(**Talking Points**)
- Now, I'll restore this backup to SQL Server on Linux via T-SQL.

Switch to Ubuntu VM and launch ```Terminal``` and type the following:
```
cd ~
ls -al *.bak
```

(**Talking Points**)
- I have my ```.bak``` file in my ```HOME``` directory
- I’ll move the ```.bak``` file to ```/var/opt/mssql/backup/```
- I have a simple ```Bash``` script to help me do that
- This is what the script looks like:

Type the following in the ```Terminal``` window:
```
cd ~/sql2017-data360/demos/sql-on-ubuntu
cat ./5-move-bak-file.sh
sudo ./5-move-bak-file.sh
```

(**Talking Points**)
- Done.
- Now, I'll restore it with ```sqlcmd```. I have a simple ```Bash``` script to do that too. This is what it looks like:

Type the following in the ```Terminal``` window:
```
cat ./6-restore-BollywoodDB.sh
cat ./sql/restore_BollywoodDB.sql
```

(**Talking Points**)
- Nothing fancy here.
- The script simply runs ```sqlcmd``` and passes it a ```.sql``` file that does the restore.
- Let's run the script

Type the following in the ```Terminal``` window:
```
./6-restore-BollywoodDB.sh
```
Type in the password (```Yukon900```) when prompted

(**Talking Points**)
- Done.
- Now let’s use SSMS to see if the restore worked.

Switch back to the Windows VM.
Refresh the *databases* node with SSMS connected to ```pbox-ubuntu```.

(**Talking Points**)
- We can see that the database ```BollywoodDB``` has been restored on SQL Server on Linux.

Expand the *tables* node and do a ```select top 1000``` from the *Actors* table.

(**Talking Points**)
- That was my first demo.
- Let’s recap what I showed you: I installed SQL on Ubuntu, installed the Linux-native command line tools, ran a simple query, connected from SSMS and restored a database backup from Windows to Linux.

### 3. Use VS Code with SQL on Linux

Shutdown the Windows VM. Leave the Ubuntu VM running.

Switch to the MacBook. 

(**Talking Points**)
- SQL is running in my Ubuntu VM.
- I'm on my MacBook and I can't use SSMS.
- What can I use to edit and run T-SQL queries interactively?
- Well, we can use Visual Studio Code and the nifty mssql extension.

Launch VS Code.

Click on the ```extensions``` button and show that the ```mssql extension``` is installed.

Create a new file and change the file type to ```SQL```

Type the following command in the editor:
```
SELECT @@VERSION
```

Click ```Execute Query```

(**Talking Points**)
- VS Code prompts me for the connection
- I'll connect to SQL running in my Ubuntu VM

Enter the following values in the connection prompt:
Property | Value
--------------- | ------
Server Name | ***pbox-ubuntu***
Authentication | ***SQL Authentication***
Username | ***sa***
Password | ***Yukon900***
Save Password | ***Yes***

(**Talking Points**)
- Show the results grid
- Let me switch to the database that I restored earlier

Click on ```master``` in the status bar and select ```BollywoodDB```

Type the following command in the editor:
```
SELECT * from dbo.Actors
```

(**Talking Points**)
- Talk to export results as CSV, JSON, Excel

(**Talking Points**)
- As you can see, we have a pretty good experience to edit and run T-SQL queries in VS Code and use it from my MacBook.
- The mssql extension for VS Code is open source under MIT and is available on the VS Code marketplace for free.
- We invite you to try VS Code on Linux, macOS and Windows with SQL Server and tell us what you think.
