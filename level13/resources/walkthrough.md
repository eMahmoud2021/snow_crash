# Flag13

```
level13@SnowCrash:~$ ls -la
total 20
dr-x------ 1 level13 level13  120 Mar  5  2016 .
d--x--x--x 1 root    users    340 Aug 30  2015 ..
-r-x------ 1 level13 level13  220 Apr  3  2012 .bash_logout
-r-x------ 1 level13 level13 3518 Aug 30  2015 .bashrc
-rwsr-sr-x 1 flag13  level13 7303 Aug 30  2015 level13
-r-x------ 1 level13 level13  675 Apr  3  2012 .profile
level13@SnowCrash:~$
```
`level13` is a binary. We have to use Ghidra to see what it contains and what it takes to work: 
```
scp -P 4242 level13@192.168.1.34:/home/user/level13/level13 ~/
```
and when I open and analyze it I find:
```
void main(void)
{
  __uid_t _Var1;
  char *param1;
  
  _Var1 = getuid();
  if (_Var1 != 0x1092) {
    _Var1 = getuid();
    printf("UID %d started us but we we expect %d\n",_Var1,0x1092);
                    /* WARNING: Subroutine does not return */
    exit(1);
  }
  param1 = ft_des("boe]!ai0FB@.:|L6l@A?>qJ}I");
  printf("your token is %s\n",param1);
  return;
}
```
`var1` takes the return value of `getuid()` and compares it with `0x1092`.
To get the token we have to modify the value stored in the variable storing the return value of the `getuid()` function.
And we need the value 4096 (0x1092 in decimals):
  ```
convert 0X1092 to decimal.

2 × 1 = 2
9 × 16 = 144
0 × 16 × 16 = 0
1 × 16 × 16 × 16 = 4096

2 + 144 + 0 + 4096 = 4242
```
So we try: 
```
level13@SnowCrash:~$ ./level13 
UID 2013 started us but we we expect 4242
```
The `getuid()` function returns 2013.
With [GDB](https://ftp.gnu.org/old-gnu/Manuals/gdb/html_chapter/gdb_13.html) we can make it store the value 4242.
From Ghidra we know that the register `eax` is compared with 4242, so we have to change its value:
```
0804859a 3d 92 10        CMP        EAX,0x1092
```

As [explained](https://www.rocq.inria.fr/secret/Anne.Canteaut/COURS_C/gdb.html), we may
* set a breakpoint:
```
(gdb) break *Ox804859a
```
* modify a variable:
```
(gdb) set $eax = 4242
```
* resume the execution of the program:
```
(gdb) run
```
* continue the execution:
```
(gdb) c
```

```
level13@SnowCrash:~$ gdb level13
GNU gdb (Ubuntu/Linaro 7.4-2012.04-0ubuntu2.1) 7.4-2012.04
Copyright (C) 2012 Free Software Foundation, Inc.
License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.  Type "show copying"
and "show warranty" for details.
This GDB was configured as "i686-linux-gnu".
For bug reporting instructions, please see:
<http://bugs.launchpad.net/gdb-linaro/>...
Reading symbols from /home/user/level13/level13...(no debugging symbols found)...done.
(gdb) break *0x804859a 
Breakpoint 1 at 0x804859a
(gdb) run
Starting program: /home/user/level13/level13 

Breakpoint 1, 0x0804859a in main ()
(gdb) set $eax = 4242
(gdb) c
Continuing.
your token is 2A31L79asukciNyi8uppkEuSx
[Inferior 1 (process 3172) exited with code 050]
level13@SnowCrash:~$ su level14
Password:
level14@SnowCrash:~$
```