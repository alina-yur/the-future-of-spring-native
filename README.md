# the-future-of-spring-native

## ML PGO

When GraalVM compiles your code ahead-of-time, it needs to make optimization decisions — which branches to optimize, what to inline, how aggressively to unroll loops. Normally these decisions are guided by profiles: data collected by actually running your program and measuring what happens.

The problem: collecting profiles is expensive. You need to build twice, find representative inputs, run the profiling build. Many projects skip it entirely.

The goal is simple: predict how likely each branch of every if statement is to execute — without actually running the program.

The compiler represents your program as a Control Flow Graph (CFG), where every conditional (`if`/`while`/`for`) has two outgoing paths: True and False.

Profile inference assigns a probability to each branch — e.g. 0.87 for True. The compiler uses these to decide which code paths to optimize aggressively.

GraalVM's **GraalNN** does this with a Graph Neural Network. For every conditional branch in your program, it answers: how likely is the True path to execute?

It works by analyzing each node in the CFG together with its neighbors — spreading structural information across the graph so that each node's representation reflects not just its own content, but its role in the broader control flow.

Each node is described using things like its estimated cost: GraalVM has a built-in cost model that knows, statically, that an integer add takes ~1 CPU cycle, a division ~20, a memory load potentially 64+. No execution needed — just a lookup per operation type. Combined with structural signals like loop nesting depth, these per-node estimates give the model a meaningful description of what each branch actually does.


