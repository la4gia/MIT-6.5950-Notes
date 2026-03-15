# Lesson 1: Introduction to Secure Hardware Design

## Table of Contents
- [Overview](#overview)
- [Why Secure Hardware Design?](#why-secure-hardware-design)
- [Attacks in Hardware: Categories and Commonalities](#attacks-in-hardware-categories-and-commonalities)
- [Abstractions in Computer Systems](#abstractions-in-computer-systems)
- [Cross-Layer Thinking: Why Attacks Span Layers](#cross-layer-thinking-why-attacks-span-layers)
- [Demo: An Abstraction Example with Performance Counters](#demo-an-abstraction-example-with-performance-counters)
- [Mitigations: Design Tradeoffs](#mitigations-design-tradeoffs)
- [Hardware Security Features as Guarantees](#hardware-security-features-as-guarantees)
- [CPU Execution Process: Instruction Lifecycle](#cpu-execution-process-instruction-lifecycle)
- [Processor Performance and Pipelining](#processor-performance-and-pipelining)
- [Resolving Pipeline Hazards](#resolving-pipeline-hazards)
- [Caching and Memory](#caching-and-memory)
- [Course Philosophy and Learning Approach](#course-philosophy-and-learning-approach)
- [What Comes Next in the Course](#what-comes-next-in-the-course)
- [Glossary](#glossary)
- [Appendix: Quick References](#appendix-quick-references)

---

### Overview
- Focus: secure hardware design—understanding hardware attacks, mitigations, and reasoning about security across the system.
- Core idea: many attacks arise from interactions across abstraction layers, not just a single component.
- Learning approach: hands-on exploration of attacks and mitigations, with emphasis on cross-layer reasoning.

### Why Secure Hardware Design?
- Hardware attacks are increasingly visible and important (side channels, speculative execution issues, memory faults like Rowhammer).
- Some attacks exploit design choices or microarchitectural features introduced for performance, not merely bugs.
- Security must be considered alongside performance and power budgets, since adding security features often impacts these areas.

### Attacks in Hardware: Categories and Commonalities
- Common attack classes you’ll study:
  - Side-channel attacks: leaking information through timing, power, electromagnetic signals, etc.
  - Speculative execution vulnerabilities: mis-speculation paths exposing sensitive data.
  - Memory-based attacks (e.g., Rowhammer): exploiting physical memory effects to induce faults.
- Important ideas:
  - Hardware bugs (deviations from spec) are not the only issue; many attacks target design choices and microarchitectural behavior.
  - Attacks often exploit existing microarchitectural mechanisms (caches, branch predictors, speculative paths) rather than a single flawed component.
- Takeaway: develop a cross-layer intuition to categorize and reason about attacks as they span software, hardware, and microarchitecture.

### Abstractions in Computer Systems
- The computer system is built in layers, each hiding complexity from the layer above:
  - Applications and libraries (what you write and call)
  - System software (OS, kernel, etc.)
  - Instruction Set Architecture (ISA)
  - CPU microarchitecture (pipelines, caches, memory hierarchy)
  - Digital circuits (combinational/sequential logic)
  - Analog circuits (voltages, thresholds, noise)
- Abstractions enable programmability and portability but can obscure how security properties actually emerge at different layers.
- A useful mindset: security analysis often requires understanding how layers interact (not just isolated behavior at one layer).

### Cross-Layer Thinking: Why Attacks Span Layers
- To understand many hardware attacks, you must connect dots across layers:
  - An attack may leverage a microarchitectural feature introduced for performance.
  - The same feature interacts with software (OS scheduling, memory hierarchy) and with hardware (caches, timing).
- Examples you’ll see include how timing and behavior can vary with software actions, even when a given layer appears to be “safe” in isolation.

### Demo: An Example with Performance Counters
- Performance counters are special hardware registers counting instructions, memory accesses, cache hits/misses, etc.
- Running simple programs that do similar tasks but differ in calls to I/O or system libraries can reveal huge differences in executed instruction counts due to abstractions.
- Example: Counting instructions for a loop vs. the same loop with a print statement shows how many hidden operations (system calls, kernel code) get executed.
- Implication: printing or debugging can drastically perturb microarchitectural states critical for hardware security experiments.

### Mitigations: Design Tradeoffs
- Comprehensive mitigation tries to block all attacks in an attack class but often with high overhead.
- Ad hoc mitigation blocks some attacks with less overhead but may miss some vectors.
- Real-world mitigations reflect practical tradeoffs among security, performance, and power.
- The course emphasizes learning to critically analyze mitigations’ capabilities and limitations.

### Hardware Security Features as Guarantees
- Hardware features like secure enclaves can provide strong guarantees even under untrusted OS.
- Example: Apple M1’s secure enclave enables authenticated boot and isolated execution.
- However, correctness and integration are crucial, and hardware features are not a silver bullet.

### CPU Execution Process: Instruction Lifecycle

#### Steps an instruction goes through inside the CPU:
1. **Fetch**:
   - The program counter (PC) points to the memory address of the next instruction.
   - Memory returns the binary instruction.
   - Instruction is stored in an instruction buffer or queue.

2. **Decode**:
   - Extract opcode (operation type), source registers, destination registers from the instruction.
   - Resolve the instruction format.

3. **Execute**:
   - Read data from registers specified as sources.
   - Perform the operation (e.g., arithmetic addition) in the Arithmetic Logic Unit (ALU).
   - For load/store, compute memory address.

4. **Memory access** (if needed):
   - For load/store instructions, access main memory to read/write data.

5. **Write-back**:
   - Write results back into the destination register.

- These stages form the classic 5-stage pipeline of simple CPUs.
- Depending on instruction type, some stages might be bypassed or handled differently.

### Processor Performance and Pipelining
- Single-cycle processors execute one instruction per clock cycle but with a very long cycle time (sum of all stages).
- Pipelining splits instruction processing into stages operating in parallel on different instructions.
- This allows starting a new instruction each clock cycle, greatly improving throughput while cycle time is the max stage delay.
- Latency of each instruction may stay the same or increase slightly, but overall throughput increases dramatically.

### Resolving Pipeline Hazards
- **Data hazards**: One instruction depends on the result of a previous instruction still in pipeline.
- **Control hazards**: Caused by branch instructions, where next instruction address depends on branch outcome.
- Common solutions:
  - Stall: insert bubbles (wait) until hazard clears.
  - Bypass (forwarding): feed results directly between pipeline stages without waiting for write-back.
  - Speculation: predict branch outcomes and continue executing; roll back if prediction wrong.

### Caching and Memory
- Memory latency is high compared to CPU speed.
- Caches (small fast memory) are placed between CPU and main memory to speed access.
- Concepts include associativity, inclusiveness, and coherence — to be studied in detail later.
- Effective caching is key for performance and is also relevant for hardware attacks exploiting timing.

### Course Philosophy and Learning Approach
- Learn through attacks and defenses to reveal system abstractions and interactions.
- Cross-layer thinking is necessary: security breaches often exploit interface boundaries.
- Hands-on labs with detailed microarchitectural interactions.
- Careful with debugging tools like print statements that perturb microarchitectural state.

### What Comes Next in the Course
- Study classical attacks and their common components.
- Explore mitigation techniques and their practical tradeoffs.
- Introduce secure hardware modules and their role.
- Hands-on challenges and recitations to build intuition.

### Glossary
- **Side-channel attack**: extracting secrets over leaked side effects like timing or power.
- **Spectre/Meltdown**: speculative execution vulnerabilities leaking data.
- **Rowhammer**: memory fault attack inducing bit flips via row activation repetition.
- **Abstraction**: simplifying interface hiding complexity underneath.
- **ISA**: Instruction Set Architecture, the programmer-visible CPU specification.
- **Microarchitecture**: implementation of a CPU (pipelines, caches, etc.).
- **Performance counter**: hardware register that counts hardware events.
- **Pipeline hazard**: stall-inducing conditions due to data/control dependencies.

### Appendix: Quick References
- Performance counters enable counting CPU internal events for analysis.
- Common abstractions span from applications to the analog level.
- Attacks and mitigations are often rooted in microarchitectural behavior underlaid by hardware-software interactions.
- Tradeoffs among security, performance, and power drive design decisions.
