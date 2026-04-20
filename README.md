# The future of Spring Native

## High peak throughput at no cost with ML-enabled PGO 🚀

When GraalVM compiles your code AOT, it needs to make optimization decisions — which branches to optimize, what to inline, how aggressively to unroll loops. Normally these decisions are guided by profiles: data collected by actually running your program and measuring what happens.

The problem is that collecting profiles is expensive. You need to build twice, find representative inputs, run the profiling build. Many projects skip it entirely.

The goal is simple: predict how likely each branch of every if statement is to execute — without actually running the program.

The compiler represents your program as a Control Flow Graph (CFG), where every conditional (`if`/`while`/`for`) has two outgoing paths: True and False.

Profile inference assigns a probability to each branch. The compiler uses these to decide which code paths to optimize aggressively.

GraalVM's **GraalNN** does this with a Graph Neural Network. It works by analyzing each node in the CFG together with its neighbors — spreading structural information across the graph so that each node's representation reflects not just its own content, but its role in the broader control flow.

Each node is described using things like its estimated cost: GraalVM has a built-in cost model that knows, statically, that an integer add takes ~1 CPU cycle, a division ~20, a memory load potentially 64+ ([see an example](https://github.com/oracle/graal/blob/8f20e09967fee69c116548680856cdd1cc939905/compiler/src/jdk.graal.compiler/src/jdk/graal/compiler/nodes/calc/IntegerDivRemNode.java#L48)). No execution needed — just a lookup per operation type. Combined with structural signals like loop nesting depth, these per-node estimates give the model a meaningful description of what each branch actually does.

- 👩‍💻 Demo: ML PGO in action

## Zero configuration migration with [`-H:Preserve`](https://github.com/oracle/graal/pull/10180)

- `-H:Preserve=package=<package>` preserves all elements from a given package
- `-H:Preserve=module=<module>` preserves all elements from a given module
- `-H:Preserve=package=<package-wildcard>` preserves all elements from packages captured by the wildcard. For example, -H:Preserve=package=my.app.*.
- `-H:Preserve=all` preserves all elements from the JDK, the classpath, and the module path
- General approach to complicated libraries:
    - Use [reachablity repo](https://github.com/oracle/graalvm-reachability-metadata/tree/master/metadata) → use framework annotations like `@Reflective` → generate configuration in `.json` files 

## Security first 🛡️

- 👩‍💻 [Demo](https://github.com/alina-yur/native-spring-boot): SBOM options: `--enable-sbom=[embed|export|classpath|class-level]`
- Adavanced obfuscation: `-H:AdvancedObfuscation=`

# Vector Search with Spring AI 🐶

- 👩‍💻 [Demo](https://github.com/alina-yur/oracle-database-vector-search): Pet store vector search with Spring Boot, Oracle Database, and GraalVM

## Spring shell with a twist 👻

👩‍💻 [Demo](https://github.com/alina-yur/native-spring-shell): Run: `./demo.sh`

## TUI

- 👩‍💻 [Demo](https://github.com/danvega/spring-initializr-tui): Spring Initializr terminal UI by Dan Vega
- 👩‍💻 [Demo](https://github.com/alina-yur/sbb-terminal-app): SBB terminal app built with Spring Boot, GraalVM, and TamboUI

## What's Next

- [GenShenandoah GC in Native Image](https://github.com/orgs/oracle/projects/6/views/1?pane=issue&itemId=130712659&issue=oracle%7Cgraal%7C12237)
- [Web Image (javac)](https://graalvm.github.io/graalvm-demos/native-image/wasm-javac/)
- [Native Image Layers](https://github.com/oracle/graal/issues/7626)
    - 👩‍💻 Demo: Layers
        - `hello-english`: share of RAM is about 63 MB
            - all of its private memory
            - plus only its slice of shared memory
- 👩‍💻 Demo: [Project Crema: Open World for Native Image](https://github.com/orgs/oracle/projects/6?pane=issue&itemId=113766307&issue=oracle%7Cgraal%7C11327)
