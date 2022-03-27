
# 1

> Run process-run.py with the following flags: -l 5:100,5:100.
What should the CPU utilization be (e.g., the percent of time the
CPU is in use?) Why do you know this? Use the -c and -p flags to
see if you were right.

```sh
./process-run.py -l 5:100,5:100
```
we run two processes for 5 instructions each. That means process 1 has to run for 5, then
process 2 for 5, so in total it takes 10.

```sh
./process-run.py -l 5:100,5:100 -cp
```

# 2

> Now run with these flags: ./process-run.py -l 4:100,1:0.
These flags specify one process with 4 instructions (all to use the
CPU), and one that simply issues an I/O and waits for it to be done.
How long does it take to complete both processes? Use -c and -p
to find out if you were right.

This will run a process for 4 CPU cycles, then do an IO on the other. I'm not sure how we are meant to know how long IO lasts...

```sh
./process-run.py -l 4:100,1:0
```

```sh
./process-run.py -l 4:100,1:0 -cp
```

Output:

```sh
Time        PID: 0        PID: 1           CPU           IOs
  1        RUN:cpu         READY             1
  2        RUN:cpu         READY             1
  3        RUN:cpu         READY             1
  4        RUN:cpu         READY             1
  5           DONE        RUN:io             1
  6           DONE       WAITING                           1
  7           DONE       WAITING                           1
  8           DONE       WAITING                           1
  9           DONE       WAITING                           1
 10           DONE       WAITING                           1
 11*          DONE   RUN:io_done             1

Stats: Total Time 11
Stats: CPU Busy 6 (54.55%)
Stats: IO Busy  5 (45.45%)
```

# 3

> Switch the order of the processes: -l 1:0,4:100. What happens
now? Does switching the order matter? Why? (As always, use -c
and -p to see if you were right)

Switching probably means that we continue the other process while waiting for IO?

```sh
./process-run.py -l 1:0,4:100
./process-run.py -l 1:0,4:100 -cp
```

output:

```sh
Time        PID: 0        PID: 1           CPU           IOs
  1         RUN:io         READY             1
  2        WAITING       RUN:cpu             1             1
  3        WAITING       RUN:cpu             1             1
  4        WAITING       RUN:cpu             1             1
  5        WAITING       RUN:cpu             1             1
  6        WAITING          DONE                           1
  7*   RUN:io_done          DONE             1

Stats: Total Time 7
Stats: CPU Busy 6 (85.71%)
Stats: IO Busy  5 (71.43%)
```

# 4

> We’ll now explore some of the other flags. One important flag is
-S, which determines how the system reacts when a process is-
sues an I/O. With the flag set to SWITCH ON END, the system
will NOT switch to another process while one is doing I/O, in-
stead waiting until the process is completely finished. What hap-
pens when you run the following two processes (-l 1:0,4:100
-c -S SWITCH ON END), one doing I/O and the other doing CPU
work?

Now we block on IO so it takes 4 or 5 clock cycles before process two can do the thing.

# 5

> Now, run the same processes, but with the switching behavior set
to switch to another process whenever one is WAITING for I/O (-l
1:0,4:100 -c -S SWITCH ON IO). What happens now? Use -c
and -p to confirm that you are right.

This is back to not having to wait for IO to finish.

# 6

> One other important behavior is what to do when an I/O com-
pletes. With -I IO RUN LATER, when an I/O completes, the pro-
cess that issued it is not necessarily run right away; rather, whatever
was running at the time keeps running. What happens when you
run this combination of processes? (Run ./process-run.py -l
3:0,5:100,5:100,5:100 -S SWITCH ON IO -I IO RUN LATER
-c -p) Are system resources being effectively utilized?

```sh
./process-run.py -l 3:0,5:100,5:100,5:100 -S SWITCH_ON_IO -I IO_RUN_LATER -cp
```

Here we kick off other processes and run io when all the CPU work is done, meaning you end up running all IOs sequentially.

# 7

> Now run the same processes, but with -I IO RUN IMMEDIATE set,
which immediately runs the process that issued the I/O. How does
this behavior differ? Why might running a process that just com-
pleted an I/O again be a good idea?

```sh
./process-run.py -l 3:0,5:100,5:100,5:100 -S SWITCH_ON_IO -I IO_RUN_IMMEDIATE -cp
```

Here we run IO as soon as it's available, effectively the "waiting for the IO" happens while the other processes are using CPU?

# 8

> Now run with some randomly generated processes: -s 1 -l 3:50,3:50
or -s 2 -l 3:50,3:50 or -s 3 -l 3:50,3:50. See if you can
predict how the trace will turn out. What happens when you use
the flag -I IO RUN IMMEDIATE vs. -I IO RUN LATER? What hap-
pens when you use -S SWITCH ON IO vs. -S SWITCH ON END?






