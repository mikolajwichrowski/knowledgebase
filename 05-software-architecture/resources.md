# Software Architecture — Books and Resources

> **TL;DR** — A curated reading list grouped by topic, with a suggested order. If you read only five: *Fundamentals of Software Architecture* (Richards & Ford), *Designing Data-Intensive Applications* (Kleppmann), *Domain-Driven Design* or *Learning Domain-Driven Design* (Evans / Khononov), *Building Evolutionary Architectures* (Ford et al.), and *Software Architecture: The Hard Parts* (Ford et al.). Use martinfowler.com, microservices.io, the Azure Architecture Center, and c4model.com as day-to-day online references.

---

## 1. Suggested learning path

| Stage | Read | Practise |
|---|---|---|
| 1. Foundations | *Fundamentals of Software Architecture*; *A Philosophy of Software Design*; *Clean Architecture* | Write ADRs for your current system; draw C4 context + container diagrams |
| 2. Design & domain | *Learning DDD* (Khononov); *Implementing DDD* (Vernon); *Refactoring* (Fowler) | Run an Event Storming; define bounded contexts and a context map |
| 3. Data & distribution | *Designing Data-Intensive Applications*; *Release It!*; *Enterprise Integration Patterns* | Implement outbox + idempotent consumer; add resilience policies; load test |
| 4. Evolution & trade-offs | *Building Evolutionary Architectures*; *Software Architecture: The Hard Parts*; *Monolith to Microservices* | Write fitness functions; plan a strangler migration |
| 5. Organisation & strategy | *Team Topologies*; *The Software Architect Elevator*; *Accelerate* | Map teams vs. architecture; architecture advice process |

---

## 2. Books

### Architecture fundamentals

| Book | Authors | Why read |
|---|---|---|
| *Fundamentals of Software Architecture* (2nd ed., 2025) | Mark Richards, Neal Ford | Best overview: characteristics, styles with star ratings, architect soft skills |
| *Software Architecture: The Hard Parts* (2021) | Ford, Richards, Sadalage, Dehghani | Trade-off analysis for distributed architectures: granularity, data ownership, sagas, contracts |
| *Software Architecture in Practice* (4th ed., 2021) | Len Bass, Paul Clements, Rick Kazman | SEI classic: quality attributes, tactics, scenarios, ATAM |
| *Building Evolutionary Architectures* (2nd ed., 2023) | Ford, Parsons, Kua, Sadalage | Fitness functions, incremental change, appropriate coupling |
| *Head First Software Architecture* (2024) | Raju Gandhi, Mark Richards, Neal Ford | Gentle visual introduction |
| *Software Architecture Metrics* (2022) | Christian Ciceri et al. | Measuring architecture |
| *The Software Architect Elevator* (2020) | Gregor Hohpe | Architect role between strategy and engineering |
| *Just Enough Software Architecture* (2010) | George Fairbanks | Risk-driven architecture |
| *Software Systems Architecture* (2nd ed., 2011) | Nick Rozanski, Eóin Woods | Viewpoints and perspectives |
| *Documenting Software Architectures* (2nd ed., 2010) | Clements et al. | Views and Beyond |
| *Software Architecture for Developers* | Simon Brown | C4 model, pragmatic architecture |
| *Communication Patterns* (2023) | Jacqui Read | Diagrams, documentation, and communicating architecture |
| *Facilitating Software Architecture* (2025) | Andrew Harmel-Law | Architecture advice process, decentralised decisions |
| *Architecture Modernization* (2024) | Nick Tune, Jean-Georges Perrin | Socio-technical modernization with DDD |

### Design principles & code

