# The future of Spring Native

## High peak throughput at no cost with ML-enabled PGO 🚀

When GraalVM compiles your code AOT, it needs to make optimization decisions — which branches to optimize, what to inline, how aggressively to unroll loops. Normally these decisions are guided by profiles: data collected by actually running your program and measuring what happens.

The problem is that collecting profiles is expensive. You need to build twice, find representative inputs, run the profiling build. Many projects skip it entirely.

The goal is simple: predict how likely each branch of every if statement is to execute — without actually running the program.

The compiler represents your program as a Control Flow Graph (CFG), where every conditional (`if`/`while`/`for`) has two outgoing paths: True and False.

Profile inference assigns a probability to each branch. The compiler uses these to decide which code paths to optimize aggressively.

GraalVM's **GraalNN** does this with a Graph Neural Network. It works by analyzing each node in the CFG together with its neighbors — spreading structural information across the graph so that each node's representation reflects not just its own content, but its role in the broader control flow.

Each node is described using things like its estimated cost: GraalVM has a built-in cost model that knows, statically, that an integer add takes ~1 CPU cycle, a division ~20, a memory load potentially 64+ ([see an example](https://github.com/oracle/graal/blob/8f20e09967fee69c116548680856cdd1cc939905/compiler/src/jdk.graal.compiler/src/jdk/graal/compiler/nodes/calc/IntegerDivRemNode.java#L48)). No execution needed — just a lookup per operation type. Combined with structural signals like loop nesting depth, these per-node estimates give the model a meaningful description of what each branch actually does.


## Security first 🛡️



