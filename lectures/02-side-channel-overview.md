# Lesson 2: Side Channels and Covert Channels

## Table of Contents
- [Introduction and Gratitude](#introduction-and-gratitude)
- [What is a Side Channel?](#what-is-a-side-channel)
- [Covert Channel versus Side Channel](#covert-channel-versus-side-channel)
- [Examples of Side Channels](#examples-of-side-channels)
  - [Acoustic Side Channels](#acoustic-side-channels)
  - [Physical Side Channels (Heat, Power)](#physical-side-channels-heat-power)
  - [Network Side Channels](#network-side-channels)
  - [Timing Side Channels](#timing-side-channels)
- [Timing Side Channel Attack Example: Password Checker](#timing-side-channel-attack-example-password-checker)
- [Microarchitecture Side Channels Overview](#microarchitecture-side-channels-overview)
- [Historical Perspective and Threat Model](#historical-perspective-and-threat-model)
- [Disk-based Side Channel Attack Example](#disk-based-side-channel-attack-example)
- [General Side Channel Communication Model](#general-side-channel-communication-model)
- [Broader Microarchitectural Side Channel Surfaces](#broader-microarchitectural-side-channel-surfaces)
- [Real-Time Side Channel Demo Overview](#real-time-side-channel-demo-overview)
- [Attack Implementation Details](#attack-implementation-details)
- [Debugging Side Channels with Performance Counters](#debugging-side-channels-with-performance-counters)
- [Summary](#summary)

---

### Introduction and Gratitude
- Congratulations on being selected for the class.
- Appreciation for everyone managing the large volume of questions and participation.
- The session will focus on side channels and covert channels.
- Half the class is dedicated to analyzing a real side channel attack demo on a machine.

### What is a Side Channel?
- Side channel: An indirect leakage route through which attackers infer secrets by measuring unintended effects of a system.
- Unlike direct exploitation (bugs), side channels rely on secondary effects.
- Example: Domino's pizza runners correlate order surges with major events, predicting breaking news.

### Covert Channel versus Side Channel
- **Covert channel**: communication between cooperating sender and receiver, usually inside secure environments attempting stealthy communication.
- **Side channel**: attacker observes victim’s behavior without victim’s knowledge or cooperation.
- Both use channels not intended/designed for communication, thus violate system spec.
- Covert channels can have higher bandwidth due to cooperation.

### Examples of Side Channels

#### Acoustic Side Channels
- Eavesdropping on sound produced by devices can reveal secrets.
- Example: Listening to PIN presses on an ATM keypad using a cheap microphone + ML.
- Other examples: sounds of computer fans, hard disk drives, mechanical parts can leak info.

#### Physical Side Channels (Heat, Power)
- Heat emitted by devices or power consumption patterns correlate with internal activity.
- Example: Cats on warm computers sense temperature side channels.
- EM signals emitted by devices can be picked up by antennas.

#### Network Side Channels
- Metadata such as packet size, timing, and frequency leak info even when packets are encrypted.
- Example: Website fingerprinting attacks that infer visited sites despite encryption.
- Traffic patterns observed on shared routers can leak activity info.

#### Timing Side Channels
- Variations in response time of a system reveal secrets.
- Classic example: password checkers with early-exit comparisons leak matching prefix length.
- Countermeasure: constant-time programming that ensures uniform execution time regardless of input.

### Timing Side Channel Attack Example: Password Checker
- Naive password verification leaks info via early termination.
- Timing attack guesses password digit-by-digit by measuring operation time.
- Solutions involve constant-time comparisons or hashing, but remain complex in practice.

### Microarchitecture Side Channels Overview
- Attacker and victim sharing hardware (CPU cores, caches, memory buses) can leak info.
- Example: measuring timing differences in accessing microarchitectural resources.
- Course focus: microarchitectural channels in upcoming modules.

### Historical Perspective and Threat Model
- Early OS designs (1977 paper on VAX VM security kernel) considered isolation challenges.
- Devices like mechanical hard disks can leak info via access patterns observable through timing or ordering.
- Attacker can issue multiple requests and measure latencies/order of requests to infer victim's secret state.

### Disk-based Side Channel Attack Example
- Disk tracks and sectors accessed modulated by victim secret.
- Attacker schedules requests to measure latency/order to deduce the secret.
- Techniques involve preconditioning the disk state and monitoring response timing.

### General Side Channel Communication Model
- Victim domain and attacker domain are supposed isolated.
- Steps:
  1. Encode secret into a shared resource (transmitter).
  2. Channel modulated by victim’s activity.
  3. Attacker preconditions the channel state.
  4. Attacker monitors state changes (e.g., latency).
  5. Attacker decodes secret from observed changes.

### Broader Microarchitectural Side Channel Surfaces
- Side channels exist across processor cores, caches (L1, L2), GPUs, memory bus, storage, IO devices.
- Any shared resource could potentially be exploited for side channel attacks.

### Real-Time Side Channel Demo Overview
- Demonstrates a covert communication using microarchitectural side channels.
- Sender repeatedly accesses a large buffer, sending a heartbeat signal by memory accesses and sleeps.
- Receiver measures latency to access a buffer every second and detects signals through changes in access time.
- Engineering details include double buffering and pinning processes to cores to reduce noise.

### Attack Implementation Details
- Memory mapped large buffers to stress particular microarchitectural resources.
- Write access triggers lazy allocation and copy-on-write semantics to ensure real physical memory use.
- Use high precision timers and memory fences for accurate measurements.
- Analysis suggests communication channel might be DRAM or cache or TLB related; performance counters help identify.

### Debugging Side Channels with Performance Counters
- Use Linux `perf` tool to monitor cache misses, cache references, TLB misses, TLB shootdowns.
- Observed results showed high TLB misses correlated with buffer size.
- Frequent page table invalidations cause TLB flushes, impacting timing and attack efficacy.

### Summary
- Side channels exploit unintended communication routes from shared resources.
- They cover a broad spectrum: physical, network, timing, and microarchitectural.
- Practical attacks require precise engineering and understanding of system internals.
- Understanding root causes involves using tools like performance counters.
- Side channels are challenging but key areas in secure hardware design, which the course will explore deeply.