## Using SSH
- [Secure Shell (SSH)](https://en.wikipedia.org/wiki/SSH_\(Secure_Shell\)) is a network protocol that runs on port `22` by default and provides users such as system administrators a secure way to access a computer remotely
- SSH can be configured with password authentication or passwordless using [public-key authentication](https://serverpilot.io/docs/how-to-use-ssh-public-key-authentication/) using an SSH public/private key pair. SSH can be used to remotely access systems on the same network, over the internet, facilitate connections to resources in other networks using port forwarding/proxying, and upload/download files to and from remote systems.
- الssh هو بروتوكول شغال علي بورت 22 وده بيديك remote access علي الجهاز اللي بتكونكت عليه وطبعا الauth بتاعه عباره عن اسم الجهاز والip والباسورد او ممكن تلوجين بالpublic key
- SSH uses a client-server model, connecting a user running an SSH client application such as `OpenSSH` to an SSH server
- SSH more stable than reverse shell and can often be used as a "jump host" to enumerate and attack other hosts in the network, transfer tools, set up persistence
```
0xTantawy@htb[/htb]$ ssh Bob@10.10.10.10
Bob@remotehost's password: *********
Bob@remotehost#
```
--------
## Using Netcat
- Netcat, `ncat`, or `nc`, is an excellent network utility for interacting with TCP/UDP ports
 - `netcat` can be used to connect to any listening port and interact with the service running on that port
 ```
0xTantawy@htb[/htb]$ netcat 10.10.10.10 22

SSH-2.0-OpenSSH_8.4p1 Debian-3
```

`SSH-2.0-OpenSSH_8.4p1 Debian-3` is SSH banner, This technique is called `Banner Grabbing`
- الbanner grabbing بيجبلي معلومات عن البورت اللي انا عايزه وايه السservice اللي شغاله عليه
- الnetcat بتكون pre-installed in most linux distributions
- Another similar network utility is **socat**
-----
## Using Tmux
- Terminal multiplexers, like `tmux` or `Screen`, are great utilities for expanding a standard Linux terminal's features, like having multiple windows within one terminal and jumping between them
##### tmux Commands Cheat Sheet

| Command          | Discription                    |
| ---------------- | ------------------------------ |
| `tmux`           | Start tmux                     |
| `ctrl+b`         | tmux: default prefix           |
| `prefix c`       | tmux: new window               |
| `prefix 1`       | tmux: switch to window (`1`)   |
| `prefix shift+%` | tmux: split pane vertically    |
| `prefix shift+"` | tmux: split pane horizontally  |
| `prefix ->`      | tmux: switch to the right pane |
- The commands above cover some basic tmux usage. It is a powerful tool and can be used for many things, including logging, which is very important during any technical engagement. This cheatsheet is a very handy reference. Also, this Introduction to tmux video by ippsec is worth your time
----
## Using Vim
- Vim is a great text editor that can be used for writing code or editing text files on Linux systems.
- Once we open a file, we are in read-only normal mode, which allows us to navigate and read the file. To edit the file, we hit i to enter insert mode, shown by the "-- INSERT --" at the bottom of Vim
- Once we are finished editing a file, we can hit the escape key `esc` to get out of `insert mode`, back into `normal mode`. When we are in `normal mode`
##### Vim Commands Cheat Sheet

| Command | Description          |
| ------- | -------------------- |
| `x`     | Cut character        |
| `dw`    | Cut word             |
| `dd`    | Cut full line        |
| `yw`    | Copy word            |
| `yy`    | Copy full line       |
| `p`     | Paste                |
| `:1`    | Go to line number 1. |
| `:w`    | Write the file, save |
| `:q`    | Quit                 |
| `:q!`   | Quit without saving  |
| `:wq`   | Write and quit       |
https://vimsheet.com/
----
