# [#](https://funtl.com/zh/linux/Linux-常用命令-开关机命令.html#linux-开关机命令)Linux 开关机命令

shutdown 命令可以用来进行关机程序，并且在关机以前传送讯息给所有使用者正在执行的程序，shutdown 也可以用来重开机。

| 命令     | 语法                                            | 参数       | 参数说明                                                     |
| :------- | :---------------------------------------------- | :--------- | :----------------------------------------------------------- |
| shutdown | shutdown [-t seconds] [-rkhncfF] time [message] |            |                                                              |
|          |                                                 | -t seconds | 设定在几秒钟之后进行关机程序                                 |
|          |                                                 | -k         | 并不会真的关机，只是将警告讯息传送给所有只用者               |
|          |                                                 | -r         | 关机后重新开机（重启）                                       |
|          |                                                 | -h         | 关机后停机                                                   |
|          |                                                 | -n         | 不采用正常程序来关机，用强迫的方式杀掉所有执行中的程序后自行关机 |
|          |                                                 | -c         | 取消目前已经进行中的关机动作                                 |
|          |                                                 | -f         | 关机时，不做 fcsk 动作(检查 Linux 档系统)                    |
|          |                                                 | -F         | 关机时，强迫进行 fsck 动作                                   |
|          |                                                 | time       | 设定关机的时间                                               |
|          |                                                 | message    | 传送给所有使用者的警告讯息                                   |

## [#](https://funtl.com/zh/linux/Linux-常用命令-开关机命令.html#重启)重启

- reboot
- shutdown -r now

## [#](https://funtl.com/zh/linux/Linux-常用命令-开关机命令.html#关机)关机

- shutdown -h now

上次更新: 2018-12-30 15:32:03

← [Linux 系统管理命令](https://funtl.com/zh/linux/Linux-常用命令-系统管理命令.html)