| Book | Authors |
|---|---|
| *Clean Code* (2008), *Clean Architecture* (2017) | Robert C. Martin |
| *Agile Software Development: Principles, Patterns, and Practices* (2002) | Robert C. Martin |
| *A Philosophy of Software Design* (2nd ed., 2021) | John Ousterhout |
| *Refactoring* (2nd ed., 2018) | Martin Fowler |
| *Working Effectively with Legacy Code* (2004) | Michael Feathers |
| *The Pragmatic Programmer* (20th anniv. ed., 2019) | David Thomas, Andrew Hunt |
| *Code Complete* (2nd ed., 2004) | Steve McConnell |
| *Tidy First?* (2023) | Kent Beck |
| *Structured Design* (1979) | Yourdon & Constantine — origin of coupling/cohesion |
| *Applying UML and Patterns* (3rd ed., 2004) | Craig Larman — GRASP |
| *What Every Programmer Should Know About Object-Oriented Design* (1995) | Meilir Page-Jones — connascence |

### Patterns

| Book | Authors |
|---|---|
| *Design Patterns: Elements of Reusable OO Software* (1994) | Gamma, Helm, Johnson, Vlissides (GoF) |
| *Head First Design Patterns* (2nd ed., 2020) | Eric Freeman, Elisabeth Robson |
| *Patterns of Enterprise Application Architecture* (2002) | Martin Fowler |
| *Pattern-Oriented Software Architecture* vol. 1–5 | Buschmann et al. |
| *Enterprise Integration Patterns* (2003) | Gregor Hohpe, Bobby Woolf |
| *Microservices Patterns* (2018; 2nd ed. in progress) | Chris Richardson |
| *Cloud Design Patterns* (online) | Microsoft |

### Domain-Driven Design

