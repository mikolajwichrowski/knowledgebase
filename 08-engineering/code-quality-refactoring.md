# Code Quality and Refactoring

> **TL;DR** — Quality code is code that is cheap and safe to change. Optimize for readability, small cohesive units, clear names and explicit dependencies. Recognize code smells (Fowler/Beck), remove them with small behaviour-preserving refactorings under test, keep complexity measurable (cyclomatic, cognitive), and let tools (formatters, linters, analyzers, SonarQube) handle everything mechanical so human review focuses on design.

Related: [Testing](testing.md) · [Git & code review](git-and-version-control.md#6-pull-requests-and-code-review) · [Software architecture](../05-software-architecture/README.md) · [Laws & principles](../10-laws-principles-mental-models/README.md)

---

## 1. Clean Code principles (condensed)

| Principle | In practice |
|---|---|
| Meaningful names | Reveal intent; avoid abbreviations and noise words (`data`, `info`, `manager`) |
| Small functions | Do one thing at one level of abstraction; few parameters (≤ 3; otherwise an object) |
| Command–query separation | A function either changes state or returns a value, not both |
| No side-effect surprises | Name reflects all effects (`saveAndNotify`) or split it |
| DRY — but… | Remove duplicated *knowledge*, not coincidentally similar code. "Rule of three" before abstracting; prefer duplication over the wrong abstraction (Sandi Metz) |
| KISS / YAGNI | Simplest thing that works; don't build for speculative futures |
| Error handling | Exceptions/results over error codes; don't return `null` for collections; fail fast at boundaries |
| Comments | Explain **why**, constraints, and non-obvious decisions; delete commented-out code |
| Boy Scout Rule | Leave code a little cleaner than you found it |
| Law of Demeter | Talk to friends, not strangers (`a.getB().getC().doX()` is a smell) |
| Tell, don't ask | Push behaviour to the object that has the data |
| Composition over inheritance | Inheritance for true "is-a" with substitutability |
| Immutability by default | Fewer moving parts, easier reasoning, safe concurrency |
| Make illegal states unrepresentable | Types (discriminated unions, value objects) over runtime checks |

### SOLID (quick recap — details in [Software architecture](../05-software-architecture/README.md))

| | Principle | Smell when violated |
|---|---|---|
| S | Single Responsibility — one reason to change (one actor) | Divergent change |
| O | Open/Closed — extend without modifying | Switch statements on type spread around |
| L | Liskov Substitution — subtypes honour base contracts | `if (x instanceof Special)` checks, `NotImplementedException` |
| I | Interface Segregation — small client-specific interfaces | Fat interfaces, stub implementations |
| D | Dependency Inversion — depend on abstractions; high-level policy does not depend on details | `new` of infrastructure in domain code |

Also: **cohesion** high, **coupling** low; **connascence** as a vocabulary for coupling strength (name < type < meaning < position < algorithm < execution order < timing < values < identity).

---

## 2. Naming

| Kind | Guideline | Good | Bad |
|---|---|---|---|
| Variables | Nouns, intent, units | `elapsedMs`, `activeUsers` | `d`, `list1`, `temp` |
| Booleans | Predicates | `isEnabled`, `hasAccess`, `shouldRetry` | `flag`, `status` |
| Functions | Verbs | `calculateTax`, `fetchInvoice` | `doIt`, `process`, `handle` |
| Classes/types | Nouns from the domain (ubiquitous language) | `Invoice`, `PaymentPolicy` | `InvoiceManager`, `DataHelper`, `Utils` |
| Collections | Plural | `orders` | `orderList` |
| Constants | Meaning, not value | `MAX_RETRIES` | `THREE` |
| Scope rule | Short names for small scopes, descriptive for wide scopes | `i` in a 3-line loop | `i` as a field |

Consistency beats perfection: one word per concept (`fetch` vs `get` vs `retrieve` — pick one). Use domain terms agreed with the business (DDD).

---

## 3. Code smells catalog

From *Refactoring* (Fowler, 2nd ed., smells chapter co-written with Kent Beck). Grouped as in refactoring.guru.

### Bloaters

| Smell | Symptom | Typical refactorings |
|---|---|---|
| **Long Method / Function** | Needs scrolling; comments separating sections | Extract Function, Replace Temp with Query, Decompose Conditional, Replace Function with Command |
| **Large Class** | Too many fields/responsibilities | Extract Class, Extract Superclass, Replace Type Code with Subclasses |
| **Long Parameter List** | > 3–4 params, boolean flags | Introduce Parameter Object, Preserve Whole Object, Remove Flag Argument |
| **Primitive Obsession** | Strings for emails, money as `decimal` + currency string, type codes | Replace Primitive with Object (value objects), Replace Type Code with Subclasses |
| **Data Clumps** | Same group of fields appearing together | Extract Class, Introduce Parameter Object |

### Object-orientation abusers

| Smell | Symptom | Refactorings |
|---|---|---|
| **Repeated Switches** | Same `switch` on type in many places | Replace Conditional with Polymorphism |
| **Temporary Field** | Fields only set in some situations | Extract Class, Introduce Special Case |
| **Refused Bequest** | Subclass ignores inherited behaviour | Push Down Method, Replace Subclass/Superclass with Delegate |
| **Alternative Classes with Different Interfaces** | Same job, different signatures | Change Function Declaration, Extract Superclass |

### Change preventers

| Smell | Symptom | Refactorings |
|---|---|---|
| **Divergent Change** | One class changed for many different reasons | Split Phase, Extract Class, Move Function |
| **Shotgun Surgery** | One change requires edits in many classes | Move Function/Field, Combine Functions into Class, Inline Class |
| **Parallel Inheritance Hierarchies** | Adding a subclass forces another subclass elsewhere | Move Function, collapse one hierarchy |

### Dispensables

| Smell | Refactorings |
|---|---|
| **Comments** (explaining bad code) | Extract Function with a good name, Rename, Introduce Assertion |
| **Duplicated Code** | Extract Function, Pull Up Method, Slide Statements |
| **Lazy Element** (class/function not pulling its weight) | Inline Function/Class, Collapse Hierarchy |
| **Data Class** (only getters/setters) | Move behaviour in, Encapsulate Record, Remove Setting Method |
| **Dead Code** | Remove Dead Code (version control remembers) |
| **Speculative Generality** | Collapse Hierarchy, Inline, Remove unused params |

### Couplers

| Smell | Symptom | Refactorings |
|---|---|---|
| **Feature Envy** | Method uses another object's data more than its own | Move Function, Extract Function |
| **Inappropriate Intimacy / Insider Trading** | Classes poke into each other's internals | Move Function/Field, Hide Delegate, Replace Subclass with Delegate |
| **Message Chains** | `a.b().c().d()` | Hide Delegate, Extract Function |
| **Middle Man** | Class only delegates | Remove Middle Man, Inline Function |

Others from the 2nd edition: **Mysterious Name**, **Global Data**, **Mutable Data**, **Loops** (replace with pipelines), **Incomplete Library Class**.

Higher-level smells: god service, anemic domain model (in DDD contexts), circular dependencies between modules, leaking abstractions, boolean blindness, stringly-typed code, deep nesting / arrow code.

---

## 4. Refactoring

> Refactoring: a change to the internal structure of software to make it easier to understand and cheaper to modify **without changing its observable behaviour** (Fowler).

### When
- **Preparatory**: "make the change easy (warning: this may be hard), then make the easy change" (Kent Beck).
- **Comprehension**: rename and extract while reading.
- **Litter-pickup**: Boy Scout Rule.
- **Planned**: only for larger debts the team agreed to schedule.
- **Rule of three**: first time do it, second time wince, third time refactor.

### When not
- No tests and no way to verify behaviour → first add characterization tests.
- Code is about to be deleted/replaced.
- In the middle of an incident.
- Mixed into a feature PR making review impossible → separate commits/PRs.

### Key moves (Fowler's catalog — most used)

| Refactoring | Inverse | Purpose |
|---|---|---|
| Extract Function | Inline Function | Name a fragment of logic |
| Extract Variable | Inline Variable | Name an expression |
| Change Function Declaration (rename, add/remove params) | — | Clarify interface |
| Encapsulate Variable / Record / Collection | — | Control access to data |
| Rename Variable / Field | — | Clarity |
| Introduce Parameter Object | — | Group data clumps |
| Combine Functions into Class / Transform | — | Group behaviour around data |
| Split Phase | — | Separate parsing from processing |
| Move Function / Move Field | — | Put behaviour with its data |
| Slide Statements | — | Group related code |
| Split Loop | — | One loop, one job |
| Replace Loop with Pipeline | — | `map/filter/reduce` |
| Decompose Conditional | — | Name branches |
| Consolidate Conditional Expression | — | Merge identical outcomes |
| Replace Nested Conditional with Guard Clauses | — | Flatten |
| Replace Conditional with Polymorphism | — | Kill repeated switches |
| Introduce Special Case (Null Object) | — | Remove repeated null checks |
| Introduce Assertion | — | Document assumptions |
| Separate Query from Modifier | — | CQS |
| Parameterize Function | — | Remove near-duplicates |
| Remove Flag Argument | — | `setOn()`/`setOff()` instead of `set(true)` |
| Replace Primitive with Object | — | Value objects |
| Replace Temp with Query | — | Reduce locals |
| Extract Class / Inline Class | — | Split or merge responsibilities |
| Hide Delegate / Remove Middle Man | — | Tune coupling |
| Replace Constructor with Factory Function | — | Named creation |
| Replace Function with Command | — | Complex operations with undo/state |
| Replace Subclass with Delegate / Superclass with Delegate | — | Composition over inheritance |
| Replace Error Code with Exception / Exception with Precheck | — | Error handling clarity |

```ts
// Before: nested conditionals
function payAmount(e: Employee) {
  let result;
  if (e.isSeparated) result = { amount: 0, reason: "SEP" };
  else {
    if (e.isRetired) result = { amount: 0, reason: "RET" };
    else result = computeNormal(e);
  }
  return result;
}
// After: guard clauses
function payAmount(e: Employee) {
  if (e.isSeparated) return { amount: 0, reason: "SEP" };
  if (e.isRetired) return { amount: 0, reason: "RET" };
  return computeNormal(e);
}
```

### Working with legacy code (Feathers)
- Legacy code = code without tests.
- Algorithm: identify change points → find test points → break dependencies (seams) → write characterization tests → change and refactor.
- Techniques: Sprout Method/Class (add new code elsewhere, tested), Wrap Method/Class, Extract Interface, Parameterize Constructor, approval/snapshot tests to pin current behaviour.
- Large-scale: Strangler Fig, Branch by Abstraction, parallel change (expand → migrate → contract).
- Automated large refactors: codemods (jscodeshift, ts-morph, OpenRewrite, Roslyn code fixes, LibCST), IDE refactorings (safer than manual edits), AI agents with tests as the safety net.

---

## 5. Complexity metrics

| Metric | Definition | Thresholds (rules of thumb) | Notes |
|---|---|---|---|
| **Cyclomatic complexity** (McCabe, 1976) | Number of linearly independent paths = decision points + 1 | 1–10 simple; 11–20 moderate; 21–50 complex; > 50 untestable | Minimum number of tests for branch coverage; counts `switch` cases equally |
| **Cognitive complexity** (SonarSource, 2017) | How hard code is for a human to understand: increments for breaks in linear flow, extra for nesting; ignores shorthand like `?.` | Sonar default per function: 15 | Penalizes nesting; `switch` counts once; better proxy for readability |
| Lines of code per function/file | Size | Function < ~40, file < ~400 (team convention) | Crude but useful |
| Nesting depth | Max indentation levels | ≤ 3 | |
| Fan-in / fan-out, afferent/efferent coupling | Dependencies | — | Instability I = Ce / (Ca + Ce) |
| Halstead metrics, maintainability index | Composite | — | Visual Studio shows MI (0–100) |
| Code churn × complexity (hotspots) | Files changed often *and* complex | — | Prioritize refactoring here (Adam Tornhill, *Your Code as a Crime Scene*; CodeScene) |

```js
// Cyclomatic 4, cognitive 7 (nesting penalties)
function f(a, b) {
  if (a) {                 // +1 (cog +1)
    for (const x of b) {   // +1 (cog +2: nesting 1)
      if (x) {             // +1 (cog +3: nesting 2)
        doIt(x);
      }
    }
  }
}
```

Use metrics to find hotspots and start conversations, not as hard gates (except generous caps that prevent extremes).

---

## 6. Linters, formatters and analyzers

| Ecosystem | Formatter | Linter / analyzer | Notes |
|---|---|---|---|
| JS/TS | Prettier, Biome, dprint | ESLint (flat config `eslint.config.js`) + typescript-eslint (type-aware rules), Biome, Oxlint | Biome = Rust formatter+linter, very fast; Oxlint as a fast pre-pass |
| CSS | Prettier/Biome | Stylelint | |
| Python | Ruff format (Black-compatible) | Ruff (hundreds of rules: pyflakes, pycodestyle, isort, bugbear, pyupgrade, bandit…), mypy/pyright | Single fast tool |
| C#/.NET | `dotnet format`, CSharpier | Roslyn analyzers (CA rules, IDE rules), `.editorconfig` severity, StyleCop.Analyzers, Roslynator, SonarAnalyzer.CSharp, Meziantou.Analyzer | `<AnalysisLevel>latest-recommended</AnalysisLevel>`, `TreatWarningsAsErrors` |
| SQL | SQLFluff | SQLFluff | |
| IaC | `terraform fmt`, Bicep formatter | tflint, Checkov, `bicep lint`, PSRule | |
| Shell | shfmt | ShellCheck | |
| Markdown/docs | Prettier | markdownlint, Vale (prose style) | |
| Multi-language | — | MegaLinter, Trunk Check | |

```js
// eslint.config.js (flat config)
import js from "@eslint/js";
import tseslint from "typescript-eslint";
import reactHooks from "eslint-plugin-react-hooks";

export default tseslint.config(
  js.configs.recommended,
  tseslint.configs.strictTypeChecked,
  reactHooks.configs["recommended-latest"],
  { languageOptions: { parserOptions: { projectService: true } } },
  { rules: { "@typescript-eslint/no-floating-promises": "error" } },
);
```

```ini
# .editorconfig (.NET)
root = true
[*.cs]
indent_style = space
indent_size = 4
dotnet_diagnostic.CA2007.severity = none          # ConfigureAwait not needed in app code
dotnet_diagnostic.CA1848.severity = warning       # use LoggerMessage delegates
csharp_style_namespace_declarations = file_scoped:warning
```

Principles:
- **Formatters are not negotiable** — run on save and in CI (`--check`); no style debates in review.
- Linters catch bugs, not taste. Prefer recommended presets; add rules after real incidents.
- Run fast checks in pre-commit (lint-staged/lefthook on changed files), everything in CI.
- Introduce in legacy code with a baseline (ESLint suppressions, Sonar "new code" period, Ruff `--add-noqa`) so new code is clean without blocking on old debt.

## 7. Static analysis platforms

| Tool | Focus |
|---|---|
| SonarQube Server / SonarQube Cloud (formerly SonarCloud) | Bugs, code smells, vulnerabilities, security hotspots, duplication, coverage; **Quality Gate** on *new code* ("Clean as You Code") |
| CodeQL (GitHub Advanced Security) | Semantic security queries (SAST) |
| Semgrep | Fast pattern-based rules, custom rules easy |
| Snyk / Dependabot / Renovate / OWASP Dependency-Check | Dependency vulnerabilities (SCA) |
| Microsoft Security DevOps / Defender for DevOps | Aggregated scanners in Azure DevOps/GitHub |
| CodeScene | Behavioural code analysis (hotspots, change coupling, team knowledge) |
| NDepend | .NET architecture and quality rules |
| dependency-cruiser, ArchUnitTS, ArchUnitNET, NetArchTest, import-linter (Python) | Architecture rules as tests |

Recommended SonarQube quality gate on new code: no new bugs/vulnerabilities, security hotspots reviewed, coverage on new code ≥ 80%, duplication on new code ≤ 3%.

---

## 8. Technical debt

| Quadrant (Fowler) | Reckless | Prudent |
|---|---|---|
| **Deliberate** | "We don't have time for design" | "We must ship now and deal with consequences" |
| **Inadvertent** | "What's layering?" | "Now we know how we should have done it" |

- Make debt visible: tagged backlog items, ADRs noting trade-offs, hotspot maps.
- Pay down where you work (hotspots), not uniformly.
- Budget: a steady share of capacity (often 15–25%) vs big rewrite projects.
- Rewrites from scratch are high risk (Joel Spolsky's "Things You Should Never Do"); prefer incremental strangling.

---

## 9. Review checklist for quality

- [ ] Names reveal intent; domain language used
- [ ] Functions small, single level of abstraction; nesting ≤ 3
- [ ] No duplicated knowledge; no speculative generality
- [ ] Errors handled explicitly at the right layer; no swallowed exceptions
- [ ] Inputs validated at boundaries; types narrow the domain
- [ ] Side effects isolated and obvious
- [ ] Tests describe behaviour and would fail if behaviour broke
- [ ] No new warnings; formatter/linter clean
- [ ] Comments explain why; no commented-out code
- [ ] Public API changes documented, backward compatible or versioned

## 10. Common mistakes

- Big-bang refactors without tests or in a single PR.
- Abstracting after the first duplication.
- Treating metrics thresholds as goals.
- Linters configured so strictly that people add disable comments everywhere.
- Refactoring and behaviour change in one commit.
- Ignoring hotspots and polishing stable code no one touches.

## References

- Refactoring catalog (Martin Fowler) — https://refactoring.com/catalog/
- Refactoring.com — https://refactoring.com
- Refactoring.guru: code smells — https://refactoring.guru/refactoring/smells
- Refactoring.guru: refactoring techniques — https://refactoring.guru/refactoring/techniques
- Martin Fowler, Code Smell — https://martinfowler.com/bliki/CodeSmell.html
- Martin Fowler, Technical Debt Quadrant — https://martinfowler.com/bliki/TechnicalDebtQuadrant.html
- Martin Fowler, Strangler Fig Application — https://martinfowler.com/bliki/StranglerFigApplication.html
- Martin Fowler, Branch by Abstraction — https://martinfowler.com/bliki/BranchByAbstraction.html
- Cyclomatic complexity — https://en.wikipedia.org/wiki/Cyclomatic_complexity
- SonarSource, Cognitive Complexity white paper — https://www.sonarsource.com/resources/cognitive-complexity/
- SonarQube docs — https://docs.sonarsource.com/sonarqube-server/
- ESLint — https://eslint.org
- typescript-eslint — https://typescript-eslint.io
- Prettier — https://prettier.io
- Biome — https://biomejs.dev
- Ruff — https://docs.astral.sh/ruff/
- .NET code analysis — https://learn.microsoft.com/dotnet/fundamentals/code-analysis/overview
- Sandi Metz, The Wrong Abstraction — https://sandimetz.com/blog/2016/1/20/the-wrong-abstraction
- Joel Spolsky, Things You Should Never Do — https://www.joelonsoftware.com/2000/04/06/things-you-should-never-do-part-i/
- *Refactoring* (Martin Fowler, 2nd ed.)
- *Clean Code* (Robert C. Martin) — read critically; some examples have aged
- *Working Effectively with Legacy Code* (Michael Feathers)
- *A Philosophy of Software Design* (John Ousterhout)
- *Tidy First?* (Kent Beck)
- *Your Code as a Crime Scene* (Adam Tornhill, 2nd ed.)
