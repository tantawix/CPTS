- كل نظام تشغيل عنده shell سواء لينكس او ويندوز ولازم نكون عارفين ان في برامج بتكون موجود بتشتغل كا terminal emulator

|**Terminal Emulator**|**Operating System**|
|:--|:--|
|[Windows Terminal](https://github.com/microsoft/terminal)|Windows|
|[cmder](https://cmder.app/)|Windows|
|[PuTTY](https://www.putty.org/)|Windows|
|[kitty](https://sw.kovidgoyal.net/kitty/)|Windows, Linux and MacOS|
|[Alacritty](https://github.com/alacritty/alacritty)|Windows, Linux and MacOS|
|[xterm](https://invisible-island.net/xterm/)|Linux|
|[GNOME Terminal](https://en.wikipedia.org/wiki/GNOME_Terminal)|Linux|
|[MATE Terminal](https://github.com/mate-desktop/mate-terminal)|Linux|
|[Konsole](https://konsole.kde.org/)|Linux|
|[Terminal](https://en.wikipedia.org/wiki/Terminal_\(macOS\))|MacOS|
|[iTerm2](https://iterm2.com/)|MacOS|

---
## Command Language Interpreters
- بالظبط زي الشخص اللي بيكون مترجم نفس الكلام هنا ان الCLI ده بيشتغل كا مترجم
- ال`command language interpreter` ده برنامج بيشتغل عشان يترجم الأوامر اللي انت بتديها والتاسكات اللي انت بتأمر الos بيها عشان يشتغل عليها
- الCLI ده بيتكون من 3 حاجات شغاله مع بعض:
**-Operating System**
execute commands
**-Terminal Emulator**
(ex. PowerShell, cmd, Terminal)
**-Command Language Interpreter**
takes commands and send it to OS
- امثله بقي علي الCLI زي:
**Linux**
-bash
-sh
-zsh
**Windows**
-cmd.exe
-PowerShell
-WSH
- الكلام ده كله مفيد جدا عشان لما تاخد شيل على جهاز، لازم تبقى فاهم:

-الجهاز ده شغال بإيه؟ bash؟ cmd؟ powershell؟
-أي أوامر ينفع تتكتب؟
-أي سكريبتينج لنجويج أستخدمها؟ 
-نوع الشيل بيأثر على نوع الباي لود اللي هتستخدمه.

## مثال بسيط:

لو أخدت Reverse Shell على Windows:

- لو CMD:  
    تكتب أوامر زي `dir`, `whoami`, `ipconfig`
    
- لو PowerShell:  
    عندك أوامر أقوى بكتير زي `Get-Process`, `Get-LocalUser`
    

لكن لو أخدت شيل bash على Linux:

- الأوامر هتبقى `ls`, `cat`, `ps`, `ifconfig`
---
## Hands-on with Terminal Emulators and Shells
- لما تفتح الterminal emulator بتاعك وتشوف ال`$` دي معناها ان هنا بنستخدم لغة Bash,Zsh,POSIX ولغات تانيه بتبدأ بال$ وبنسميها ال`shell prompt`
- طيب في لغات تانيه مفيش فيها `shell prompt` فا انا كده هعرف ازاي اكتب بلغة ايه؟
- هنا بقي هنستخدم امر اسمه `ps`
#### Shell Validation From 'ps'

```shell-session
0xTroj6n@htb[/htb]$ ps

    PID TTY          TIME CMD
   4232 pts/1    00:00:00 bash
  11435 pts/1    00:00:00 ps
```
- او أمر `env`
#### Shell Validation Using 'env'

```shell-session
0xTroj6n@htb[/htb]$ env

SHELL=/bin/bash
```