# Software and Engineering Laws

> **TL;DR** — Eponymous laws, rules of thumb and design principles that recur in software engineering: performance and scaling limits, queues, distributed-systems trade-offs, how codebases evolve, craft heuristics, security axioms, technology trend "laws" and human–computer interaction laws. Most are empirical observations, not physical laws: use them as prompts for questions, not as proofs. Organizational laws (Conway, Parkinson, Goodhart, ...) live in [organizational-and-management-laws.md](organizational-and-management-laws.md); network-value laws (Metcalfe, Reed, Sarnoff) and Jevons live in [economics-and-strategy-models.md](economics-and-strategy-models.md).

Entry format: **Statement** / **Origin** / **In practice** / **Example** / **Link**.

Contents:
1. [Performance, scaling and hardware](#performance-scaling-and-hardware)
2. [Queues and flow](#queues-and-flow)
3. [Distributed systems and interfaces](#distributed-systems-and-interfaces)
4. [Complexity, evolution and estimation](#complexity-evolution-and-estimation)
5. [Lehman's laws of software evolution](#lehmans-laws-of-software-evolution)
6. [Craft and design principles](#craft-and-design-principles)
7. [Unix philosophy and language philosophies](#unix-philosophy-and-language-philosophies)
8. [Security principles](#security-principles)
9. [Technology trend laws](#technology-trend-laws)
10. [Human–computer interaction laws](#humancomputer-interaction-laws)

---

## Performance, scaling and hardware

### Amdahl's law
- **Statement:** The speed-up from parallelising a task is limited by the fraction that must remain serial: `S(n) = 1 / ((1 − p) + p/n)`.
- **Origin:** Gene Amdahl, AFIPS conference, 1967.
- **In practice:** Before adding cores, nodes or people, find the serial part (locks, a single DB writer, a coordinating meeting). If 10% is serial, the maximum speed-up is 10× no matter how much you scale out.
- **Example:** A batch job is 95% parallelisable; with infinite workers it still cannot beat 20× faster. Removing a global lock is worth more than doubling the cluster.
- **Link:** https://en.wikipedia.org/wiki/Amdahl%27s_law

### Gustafson's law
- **Statement:** If problem size grows with available processors, the scaled speed-up grows almost linearly: `S = n − (1 − p)(n − 1)`.
- **Origin:** John L. Gustafson, 1988, as a counterpoint to Amdahl.
- **In practice:** Amdahl assumes fixed work; Gustafson assumes fixed time. More hardware usually lets you do *more* (bigger models, finer resolution) rather than the same thing faster.
- **Example:** A data platform that gains nodes processes more partitions in the same nightly window rather than finishing the same job sooner.
- **Link:** https://en.wikipedia.org/wiki/Gustafson%27s_law

### Universal Scalability Law
- **Statement:** Throughput under load is limited by contention (serialisation, as in Amdahl) and coherency cost (crosstalk between nodes), which can make throughput *decrease* past a peak.
- **Origin:** Neil J. Gunther, 1993 onward.
- **In practice:** Fit measured throughput at several concurrency levels to find where adding nodes or threads starts to hurt. Explains retrograde scaling in chatty clusters and lock-heavy databases.
- **Example:** A cache cluster gets slower at 32 nodes than at 16 because every write invalidates across all peers.
- **Link:** https://en.wikipedia.org/wiki/Neil_J._Gunther

### Knuth's premature optimization principle
- **Statement:** Premature optimisation is "the root of all evil" — most effort spent on efficiency in non-critical code is wasted; focus on the critical few percent once identified.
- **Origin:** Donald Knuth, "Structured Programming with go to Statements", 1974 (the full passage stresses not ignoring the critical 3%).
- **In practice:** Write clear code, measure with a profiler, then optimise hot paths. Do not use the quote to excuse ignoring algorithmic complexity or known architectural performance needs.
- **Example:** Replace an O(n²) join found by profiling; don't hand-unroll loops in configuration parsing.
- **Link:** https://en.wikipedia.org/wiki/Program_optimization

### Wirth's law
- **Statement:** Software gets slower faster than hardware gets faster.
- **Origin:** Niklaus Wirth, "A Plea for Lean Software", 1995 (credits Martin Reiser).
- **In practice:** Hardware gains are eaten by layers, frameworks and features. Performance budgets (bundle size, p95 latency, memory) keep this in check.
- **Example:** A chat app consuming 1 GB RAM on a machine 100× faster than the one that ran a full office suite.
- **Link:** https://en.wikipedia.org/wiki/Wirth%27s_law

### Dennard scaling
- **Statement:** As transistors shrink, power density stays roughly constant, so smaller transistors also run faster at equal power.
- **Origin:** Robert Dennard et al., 1974. Broke down around 2005–2007 due to leakage current.
- **In practice:** The end of Dennard scaling is why clock speeds plateaued and the industry moved to multicore, GPUs and specialised accelerators — and why concurrency became every developer's problem.
- **Example:** CPU clock speeds have hovered at 3–5 GHz for roughly two decades while core counts grew.
- **Link:** https://en.wikipedia.org/wiki/Dennard_scaling

---

## Queues and flow

### Little's law
- **Statement:** In a stable system, average items in the system = average arrival (throughput) rate × average time in the system: `L = λW`.
- **Origin:** John D. C. Little, formal proof 1961.
- **In practice:** Holds for any stable queue regardless of distribution. In Kanban: average cycle time = average WIP / average throughput. To shorten lead time without working faster, reduce WIP. For services: concurrency = RPS × latency, which sizes thread pools and connection pools.
- **Example:** A team finishes 5 items/week with 20 items in progress → average cycle time ≈ 4 weeks. Cap WIP at 10 → ≈ 2 weeks.
- **Link:** https://en.wikipedia.org/wiki/Little%27s_law

### Kingman's formula
- **Statement:** Expected queue waiting time ≈ (ρ / (1 − ρ)) × ((c²ₐ + c²ₛ) / 2) × mean service time; wait time explodes as utilisation ρ approaches 100% and grows with variability.
- **Origin:** J. F. C. Kingman, 1961 (G/G/1 approximation).
- **In practice:** Running people or servers at 95% utilisation guarantees long queues. Keep slack (~70–80% for variable work) and reduce variability (smaller, more uniform batches).
- **Example:** A CI runner at 90% utilisation has ~9× the wait of one at 50% — the build queue, not build time, dominates feedback latency.
- **Link:** https://en.wikipedia.org/wiki/Kingman%27s_formula

### Queueing theory basics
- **Statement:** Arrival rate, service rate, number of servers and variability determine waiting time; M/M/1, M/M/c etc. are the standard models (Kendall notation).
- **Origin:** Agner Krarup Erlang, telephone exchanges, 1909–1917; David Kendall's notation, 1953.
- **In practice:** One shared queue feeding several servers beats separate queues per server (pooling). Batching increases wait. Utilisation, not raw speed, drives latency.
- **Example:** A single support queue served by four engineers outperforms four personal queues.
- **Link:** https://en.wikipedia.org/wiki/Queueing_theory

---

## Distributed systems and interfaces

### CAP theorem
- **Statement:** When a network partition occurs, a distributed data store must choose between consistency (linearizability) and availability.
- **Origin:** Eric Brewer, conjecture at PODC 2000; proven by Seth Gilbert and Nancy Lynch, 2002.
- **In practice:** Partitions are not optional, so the real choice is CP vs AP *during a partition*. "CA" is not a meaningful option for distributed systems. Pick per use case (payments ≈ CP; shopping cart ≈ AP).
- **Example:** A multi-region DynamoDB-style store accepts writes in both regions during a split and reconciles later (AP).
- **Link:** https://en.wikipedia.org/wiki/CAP_theorem

### PACELC
- **Statement:** If Partitioned, choose Availability or Consistency; Else (normal operation), choose Latency or Consistency.
- **Origin:** Daniel Abadi, 2010 (paper 2012).
- **In practice:** Extends CAP to the common case: even without partitions, synchronous replication for consistency costs latency. Classify stores as PA/EL, PC/EC, etc.
- **Example:** Cassandra with low consistency levels is PA/EL; Spanner is PC/EC.
- **Link:** https://en.wikipedia.org/wiki/PACELC_design_principle

### Fallacies of distributed computing
- **Statement:** Eight false assumptions: the network is reliable; latency is zero; bandwidth is infinite; the network is secure; topology doesn't change; there is one administrator; transport cost is zero; the network is homogeneous.
- **Origin:** L. Peter Deutsch and others at Sun Microsystems, 1994 (the eighth added by James Gosling, 1997).
- **In practice:** Every remote call needs timeouts, retries with backoff and jitter, idempotency, circuit breakers, encryption and observability. Microservices turn function calls into network calls and inherit all eight.
- **Example:** A synchronous chain of six services with 99.9% availability each has ~99.4% end-to-end availability.
- **Link:** https://en.wikipedia.org/wiki/Fallacies_of_distributed_computing

### End-to-end principle
- **Statement:** Functions such as reliability or security can only be completely implemented at the endpoints; implementing them in lower layers is at best a performance optimisation.
- **Origin:** Saltzer, Reed and Clark, "End-to-End Arguments in System Design", 1981/1984.
- **In practice:** Do not rely on the message broker for exactly-once business semantics; make consumers idempotent. TLS on one hop does not mean end-to-end confidentiality.
- **Example:** A file transfer verifies a checksum at the receiver even though TCP has checksums.
- **Link:** https://en.wikipedia.org/wiki/End-to-end_principle

### Two Generals' Problem
- **Statement:** Two parties communicating over an unreliable channel cannot reach certain agreement on a coordinated action.
- **Origin:** Akkoyunlu, Ekanadham and Huber, 1975; named by Jim Gray, 1978.
- **In practice:** Explains why "exactly once delivery" is impossible in general and why we use idempotency, deduplication and at-least-once delivery instead.
- **Example:** A payment API returns a timeout: the client cannot know if the charge happened, so it retries with an idempotency key.
- **Link:** https://en.wikipedia.org/wiki/Two_Generals%27_Problem

### Byzantine fault tolerance
- **Statement:** Reaching consensus when some nodes may behave arbitrarily (including maliciously) requires at least 3f + 1 nodes to tolerate f faulty ones.
- **Origin:** Lamport, Shostak and Pease, "The Byzantine Generals Problem", 1982.
- **In practice:** Most internal systems assume crash faults only (Raft/Paxos, 2f + 1 nodes). Byzantine tolerance matters for blockchains and adversarial multi-party settings.
- **Example:** A 5-node Raft cluster tolerates 2 crashed nodes but not a node that lies.
- **Link:** https://en.wikipedia.org/wiki/Byzantine_fault

### Postel's law
- **Statement:** Be conservative in what you send, be liberal in what you accept.
- **Origin:** Jon Postel, RFC 760/761 (TCP/IP specifications), 1980.
- **In practice:** Aids interoperability in early ecosystems but tolerated malformed input becomes de facto standard and a security risk. RFC 9413 (2023) argues for actively maintaining protocols instead of indefinite tolerance. Be strict at trust boundaries.
- **Example:** Browsers' lenient HTML parsing made the web resilient but entrenched broken markup for decades.
- **Link:** https://en.wikipedia.org/wiki/Robustness_principle

### Robustness principle
- **Statement:** Synonym for Postel's law; in broader engineering, the design goal of continuing to function under unexpected inputs or conditions.
- **Origin:** Postel, 1980; critique in RFC 9413 "Maintaining Robust Protocols", 2023.
- **In practice:** Combine tolerant readers (ignore unknown fields) with strict validation of security-relevant fields; version contracts explicitly.
- **Example:** A JSON consumer ignores unknown properties (tolerant reader) but rejects a missing signature.
- **Link:** https://www.rfc-editor.org/rfc/rfc9413

### Hyrum's law
- **Statement:** With a sufficient number of users of an API, all observable behaviours of your system will be depended on by somebody, regardless of what the contract says.
- **Origin:** Hyrum Wright, Google, circa 2012; popularised in *Software Engineering at Google*, 2020.
- **In practice:** Undocumented ordering, timing, error messages and performance become contracts. Mitigate with randomisation (e.g., hash iteration order), narrow interfaces, contract tests and deprecation policies.
- **Example:** Changing the text of an error message breaks a customer's script that parsed it.
- **Link:** https://www.hyrumslaw.com/

### Law of leaky abstractions
- **Statement:** All non-trivial abstractions, to some degree, are leaky.
- **Origin:** Joel Spolsky, 2002.
- **In practice:** You still need to understand the layer below (ORMs → SQL, TCP → packet loss, serverless → cold starts, managed Kubernetes → nodes). Abstractions save time while working, not learning time when they break.
- **Example:** An ORM-generated N+1 query pattern only becomes visible when someone reads the SQL log.
- **Link:** https://www.joelonsoftware.com/2002/11/11/the-law-of-leaky-abstractions/

---

## Complexity, evolution and estimation

### Gall's law
- **Statement:** A complex system that works is invariably found to have evolved from a simple system that worked; a complex system designed from scratch never works and cannot be patched to make it work.
- **Origin:** John Gall, *Systemantics*, 1975.
- **In practice:** Start with a walking skeleton or monolith, deliver end to end, and evolve. Big-bang rewrites and "platform first" programmes violate it.
- **Example:** Successful microservice estates usually began as a monolith that was carved up along proven seams.
- **Link:** https://en.wikipedia.org/wiki/John_Gall_(author)

### Brooks's law
- **Statement:** Adding manpower to a late software project makes it later.
- **Origin:** Fred Brooks, *The Mythical Man-Month*, 1975.
- **In practice:** New people need ramp-up and increase communication paths (`n(n−1)/2`). Add people early, to separable work, or reduce scope instead. Brooks himself called it an "outrageous simplification".
- **Example:** Going from 5 to 10 developers raises communication paths from 10 to 45.
- **Link:** https://en.wikipedia.org/wiki/Brooks%27s_law

### Second-system effect
- **Statement:** The second system a designer builds tends to be over-engineered, packed with all the ideas cautiously left out of the first.
- **Origin:** Fred Brooks, *The Mythical Man-Month*, 1975.
- **In practice:** Rewrites ("v2 done right") accumulate scope. Guard with explicit non-goals, incremental migration (strangler fig) and time boxes.
- **Example:** A rewrite promised to add multi-tenancy, plugins and a new UI and never reached parity with v1.
- **Link:** https://en.wikipedia.org/wiki/Second-system_effect

### Hofstadter's law
- **Statement:** It always takes longer than you expect, even when you take into account Hofstadter's law.
- **Origin:** Douglas Hofstadter, *Gödel, Escher, Bach*, 1979.
- **In practice:** Self-correction of estimates is insufficient; use historical data (reference class forecasting), probabilistic forecasts (Monte Carlo on throughput) and explicit buffers.
- **Example:** A team that padded by 50% still misses; their historical cycle-time data gives a better 85th-percentile forecast.
- **Link:** https://en.wikipedia.org/wiki/Hofstadter%27s_law

### Ninety-ninety rule
- **Statement:** The first 90% of the code accounts for the first 90% of development time; the remaining 10% accounts for the other 90%.
- **Origin:** Tom Cargill, Bell Labs; popularised by Jon Bentley, *Communications of the ACM*, 1985.
- **In practice:** Edge cases, integration, hardening, migration and operability are the long tail. Define "done" to include them; track remaining work not % complete.
- **Example:** A demo works in week 3; production readiness takes until week 12.
- **Link:** https://en.wikipedia.org/wiki/Ninety%E2%80%93ninety_rule

### Murphy's law
- **Statement:** Anything that can go wrong will go wrong.
- **Origin:** Attributed to Capt. Edward A. Murphy Jr., US Air Force rocket-sled tests, Edwards AFB, 1949.
- **In practice:** Design for failure: chaos testing, defensive defaults, idempotency, backups you actually restore, and making wrong actions hard (poka-yoke). Corollaries: *Finagle's law* (anything that can go wrong will, at the worst possible moment) and *Sod's law* (British variant, emphasising bad luck).
- **Example:** A connector that "can only be inserted one way" was originally inserted backwards — Murphy's original sensor anecdote.
- **Link:** https://en.wikipedia.org/wiki/Murphy%27s_law

### Finagle's law
- **Statement:** Anything that can go wrong, will — at the worst possible moment.
- **Origin:** Science-fiction fandom, popularised by John W. Campbell Jr.'s *Astounding* editorials, 1950s–60s.
- **In practice:** Plan failure drills for peak traffic, holidays and end-of-quarter; freeze windows exist for this reason.
- **Example:** The certificate expires on Black Friday.
- **Link:** https://en.wikipedia.org/wiki/Finagle%27s_law

### Kernighan's law
- **Statement:** Debugging is twice as hard as writing the code in the first place; so if you write code as cleverly as possible, you are by definition not smart enough to debug it.
- **Origin:** Brian Kernighan and P. J. Plauger, *The Elements of Programming Style*, 2nd ed., 1978.
- **In practice:** Prefer boring, readable code; clever tricks should pay for their maintenance cost and be documented.
- **Example:** A one-line regex validating email addresses that nobody can modify safely.
- **Link:** https://github.com/dwmkerr/hacker-laws

### Eagleson's law
- **Statement:** Any code of your own that you haven't looked at for six or more months might as well have been written by someone else.
- **Origin:** Attributed to Peter Eagleson; circulated in programmer folklore.
- **In practice:** Write for the future reader: naming, tests as documentation, ADRs for "why" decisions.
- **Example:** `git blame` reveals the confusing code is yours.
- **Link:** https://github.com/dwmkerr/hacker-laws

### Greenspun's tenth rule
- **Statement:** Any sufficiently complicated C or Fortran program contains an ad hoc, informally specified, bug-ridden, slow implementation of half of Common Lisp.
- **Origin:** Philip Greenspun, circa 1993 (there are no rules one to nine).
- **In practice:** Generalised: systems grow their own poorly built interpreters, rule engines, workflow engines or config languages. Recognise when to adopt a proper one.
- **Example:** A YAML pipeline format with conditionals, loops and variables becomes an undocumented programming language.
- **Link:** https://en.wikipedia.org/wiki/Greenspun%27s_tenth_rule

### Zawinski's law
- **Statement:** Every program attempts to expand until it can read mail; those that cannot are replaced by ones that can.
- **Origin:** Jamie Zawinski, circa 1995.
- **In practice:** Feature creep pulls products towards communication/platform features. Today's variant: every app grows chat, AI assistants and notifications.
- **Example:** A project-tracking tool adds messaging, docs and video calls.
- **Link:** https://en.wikipedia.org/wiki/Jamie_Zawinski

### Tesler's law
- **Statement:** Every application has an inherent amount of complexity that cannot be removed, only moved — to the user or to the system/developer.
- **Origin:** Larry Tesler, Xerox PARC / Apple, mid-1980s ("law of conservation of complexity").
- **In practice:** Absorb complexity in the system where it pays (sensible defaults, smart parsing) rather than in configuration screens; but beware over-hiding.
- **Example:** A date field that parses "next Friday" moves complexity from the user to the developer.
- **Link:** https://en.wikipedia.org/wiki/Law_of_conservation_of_complexity

### Linus's law
- **Statement:** Given enough eyeballs, all bugs are shallow.
- **Origin:** Eric S. Raymond, *The Cathedral and the Bazaar*, 1999, named in honour of Linus Torvalds.
- **In practice:** Code review and openness help only if the eyeballs actually look. Heartbleed (2014) in OpenSSL showed widely used code can go unreviewed. Pair it with fuzzing, static analysis and funding of maintainers.
- **Example:** A public bug bounty finds issues internal QA missed.
- **Link:** https://en.wikipedia.org/wiki/Linus%27s_law

### Worse is better
- **Statement:** Simplicity of implementation can matter more than correctness, consistency and completeness; simpler, "worse" systems spread faster and then improve.
- **Origin:** Richard P. Gabriel, "Lisp: Good News, Bad News, How to Win Big", 1989/1991 (the "New Jersey" vs "MIT" style).
- **In practice:** Ship something simple that people adopt; virality and iteration beat perfection. Counter-risk: early shortcuts become permanent (Hyrum).
- **Example:** Unix and C beat more elegant contemporaries; JavaScript beat more principled languages in the browser.
- **Link:** https://en.wikipedia.org/wiki/Worse_is_better

---

## Lehman's laws of software evolution

### Lehman's laws of software evolution
- **Statement:** Eight laws describing how "E-type" software (systems embedded in and reflecting a real-world domain) evolves over time.
- **Origin:** Meir M. Lehman and László Bélády, from studying IBM OS/360, 1974–1996.
- **In practice:** Software in use must change, grows more complex unless work is done to reduce it, and its quality declines unless actively maintained. Budget continuous refactoring and platform upkeep, not just features.
- **Example:** A stable-looking enterprise system still needs ~15–25% capacity for upgrades, dependency updates and simplification.
- **Link:** https://en.wikipedia.org/wiki/Lehman%27s_laws_of_software_evolution

| # | Law (year) | Statement (paraphrased) | Engineering implication |
|---|---|---|---|
| I | **Continuing change** (1974) | An E-type system must be continually adapted or it becomes progressively less satisfactory. | "Done" software in use is a myth; plan run/maintain capacity. |
| II | **Increasing complexity** (1974) | As a system evolves, its complexity increases unless work is done to maintain or reduce it. | Refactoring is not optional; track complexity metrics. |
| III | **Self-regulation** (1974) | Evolution of large systems is self-regulating, with statistically predictable distributions of product and process measures. | Throughput and growth trends are forecastable from history. |
| IV | **Conservation of organisational stability** (1978) | The average effective global activity rate on an evolving system is invariant over its lifetime. | Adding resources does not change the rate much (cf. Brooks). |
| V | **Conservation of familiarity** (1978) | The incremental growth per release stays roughly constant because everyone must keep mastering the content. | Huge releases cause quality dips; ship smaller increments. |
| VI | **Continuing growth** (1991) | Functional content must continually increase to maintain user satisfaction. | Scope grows; plan for capacity and modularity. |
| VII | **Declining quality** (1996) | Quality will appear to decline unless rigorously maintained and adapted to operational changes. | Environment drifts (OS, browsers, regulations); quality is maintained, not retained. |
| VIII | **Feedback system** (1996) | Evolution processes are multi-level, multi-loop, multi-agent feedback systems. | Improve the process (feedback loops), not just the code. |

---

## Craft and design principles

### Make it work, make it right, make it fast
- **Statement:** First get it working, then make it correct and clean, then — only if needed — make it fast.
- **Origin:** Commonly attributed to Kent Beck; recorded on Ward Cunningham's C2 wiki (1990s), rooted in Unix lore.
- **In practice:** Sequencing guard against premature optimisation and premature abstraction; the TDD red/green/refactor cycle embodies the first two steps.
- **Example:** Spike a prototype, add tests and refactor, then profile under realistic load.
- **Link:** https://wiki.c2.com/?MakeItWorkMakeItRightMakeItFast

### Boy Scout rule
- **Statement:** Leave the code cleaner than you found it.
- **Origin:** Robert C. Martin, adapting Robert Baden-Powell's scouting maxim; *97 Things Every Programmer Should Know*, 2010.
- **In practice:** Small, continuous refactoring alongside feature work counteracts Lehman's increasing complexity without big "refactoring sprints". Keep such changes small and reviewable.
- **Example:** While fixing a bug, rename a misleading variable and add the missing test.
- **Link:** https://github.com/dwmkerr/hacker-laws

### Rule of three
- **Statement:** Tolerate duplication twice; on the third occurrence, refactor into an abstraction.
- **Origin:** Attributed to Don Roberts; popularised by Martin Fowler, *Refactoring*, 1999.
- **In practice:** Avoids wrong abstractions built from one or two examples. "Duplication is far cheaper than the wrong abstraction" (Sandi Metz, 2016) is the same idea.
- **Example:** Copy a CSV parser once; extract a shared library when a third consumer appears.
- **Link:** https://en.wikipedia.org/wiki/Rule_of_three_(computer_programming)

### DRY — Don't Repeat Yourself
- **Statement:** Every piece of knowledge must have a single, unambiguous, authoritative representation within a system.
- **Origin:** Andy Hunt and Dave Thomas, *The Pragmatic Programmer*, 1999.
- **In practice:** DRY is about knowledge, not identical-looking code. Two similar code blocks representing different business rules should stay separate.
- **Example:** Tax rate defined once in configuration, not in UI, API and reports.
- **Link:** https://en.wikipedia.org/wiki/Don%27t_repeat_yourself

### KISS
- **Statement:** Keep it simple; most systems work best if kept simple rather than made complicated.
- **Origin:** US Navy, 1960; associated with Kelly Johnson of Lockheed Skunk Works.
- **In practice:** Prefer the simplest design that meets the requirements; complexity must earn its place.
- **Example:** A cron job and a table instead of a workflow engine for three daily tasks.
- **Link:** https://en.wikipedia.org/wiki/KISS_principle

### YAGNI
- **Statement:** You aren't gonna need it — don't build functionality until it is actually needed.
- **Origin:** Extreme Programming, Ron Jeffries / Kent Beck, late 1990s.
- **In practice:** Speculative generality costs build, carry and delay. Exception: decisions that are expensive to reverse (data model, public API) deserve forethought.
- **Example:** No plugin system until a second integration actually exists.
- **Link:** https://en.wikipedia.org/wiki/You_aren%27t_gonna_need_it

### Separation of concerns
- **Statement:** Divide a program into distinct sections, each addressing a separate concern.
- **Origin:** Edsger W. Dijkstra, "On the role of scientific thought", 1974.
- **In practice:** Underpins layering, modules, bounded contexts and MVC. Separate by reason-to-change.
- **Example:** Pricing rules isolated from HTTP handlers and persistence.
- **Link:** https://en.wikipedia.org/wiki/Separation_of_concerns

### SOLID
- **Statement:** Five object-oriented design principles: Single responsibility, Open–closed, Liskov substitution, Interface segregation, Dependency inversion.
- **Origin:** Robert C. Martin, early 2000s; acronym coined by Michael Feathers circa 2004.
- **In practice:** Heuristics for change-friendly modules; apply with judgement, not as checklist dogma.
- **Example:** See the individual principles below.
- **Link:** https://en.wikipedia.org/wiki/SOLID

### Single-responsibility principle
- **Statement:** A module should have one, and only one, reason to change (one actor it serves).
- **Origin:** Robert C. Martin, circa 2000 (building on cohesion work by Tom DeMarco and Meilir Page-Jones).
- **In practice:** Group code that changes together for the same stakeholder; split code that changes for different stakeholders.
- **Example:** Separate report formatting (changed by finance) from report calculation (changed by actuaries).
- **Link:** https://en.wikipedia.org/wiki/Single-responsibility_principle

### Open–closed principle
- **Statement:** Software entities should be open for extension but closed for modification.
- **Origin:** Bertrand Meyer, *Object-Oriented Software Construction*, 1988; reinterpreted via polymorphism by Martin.
- **In practice:** Add new behaviour via new code (strategies, plugins) rather than editing stable code; don't pre-build extension points without evidence (YAGNI).
- **Example:** A new payment provider is a new adapter, not another `if` branch.
- **Link:** https://en.wikipedia.org/wiki/Open%E2%80%93closed_principle

### Liskov substitution principle
- **Statement:** Objects of a subtype must be usable wherever the supertype is expected without altering correctness.
- **Origin:** Barbara Liskov, 1987 keynote; formalised with Jeannette Wing, 1994.
- **In practice:** Subtypes must not strengthen preconditions or weaken postconditions. Applies to API versions and service contracts too.
- **Example:** A `Square` subclass of `Rectangle` breaks code that sets width and height independently.
- **Link:** https://en.wikipedia.org/wiki/Liskov_substitution_principle

### Interface segregation principle
- **Statement:** Clients should not be forced to depend on methods they do not use.
- **Origin:** Robert C. Martin, consulting for Xerox, 1990s.
- **In practice:** Prefer small, role-based interfaces; reduces coupling and Hyrum's-law exposure.
- **Example:** A `Printer` interface separate from `Scanner` rather than one `MultiFunctionDevice`.
- **Link:** https://en.wikipedia.org/wiki/Interface_segregation_principle

### Dependency inversion principle
- **Statement:** High-level modules should not depend on low-level modules; both should depend on abstractions. Abstractions should not depend on details.
- **Origin:** Robert C. Martin, *C++ Report*, 1996.
- **In practice:** The domain defines the interfaces (ports) that infrastructure implements (adapters) — the basis of hexagonal/clean architecture. See [../05-software-architecture/README.md](../05-software-architecture/README.md).
- **Example:** `OrderService` depends on an `OrderRepository` interface; the Postgres implementation lives at the edge.
- **Link:** https://en.wikipedia.org/wiki/Dependency_inversion_principle

### Hollywood principle
- **Statement:** "Don't call us, we'll call you" — frameworks call your code, not the other way round (inversion of control).
- **Origin:** Phrase used in 1980s software literature (e.g., Richard Sweet on the Mesa environment, 1985); popularised by frameworks and the Template Method pattern.
- **In practice:** Event handlers, dependency-injection containers, plugin hooks and serverless functions all follow it. Trade-off: control flow becomes harder to trace.
- **Example:** A web framework invokes your controller method when a matching request arrives.
- **Link:** https://en.wikipedia.org/wiki/Inversion_of_control

### Law of Demeter
- **Statement:** A unit should talk only to its immediate friends — not to strangers reachable through them ("principle of least knowledge").
- **Origin:** Ian Holland, Northeastern University (Demeter project), 1987.
- **In practice:** Avoid chains like `order.getCustomer().getAddress().getCity()`; ask the collaborator to do the work ("tell, don't ask"). Reduces coupling to internal structure.
- **Example:** `order.shippingCity()` instead of traversing three objects.
- **Link:** https://en.wikipedia.org/wiki/Law_of_Demeter

### Composition over inheritance
- **Statement:** Favour object composition over class inheritance for reuse.
- **Origin:** Gamma, Helm, Johnson, Vlissides, *Design Patterns*, 1994.
- **In practice:** Inheritance couples to parent internals (fragile base class); composition allows swapping behaviour at runtime.
- **Example:** A `Notifier` composed with `EmailChannel`/`SmsChannel` instead of `EmailNotifier extends Notifier`.
- **Link:** https://en.wikipedia.org/wiki/Composition_over_inheritance

### Principle of least astonishment
- **Statement:** A component should behave in the way most users expect it to.
- **Origin:** Programming-language and UI design literature, 1960s–70s (PL/I discussions).
- **In practice:** Consistent naming, side-effect-free getters, conventional defaults, and APIs that mirror platform idioms.
- **Example:** A `delete` endpoint that silently archives surprises users; name it `archive`.
- **Link:** https://en.wikipedia.org/wiki/Principle_of_least_astonishment

### Fail-fast
- **Statement:** Report failure immediately at the point it occurs rather than continuing in a possibly corrupted state.
- **Origin:** Systems design practice; Jim Shore, "Fail Fast", *IEEE Software*, 2004.
- **In practice:** Validate config at startup, assert invariants, crash and restart (Erlang "let it crash") rather than limp on. Distinct from "fail-safe" (fail into a safe state).
- **Example:** A service refuses to start when a required secret is missing instead of failing at the first request.
- **Link:** https://en.wikipedia.org/wiki/Fail-fast_system

### Command–query separation
- **Statement:** A method should either change state (command) or return data (query), not both.
- **Origin:** Bertrand Meyer, Eiffel, 1988. CQRS (Greg Young, ~2010) applies it at architecture level.
- **In practice:** Makes reasoning, caching and testing easier; queries are safe to repeat.
- **Example:** `stack.pop()` violates CQS; `peek()` + `remove()` respects it (trade-off: concurrency).
- **Link:** https://en.wikipedia.org/wiki/Command%E2%80%93query_separation

### Coupling and cohesion
- **Statement:** Good modules have high cohesion (related things together) and low coupling (few, stable dependencies between modules).
- **Origin:** Larry Constantine, structured design, late 1960s; Yourdon & Constantine, *Structured Design*, 1979.
- **In practice:** The underlying metric behind most design principles, microservice boundaries and team boundaries (see Conway).
- **Example:** Services that must always deploy together are one service with network latency.
- **Link:** https://en.wikipedia.org/wiki/Coupling_(computer_programming)

### Egoless programming
- **Statement:** Separate your identity from your code; invite review and criticism of the work, not the person.
- **Origin:** Gerald M. Weinberg, *The Psychology of Computer Programming*, 1971.
- **In practice:** Foundation of modern code review culture and collective code ownership.
- **Example:** Review comments framed as questions on the code ("what happens if x is null?").
- **Link:** https://en.wikipedia.org/wiki/Egoless_programming

### Weinberg's laws
- **Statement:** A set of aphorisms. Best known: *Weinberg's second law* — if builders built buildings the way programmers write programs, the first woodpecker that came along would destroy civilisation; and "no matter what the problem is, it's always a people problem".
- **Origin:** Gerald M. Weinberg, *The Secrets of Consulting* (1985) and other works.
- **In practice:** Technical failures usually trace to communication, incentives or understanding. Consultants: the client's problem is rarely the one first stated.
- **Example:** An "integration bug" turns out to be two teams with different definitions of "customer".
- **Link:** https://en.wikipedia.org/wiki/Gerald_Weinberg

---

## Unix philosophy and language philosophies

### Unix philosophy (McIlroy)
- **Statement:** Write programs that do one thing and do it well; write programs to work together; write programs to handle text streams, because that is a universal interface.
- **Origin:** Doug McIlroy, foreword to the Unix issue of the *Bell System Technical Journal*, 1978; summarised by Peter Salus, 1994.
- **In practice:** Small composable tools, standard interfaces, pipes. Maps to microservices, serverless functions, data pipelines and CLI design.
- **Example:** `grep ERROR app.log | sort | uniq -c | sort -nr | head`.
- **Link:** https://en.wikipedia.org/wiki/Unix_philosophy

### Gancarz's Unix tenets
- **Statement:** Nine tenets: (1) small is beautiful; (2) make each program do one thing well; (3) build a prototype as soon as possible; (4) choose portability over efficiency; (5) store data in flat text files; (6) use software leverage; (7) use shell scripts to increase leverage and portability; (8) avoid captive user interfaces; (9) make every program a filter.
- **Origin:** Mike Gancarz, *The UNIX Philosophy*, 1994.
- **In practice:** Prototype early, prefer plain-text formats (JSON, CSV, YAML) for interoperability, make tools scriptable rather than interactive-only.
- **Example:** A deployment CLI that reads stdin and writes JSON is automatable; one with only interactive prompts is not.
- **Link:** https://en.wikipedia.org/wiki/Unix_philosophy

### Zen of Python
- **Statement:** 19 aphorisms guiding Python's design (e.g., explicit beats implicit; simple beats complex; readability counts; there should be one obvious way to do it).
- **Origin:** Tim Peters, 1999; PEP 20, 2004. Run `import this`.
- **In practice:** A useful style guide beyond Python for code review discussions.
- **Example:** Prefer an explicit parameter over hidden global configuration.
- **Link:** https://peps.python.org/pep-0020/

---

## Security principles

### Principle of least privilege
- **Statement:** Every program and every user should operate using the least set of privileges necessary to complete the job.
- **Origin:** Jerome Saltzer, 1974; Saltzer & Schroeder, "The Protection of Information in Computer Systems", 1975.
- **In practice:** Scoped IAM roles, short-lived credentials, just-in-time admin access, per-service database users. Limits blast radius when (not if) something is compromised.
- **Example:** A reporting job gets read-only access to one schema rather than the DBA role.
- **Link:** https://en.wikipedia.org/wiki/Principle_of_least_privilege

### Kerckhoffs's principle
- **Statement:** A cryptosystem should be secure even if everything about the system, except the key, is public knowledge.
- **Origin:** Auguste Kerckhoffs, *La cryptographie militaire*, 1883. Claude Shannon's restatement ("the enemy knows the system", Shannon's maxim), 1949.
- **In practice:** Use public, peer-reviewed algorithms and protocols; protect and rotate keys. Never rely on secret algorithms.
- **Example:** AES-GCM with keys in a KMS, not a homegrown XOR "encryption".
- **Link:** https://en.wikipedia.org/wiki/Kerckhoffs%27s_principle

### Schneier's law
- **Statement:** Anyone can design a security system so clever that they themselves cannot think of a way to break it.
- **Origin:** Bruce Schneier, 1998; named by Cory Doctorow, 2004.
- **In practice:** Your own inability to break your design proves nothing; require external review, threat modelling and penetration testing.
- **Example:** A custom token format reviewed only by its author.
- **Link:** https://en.wikipedia.org/wiki/Bruce_Schneier

### Defense in depth
- **Statement:** Layer multiple independent security controls so that the failure of one does not compromise the system.
- **Origin:** Military strategy; adopted by the NSA for information assurance.
- **In practice:** Network segmentation + authentication + authorisation + encryption + monitoring. Related to the Swiss cheese model in [systems-thinking-and-complexity.md](systems-thinking-and-complexity.md).
- **Example:** A leaked API key still cannot reach the database because of network policy and row-level security.
- **Link:** https://en.wikipedia.org/wiki/Defense_in_depth_(computing)

### Security through obscurity
- **Statement:** (Anti-pattern) relying on secrecy of design or implementation as the main security mechanism.
- **Origin:** Criticised since Alfred Hobbs's locksmithing debates, 1851; opposed by Kerckhoffs's principle.
- **In practice:** Obscurity may add a thin layer (e.g., non-default ports) but must never be the primary control.
- **Example:** An "unlisted" admin URL without authentication.
- **Link:** https://en.wikipedia.org/wiki/Security_through_obscurity

### Secure by default
- **Statement:** Default configuration should be the most secure reasonable setting; insecurity requires explicit opt-in.
- **Origin:** Security engineering practice; Saltzer & Schroeder's "fail-safe defaults", 1975.
- **In practice:** Buckets private by default, TLS on, deny-all network policies, MFA enforced.
- **Example:** Cloud providers switched new object-storage buckets to block public access by default.
- **Link:** https://en.wikipedia.org/wiki/Secure_by_design

### Zero trust
- **Statement:** Never trust, always verify — no implicit trust based on network location.
- **Origin:** Coined by Stephen Paul Marsh (1994 thesis); model popularised by John Kindervag at Forrester, 2010; Google BeyondCorp, 2014; NIST SP 800-207, 2020.
- **In practice:** Authenticate and authorise every request (identity-aware proxies, mTLS, device posture). See [../06-cloud-architecture/README.md](../06-cloud-architecture/README.md).
- **Example:** Internal dashboards require SSO even on the corporate network.
- **Link:** https://en.wikipedia.org/wiki/Zero_trust_security_model

---

## Technology trend laws

### Moore's law
- **Statement:** The number of transistors on an integrated circuit doubles about every two years.
- **Origin:** Gordon Moore, *Electronics*, 1965 (yearly doubling), revised in 1975 to every two years.
- **In practice:** An economic and industrial roadmap, not physics; slowed noticeably since the 2010s. Long-term capacity planning and "wait for hardware" strategies should not assume it continues.
- **Example:** Cost per transistor stopped falling as fast below ~28 nm.
- **Link:** https://en.wikipedia.org/wiki/Moore%27s_law

### Koomey's law
- **Statement:** The number of computations per joule of energy dissipated doubled roughly every 1.57 years (slowing to ~2.6 years after 2000).
- **Origin:** Jonathan Koomey et al., 2010/2011.
- **In practice:** Energy efficiency drives mobile, edge and data-centre economics; relevant to sustainable/green software and AI compute costs.
- **Example:** A phone today outperforms a 1990s supercomputer on battery power.
- **Link:** https://en.wikipedia.org/wiki/Koomey%27s_law

### Bell's law of computer classes
- **Statement:** Roughly every decade a new, lower-priced computer class forms, based on a new programming platform, network and interface, creating a new industry.
- **Origin:** Gordon Bell, 1972 (formalised 2008).
- **In practice:** Mainframe → minicomputer → PC → mobile → IoT/embedded → (arguably) AI assistants. Architecture choices should anticipate the next class.
- **Example:** Smartphones displaced PCs as the primary computing device for most people.
- **Link:** https://en.wikipedia.org/wiki/Bell%27s_law_of_computer_classes

### Norvig's law
- **Statement:** Any technology that surpasses 50% penetration will never double again (in any number of months).
- **Origin:** Peter Norvig, early 2000s.
- **In practice:** Growth-rate projections must flatten as saturation approaches (S-curve); be sceptical of hockey-stick extrapolations in mature markets.
- **Example:** Smartphone shipments plateaued once most adults owned one.
- **Link:** https://github.com/dwmkerr/hacker-laws

---

## Human–computer interaction laws

Further UX laws and heuristics: [../01-ux-ui/README.md](../01-ux-ui/README.md) and https://lawsofux.com/.

### Hick's law
- **Statement:** Decision time increases logarithmically with the number of (equally likely) choices: `T = b · log₂(n + 1)`.
- **Origin:** William Edmund Hick and Ray Hyman, 1952.
- **In practice:** Reduce and group options, use progressive disclosure, highlight defaults. Applies to menus, pricing pages, configuration and APIs.
- **Example:** Onboarding that asks one question per screen converts better than a 20-field form.
- **Link:** https://en.wikipedia.org/wiki/Hick%27s_law

### Fitts's law
- **Statement:** Time to acquire a target is a function of distance to and size of the target: `T = a + b · log₂(1 + D/W)`.
- **Origin:** Paul Fitts, 1954.
- **In practice:** Make primary actions large and close; screen edges and corners are effectively infinite targets; touch targets need ≥ ~44–48 px.
- **Example:** A full-width "Pay" button at the bottom of a mobile screen.
- **Link:** https://en.wikipedia.org/wiki/Fitts%27s_law

### Miller's law
- **Statement:** The average person can hold about 7 ± 2 items in working memory.
- **Origin:** George A. Miller, "The Magical Number Seven, Plus or Minus Two", 1956. Later work (Cowan, 2001) suggests ~4 chunks.
- **In practice:** Chunk information (phone numbers, IBANs), limit simultaneous concepts; not a rule that menus must have seven items.
- **Example:** Displaying a 16-digit code in four groups of four.
- **Link:** https://en.wikipedia.org/wiki/The_Magical_Number_Seven,_Plus_or_Minus_Two

### Jakob's law
- **Statement:** Users spend most of their time on other sites, so they prefer your site to work the same way as the sites they already know.
- **Origin:** Jakob Nielsen, 2000.
- **In practice:** Follow conventions (cart top-right, logo links home); innovate where it creates value, not in navigation basics.
- **Example:** Moving search to an unexpected location reduces its use.
- **Link:** https://lawsofux.com/jakobs-law/

### Doherty threshold
- **Statement:** Productivity soars when a computer and its users interact at a pace (< ~400 ms) that ensures neither has to wait on the other.
- **Origin:** Walter J. Doherty and Ahrvind J. Thadani, IBM Systems Journal, 1982.
- **In practice:** Set latency budgets for interactive paths; use optimistic UI and skeleton screens when you cannot meet them.
- **Example:** Instant search results as you type.
- **Link:** https://lawsofux.com/doherty-threshold/

### Von Restorff effect
- **Statement:** When multiple similar objects are present, the one that differs is most likely to be remembered.
- **Origin:** Hedwig von Restorff, 1933.
- **In practice:** Make the primary call to action visually distinct — but only one per view, or nothing stands out. Do not rely on colour alone (accessibility).
- **Example:** A single highlighted "Recommended" pricing tier.
- **Link:** https://en.wikipedia.org/wiki/Von_Restorff_effect

---

## References
- dwmkerr, *Hacker Laws* — https://github.com/dwmkerr/hacker-laws
- Brooks, F. *The Mythical Man-Month* (1975, anniversary ed. 1995) — https://en.wikipedia.org/wiki/The_Mythical_Man-Month
- Lehman, M. M. "Programs, life cycles, and laws of software evolution" (1980) — https://en.wikipedia.org/wiki/Lehman%27s_laws_of_software_evolution
- Spolsky, J. "The Law of Leaky Abstractions" (2002) — https://www.joelonsoftware.com/2002/11/11/the-law-of-leaky-abstractions/
- Winters, Manshreck, Wright, *Software Engineering at Google* (2020) — https://www.hyrumslaw.com/
- Laws of UX — https://lawsofux.com/
- RFC 9413, Maintaining Robust Protocols (2023) — https://www.rfc-editor.org/rfc/rfc9413
- Related sections: [../05-software-architecture/README.md](../05-software-architecture/README.md), [../06-cloud-architecture/README.md](../06-cloud-architecture/README.md), [../08-engineering/README.md](../08-engineering/README.md), [../01-ux-ui/README.md](../01-ux-ui/README.md)
