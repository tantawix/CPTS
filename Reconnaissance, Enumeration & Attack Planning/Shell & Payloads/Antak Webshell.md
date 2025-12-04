## ASPX Explained
- ال`Active Server Page Extended` (`ASPX`) ده عباره عن extension مكتوب الASP .NET بلغة #C 
---
## Antak Webshell
- الAntak ده web shell مبني علي ASP .NET بواسطة Nishang project
- الNighang ده Offensive PowerShell toolset بتديك اختيارات وامتيازات كتير كا pentester
- الAntak بتستخدم الPowerShell عشان تتفاعل مع الهوست
----
## Working with Antak

The Antak files can be found in the `/usr/share/nishang/Antak-WebShell` directory.

```shell-session
[!bash!]$ ls /usr/share/nishang/Antak-WebShell

antak.aspx  Readme.md
```

Antak web shell functions like a Powershell Console. However, it will execute each command as a new process. It can also execute scripts in memory and encode commands you send. As a web shell, Antak is a pretty powerful tool.

---
## Antak Demonstration

Now that we understand what Antak is and how it works let's put it to the test against the same web application from the Laudanum section. If you wish to follow along with this demonstration, you will need to add an entry into your `/etc/hosts` file on your attack VM or within Pwnbox for the host we are attacking. That entry should read: `<target ip> status.inlanefreight.local`. Once this is done, as long as you are on the VPN or using Pwnbox, you can also play and explore this demonstration.
#### Move a Copy for Modification

```shell-session
[!bash!]$ cp /usr/share/nishang/Antak-WebShell/antak.aspx /home/administrator/Upload.aspx
```

Make sure you set credentials for access to the web shell. Modify `line 14`, adding a user (green arrow) and password (orange arrow). This comes into play when you browse to your web shell, much like Laudanum. This can help make your operations more secure by ensuring random people can't just stumble into using the shell. It can be prudent to remove the ASCII art and comments from the file. These items in a payload are often signatured on and can alert the defenders/AV to what you are doing

For the sake of demonstrating the tool, we are uploading it to the same status portal we used for Laudanum. That host was a Windows host, so our shell should work just fine with PowerShell. Upload the file and then navigate to the page for use. It will give you a user and password prompt. Remember, with this web application, the files are stored in the `\\files\` directory. When you navigate to the `upload.aspx` file, you should see a prompt as we have below
![](Pasted%20image%2020251202202736.png)

----
