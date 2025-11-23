###### Shell
- الshell هو برنامج بيخلي الuser بيتحكم فالكمبيوتر عن طريق instructions بيديها للكمبيوتر عشان ينفذها وبيظهرله output نتيجة الأوامر دي طبعا (Bash, Zsh, cmd, PowerShell)
- احنا كا pentesters الshell عادة ده بيكون نتيجة الexpoliting بتاع الattack اللي بنفذه
- دايما بنسمع جمل زي "انا اخدت شيل" "انا مسكت شيل" "انا جوا الشيل" ودي معناها كلها اننا خدنا أكسس علي الجهاز الضحيه
- دا الcommon goal للpentester وهو بيعمل عملية الpentesting
---
## Why Get a Shell?
- خلي بالك الshell بيدينا direct access علي الOS System commands والfile system لذلك لو احنا اخدنا اكسس هنقدر نعمل enumerating للسيستم ومن خلال الshell ممكن نعمل privilege escalation او pivoting او حتي transfer files
- فكرة انك تاخد session shell كده انت بتكون جوا جهاز الضحيه يعني بتضمن لنفسك الوصول المتكرر للنظام حتي لو اتعمله restart
- الوصول عبر سطر الأوامر (CLI) بيكون **أصعب في الملاحظة** والمتابعة من قبل فرق الأمن، مقارنة بالوصول الرسومي عن بعد (Remote Accessing a graphical shell) عبر بروتوكولات زي **VNC** أو **RDP**، اللي غالبًا بتترك آثارًا أو بتكون أسهل في المراقبة

| **Perspective**               | **Description**                                                                                                                                                                                                                                                                                                                                                                                     |
| ----------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Computing`                   | The text-based userland environment that is utilized to administer tasks and submit instructions on a PC. Think Bash, Zsh, cmd, and PowerShell.                                                                                                                                                                                                                                                     |
| `Exploitation` `&` `Security` | A shell is often the result of exploiting a vulnerability or bypassing security measures to gain interactive access to a host. An example would be triggering [EternalBlue](https://www.cisecurity.org/wp-content/uploads/2019/01/Security-Primer-EternalBlue.pdf) on a Windows host to gain access to the cmd-prompt on a host remotely.                                                           |
| `Web`                         | This is a bit different. A web shell is much like a standard shell, except it exploits a vulnerability (often the ability to upload a file or script) that provides the attacker with a way to issue instructions, read and access files, and potentially perform destructive actions to the underlying host. Control of the web shell is often done by calling the script within a browser window. |

---
## Payloads Deliver us Shells

a `payload` can be defined in a few different ways:

- *Networking* : encapsulated data portiob of a packet traversing modern computer networks
- *Basic Computing* : a paylaod is the portion of an instruction set that defines the action to be taken
- *Programming* : the data portion referenced or carried by the programming lang instruction
- *Expolitation & Security* : a payload is code crafted with the intent to expolit a vuln on a computer system. the term payload can decribe types of malware, including but not limited to ransomware
---
