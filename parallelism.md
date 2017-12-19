# Parallelism

Some notes on parallelism (parallel architectures) as my Computer
Architecture has become really rusty.

Disclaimer: these are adapted from the [Parallel Architectures](https://www.inf.ed.ac.uk/teaching/courses/pa/slides.html)
course at the University of Edinburgh (because Cambridge has denied me
access) and paraphrased with my own words.

## Overview

Instead of asking how can I make my processing faster, ask how can I
fully utilise all my processing resources?

Programming model:
- Execution stage (fetch, decode, execute, write back)
- Processor instruction (load, store, add, etc.) has multiple stages
- Thread has multiple instructions
- Process has multiple threads
- Session has multiple processes

Hardware variables:
- Length of processing pipe (i.e. number of execution stages)
   - Exploit using pipelining
- Width of processing pipe (i.e. number of simultaneous instructions,
amount of data per instruction)
   - Increase number of simultaneous same instructions (superscalar),
     increase number of simultaneous different instructions (VLIW), or
     increase amount of data per instruction (vector)
- Throughput of processing pipe
   - Reduce idleness when blocking/waiting by switch in other threads or
     processes (multithreading)
- Number of processing pipes (i.e. number of processors)
   - Increase number of processors (multiprocessing)

The techniques in each cell can be implemented independently of other
techniques, i.e. you can have superscalar processing without pipelining.

Level           | Software               | One processor                | Multiple processors
---             | ---                    | ---                          | ---
Execution stage | N/A                    | Pipelining                   | N/A
Instruction     | VLIW                   | VLIW, Superscalar            | N/A
Thread          | Concurrency primitives | Multithreading (f/c/s)*      | Multiprocessing
Process         | Kernel scheduler       | Multithreading (f/c/s)*      | Multiprocessing
Data            | VLIW, Vector           | Vector                       | ???

*(f/c/s) means (fine/coarse/simultaneous). Fine/coarse multithreading
runs only one thread at any one time; the "parallelism" arises from
switching to another thread when the previous thread executes a
high-latency instruction, thus increasing utilisation. The difference
between fine and coarse is how often the switching is performed; for
fine it's every cycle, for coarse it's when a high-latency instruction
is executed.

Simultaneous multithreading requires a superscalar processor and can be
truly parallel in the sense that if the width of the processor pipe is
large enough (e.g. four instructions), it can theoretically fetch
one instruction each from four different threads and execute them
simultaneously.

## Flynn's taxonomy

## Pipelining

## VLIW

## Superscalar

## Vector

## Multithreading

### Blocked (coarse-grained) multithreading

### Interleaved (fine-grained) multithreading

### Simultaneous multithreading

### "Hyperthreading"

## Multiprocessing