| Book | Authors |
|---|---|
| *Domain-Driven Design* (2003) — "the blue book" | Eric Evans |
| *Implementing Domain-Driven Design* (2013) — "the red book" | Vaughn Vernon |
| *Domain-Driven Design Distilled* (2016) | Vaughn Vernon |
| *Learning Domain-Driven Design* (2021) | Vlad Khononov |
| *Balancing Coupling in Software Design* (2024) | Vlad Khononov |
| *Patterns, Principles, and Practices of DDD* (2015) | Scott Millett, Nick Tune |
| *Domain Modeling Made Functional* (2018) | Scott Wlaschin (F#, applies broadly) |
| *Introducing EventStorming* (Leanpub) | Alberto Brandolini |
| *Domain Storytelling* (2021) | Stefan Hofer, Henning Schwentner |
| *Strategic Monoliths and Microservices* (2021) | Vaughn Vernon, Tomasz Jaskuła |
| *Architecture Patterns with Python* ("Cosmic Python", 2020) | Harry Percival, Bob Gregory — free online |

### Distributed systems, data, and microservices

| Book | Authors |
|---|---|
| *Designing Data-Intensive Applications* (2017; 2nd ed. 2025/26) | Martin Kleppmann (2nd ed. with Chris Riccomini) |
| *Database Internals* (2019) | Alex Petrov |
| *Release It!* (2nd ed., 2018) | Michael Nygard |
| *Building Microservices* (2nd ed., 2021) | Sam Newman |
| *Monolith to Microservices* (2019) | Sam Newman |
| *Building Event-Driven Microservices* (O'Reilly) | Adam Bellemare |
| *Understanding Distributed Systems* (2nd ed., 2022) | Roberto Vitillo |
| *Distributed Systems* (4th ed.) | Maarten van Steen, Andrew Tanenbaum — free PDF via distributed-systems.net |
| *Site Reliability Engineering* (2016) | Google — free online |
| *The Art of Scalability* (2nd ed., 2015) | Abbott & Fisher |
| *Systems Performance* (2nd ed., 2020) | Brendan Gregg |
| *NoSQL Distilled* (2012) | Pramod Sadalage, Martin Fowler |
| *Refactoring Databases* (2006) | Scott Ambler, Pramod Sadalage |
| *System Design Interview* vol. 1–2 | Alex Xu (and Sahn Lam) |

### APIs & integration

| Book | Authors |
|---|---|
| *The Design of Web APIs* (2nd ed., 2025) | Arnaud Lauret |
| *Principles of Web API Design* (2021) | James Higginbotham |
| *API Design Patterns* (2021) | JJ Geewax (Google AIP author) |
| *Patterns for API Design* (2022) | Zimmermann, Stocker, Lübke, Zdun, Pautasso |
| *Continuous API Management* (2nd ed., 2021) | Medjaoui, Wilde, Mitra, Amundsen |
| *Kafka: The Definitive Guide* (2nd ed., 2021) | Shapira, Palino, Sivaram, Petty |

### Security

| Book | Authors |
|---|---|
| *Threat Modeling: Designing for Security* (2014) | Adam Shostack |
| *Threats: What Every Engineer Should Learn From Star Wars* (2023) | Adam Shostack |
| *Designing Secure Software* (2021) | Loren Kohnfelder |
| *API Security in Action* (2020) | Neil Madden |
| *OAuth 2 in Action* (2017) | Justin Richer, Antonio Sanso |
| *Zero Trust Networks* (2nd ed., 2024) | Razi Rais et al. |
| *Software Supply Chain Security* (2024) | Cassie Crossley |

### Evolution, legacy, and organisation

| Book | Authors |
|---|---|
| *Team Topologies* (2019; 2nd ed. 2025) | Matthew Skelton, Manuel Pais |
| *Accelerate* (2018) | Forsgren, Humble, Kim |
| *The Mikado Method* (2014) | Ola Ellnestam, Daniel Brolund |
| *Your Code as a Crime Scene* (2nd ed., 2024) | Adam Tornhill |
| *Software Design X-Rays* (2018) | Adam Tornhill |
| *Kill It with Fire* (2021) | Marianne Bellotti — legacy modernization |
| *The Mythical Man-Month* (1975/1995) | Fred Brooks |

### Frontend architecture

| Book | Authors |
|---|---|
| *Micro Frontends in Action* (2020) | Michael Geers |
| *Building Micro-Frontends* (O'Reilly) | Luca Mezzalira |
| *Atomic Design* (2016, free online) | Brad Frost |
| *Learning Patterns* / patterns.dev (free) | Lydia Hallie, Addy Osmani |

---

## 3. Websites and online references

### General architecture

| Resource | URL |
|---|---|
| Martin Fowler — Software Architecture Guide | https://martinfowler.com/architecture/ |
| Martin Fowler — bliki index | https://martinfowler.com/bliki/ |
| Azure Architecture Center | https://learn.microsoft.com/azure/architecture/ |
| Azure — Cloud design patterns | https://learn.microsoft.com/azure/architecture/patterns/ |
| Azure — Architecture styles | https://learn.microsoft.com/azure/architecture/guide/architecture-styles/ |
| .NET Microservices: Architecture for Containerized .NET Apps (eBook) | https://learn.microsoft.com/dotnet/architecture/microservices/ |
| AWS Architecture Center | https://aws.amazon.com/architecture/ |
| AWS Builders' Library | https://aws.amazon.com/builders-library/ |
| Google Cloud Architecture Center | https://cloud.google.com/architecture |
| InfoQ — Architecture & Design | https://www.infoq.com/architecture-design/ |
| Thoughtworks Technology Radar | https://www.thoughtworks.com/radar |
| The Architecture of Open Source Applications | https://aosabook.org/ |
| SEI Software Architecture | https://www.sei.cmu.edu/ |
| Developer to Architect (Mark Richards) | https://developertoarchitect.com/ |
| System Design Primer | https://github.com/donnemartin/system-design-primer |
| High Scalability | http://highscalability.com/ |

### Patterns & principles

| Resource | URL |
|---|---|
| Refactoring.Guru (design patterns, refactorings) | https://refactoring.guru/ |
| PoEAA catalog | https://martinfowler.com/eaaCatalog/ |
| Refactoring catalog | https://refactoring.com/catalog/ |
| Connascence | https://connascence.io/ |
| microservices.io (Chris Richardson) | https://microservices.io/ |
| Enterprise Integration Patterns | https://www.enterpriseintegrationpatterns.com/ |
| Patterns.dev | https://www.patterns.dev/ |

### DDD

| Resource | URL |
|---|---|
| DDD Crew (canvases, starter process) | https://github.com/ddd-crew |
| Domain Language (Evans) — DDD Reference | https://www.domainlanguage.com/ddd/reference/ |
| Virtual DDD community | https://virtualddd.com/ |
| EventStorming | https://www.eventstorming.com/ |
| Domain Storytelling | https://domainstorytelling.org/ |
| Context Mapper | https://contextmapper.org/ |
| Cosmic Python (free book) | https://www.cosmicpython.com/ |

### Documentation & decisions

| Resource | URL |
|---|---|
| C4 model | https://c4model.com/ |
| Structurizr | https://structurizr.com/ |
| arc42 | https://arc42.org/ |
| ADR GitHub org | https://adr.github.io/ |
| MADR | https://adr.github.io/madr/ |
| Mermaid | https://mermaid.js.org/ |
| PlantUML | https://plantuml.com/ |
| Architectural katas (Neal Ford) | https://nealford.com/katas/ |

### Distributed systems & data

| Resource | URL |
|---|---|
| Jepsen (consistency analyses) | https://jepsen.io/ |
| Raft | https://raft.github.io/ |
| Martin Kleppmann's blog | https://martin.kleppmann.com/ |
| Use The Index, Luke | https://use-the-index-luke.com/ |
| Google SRE books | https://sre.google/books/ |
| Brendan Gregg (performance) | https://www.brendangregg.com/ |

### APIs

| Resource | URL |
|---|---|
| OpenAPI | https://www.openapis.org/ |
| AsyncAPI | https://www.asyncapi.com/ |
| Google AIP | https://google.aip.dev/ |
| Microsoft REST API Guidelines | https://github.com/microsoft/api-guidelines |
| Zalando RESTful API Guidelines | https://opensource.zalando.com/restful-api-guidelines/ |
| GraphQL | https://graphql.org/ |
| gRPC | https://grpc.io/ |
| CloudEvents | https://cloudevents.io/ |

### Security

| Resource | URL |
|---|---|
| OWASP Top 10 | https://owasp.org/Top10/ |
| OWASP ASVS | https://owasp.org/www-project-application-security-verification-standard/ |
| OWASP Cheat Sheet Series | https://cheatsheetseries.owasp.org/ |
| OWASP API Security | https://owasp.org/API-Security/ |
| Threat Modeling Manifesto | https://www.threatmodelingmanifesto.org/ |
| oauth.net | https://oauth.net/ |
| SLSA | https://slsa.dev/ |
| NIST Zero Trust (SP 800-207) | https://csrc.nist.gov/pubs/sp/800/207/final |

### Frontend

| Resource | URL |
|---|---|
| web.dev | https://web.dev/ |
| MDN Web Docs | https://developer.mozilla.org/ |
| micro-frontends.org | https://micro-frontends.org/ |
| Feature-Sliced Design | https://feature-sliced.design/ |

---

## 4. Key articles and papers (classic must-reads)

| Title | Author(s) | Link |
|---|---|---|
| Microservices (2014) | James Lewis, Martin Fowler | https://martinfowler.com/articles/microservices.html |
| Who Needs an Architect? (2003) | Martin Fowler | https://martinfowler.com/ieeeSoftware/whoNeedsArchitect.pdf |
| Is High Quality Software Worth the Cost? | Martin Fowler | https://martinfowler.com/articles/is-quality-worth-cost.html |
| Big Ball of Mud (1997) | Brian Foote, Joseph Yoder | http://www.laputan.org/mud/ |
| On the Criteria To Be Used in Decomposing Systems into Modules (1972) | David Parnas | https://en.wikipedia.org/wiki/Information_hiding |
| How Do Committees Invent? (1968) — Conway's Law | Melvin Conway | https://www.melconway.com/Home/Committees_Paper.html |
| Hexagonal Architecture (2005) | Alistair Cockburn | https://alistair.cockburn.us/hexagonal-architecture/ |
| The Clean Architecture (2012) | Robert C. Martin | https://blog.cleancoder.com/uncle-bob/2012/08/13/the-clean-architecture.html |
| Documenting Architecture Decisions (2011) | Michael Nygard | https://www.cognitect.com/blog/2011/11/15/documenting-architecture-decisions |
| Effective Aggregate Design (2011) | Vaughn Vernon | https://www.dddcommunity.org/library/vernon_2011/ |
| Dynamo: Amazon's Highly Available Key-value Store (2007) | DeCandia et al. | https://www.allthingsdistributed.com/2007/10/amazons_dynamo.html |
| In Search of an Understandable Consensus Algorithm (Raft, 2014) | Ongaro, Ousterhout | https://raft.github.io/raft.pdf |
| Time, Clocks, and the Ordering of Events (1978) | Leslie Lamport | https://en.wikipedia.org/wiki/Lamport_timestamp |
| The Tail at Scale (2013) | Dean, Barroso | https://research.google/pubs/the-tail-at-scale/ |
| Architectural Styles and the Design of Network-based Software Architectures (2000) | Roy Fielding | https://www.ics.uci.edu/~fielding/pubs/dissertation/top.htm |
| Out of the Tar Pit (2006) | Moseley, Marks | (search title; widely mirrored) |
| No Silver Bullet (1986) | Fred Brooks | https://en.wikipedia.org/wiki/No_Silver_Bullet |
| Simple Made Easy (talk, 2011) | Rich Hickey | https://www.infoq.com/presentations/Simple-Made-Easy/ |

---

## 5. Talks, podcasts, newsletters

| Type | Name |
|---|---|
| Talks | Neal Ford & Mark Richards (O'Reilly), Sam Newman, Gregor Hohpe, Eric Evans (Explore DDD, DDD Europe), Alberto Brandolini, Martin Kleppmann, Udi Dahan ("Advanced Distributed Systems Design") |
| Conferences | DDD Europe, Explore DDD, O'Reilly Software Architecture (archive), QCon/InfoQ, GOTO, NDC, KanDDDinsky, Devoxx |
| Podcasts | Software Engineering Radio, InfoQ Podcast, GOTO Unscripted, Software Architecture Gathering, The Architecture Coffee Break, Thoughtworks Technology Podcast |
| Newsletters | Software Architecture Monday (Mark Richards, developertoarchitect.com), The Pragmatic Engineer, ByteByteGo, InfoQ Architecture & Design |

---

## 6. Certifications and curricula (optional)

| Programme | Notes |
|---|---|
| iSAQB CPSA-F / CPSA-A | International Software Architecture Qualification Board — foundation and advanced levels; vendor-neutral; strong in German-speaking/European markets (https://www.isaqb.org/) |
| Microsoft Certified: Azure Solutions Architect Expert | Cloud-specific; see [../06-cloud-architecture/README.md](../06-cloud-architecture/README.md) |
| AWS Solutions Architect (Associate/Professional) | Cloud-specific |
| TOGAF / ArchiMate | Enterprise architecture; see [../11-enterprise-architecture-business/README.md](../11-enterprise-architecture-business/README.md) |
| OWASP / (ISC)² CSSLP | Secure software lifecycle |

## Related

- Section index: [README.md](README.md)
- Laws, principles, mental models: [../10-laws-principles-mental-models/README.md](../10-laws-principles-mental-models/README.md)
- Engineering practices: [../08-engineering/README.md](../08-engineering/README.md)

## References

- See links inline above. Book editions and years are given as of 2026; check publishers (O'Reilly, Addison-Wesley, Manning, Pragmatic Bookshelf, Leanpub) for latest editions.
