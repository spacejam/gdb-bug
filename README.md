# gdb-bug

`thread.c:1123: internal-error: void finish_thread_state(ptid_t): Assertion `tp' failed.`

## reproduce:

1. cargo build
2. ./debug.py


## full output

```
λ ./debug.py
GNU gdb (GDB) 8.0.1
Copyright (C) 2017 Free Software Foundation, Inc.
License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.  Type "show copying"
and "show warranty" for details.
This GDB was configured as "x86_64-pc-linux-gnu".
Type "show configuration" for configuration details.
For bug reporting instructions, please see:
<http://www.gnu.org/software/gdb/bugs/>.
Find the GDB manual and other documentation resources online at:
<http://www.gnu.org/software/gdb/documentation/>.
For help, type "help".
Type "apropos word" to search for commands related to "word".
seeding with 752097355547
+file target/debug/race
warning: Missing auto-load script at offset 0 in section .debug_gdb_scripts
of file /home/t/src/gdb-bug/target/debug/race.
Use `info auto-load python-scripts [REGEXP]' to list them.
+set non-stop on
+set confirm off
+b src/main.rs:8
Breakpoint 1 at 0xf0f2: file src/main.rs, line 8.
+r
[Thread debugging using libthread_db enabled]
Using host libthread_db library "/usr/lib/libthread_db.so.1".
[New Thread 0x7ffff6bff700 (LWP 11825)]
[New Thread 0x7ffff69fe700 (LWP 11826)]

Thread 2 "race" hit Breakpoint 1, race::bad_txn () at src/main.rs:8
8           if A.load(SeqCst) == 0 {
(gdb) running schedule
+d
Breakpoint 2 at 0x5555555630f2: file src/main.rs, line 8.
Breakpoint 3 at 0x55555556312a: file src/main.rs, line 9.
Breakpoint 4 at 0x555555596e71: file /checkout/src/liballoc/boxed.rs, line 241.
Breakpoint 5 at 0x55555556330a: file src/main.rs, line 12.
+t 2
[Switching to thread 2 (Thread 0x7ffff6bff700 (LWP 11825))]
#0  race::bad_txn () at src/main.rs:8
8           if A.load(SeqCst) == 0 {
+c
Selected thread is running.
(gdb) thread 2 hit breakpoint at src/main.rs:9
+t 2
[Switching to thread 2 (Thread 0x7ffff6bff700 (LWP 11825))]
#0  race::bad_txn () at src/main.rs:9
9               A.fetch_add(10, SeqCst);
+c
Selected thread is running.
(gdb) success!
+t 3
[Switching to thread 3 (Thread 0x7ffff69fe700 (LWP 11826))]
#0  race::bad_txn () at src/main.rs:8
8           if A.load(SeqCst) == 0 {
+c
Selected thread is running.
(gdb) restarting execution after running out of valid threads
+k
+file target/debug/race
warning: Missing auto-load script at offset 0 in section .debug_gdb_scripts
of file /home/t/src/gdb-bug/target/debug/race.
Use `info auto-load python-scripts [REGEXP]' to list them.
reseeding with 40033280878
+b src/main.rs:8
Breakpoint 6 at 0xf0f2: file src/main.rs, line 8.
+r
thread.c:1123: internal-error: void finish_thread_state(ptid_t): Assertion `tp' failed.
A problem internal to GDB has been detected,
further debugging may prove unreliable.

This is a bug, please report it.  For instructions, see:
<http://www.gnu.org/software/gdb/bugs/>.

[1]    11808 abort (core dumped)  ./debug.py
```
