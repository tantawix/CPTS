- الـ`Network File System` (`NFS`)نفس الSMB الغرض منه أني اقدر أأكسس علي الفايلات او الفولدرات المتشيره علي الnetwork بس هنا بنستخدم بروتوكول تاني اللي هو الNFS
- NFS clients cannot communicate directly with SMB servers

| **Version** | **Features**                                                                                                                                                                                                                                                         |
| ----------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `NFSv2`     | It is older but is supported by many systems and was initially operated entirely over UDP.                                                                                                                                                                           |
| `NFSv3`     | It has more features, including variable file size and better error reporting, but is not fully compatible with NFSv2 clients.                                                                                                                                       |
| `NFSv4`     | It includes Kerberos, works through firewalls and on the Internet, no longer requires portmappers, supports ACLs, applies state-based operations, and provides performance improvements and high security. It is also the first version to have a stateful protocol. |
- الNFS مش بيشتغل لوحده بيشتغل فوق بروتوكول الRPC وده بيشتغل علي بورت 111
- بيستخدم صيغة اسمها XDR عشان ينقل البيانات بين OS مختلفه
- الـNFS نفسه **ما عندوش آلية مصادقة أو authorization** مدمجة الاعتماد كله على RPC اللي ممكن يوفّر شوية خيارات للمصادقة
- نظام الAuth فالNFS مختلف شويه
-كل يوزر ف لينكس ليه `UID` وكل جروب ليه `GID`
-السيرفر بقي بياخد الUID/GID اللي جاي من الكلاينت ويحوله علي صيغة نظام الملفات عنده
- الNFS مش امن لأنه مش بيتأكد إذا الـ UID/GID ده فعلاً تابع لنفس الشخص على الـ client يعني لو عندك client يوزر اسمه "محمد" بــ UID=1000، وسيرفر عنده يوزر "محمود" بــ UID=1000 → السيرفر هيعتبرهم نفس الشخص
---
## Default Configuration
#### Exports File

```shell-session
0xTroj6n@htb[/htb]$ cat /etc/exports 

# /etc/exports: the access control list for filesystems which may be exported
#               to NFS clients.  See exports(5).
#
# Example for NFSv2 and NFSv3:
# /srv/homes       hostname1(rw,sync,no_subtree_check) hostname2(ro,sync,no_subtree_check)
#
# Example for NFSv4:
# /srv/nfs4        gss/krb5i(rw,sync,fsid=0,crossmnt,no_subtree_check)
# /srv/nfs4/homes  gss/krb5i(rw,sync,no_subtree_check)
```

|**Option**|**Description**|
|---|---|
|`rw`|Read and write permissions.|
|`ro`|Read only permissions.|
|`sync`|Synchronous data transfer. (A bit slower)|
|`async`|Asynchronous data transfer. (A bit faster)|
|`secure`|Ports above 1024 will not be used.|
|`insecure`|Ports above 1024 will be used.|
|`no_subtree_check`|This option disables the checking of subdirectory trees.|
|`root_squash`|Assigns all permissions to files of root UID/GID 0 to the UID/GID of anonymous, which prevents `root` from accessing files on an NFS mount.|

Let us create such an entry for test purposes and play around with the settings.

#### ExportFS

```shell-session
root@nfs:~# echo '/mnt/nfs  10.129.14.0/24(sync,no_subtree_check)' >> /etc/exports
root@nfs:~# systemctl restart nfs-kernel-server 
root@nfs:~# exportfs

/mnt/nfs      	10.129.14.0/24
```
- `/mnt/nfs`: ده الفولدر اللي عايز تشاركه (Share) من على السيرفر.
- `10.129.14.0/24`: دي الشبكة اللي مسموح لها تشوف الفولدر (يعني كل الأجهزة اللي IP بتاعها يبدأ بـ 10.129.14.*).
- `(sync,no_subtree_check)`:
    - **sync** = أي تعديل (كتابة/حذف) لازم يتسجل على السيرفر فورًا، عشان يقلل مشاكل الـ corruption.
    - **no_subtree_check** = بيمنع السيرفر من إنه يعمل check على parent directory عشان الأداء يكون أسرع.

## Dangerous Settings

|**Option**|**Description**|
|---|---|
|`rw`|Read and write permissions.|
|`insecure`|Ports above 1024 will be used.|
|`nohide`|If another file system was mounted below an exported directory, this directory is exported by its own exports entry.|
|`no_root_squash`|All files created by root are kept with the UID/GID 0.|
`insecure` is very dangerous beacause first 1024 port can only used by root

---
## Footprinting the Service
#### Nmap

```shell-session
0xTroj6n@htb[/htb]$ sudo nmap 10.129.14.128 -p111,2049 -sV -sC

Starting Nmap 7.80 ( https://nmap.org ) at 2021-09-19 17:12 CEST
Nmap scan report for 10.129.14.128
Host is up (0.00018s latency).

PORT    STATE SERVICE VERSION
111/tcp open  rpcbind 2-4 (RPC #100000)
| rpcinfo: 
|   program version    port/proto  service
|   100000  2,3,4        111/tcp   rpcbind
|   100000  2,3,4        111/udp   rpcbind
|   100000  3,4          111/tcp6  rpcbind
|   100000  3,4          111/udp6  rpcbind
|   100003  3           2049/udp   nfs
|   100003  3           2049/udp6  nfs
|   100003  3,4         2049/tcp   nfs
|   100003  3,4         2049/tcp6  nfs
|   100005  1,2,3      41982/udp6  mountd
|   100005  1,2,3      45837/tcp   mountd
|   100005  1,2,3      47217/tcp6  mountd
|   100005  1,2,3      58830/udp   mountd
|   100021  1,3,4      39542/udp   nlockmgr
|   100021  1,3,4      44629/tcp   nlockmgr
|   100021  1,3,4      45273/tcp6  nlockmgr
|   100021  1,3,4      47524/udp6  nlockmgr
|   100227  3           2049/tcp   nfs_acl
|   100227  3           2049/tcp6  nfs_acl
|   100227  3           2049/udp   nfs_acl
|_  100227  3           2049/udp6  nfs_acl
2049/tcp open  nfs_acl 3 (RPC #100227)
MAC Address: 00:00:00:00:00:00 (VMware)

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 6.58 seconds
```

