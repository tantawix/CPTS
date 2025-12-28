- لو عملت تحديث للmetasploit من الترمينال عادي هينزل كل الexploits والادوات الجديده اللي المبرمجين ضافوها إنما انت لو عايز ثغرة معينه بس مش لازم تحدث البرنامج كله تقدر تحمل الملف بتاع الثغره دي وتضيفه للميتاسبلويت
- موقع ExploitDB هيساعدنا جدا فالموضوع ده وتقدر تسيرش علي custom exploits مثلا دلوقتي لقينا exploit لNagios3 وده الexploit بيستغلcommand injection vuln ملقيتهاش فالميتاسبلويت تروح مدور عليها ف اداة اسمها `searchsploit` ودي بتجيب من exploitdb علاطول

```shell-session
[!bash!]$ searchsploit nagios3

--------------------------------------------------------------------------------------------------------------------------------------------- ---------------------------------
 Exploit Title                                                                                                                               |  Path
--------------------------------------------------------------------------------------------------------------------------------------------- ---------------------------------
Nagios3 - 'history.cgi' Host Command Execution (Metasploit)                                                                                  | linux/remote/24159.rb
Nagios3 - 'history.cgi' Remote Command Execution                                                                                             | multiple/remote/24084.py
Nagios3 - 'statuswml.cgi' 'Ping' Command Execution (Metasploit)                                                                              | cgi/webapps/16908.rb
Nagios3 - 'statuswml.cgi' Command Injection (Metasploit)                                                                                     | unix/webapps/9861.rb
--------------------------------------------------------------------------------------------------------------------------------------------- ---------------------------------
Shellcodes: No Results
```
- لو عايز تفلتر البحث في searchsploit
- 
```shell-session
[!bash!]$ searchsploit -t Nagios3 --exclude=".py"

--------------------------------------------------------------------------------------------------------------------------------------------- ---------------------------------
 Exploit Title                                                                                                                               |  Path
--------------------------------------------------------------------------------------------------------------------------------------------- ---------------------------------
Nagios3 - 'history.cgi' Host Command Execution (Metasploit)                                                                                  | linux/remote/24159.rb
Nagios3 - 'statuswml.cgi' 'Ping' Command Execution (Metasploit)                                                                              | cgi/webapps/16908.rb
Nagios3 - 'statuswml.cgi' Command Injection (Metasploit)                                                                                     | unix/webapps/9861.rb
--------------------------------------------------------------------------------------------------------------------------------------------- ---------------------------------
Shellcodes: No Results
```
#### MSF - Directory Structure

```shell-session
[!bash!]$ ls /usr/share/metasploit-framework/

app     db             Gemfile.lock                  modules     msfdb            msfrpcd    msf-ws.ru  ruby             script-recon  vendor
config  documentation  lib                           msfconsole  msf-json-rpc.ru  msfupdate  plugins    script-exploit   scripts
data    Gemfile        metasploit-framework.gemspec  msfd        msfrpc           msfvenom   Rakefile   script-password  tools
```

```shell-session
[!bash!]$ ls .msf4/

history  local  logos  logs  loot  modules  plugins  store
```
- لازم لو عايز تضيف اي exploit للميتا تحطه فالdir ده
- لو الثغرة عبارة عن exploit لنظام linux ونوعها remote، لازم تعمل المسار ده بالظبط: `~/.msf4/modules/exploits/linux/remote/`
#### MSF - Loading Additional Modules at Runtime

```shell-session
[!bash!]$ cp ~/Downloads/9861.rb /usr/share/metasploit-framework/modules/exploits/unix/webapp/nagios3_command_injection.rb
[!bash!]$ msfconsole -m /usr/share/metasploit-framework/modules/
```
عشان نخلي الميتا تشوف بقي الملف بالحاجات الجديده اللي ضفناها هنكتب امر `loadpath` او `reload_all`

---
## Porting Over Scripts into Metasploit Modules
- ازاي لو في exploit مكتوب بلغة زي ruby مثلا نحوله للغه تانيه
1. **القاعدة الذهبية: "ما تبدأش من الصفر"**

بدل ما تضيع وقتك في كتابة كود Ruby من الأول، الميتاسبلويت بيعتمد على نظام **القوالب (Boilerplate)**.

- بتدور على موديول شغال لـ نفس البرنامج (زي **Bludit**) أو بيستهدف نفس الخدمة (زي **HTTP**).
- بتاخد الكود بتاعه وتعدل عليه (Repurpose).

2. **التجهيز والترتيب**

لازم تحط الملف في المسار الصح عشان الميتافلويت "يشوفه" وسط زمايله.
- **الأمر:**
    
    ```
cp ~/Downloads/48746.rb /usr/share/metasploit-framework/modules/exploits/linux/http/bludit_auth_bruteforce_mitigation_bypass.rb
    ```
    
- **شروط التسمية:** لازم تستخدم `snake_case` (حروف صغيرة وشرطة تحت `_`) وابعد عن الـ dashes `-`.

###### المكونات الأساسية للموديول (Inside the Ruby File)

أي موديول احترافي بيتكون من أجزاء ثابتة لازم تفهمها:

1. **الـ Mixins (المكتبات الجاهزة)**

دي أهم حتة، بتستخدم كلمة `include` في أول الكود عشان تنادي على وظائف جاهزة.

- **مثال:** لو بتخترق موقع، هتحتاج `include Msf::Exploit::Remote::HttpClient`.
    
- **ليه؟** عشان بدل ما تكتب 50 سطر كود عشان تبعت POST request، الـ Mixin بيديك أمر جاهز اسمه `send_request_cgi`.
    
**2. التنسيق (Tabs vs Spaces)**

- الميتافلويت صارم جداً في التنسيق؛ لازم تستخدم **Hard Tabs** (زرار الـ Tab في الكيبورد) مش المسافات، وإلا الموديول ممكن يضرب Error وهو بيعمل Load.

3. **التوثيق (Documentation)**

لازم ترجع للموقع الرسمي بتاع Metasploit عشان تعرف الـ **Classes** والـ **Methods** المتاحة ليك، لأن كل بروتوكول (HTTP, SMB, FTP) ليه Mixins مختلفة تماماً

---