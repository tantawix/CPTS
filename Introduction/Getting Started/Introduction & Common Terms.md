## Infosec Overview
**Information Security** (infosec) is a vast field. The field has grown and evolved greatly in the last few years. It offers many specializations, including but not limited to:
- Network and infrastructure security
- Application security
- Security testing
- Systems auditing
- Business continuity planning
- Digital forensics
- Incident detection and response
Data can be electronic or physical and tangible (e.g., design blueprints) or intangible (knowledge). A common phrase that will come up many times in our infosec career is protecting the "confidentiality, integrity, and availability of data," or the **CIA triad**
## Risk Management Process

| Step                   | Explanation                                                                                                                                                                               |
| ---------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Identifying the Risk` | Identifying risks the business is exposed to, such as legal, environmental, market, regulatory, and other types of risks.                                                                 |
| `Analyze the Risk`     | Analyzing the risks to determine their impact and probability. The risks should be mapped to the organization's various policies, procedures, and business processes.                     |
| `Evaluate the Risk`    | Evaluating, ranking, and prioritizing risks. Then, the organization must decide to accept (unavoidable), avoid (change plans), control (mitigate), or transfer risk (insure).             |
| `Dealing with Risk`    | Eliminating or containing the risks as best as possible. This is handled by interfacing directly with the stakeholders for the system or process that the risk is associated with.        |
| `Monitoring Risk`      | All risks must be constantly monitored. Risks should be constantly monitored for any situational changes that could change their impact score, `i.e., from low to medium or high impact`. |
## Common Terms
#### Shell
the shell is a program that takes input from the user via the keyboard and passes these commands to the operating system to perform a specific function.
- الshell هو واجهة سطر اوامر بتدخلو command معين يرد عليك بoutput حسب الinput اللي انت دخلته
- زي مثلا ال linux terminal او Windows PowerShell او الCMD
- معظم أنظمة **Linux** بتشتغل ببرنامج اسمه **Bash**، وده اختصار لـ **Bourne Again Shell**. الـ **Bash** ده عبارة عن برنامج "Shell" بنستخدمه عشان نتعامل مع نظام التشغيل عن طريق الأوامر

| **Shell Type**  | **Description**                                                                                                                                                                                                                                   |
| --------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Reverse shell` | Initiates a connection back to a "listener" on our attack box.                                                                                                                                                                                    |
| `Bind shell`    | "Binds" to a specific port on the target host and waits for a connection from our attack box.                                                                                                                                                     |
| `Web shell`     | Runs operating system commands via the web browser, typically not interactive or semi-interactive. It can also be used to run single commands (i.e., leveraging a file upload vulnerability and uploading a `PHP` script to run a single command. |
- عشان احصل علي shell علي جهاز الضحيه ده بيتم عن طريق script ايا كان كان كبير او بسيط بس بيتم عن طريقه بكتب الاسكريبت ده بلغات زي Python, Go, PHP اي لغه مش هتفرق المهم ان الكود يعمل المهمه المعينه اللي عايزنها وهي got the shell
## What is a Port?
- Ports are virtual points where network connections begin and end. They are software-based and managed by the host operating system.
- Ports are associated with a specific process or service and allow computers to differentiate between different traffic types (SSH traffic flows to a different port than web requests to access a website even though the access requests are sent over the same network connection).
- البورت هو عامل زي الشباك بتاع بيتك لو مش مقفول او كويس وبإحكام ممكن الحرامي يخش البيت (unauthorized access)
- نفس الكلام بس فالمجال بتاعنا هو البورت اللي بيشتغل عليه خدمات كل بورت ليه رقم بيكون شغال عليه الخدمه دي يعني مثلا ال HTTP بيشتغل علي بورت 80 (علي فكره مش شرط يكون علي بورت 80 بس ده الشائع يعني) او مثل الHTTPS علي بورت 443
- There are two categories of ports, [Transmission Control Protocol (TCP)](https://en.wikipedia.org/wiki/Transmission_Control_Protocol), and [User Datagram Protocol (UDP)](https://en.wikipedia.org/wiki/User_Datagram_Protocol).  
- `TCP` is connection-oriented, meaning that a connection between a client and a server must be established before data can be sent. The server must be in a listening state awaiting connection requests from clients.  
- `UDP` utilizes a connectionless communication model. There is no "handshake" and therefore introduces a certain amount of unreliability since there is no guarantee of data delivery. `UDP` is useful when error correction/checking is either not needed or is handled by the application itself. `UDP` is suitable for applications that run time-sensitive tasks since dropping packets is faster than waiting for delayed packets due to retransmission, as is the case with `TCP` and can significantly affect a real-time system. There are `65,535` `TCP` ports and `65,535` different `UDP` ports, each denoted by a number. Some of the most well-known `TCP` and `UDP` ports are listed below:
- في نوعين من الports هما TCP و UDP
- الTCP ده connection-oriented يعني بيبدأ يعمل handshake مع السيرفر قبل ما يبعت الداتا عشان يضمن ان الاتصال شغال تمام ومفيش اي فقدان فالداتا او قطع فالاتصال
- الUDP بقي هو هنا بينقل البيانات ومش بيهتم اذا كان في فقدان او تأخير ولا لأ هو اهم حاجه انه ينقلها وخلاص وده لأن الUDP بيشتغل مع التطبيقات ال run time يعني لو في استريم مثلا او فيديو او لعبة مش لسه هتستني السيرفر يفتح connection وليله كبيره وساعتها الاتصال هيبقي بطئ جدا انما الUDP اسرع بكتير

| Port(s)         | Protocol              |
| --------------- | --------------------- |
| `20`/`21` (TCP) | `FTP`                 |
| `22` (TCP)      | `SSH`                 |
| `23` (TCP)      | `Telnet`              |
| `25` (TCP)      | `SMTP`                |
| `80` (TCP)      | `HTTP`                |
| `161` (TCP/UDP) | `SNMP`                |
| `389` (TCP/UDP) | `LDAP`                |
| `443` (TCP)     | `SSL`/`TLS` (`HTTPS`) |
| `445` (TCP)     | `SMB`                 |
| `3389` (TCP)    | `RDP`                 |
## What is a Web Server
- A web server is an application that runs on the back-end server, which handles all of the `HTTP` traffic from the client-side browser, routes it to the requests destination pages, and finally responds to the client-side browser. Web servers usually run on TCP ports `80` or `443`, and are responsible for connecting end-users to various parts of the web application
- الويب سيرفر عباره عن سيرفر او جهاز يعني مهتمه انه يهندل طلبات الHTTP اللي جياله ويرد عليك ب response بصفحة الويب اللي انت طالبها منو ويرد علي الclient-side browser اللي هو المتصفح يعني والمتصفحه بيبدأ يرندرلك ملفات الhtml والcss والjs اللي الويب سيرفر اداهاله
- مواقع الويب بيكون فيها ثغرات واحنا كا pentesters لازم نعرف الثغرات دي ونكتشفها
- The current OWASP Top 10 list is:

|Number|Category|Description|
|---|---|---|
|1.|[Broken Access Control](https://owasp.org/Top10/A01_2021-Broken_Access_Control/)|Restrictions are not appropriately implemented to prevent users from accessing other users accounts, viewing sensitive data, accessing unauthorized functionality, modifying data, etc.|
|2.|[Cryptographic Failures](https://owasp.org/Top10/A02_2021-Cryptographic_Failures/)|Failures related to cryptography which often leads to sensitive data exposure or system compromise.|
|3.|[Injection](https://owasp.org/Top10/A03_2021-Injection/)|User-supplied data is not validated, filtered, or sanitized by the application. Some examples of injections are SQL injection, command injection, LDAP injection, etc.|
|4.|[Insecure Design](https://owasp.org/Top10/A04_2021-Insecure_Design/)|These issues happen when the application is not designed with security in mind.|
|5.|[Security Misconfiguration](https://owasp.org/Top10/A05_2021-Security_Misconfiguration/)|Missing appropriate security hardening across any part of the application stack, insecure default configurations, open cloud storage, verbose error messages which disclose too much information.|
|6.|[Vulnerable and Outdated Components](https://owasp.org/Top10/A06_2021-Vulnerable_and_Outdated_Components/)|Using components (both client-side and server-side) that are vulnerable, unsupported, or out of date.|
|7.|[Identification and Authentication Failures](https://owasp.org/Top10/A07_2021-Identification_and_Authentication_Failures/)|Authentication-related attacks that target user's identity, authentication, and session management.|
|8.|[Software and Data Integrity Failures](https://owasp.org/Top10/A08_2021-Software_and_Data_Integrity_Failures/)|Software and data integrity failures relate to code and infrastructure that does not protect against integrity violations. An example of this is where an application relies upon plugins, libraries, or modules from untrusted sources, repositories, and content delivery networks (CDNs).|
|9.|[Security Logging and Monitoring Failures](https://owasp.org/Top10/A09_2021-Security_Logging_and_Monitoring_Failures/)|This category is to help detect, escalate, and respond to active breaches. Without logging and monitoring, breaches cannot be detected..|
|10.|[Server-Side Request Forgery](https://owasp.org/Top10/A10_2021-Server-Side_Request_Forgery_%28SSRF%29/)|SSRF flaws occur whenever a web application is fetching a remote resource without validating the user-supplied URL. It allows an attacker to coerce the application to send a crafted request to an unexpected destination, even when protected by a firewall, VPN, or another type of network access control list (ACL).|
