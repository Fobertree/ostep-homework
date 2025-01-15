# 4. [Processes (CPU Intro)](https://pages.cs.wisc.edu/~remzi/OSTEP/cpu-intro.pdf)

### 1. . Run `process-run.py` with the following flags: `-l 5:100,5:100`. What should the CPU utilization be (e.g., the percent of time the CPU is in use?) Why do you know this? Use the `-c` and `-p` flags to see if you were right.
- The instruction basically is run 2 processes 5 instructions each 100% chance issuing CPU instructions. 100%, because we have 100% chance issuing cpu instructions (no I/O instructions means no chance of cpu getting blocked or context switching before either process completes)

### 2.  Now run with these flags: `./process-run.py -l 4:100,1:0`. These flags specify one process with 4 instructions (all to use the CPU), and one that simply issues an I/O and waits for it to be done. How long does it take to complete both processes? Use `-c` and `-p` to find out if you were right.
- Assuming each instruction takes 1 clock cycle, it would take 4 clock cycles plus the time for the I/O instruction to complete

### 3. Switch the order of the processes: `-l 1:0,4:100`. What happens now? Does switching the order matter? Why? (As always, use `-c` and `-p` to see if you were right)
- The I/O operation by process 1 is started, where the I/O operation is blocked (then ready in the time between the I/O completing and process 2 finishing execution, if applicable). Process 2 executes its instructions, then it returns the CPU core (assuming that we are doing this all in a single CPU core, so only one process can be executed at a time) to executing process 1
- Switching the order does matter. Now, we start the I/O instruction first. The I/O instruction can complete asynchronously while process 2 is executing non-I/O instructions. This means we can finish execution of all instructions sooner.

### 4. We’ll now explore some of the other flags. One important flag is `-S`, which determines how the system reacts when a process issues an I/O. With the flag set to SWITCH ON END, the system will NOT switch to another process while one is doing I/O, instead waiting until the process is completely finished. What happens when you run the following two processes (`-l 1:0,4:100 -c -S` SWITCH ON END), one doing I/O and the other doing CPU work?
- It should overall take at least the same amount of time or longer, since it is generally more efficient to context switch between processes

### 5. Now, run the same processes, but with the switching behavior set to switch to another process whenever one is WAITING for I/O (-l 1:0,4:100 -c -S SWITCH ON IO). What happens now? Use `-c` and `-p` to confirm that you are right.
- It should be more efficient since we aren't wasting time trying to run a blocked process

### 6.  One other important behavior is what to do when an I/O completes. With -I IO RUN LATER, when an I/O completes, the process that issued it is not necessarily run right away; rather, whatever was running at the time keeps running. What happens when you run this combination of processes? (`./process-run.py -l 3:0,5:100,5:100,5:100 -S SWITCH ON IO -c -p -I IO RUN LATER`) Are system resources being effectively utilized?
- It should still be efficient since we are processing instructions as much as possible. If process doesn't switch back, we are still running an active process and its instructions.

### 7. Now run the same processes, but with `-I IO RUN IMMEDIATE` set, which immediately runs the process that issued the I/O. How does this behavior differ? Why might running a process that just completed an I/O again be a good idea?
- Immediately switches back to the first process on completed I/O. This might be more useful if we want to prioritize a process that happens to be heavy on I/O


Skipped Q8