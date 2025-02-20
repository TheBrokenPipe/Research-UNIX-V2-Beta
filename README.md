# Research-UNIX-V2-Beta
A Research UNIX V2 beta from 1972 brought back to life.

This copy of Research UNIX is dated mid-1972 and sits between V1 and V2. Its kernel offers a 16 KiB (8K word) user core and is capable of executing UNIX V2 (0407) a.out formatted executables; however, none of the V2 syscalls are supported. It is the earliest surviving version of UNIX in machine-readable form.

It comes from the "s1" and "s2" tapes given to [@DoctorWkt](https://github.com/DoctorWkt) by Dennis Ritchie in 1997. The "s1" tape is a copy of the UNIX INIT DECtape containing the kernel and tools necessary for restoring the system from DECtapes, and the "s2" tape contains the rest of the system.

The entire system resides on an RF disk, with /usr also stored on the RF. This repo provides an image file of the RF disk - [`s1s2unix_rf.img`](./s1s2unix_rf.img).

# Emulation
There are two emulators known to boot this version of UNIX. [SIMH](https://opensimh.org) and [@aap](https://github.com/aap)'s [PDP-11 emulator](https://github.com/aap/pdp11).

My config for SIMH is as follows:
```
set cr disabled
set xq disabled
set rk disabled
set hk disabled
set rha disabled
set tm disabled
set rx disabled
set rl disabled
set tq disabled
set dci disabled
set cpu 11/20
set cpu 32K
set ke enabled
set rf 2p
set rf enabled
att rf s1s2unix_rf.img         <-- RF disk image
set tc enabled
set tc locked
att tc s1.itp                  <-- s1 tape
load m792low.load              <-- Modified UNIX ROM from unix-june72 project
dep system sr 173700
go 73700                       <-- Low address used by the modded ROM
```

While you technically can boot from the `s1` tape, it does not let you login due to `/etc/passwd` being missing, so you'll not be able to install it the intended way. Right now, just use my hand-rolled RF disk image and boot from the RF. Once booted, it looks something like:

```
login: root
root
# ls -la
total   42
 41 sdrwrw  7 root     80 Jan  1 00:02:02 .
 41 sdrwrw  7 root     80 Jan  1 00:02:02 ..
 43 sdrwrw  2 root    620 Jan  1 00:01:30 bin
147 l-rwrw  1 root  16448 Jan  1 00:33:51 core
 42 sdrwrw  2 root    250 Jan  1 00:01:51 dev
 49 sdrwrw  2 root    110 Jan  1 00:01:55 etc
 54 sdrwrw  2 root     50 Jan  1 00:00:52 tmp
 55 sdrwrw  7 root     80 Jan  1 00:00:52 usr
# ls -la usr
total    8
 55 sdrwrw  7 root     80 Jan  1 00:00:52 .
 41 sdrwrw  7 root     80 Jan  1 00:02:02 ..
 56 sdrwrw  2  28      60 Jan  1 00:02:22 fort
 57 sdrwrw  2 jack     50 Jan  1 00:02:39 jack
 58 sdrwrw  2   6      30 Jan  1 00:02:36 ken
 59 sdrwrw  2 root    120 Jan  1 00:00:52 lib
 60 sdrwrw  2 sys      50 Jan  1 00:02:45 sys
142 s-rwrw  1 jack     54 Jan  1 00:52:29 x
# ed
a
main() printf("hello world!\n");
.
w hello.c
33
q
# cc hello.c
I
II
# ls -l a.out
total    3
153 sxrwrw  1 root   1328 Jan  1 00:02:12 a.out
# a.out
hello world!
#
```

# Filesystem Layout
The filesystem on the RF disk has the following layout (files enclosed in square brackets are not real files):

```
Mode       UID GID  Size Date                Name
---------- --- --- ----- ------------------- ----------------
-rw-rw-rw-   0   0   512 1970-01-01 00:00:00 [vcboot]
-rw-rw-rw-   0   0  2048 1970-01-01 00:00:00 [bos]
-rw-rw-rw-   0   0 12288 1970-01-01 00:00:00 [wunix]
-rw-rw-rw-   0   0 12288 1970-01-01 00:00:00 [cunix]
-rw-rw-rw-   0   0  3072 1970-01-01 00:00:00 [unassigned]
-rwxr-xr-x   3   0    82 1972-01-17 17:53:35 /bin/chmod
-rwsr-xr-x   0   0   794 1972-04-19 23:45:12 /bin/date
-rwsr-xr-x   0   0  1290 1972-03-28 15:19:21 /bin/login
-rwsr-xr-x   0   0   232 1972-11-05 21:42:18 /bin/mkdir
-rwxr-xr-x   1   0   954 1972-01-19 17:20:54 /bin/sh
-rwsr-xr-x   0   0  3678 1972-12-30 04:08:39 /bin/tap
-rwxr-xr-x   3   0  2010 1972-01-17 17:53:39 /bin/ls
-rwxr-xr-x   3   0   718 1972-11-21 22:51:32 /bin/chown
-rwxr-xr-x   3   0   160 1972-01-17 17:53:36 /bin/cp
-rwxr-xr-x   3   0   104 1972-01-17 17:53:39 /bin/ln
-rwsr-xr-x   0   0   784 1972-01-17 17:46:57 /bin/mv
-rwxr-xr-x   3   0    93 1972-01-17 17:53:41 /bin/rm
-rwsr-xr-x   0   0   282 1972-09-27 21:37:12 /bin/rmdir
-rwxr-xr-x   3   0  1026 1972-01-17 17:53:42 /bin/stat
-rwxr-xr-x   3   0   524 1972-01-17 17:53:45 /bin/write
-rwxr-xr-x   3   0   480 1972-05-26 00:21:13 /bin/strip
-rwxr-xr-x   3   0   244 1972-01-17 17:56:30 /bin/dsw
-rwxr-xr-x   4   0  7154 1972-07-20 12:42:42 /bin/roff
-rwxr-xr-x   3   0   518 1972-06-29 20:09:07 /bin/nm
-rwxr-xr-x   3   0   560 1972-01-17 17:54:56 /bin/who
-rwxr-xr-x   3   0   464 1972-01-17 17:53:37 /bin/du
-rwxr-xr-x   3   0  5922 1972-01-17 17:56:30 /bin/bas
-rwsr-xr-x   1   0   192 1972-11-21 19:35:16 /bin/df
-rwxr-xr-x   3   0   420 1972-01-17 17:53:35 /bin/cmp
-rwxrwxrwx   3   0    76 1972-04-05 19:42:00 /bin/chball
-rwxr-xr-x   3   0  4066 1972-03-28 16:42:29 /bin/db
-rwxr-xr-x   3   0  2310 1972-01-26 17:20:48 /bin/ar
-rwxr-xr-x   3   0  2942 1972-06-29 19:26:31 /bin/ld
-rwxr-xr-x   3   0   134 1972-01-17 17:53:35 /bin/cat
-rwxr-xr-x   3   0  1166 1972-01-17 17:54:56 /bin/pr
-rwxr-xr-x   3   0   238 1972-06-29 18:37:31 /bin/un
-rwsr-xr-x   1   0   790 1972-11-25 14:24:14 /bin/tm
-rwxr-xr-x   3   0    70 1972-01-17 17:53:41 /bin/rew
-rwsr-xr-x   1   0  1084 1972-11-21 19:35:16 /bin/check
-rwxr-xr-x   3   0    80 1972-01-17 17:53:43 /bin/tty
-rwxr-xr-x   3   0   154 1972-01-17 17:53:40 /bin/mesg
-rwxr-xr-x   3   0  3998 1972-05-09 15:23:19 /bin/ed
-rwxr-xr-x   3   0  1912 1972-01-04 17:05:31 /bin/form
-rwxr-xr-x   3   0   644 1972-01-17 17:54:56 /bin/wc
-rwxr-xr-x   3   0   300 1972-01-17 17:54:56 /bin/od
-rwsr-xr-x   0   0  3940 1972-01-05 19:26:54 /bin/mail
-rwxr-xr-x   3   0   604 1972-11-21 22:51:32 /bin/sort
-rwxr-xr-x   3   0    16 1972-01-17 17:56:30 /bin/:
-rwxr-xr-x   3   0  1054 1972-01-17 17:56:30 /bin/echo
-rwxr-xr-x   3   0  1154 1972-01-17 17:56:29 /bin/goto
-rwxr-xr-x   3   0  1844 1972-01-17 17:56:29 /bin/if
-rwxr-xr-x   3   0   276 1972-01-17 17:56:30 /bin/exit
-rwxr-xr-x   3   0   750 1972-01-17 17:54:56 /bin/skip
-rwxr-xr-x   3   0  1048 1972-01-31 18:14:08 /bin/stty
-rwxrwxrwx   6   0  2860 1972-03-06 12:23:39 /bin/cal
-rwxrwxrwx   3   0   214 1972-07-14 01:21:57 /bin/sum
-rwxr-xr-x   3   0  6846 1972-04-13 20:50:45 /bin/dc
-rwsr-xr-x   0   0   746 1972-05-02 22:54:21 /bin/su
-rwxr-xr-x  10   0  2730 1972-05-26 23:37:25 /bin/fc
-rwxrwxrwx   3   0  4672 1972-06-29 21:53:34 /bin/cc
-rwxr-xr-x   3   0  1446 1972-06-29 20:15:35 /bin/size
-rwsr-xr-x   1   0   872 1972-05-26 00:26:05 /bin/ds
-rwxr-xr-x   3   0   330 1972-05-26 00:21:08 /bin/find
-rwxr-xr-x   3   0  7582 1972-06-29 17:45:21 /bin/as
-rwxrwxrwx   0   0   698 1972-01-01 01:35:34 /bin/maki
-rwxr-xr-x   1   0   424 1973-02-05 14:50:16 /etc/init
-rwxrwxrwx   3   0   446 1972-04-06 16:40:13 /etc/getty
-rw-r--r--   3   0  2082 1972-03-27 16:42:26 /etc/suftab
-rw-r--r--   1   0    70 1973-02-05 14:38:57 /etc/uids
-rw-------   1   0   221 1973-02-05 14:35:10 /etc/passwd
-rwxr-xr-x   1   0   186 1972-01-06 18:01:18 /etc/msh
-rwxr-xr-x   1   0  2662 1972-01-06 18:01:17 /etc/glob
-rwxrwxrwx   3   0  5778 1972-06-29 17:47:16 /etc/as2
-rw-rw-rw-   0   0   512 1972-01-01 01:33:59 /etc/std0
-rw-rw-rw-   0   0 16448 1972-01-01 01:36:34 /usr/sys/core
-rwxrwxrwx   0   0  2192 1972-01-01 01:37:02 /usr/sys/a.out
-rw-r--r--   1   0  1928 1972-01-01 01:36:55 /usr/sys/maki.s
-rw-rw-rw-   0   0 12158 1972-06-29 18:40:35 /usr/lib/liba.a
-rw-rw-rw-   0   0  6626 1972-06-19 10:47:35 /usr/lib/libb.a
-rw-rw-rw-   0   0  3498 1972-06-08 17:10:05 /usr/lib/bilib.a
-rwxrwxrwx   3   0 17092 1972-06-05 21:26:04 /usr/lib/c1
-rwxrwxrwx   3   0 19948 1972-06-29 21:21:47 /usr/lib/c0
-rw-rw-rw-  28   0  9158 1972-06-29 20:46:07 /usr/lib/libf.a
-rw-rw-rw-   0   0  5242 1972-06-29 21:20:00 /usr/lib/libc.a
-rw-rw-rw-  28   0   304 1972-06-29 20:31:32 /usr/lib/fr0.o
-rw-rw-rw-  28   0 12222 1972-06-29 20:37:02 /usr/lib/filib.a
-rw-rw-rw-   0   0   108 1972-06-29 21:11:20 /usr/lib/crt0.o
-rw-rw-rw-  10   0    84 1972-01-01 00:58:12 /usr/jack/x.f
-rwxrwxrwx   0   0  7514 1972-01-01 00:58:33 /usr/jack/a.out
-rw-rw-rw-   0   0   312 1972-01-01 00:58:26 /usr/jack/x.o
-rwxr-xr-x  28   0  3462 1972-06-30 16:35:35 /usr/fort/fc1
-rwxr-xr-x  28   0  3238 1972-06-30 16:35:36 /usr/fort/fc2
-rwxr-xr-x  28   0  6840 1972-06-30 16:35:41 /usr/fort/fc3
-rwxr-xr-x  28   0  4918 1972-06-30 16:35:43 /usr/fort/fc4
-rw-rw-rw-  10   0    54 1972-01-01 00:52:29 /usr/x
-rw-rw-rw-   1   0   670 1972-01-01 01:33:33 /usr/ken/maki.s
-rw-r--r--   0   0   142 1972-01-01 01:33:53 /tmp/utmp
-r--r--r--   1   0  1664 1972-01-01 01:31:19 /tmp/etma
-rw-rw-rw-   0   0    26 1972-01-01 01:35:47 /tmp/ttmp
-rw-rw-rw-   0   0 16448 1972-01-01 00:33:51 /core
```
