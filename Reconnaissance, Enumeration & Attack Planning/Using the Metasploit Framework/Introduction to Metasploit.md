## About Tools
- في ناس مختلفه فالرأي عن الtools وازاي هيا بتأثر علي الpentester
1. في شق شايف ان الأدوات بتسهل الشغل زيادة عن اللزوم ومش بتدي فرصة للpentester إنه يثبت مهاراته
2. في شق تاني شايف ان الأدوات دي رحمة خصوصا للمبتدئين لأنها بتبسط الدنيا وبتوفر وقت
----
## سلبيات الموضوع ده
1. اول حاجه الtools بتحط ف حتة الcomfort zone وهتتكسل تتعلم مهارات جديده وهتعتمد عالاداه اعتماد كلي
2. لما الأداه بتنزل للعلن بتدي فرصة للscript kiddies انهم يضرو الناس بالtools ديط
----
## ايه الحل؟
- افهم أدواتك اللي بتستخدمها واقرأ الDocumentation والfunctions بتاعت التول
- حط ف بالك إن الأداه مساعد مش بديل
- خليك متطور دايما
----
## Metasploit
الMetasploit Project ده معمول بلغة Ruby
- الأداة دي بتخليك تكتب وتختبر وتنفذ exploit code
- الExploit Code ده ممكن يكون custom-made او متاخد من الdb بتاعت التول
- الMetasploit Framework فيها مجموعه من الأدوات اللي ممكن تtest بيها vulns وتعمل enum للnetwork وتنفذ attacks
- الMetasploit Project مجموعه من الأدوات الشائع استخدامها عشان تعملك بيئه pentest متكامله
- الModules دي بتكون فالmetasploit ودي عباره exploit poc اللي بتكون نازله اصلا قبل كده علي مواقع مختلفه او الmetaspolit ضايفاها فالdb بتاعها
- الفرق بين الExploit والPayload
-الExploit ده الكود اللي بيخليك تستغل الثغره
-الPayload ده الكود اللي بيتبعت بعد ما الثغره تستغل وده اللي بيديك access زي انه مثلا يفتحلك cmd
- بعد ماتخترق الجهاز الmsf بيسهل عليك عملية التنقل بين الاتصالات يعني لو اخترقت كذا ف نفس الوقت الmsf بتوفرلك طريقه تدير بيها sessions دي وتكمل شغلك
----
## Metasploit Framework Console
- الmsfconsole ده الterminal الخاص بالmetasploit
- عبارة عن centeralized console بيمسح للوصول لكل مميزات الmetasploit
The features that `msfconsole` generally brings are the following:

- It is the only supported way to access most of the features within `Metasploit`
- Provides a console-based interface to the `Framework`
- Contains the most features and is the most stable `MSF` interface
- Full readline support, tabbing, and command completion
- Execution of external commands in `msfconsole`
----
## Understanding the Architecture
- لازم نفهم الأداة شغالع ازاي وبتستخدم ايه عشان منبقاش بنستخدمها وخلاص كده
- مكان وجود الأداة ف كالي وباروت `/usr/share/metasploit-framework`
#### Data, Documentation, Lib
#### Modules

The Modules detailed above are split into separate categories in this folder. We will go into detail about these in the next sections. They are contained in the following folders:

```shell-session
0xTroj6n@htb[/htb]$ ls /usr/share/metasploit-framework/modules

auxiliary  encoders  evasion  exploits  nops  payloads  post
```
#### Plugins

Plugins offer the pentester more flexibility when using the `msfconsole` since they can easily be manually or automatically loaded as needed to provide extra functionality and automation during our assessment.

```shell-session
0xTroj6n@htb[/htb]$ ls /usr/share/metasploit-framework/plugins/

aggregator.rb      ips_filter.rb  openvas.rb           sounds.rb
alias.rb           komand.rb      pcap_log.rb          sqlmap.rb
auto_add_route.rb  lab.rb         request.rb           thread.rb
beholder.rb        libnotify.rb   rssfeed.rb           token_adduser.rb
db_credcollect.rb  msfd.rb        sample.rb            token_hunter.rb
db_tracker.rb      msgrpc.rb      session_notifier.rb  wiki.rb
event_tester.rb    nessus.rb      session_tagger.rb    wmap.rb
ffautoregen.rb     nexpose.rb     socket_logger.rb
```
#### Scripts

Meterpreter functionality and other useful scripts.

```shell-session
0xTroj6n@htb[/htb]$ ls /usr/share/metasploit-framework/scripts/

meterpreter  ps  resource  shell
```
#### Tools

Command-line utilities that can be called directly from the `msfconsole` menu.

```shell-session
0xTroj6n@htb[/htb]$ ls /usr/share/metasploit-framework/tools/

context  docs     hardware  modules   payloads
dev      exploit  memdump   password  recon
```
---