```shell-session
0xTroj6n@htb[/htb]$ sudo nmap --script nfs* 10.129.14.128 -sV -p111,2049

Starting Nmap 7.80 ( https://nmap.org ) at 2021-09-19 17:37 CEST
Nmap scan report for 10.129.14.128
Host is up (0.00021s latency).

PORT     STATE SERVICE VERSION
111/tcp  open  rpcbind 2-4 (RPC #100000)
| nfs-ls: Volume /mnt/nfs
|   access: Read Lookup NoModify NoExtend NoDelete NoExecute
| PERMISSION  UID    GID    SIZE  TIME                 FILENAME
| rwxrwxrwx   65534  65534  4096  2021-09-19T15:28:17  .
| ??????????  ?      ?      ?     ?                    ..
| rw-r--r--   0      0      1872  2021-09-19T15:27:42  id_rsa
| rw-r--r--   0      0      348   2021-09-19T15:28:17  id_rsa.pub
| rw-r--r--   0      0      0     2021-09-19T15:22:30  nfs.share
|_
| nfs-showmount: 
|_  /mnt/nfs 10.129.14.0/24
| nfs-statfs: 
|   Filesystem  1K-blocks   Used       Available   Use%  Maxfilesize  Maxlink
|_  /mnt/nfs    30313412.0  8074868.0  20675664.0  29%   16.0T        32000
| rpcinfo: 
|   program version    port/proto  service
|   100000  2,3,4        111/tcp   rpcbind
|   100000  2,3,4        111/udp   rpcbind
|   100000  3,4          111/tcp6  rpcbind
|   100000  3,4          111/udp6  rpcbind
|   100003  3           2049/udp   nfs
|   100003  3           2049/udp6  nfs
|   100003  3,4         2049/tcp   nfs
|   100003  3,4         2049/tcp6  nfs
|   100005  1,2,3      41982/udp6  mountd
|   100005  1,2,3      45837/tcp   mountd
|   100005  1,2,3      47217/tcp6  mountd
|   100005  1,2,3      58830/udp   mountd
|   100021  1,3,4      39542/udp   nlockmgr
|   100021  1,3,4      44629/tcp   nlockmgr
|   100021  1,3,4      45273/tcp6  nlockmgr
|   100021  1,3,4      47524/udp6  nlockmgr
|   100227  3           2049/tcp   nfs_acl
|   100227  3           2049/tcp6  nfs_acl
|   100227  3           2049/udp   nfs_acl
|_  100227  3           2049/udp6  nfs_acl
2049/tcp open  nfs_acl 3 (RPC #100227)
MAC Address: 00:00:00:00:00:00 (VMware)

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 0.45 seconds
```

#### Show Available NFS Shares

```shell-session
0xTroj6n@htb[/htb]$ showmount -e 10.129.14.128

Export list for 10.129.14.128:
/mnt/nfs 10.129.14.0/24
```

#### Mounting NFS Share

```shell-session
0xTroj6n@htb[/htb]$ mkdir target-NFS
0xTroj6n@htb[/htb]$ sudo mount -t nfs 10.129.14.128:/ ./target-NFS/ -o nolock
0xTroj6n@htb[/htb]$ cd target-NFS
0xTroj6n@htb[/htb]$ tree .

.
└── mnt
    └── nfs
        ├── id_rsa
        ├── id_rsa.pub
        └── nfs.share

2 directories, 3 files
```

#### List Contents with Usernames & Group Names
```shell-session
0xTroj6n@htb[/htb]$ ls -l mnt/nfs/

total 16
-rw-r--r-- 1 cry0l1t3 cry0l1t3 1872 Sep 25 00:55 cry0l1t3.priv
-rw-r--r-- 1 cry0l1t3 cry0l1t3  348 Sep 25 00:55 cry0l1t3.pub
-rw-r--r-- 1 root     root     1872 Sep 19 17:27 id_rsa
-rw-r--r-- 1 root     root      348 Sep 19 17:28 id_rsa.pub
-rw-r--r-- 1 root     root        0 Sep 19 17:22 nfs.share
```

#### List Contents with UIDs & GUIDs

```shell-session
0xTroj6n@htb[/htb]$ ls -n mnt/nfs/

total 16
-rw-r--r-- 1 1000 1000 1872 Sep 25 00:55 cry0l1t3.priv
-rw-r--r-- 1 1000 1000  348 Sep 25 00:55 cry0l1t3.pub
-rw-r--r-- 1    0 1000 1221 Sep 19 18:21 backup.sh
-rw-r--r-- 1    0    0 1872 Sep 19 17:27 id_rsa
-rw-r--r-- 1    0    0  348 Sep 19 17:28 id_rsa.pub
-rw-r--r-- 1    0    0    0 Sep 19 17:22 nfs.share
```

#### Unmounting

```shell-session
0xTroj6n@htb[/htb]$ cd ..
0xTroj6n@htb[/htb]$ sudo umount ./target-NFS
```
---
