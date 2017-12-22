# Parallelism

Some notes on parallelism (parallel architectures) as my Computer
Architecture has become really rusty.

Disclaimer: these are adapted from the [Parallel Architectures](https://www.inf.ed.ac.uk/teaching/courses/pa/slides.html)
course at the University of Edinburgh (because Cambridge has denied me
access) and paraphrased with my own words.

## Overview

Instead of asking how can I make my processing faster, ask how can I
fully utilise all my processing resources?

Computation model:
- Stage (fetch, decode, execute, write back)
- Processor instruction (load, store, add, etc.) has multiple stages
- Thread has multiple instructions
- Process has multiple threads
- Session has multiple processes

Variables:
- Length of processing pipe (i.e. number of execution stages)
   - Increase number of instructions in the pipe (not at equal stages
     of execution) using pipelining
- Width of processing pipe (i.e. number of simultaneous instructions,
amount of data per instruction)
   - Increase number of simultaneous same instructions (superscalar),
     increase number of simultaneous different instructions (VLIW), or
     increase amount of data per instruction (vector)
- Throughput of processing pipe
   - Reduce idleness when blocking/waiting by switching in other threads
     or processes (multithreading)
- Number of processing pipes
   - Increase number of processors (multiprocessing)

The techniques in each cell can be implemented independently of other
techniques, i.e. you can have superscalar processing without pipelining.

Level           | Software               | One processor                | Multiple processors
---             | ---                    | ---                          | ---
Instruction     | VLIW                   | Pipelining, VLIW, Superscalar| Impractical
Thread          | Concurrency primitives | Multithreading (f/c/s)*      | Multiprocessing
Process         | Kernel scheduler       | Multithreading (f/c/s)*      | Multiprocessing
Data            | VLIW, Vector           | Vector                       | Hadoop???

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

### Hyperthreading

Hyperthreading is Intel's implementation of simultaneous multithreading.
That's it: they are conceptually the same. Each physical Intel processor
appears as two logical processors ("width of pipe" is two? Anyway it's
superscalar), but under the hood they share the same resources.

So how does hyperthreading work? Consider a system with one physical
processor with hyperthreading (i.e. two logical processors). The OS
(which needs to support hyperthreading, by the way) doesn't know what's
happening underneath; when it schedules two threads, it could schedule
a thread to each logical processor.

Then what really happens is simple SMT at work: the single physical
processor consumes two instructions at each cycle (because it's
superscalar). If one thread is blocked because of a long instruction,
then the physical processor can consume two instructions from the other
thread. The point is, the pipe is big and hyperthreading tries to fill
idle bubbles (a legit term) in the pipeline.

Hyperthreading is a marketing term. And hyperthreading doesn't scale,
for obvious reasons: a hyperthreaded physical core is not the same as
two physical cores. After all, the logical processors share the same
physical resources, and there will be benefit (read: reduced idleness)
only if the instructions in two threads cause bubbles.

The OS needs to support hyperthreading, otherwise it may result in
degraded performance. Consider a system with two physical cores with
hyperthreading. That's four logical cores. If the OS didn't know
better (why should it?) and schedules two threads to run on two logical
processors that actually belong to the same physical processor, there
will be one completely idle physical processor and one overworked
physical processor.

What about AMD? AMD's first line of processors that will have SMT is the
Zen series of processors, which is released in 2017. Apparently AMD
received flak for dishonest marketing with "cores" and "modules" in
their previous Bulldozer line.

## Multiprocessing

