# Flag09

```
level09@SnowCrash:~$ ls -la
total 24
dr-x------ 1 level09 level09  140 Mar  5  2016 .
d--x--x--x 1 root    users    340 Aug 30  2015 ..
-r-x------ 1 level09 level09  220 Apr  3  2012 .bash_logout
-r-x------ 1 level09 level09 3518 Aug 30  2015 .bashrc
-rwsr-sr-x 1 flag09  level09 7640 Mar  5  2016 level09
-r-x------ 1 level09 level09  675 Apr  3  2012 .profile
----r--r-- 1 flag09  level09   26 Mar  5  2016 token
level09@SnowCrash:~$ cat token
f4kmm6p|=�p�n��DB�Du{��
level09@SnowCrash:~$ cat -et token
f4kmm6p|=M-^B^?pM-^BnM-^CM-^BDBM-^CDu{^?M-^LM-^I$
level09@SnowCrash:~$ ./level09 token
tpmhr
level09@SnowCrash:~$
```

Dcode [code identifier](https://www.dcode.fr/identification-chiffrement) links me to an image of Anakin Skywalker on [imgur](https://imgur.com/a/tpmhr):

![anakin](341oyfV.jpeg)
```
~ $ scp -P 4242 level09@10.13.250.56:/home/user/level09/level09 ~/
           _____                      _____               _
          / ____|                    / ____|             | |
         | (___  _ __   _____      _| |     _ __ __ _ ___| |__
          \___ \| '_ \ / _ \ \ /\ / / |    | '__/ _` / __| '_ \
          ____) | | | | (_) \ V  V /| |____| | | (_| \__ \ | | |
         |_____/|_| |_|\___/ \_/\_/  \_____|_|  \__,_|___/_| |_|

  Good luck & Have fun

          10.0.2.15
level09@10.13.250.56's password:
level09                                                                               100% 7640     1.4MB/s   00:00

~ $ ghidra
```
Here is the decompiled `main()`:
```
size_t main(int param_1,int param_2)

{
  char cVar1;
  bool bVar2;
  long lVar3;
  size_t sVar4;
  int iVar5;
  int iVar6;
  uint uVar7;
  int in_GS_OFFSET;
  byte bVar8;
  char *pcVar9;
  FILE *pFVar10;
  int iVar11;
  uint local_120;
  char local_114 [256];
  int local_14;
  
  bVar8 = 0;
  local_14 = *(int *)(in_GS_OFFSET + 0x14);
  bVar2 = false;
  local_120 = 0xffffffff;
  iVar11 = 1;
  pFVar10 = (FILE *)0x0;
  lVar3 = ptrace(PTRACE_TRACEME,0,1,0);
  if (lVar3 < 0) {
    pcVar9 = "You should not reverse this";
    puts("You should not reverse this");
    sVar4 = 1;
  }
  else {
    pcVar9 = getenv("LD_PRELOAD");
    if (pcVar9 == (char *)0x0) {
      iVar11 = open("/etc/ld.so.preload",0);
      if (iVar11 < 1) {
        iVar5 = syscall_open("/proc/self/maps",0);
        if (iVar5 == -1) {
          iVar11 = 0x46;
          pFVar10 = (FILE *)0x1;
          pcVar9 = "/proc/self/maps is unaccessible, probably a LD_PRELOAD attempt exit..\n";
          fwrite("/proc/self/maps is unaccessible, probably a LD_PRELOAD attempt exit..\n",1,0x46,
                 stderr);
          sVar4 = 1;
        }
        else {
          do {
            do {
              while( true ) {
                pFVar10 = (FILE *)0x100;
                pcVar9 = local_114;
                iVar11 = iVar5;
                sVar4 = syscall_gets((int)pcVar9,0x100);
                if (sVar4 == 0) goto LAB_08048a77;
                iVar6 = isLib(local_114,0x8048c2b);
                if (iVar6 == 0) break;
                bVar2 = true;
              }
            } while (!bVar2);
            iVar6 = isLib(local_114,0x8048c30);
            if (iVar6 != 0) {
              if (param_1 == 2) goto LAB_08048996;
              iVar11 = 0x22;
              pFVar10 = (FILE *)0x1;
              pcVar9 = "You need to provied only one arg.\n";
              sVar4 = fwrite("You need to provied only one arg.\n",1,0x22,stderr);
              goto LAB_08048a77;
            }
            pcVar9 = afterSubstr(local_114,0x8048c57);
          } while (pcVar9 != (char *)0x0);
          iVar11 = 0x30;
          pFVar10 = (FILE *)0x1;
          pcVar9 = "LD_PRELOAD detected through memory maps exit ..\n";
          sVar4 = fwrite("LD_PRELOAD detected through memory maps exit ..\n",1,0x30,stderr);
        }
      }
      else {
        iVar11 = 0x25;
        pFVar10 = (FILE *)0x1;
        pcVar9 = "Injection Linked lib detected exit..\n";
        fwrite("Injection Linked lib detected exit..\n",1,0x25,stderr);
        sVar4 = 1;
      }
    }
    else {
      iVar11 = 0x25;
      pFVar10 = (FILE *)0x1;
      pcVar9 = "Injection Linked lib detected exit..\n";
      fwrite("Injection Linked lib detected exit..\n",1,0x25,stderr);
      sVar4 = 1;
    }
  }
LAB_08048a77:
  if (local_14 == *(int *)(in_GS_OFFSET + 0x14)) {
    return sVar4;
  }
                    /* WARNING: Subroutine does not return */
  __stack_chk_fail(pcVar9,pFVar10,iVar11);
LAB_08048996:
  local_120 = local_120 + 1;
  uVar7 = 0xffffffff;
  pcVar9 = *(char **)(param_2 + 4);
  do {
    if (uVar7 == 0) break;
    uVar7 = uVar7 - 1;
    cVar1 = *pcVar9;
    pcVar9 = pcVar9 + (uint)bVar8 * -2 + 1;
  } while (cVar1 != '\0');
  if (~uVar7 - 1 <= local_120) goto code_r0x080489ca;
  putchar((int)*(char *)(local_120 + *(int *)(param_2 + 4)) + local_120);
  goto LAB_08048996;
code_r0x080489ca:
  pcVar9 = (char *)0xa;
  pFVar10 = stdout;
  sVar4 = fputc(10,stdout);
  goto LAB_08048a77;
}

```
Actually, the program makes a sort of ROT ciphering on the string passed as parameter in which each character is converted into the nth letter following it in the ASCII table, the number being given by its position in the string:
```
level09@SnowCrash:~$ ./level09 aaaaaaaaaaaaaaaaaaaaaaaaaaaaa
abcdefghijklmnopqrstuvwxyz{|}
```
So I created a program that makes a rotation in the opposite direction:
```
$ cat rot.c
#include <stdio.h>
#include <fcntl.h>
#include <stdlib.h>
#include <unistd.h>

int     main(int ac, char *av[]) {

        char    buffer[1001];
        if (ac != 2) {
                write(1, "bad num of args", 15);
                exit(1);
        }
        int     fd = open(av[1], O_RDONLY);
        if (fd == -1) {
                write(1, "read failed", 11);
                exit(1);
        }
        int     rd = read(fd, buffer, 1000);
        buffer[rd] = 0;

        int     i = 0;
        while (buffer[i]) {
                char    c = buffer[i];
                if (c < i)
                        c = 256 - i + c;
                else
                        c -= i;
                write(1, &c, 1);
                i++;
        }
}

~ $ ./a.out token
f3iji1ju5yuevaus41q1afiuq�
```
So I tried `f3iji1ju5yuevaus41q1afiuq`:
```
level09@SnowCrash:~$ su flag09
Password:
Don't forget to launch getflag !
flag09@SnowCrash:~$ getflag
Check flag.Here is your token : s5cAJpM8ev6XHw998pRWG728z
flag09@SnowCrash:~$ su level10
Password:
level10@SnowCrash:~$
```