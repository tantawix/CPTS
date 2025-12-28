- الplugins هيا إضافات اللي عملها ناس عاديه والmetasploit واقفت عليها وحطوها فالاداة
- استخدام الplugins بيخليك انت كا pentester تجربتك اسهل وبتزود قدرات الmsfconsole
---
## Using Plugins
- مبدأيا اول حاجه الplugins بتكون موجوده ف `/usr/share/metasploit-framework/plugins`
```shell-session
0xTroj6n@htb[/htb]$ ls /usr/share/metasploit-framework/plugins

aggregator.rb      beholder.rb        event_tester.rb  komand.rb     msfd.rb    nexpose.rb   request.rb  session_notifier.rb  sounds.rb  token_adduser.rb  wmap.rb
alias.rb           db_credcollect.rb  ffautoregen.rb   lab.rb        msgrpc.rb  openvas.rb   rssfeed.rb  session_tagger.rb    sqlmap.rb  token_hunter.rb
auto_add_route.rb  db_tracker.rb      ips_filter.rb    libnotify.rb  nessus.rb  pcap_log.rb  sample.rb   socket_logger.rb     thread.rb  wiki.rb
```
#### MSF - Load Nessus

```shell-session
msf6 > load nessus

[*] Nessus Bridge for Metasploit
[*] Type nessus_help for a command listing
[*] Successfully loaded Plugin: Nessus


msf6 > nessus_help

Command                     Help Text
-------                     ---------
Generic Commands            
-----------------           -----------------
nessus_connect              Connect to a Nessus server
nessus_logout               Logout from the Nessus server
nessus_login                Login into the connected Nessus server with a different username and 

<SNIP>

nessus_user_del             Delete a Nessus User
nessus_user_passwd          Change Nessus Users Password
                            
Policy Commands             
-----------------           -----------------
nessus_policy_list          List all polciies
nessus_policy_del           Delete a policy
```
---
## Installing new Plugins
- تعالي نجرب نسطب الplugins دول  [DarkOperator's Metasploit-Plugins](https://github.com/darkoperator/Metasploit-Plugins.git)
- خلي بالك لازم يكونو `.rb`
#### Downloading MSF Plugins

```shell-session
0xTroj6n@htb[/htb]$ git clone https://github.com/darkoperator/Metasploit-Plugins
0xTroj6n@htb[/htb]$ ls Metasploit-Plugins

aggregator.rb      ips_filter.rb  pcap_log.rb          sqlmap.rb
alias.rb           komand.rb      pentest.rb           thread.rb
auto_add_route.rb  lab.rb         request.rb           token_adduser.rb
beholder.rb        libnotify.rb   rssfeed.rb           token_hunter.rb
db_credcollect.rb  msfd.rb        sample.rb            twitt.rb
db_tracker.rb      msgrpc.rb      session_notifier.rb  wiki.rb
event_tester.rb    nessus.rb      session_tagger.rb    wmap.rb
ffautoregen.rb     nexpose.rb     socket_logger.rb
growl.rb           openvas.rb     sounds.rb
```
هناخدهم copy نحطهم ف dir الplugins بتاع الميتا
#### MSF - Copying Plugin to MSF

```shell-session
0xTroj6n@htb[/htb]$ sudo cp ./Metasploit-Plugins/pentest.rb /usr/share/metasploit-framework/plugins/pentest.rb
```
#### MSF - Load Plugin

```shell-session
0xTroj6n@htb[/htb]$ msfconsole -q

msf6 > load pentest

       ___         _          _     ___ _           _
      | _ \___ _ _| |_ ___ __| |_  | _ \ |_  _ __ _(_)_ _
      |  _/ -_) ' \  _/ -_|_-<  _| |  _/ | || / _` | | ' \ 
      |_| \___|_||_\__\___/__/\__| |_| |_|\_,_\__, |_|_||_|
                                              |___/
      
Version 1.6
Pentest Plugin loaded.
by Carlos Perez (carlos_perez[at]darkoperator.com)
[*] Successfully loaded plugin: pentest


msf6 > help

Tradecraft Commands
===================

    Command          Description
    -------          -----------
    check_footprint  Checks the possible footprint of a post module on a target system.


auto_exploit Commands
=====================

    Command           Description
    -------           -----------
    show_client_side  Show matched client side exploits from data imported from vuln scanners.
    vuln_exploit      Runs exploits based on data imported from vuln scanners.


Discovery Commands
==================

    Command                 Description
    -------                 -----------
    discover_db             Run discovery modules against current hosts in the database.
    network_discover        Performs a port-scan and enumeration of services found for non pivot networks.
    pivot_network_discover  Performs enumeration of networks available to a specified Meterpreter session.
    show_session_networks   Enumerate the networks one could pivot thru Meterpreter in the active sessions.


Project Commands
================

    Command       Description
    -------       -----------
    project       Command for managing projects.


Postauto Commands
=================

    Command             Description
    -------             -----------
    app_creds           Run application password collection modules against specified sessions.
    get_lhost           List local IP addresses that can be used for LHOST.
    multi_cmd           Run shell command against several sessions
    multi_meter_cmd     Run a Meterpreter Console Command against specified sessions.
    multi_meter_cmd_rc  Run resource file with Meterpreter Console Commands against specified sessions.
    multi_post          Run a post module against specified sessions.
    multi_post_rc       Run resource file with post modules and options against specified sessions.
    sys_creds           Run system password collection modules against specified sessions.

<SNIP>
```
---
## Mixins
- الMetasploit مكتوبة بلغة Ruby ودي لغة Object Oriented وبتدعم حاجه مهمه جدا اسمها Mixins
- **الMixins** دي قطعة كود جاهزة بتعمل وظيفه معينة تقدر تضيفها لأي module من غير ما يكون في inheritance
- الMixins بتستخدم لما تكون 
-عايز Features اختياريه يعني Module ممكن يحتاج http , smb , file system ف تضيف اللي محتاجه بس
-او انك عايز feature واحده لأكثر من Module زي login او تعامل مع sessions
##### في Ruby بيبقولو

في Ruby:
- Mixins = **Modules**
- بتتضاف بالكلمة:

`include`
مثال توضيحي:

`include Msf::Exploit::Remote::HttpClient`

ده يخلي الـ exploit:

- يعرف يبعت HTTP Requests
- من غير ما تكتب الكود بنفسك
---
