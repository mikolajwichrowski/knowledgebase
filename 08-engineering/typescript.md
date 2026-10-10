# TypeScript

> **TL;DR** — TypeScript is a *structural*, gradually-typed superset of JavaScript whose types are erased at compile time. Turn on `strict`, model your domain with discriminated unions, prefer `unknown` over `any`, validate every trust boundary at runtime (zod/valibot), and let inference do the work: annotate inputs and public APIs, infer the rest.

Related: [JavaScript runtime](javascript-runtime.md) · [React](react.md) · [Code quality](code-quality-refactoring.md) · [Software architecture](../05-software-architecture/README.md)

---

## 1. Mental model

| Concept | What it means in practice |
|---|---|
| Types are erased | No runtime type info. `typeof x === "string"` is JS, not TS. Validation of external data must be done with code. |
| Structural typing | Compatibility is by *shape*, not by name. Two interfaces with identical members are interchangeable. |
| Gradual | `any` switches type checking off locally; `unknown` keeps it on and forces narrowing. |
| Sets of values | A type is a set of values. `never` = empty set, `unknown` = universal set, union = set union, intersection = set intersection. |
| Control-flow analysis | The type of a variable changes along code paths (`if`, `switch`, `return`, `throw`, assertions). |
| Soundness is not a goal | Some holes are intentional (array index access, bivariant method params, type assertions). Know them. |

### Structural typing

```ts
interface Point { x: number; y: number }
class Vec { constructor(public x: number, public y: number) {} }

const p: Point = new Vec(1, 2);           // OK — same shape
const q: Point = { x: 1, y: 2, z: 3 };    // Error — excess property check (only on fresh object literals)
const tmp = { x: 1, y: 2, z: 3 };
const r: Point = tmp;                      // OK — not fresh, extra props allowed
```

Consequence: you cannot distinguish `UserId` from `OrderId` if both are `string` — use **branded types** (section 6).

---

## 2. Core building blocks

### Primitives, literals, unions, intersections

```ts
type Status = "draft" | "published" | "archived";   // literal union (prefer over enum)
type WithTimestamps = { createdAt: Date; updatedAt: Date };
type Post = { id: string; status: Status } & WithTimestamps;
```

### `interface` vs `type`

| | `interface` | `type` alias |
|---|---|---|
| Object shapes | Yes | Yes |
| Unions, tuples, primitives, mapped/conditional | No | Yes |
| Declaration merging | Yes (useful for augmenting libs) | No |
| Extends | `extends` (errors on conflicts, faster checking) | `&` (silently produces `never` on conflict) |
| Rule of thumb | Public object contracts, class shapes | Everything else |

### `enum` — prefer alternatives

```ts
// Avoid: emits runtime code, numeric enums accept any number, not erasable
enum Color { Red, Green }

// Prefer: const object + derived union
export const Color = { Red: "red", Green: "green" } as const;
export type Color = (typeof Color)[keyof typeof Color]; // "red" | "green"
```

Node's built-in type stripping and the `erasableSyntaxOnly` flag (TS 5.8+) reject enums, namespaces with values and parameter properties — another reason to avoid them in new code.

---

## 3. Narrowing

| Technique | Example |
|---|---|
| `typeof` | `typeof v === "string"` |
| `instanceof` | `err instanceof HttpError` |
| `in` | `"kind" in shape` |
| Equality / truthiness | `if (x != null)` removes `null` and `undefined` |
| Discriminant property | `switch (event.type)` |
| `Array.isArray` | `Array.isArray(v)` |
| User-defined type guard | `function isUser(v: unknown): v is User` |
| Assertion function | `function assert(c: unknown, msg?: string): asserts c` |
| Inferred type predicates (TS 5.5+) | `arr.filter(x => x !== undefined)` now narrows to `T[]` |

```ts
function isNonEmpty<T>(arr: T[]): arr is [T, ...T[]] {
  return arr.length > 0;
}

function assertIsDefined<T>(v: T, msg = "value is undefined"): asserts v is NonNullable<T> {
  if (v == null) throw new Error(msg);
}
```

**Warning:** type guards are trusted blindly. A wrong guard is a lie the compiler believes. Generate them from a schema (zod) when possible.

---

## 4. Discriminated unions and exhaustiveness

The single most valuable modelling tool in TypeScript: make illegal states unrepresentable.

```ts
type RemoteData<T, E = Error> =
  | { status: "idle" }
  | { status: "loading" }
  | { status: "success"; data: T }
  | { status: "error"; error: E };

function render(state: RemoteData<User[]>): string {
  switch (state.status) {
    case "idle":    return "Start";
    case "loading": return "Loading…";
    case "success": return `${state.data.length} users`;   // data is available here only
    case "error":   return state.error.message;
    default:        return assertNever(state);              // compile error if a case is added and not handled
  }
}

function assertNever(x: never): never {
  throw new Error(`Unhandled variant: ${JSON.stringify(x)}`);
}
```

Compare with the "bag of optionals" anti-pattern: `{ loading: boolean; data?: T; error?: E }` allows `loading: true` with `error` set — 8 combinations, 4 valid.

Alternative exhaustiveness trick without a helper: `const _exhaustive: never = state;` or `satisfies never`.

---

## 5. Generics

```ts
// Inference from arguments
function first<T>(xs: readonly T[]): T | undefined { return xs[0]; }

// Constraints
function getProp<T, K extends keyof T>(obj: T, key: K): T[K] { return obj[key]; }

// Defaults
type ApiResponse<T = unknown> = { data: T; meta: { requestId: string } };

// const type parameters (TS 5.0) — infer literal types without `as const` at call site
function routes<const T extends readonly string[]>(r: T) { return r; }
const r = routes(["/home", "/about"]); // readonly ["/home", "/about"]

// NoInfer (TS 5.4) — prevent inference from a specific position
function createFSM<S extends string>(initial: NoInfer<S>, states: S[]) {}
```

Guidelines:
- A generic parameter should appear **at least twice** (relate input to output). If it appears once, use the constraint type directly.
- Do not use generics to "return whatever the caller wants" (`function parse<T>(s: string): T`) — that is a disguised `as`.
- Prefer fewer type parameters; push complexity into helper types.

---

## 6. Type-level programming

### Operators

| Operator | Purpose | Example |
|---|---|---|
| `keyof T` | Union of keys | `keyof User` → `"id" \| "name"` |
| `typeof v` | Type of a value | `typeof config` |
| `T[K]` | Indexed access | `User["id"]`, `Tuple[number]` |
| `T extends U ? X : Y` | Conditional | distributive over unions when `T` is a naked type param |
| `infer` | Extract inside conditional | `T extends Promise<infer U> ? U : T` |
| `{ [K in keyof T]: … }` | Mapped type | modifiers `readonly`, `?`, `-readonly`, `-?` |
| `as` in mapped type | Key remapping | `[K in keyof T as \`get${Capitalize<K & string>}\`]` |
| Template literal | String manipulation | `` `${Method} /${string}` `` |

### Conditional types

```ts
type UnwrapPromise<T> = T extends Promise<infer U> ? UnwrapPromise<U> : T;
type ElementOf<T> = T extends readonly (infer E)[] ? E : never;

// Distributive: applied to each union member
type NonNull<T> = T extends null | undefined ? never : T;
// Prevent distribution by wrapping in a tuple
type IsUnion<T, U = T> = [T] extends [never] ? false : T extends U ? ([U] extends [T] ? false : true) : never;
```

### Mapped types

```ts
type Mutable<T> = { -readonly [K in keyof T]: T[K] };
type Getters<T> = { [K in keyof T as `get${Capitalize<string & K>}`]: () => T[K] };
type PickByValue<T, V> = { [K in keyof T as T[K] extends V ? K : never]: T[K] };
```

### Template literal types

```ts
type HttpMethod = "GET" | "POST" | "PUT" | "DELETE";
type Route = `/${string}`;
type Endpoint = `${HttpMethod} ${Route}`;        // "GET /users" etc.

type EventName<T extends string> = `on${Capitalize<T>}`;
type Handlers = { [E in "click" | "focus" as EventName<E>]: () => void }; // onClick, onFocus

// Parse path params
type Params<P extends string> =
  P extends `${string}:${infer Name}/${infer Rest}` ? Name | Params<`/${Rest}`>
  : P extends `${string}:${infer Name}` ? Name : never;
type T1 = Params<"/users/:userId/posts/:postId">; // "userId" | "postId"
```

### Branded (nominal) types

```ts
declare const brand: unique symbol;
type Brand<T, B extends string> = T & { readonly [brand]: B };

type UserId = Brand<string, "UserId">;
type OrderId = Brand<string, "OrderId">;

const UserId = (s: string): UserId => {
  if (!/^usr_[a-z0-9]+$/.test(s)) throw new Error("invalid user id");
  return s as UserId;                          // the only place a cast is allowed
};

function loadOrder(id: OrderId) {}
loadOrder(UserId("usr_1"));                    // Error — good
```

Zod supports this directly: `z.string().brand<"UserId">()`.

---

## 7. Utility types cheat sheet

| Utility | Result | Typical use |
|---|---|---|
| `Partial<T>` | all props optional | patch/update DTOs |
| `Required<T>` | all props required | after defaults are applied |
| `Readonly<T>` | all props readonly (shallow) | immutable state |
| `Pick<T, K>` | subset of keys | view models |
| `Omit<T, K>` | remove keys (K not checked against T!) | create DTO without `id` |
| `Record<K, V>` | object with keys K | lookup tables (`Record<Status, string>` forces exhaustiveness) |
| `Exclude<U, E>` | remove union members | `Exclude<Status, "archived">` |
| `Extract<U, E>` | keep matching members | `Extract<Event, { type: "click" }>` |
| `NonNullable<T>` | remove `null \| undefined` | after guards |
| `ReturnType<F>` | function return type | derive from implementation |
| `Parameters<F>` | tuple of params | wrappers/decorators |
| `ConstructorParameters<C>` / `InstanceType<C>` | class helpers | factories, DI |
| `Awaited<T>` | unwrap promises recursively | `Awaited<ReturnType<typeof fetchUser>>` |
| `NoInfer<T>` | block inference site | generic APIs |
| `Uppercase/Lowercase/Capitalize/Uncapitalize<S>` | string literal transforms | template literal types |
| `ThisParameterType`, `OmitThisParameter` | `this` typing | legacy APIs |

---

## 8. `satisfies`, `as const`, and assertions

```ts
type Theme = Record<"primary" | "secondary", `#${string}`>;

// Annotation: widens to Theme, loses literal info
const a: Theme = { primary: "#000", secondary: "#fff" };

// satisfies: validates against Theme but keeps the narrower inferred type
const b = { primary: "#000", secondary: "#fff" } satisfies Theme;
b.primary; // "#000"-ish string, keys known exactly

// as const: deep readonly + literal types
const ROLES = ["admin", "editor", "viewer"] as const;
type Role = (typeof ROLES)[number];   // "admin" | "editor" | "viewer"

// Combine
const config = { retries: 3, mode: "strict" } as const satisfies { retries: number; mode: "strict" | "loose" };
```

| Construct | Checks? | Changes inferred type? | Risk |
|---|---|---|---|
| `: T` annotation | Yes | Yes (to T) | loses precision |
| `satisfies T` | Yes | No | none — preferred for config objects |
| `as T` | Only "sufficient overlap" | Yes | lies to compiler |
| `as unknown as T` | No | Yes | full escape hatch — code smell |
| `!` non-null | No | removes null | runtime crash if wrong |

---

## 9. `unknown` vs `any` vs `never`

| | `any` | `unknown` | `never` |
|---|---|---|---|
| Assign anything to it | Yes | Yes | No |
| Assign it to anything | Yes | Only `unknown`/`any` | Yes (bottom type) |
| Use members | Yes (unchecked) | Only after narrowing | n/a |
| Use for | migration, truly dynamic glue (sparingly) | external input, `catch (e)`, JSON | exhaustiveness, impossible branches, functions that throw |

- With `useUnknownInCatchVariables` (part of `strict`), `catch (e)` is `unknown`. Narrow: `e instanceof Error ? e.message : String(e)`.
- `JSON.parse` returns `any` — immediately pipe into a schema.
- Lint rules: `@typescript-eslint/no-explicit-any`, `no-unsafe-assignment`, `no-unsafe-member-access`.

---

## 10. tsconfig — strictness flags

```jsonc
{
  "compilerOptions": {
    "target": "ES2022",
    "module": "NodeNext",            // or "ESNext" + "moduleResolution": "Bundler" for apps built by Vite/Next
    "moduleResolution": "NodeNext",
    "strict": true,
    "noUncheckedIndexedAccess": true,
    "exactOptionalPropertyTypes": true,
    "noImplicitOverride": true,
    "noFallthroughCasesInSwitch": true,
    "noPropertyAccessFromIndexSignature": true,
    "verbatimModuleSyntax": true,
    "isolatedModules": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true,
    "declaration": true,
    "sourceMap": true
  }
}
```

| Flag | In `strict`? | Effect |
|---|---|---|
| `noImplicitAny` | Yes | Error when a type cannot be inferred and falls back to `any` |
| `strictNullChecks` | Yes | `null`/`undefined` are distinct types — the most important flag |
| `strictFunctionTypes` | Yes | Contravariant parameter checks for function types (not methods) |
| `strictBindCallApply` | Yes | Type-check `bind/call/apply` |
| `strictPropertyInitialization` | Yes | Class props must be initialized in constructor |
| `noImplicitThis` | Yes | Error on `this` of type `any` |
| `alwaysStrict` | Yes | Emit `"use strict"` |
| `useUnknownInCatchVariables` | Yes | `catch (e)` is `unknown` |
| `noUncheckedIndexedAccess` | No | `arr[i]` and `record[key]` are `T \| undefined` — strongly recommended |
| `exactOptionalPropertyTypes` | No | `{ a?: string }` does not accept explicit `undefined` |
| `noImplicitReturns` | No | All code paths must return |
| `noImplicitOverride` | No | Require `override` keyword |
| `noPropertyAccessFromIndexSignature` | No | Forces `obj["key"]` for index signatures |
| `verbatimModuleSyntax` | No | `import type` required for type-only imports; predictable emit |
| `isolatedModules` | No | Each file transpilable alone (required by esbuild/SWC/Babel) |
| `erasableSyntaxOnly` | No | Disallow TS-only runtime constructs (enums, namespaces, param properties) |

Migration tip: enable `strict` per folder via project references or use `// @ts-expect-error` with a ticket number, never `// @ts-ignore`.

---

## 11. Runtime validation (zod)

Types vanish at runtime; every **trust boundary** (HTTP request bodies, API responses, env vars, `localStorage`, message queues, URL params, files) needs a validator. Define the schema once and **derive** the type.

```ts
import { z } from "zod";

export const UserSchema = z.object({
  id: z.string().uuid(),
  email: z.string().email(),
  role: z.enum(["admin", "editor", "viewer"]),
  createdAt: z.coerce.date(),
  tags: z.array(z.string()).default([]),
});
export type User = z.infer<typeof UserSchema>;

// Parse, don't validate: output is typed and normalized
const result = UserSchema.safeParse(await res.json());
if (!result.success) {
  logger.warn({ issues: result.error.issues }, "invalid user payload");
  throw new BadGateway();
}
const user: User = result.data;

// Env vars at startup — fail fast
const Env = z.object({
  DATABASE_URL: z.string().url(),
  PORT: z.coerce.number().int().default(3000),
  NODE_ENV: z.enum(["development", "test", "production"]),
});
export const env = Env.parse(process.env);
```

| Library | Notes |
|---|---|
| zod | De-facto standard, rich ecosystem (tRPC, React Hook Form resolvers, OpenAPI generators) |
| valibot | Modular, tree-shakeable, very small bundles |
| ArkType | TS-syntax-like string definitions, fast |
| TypeBox | Emits JSON Schema; pairs with Ajv/Fastify |
| Standard Schema | Common interface implemented by zod/valibot/ArkType so libraries accept any of them |

Also consider generating types from contracts: OpenAPI → `openapi-typescript`, GraphQL → GraphQL Code Generator, database → Prisma/Drizzle/Kysely.

---

## 12. Patterns

- **Parse, don't validate** — convert untyped input into a precise type once at the edge; core code never re-checks.
- **Result type** for expected failures instead of throwing:
  ```ts
  type Result<T, E = Error> = { ok: true; value: T } | { ok: false; error: E };
  ```
- **Builder via generics** that accumulates state in the type (e.g. query builders).
- **Exhaustive maps**: `const labels: Record<Status, string> = {...}` — adding a status breaks compilation until the label exists.
- **Opaque module boundary**: export types and factory functions, not raw constructors.
- **Type-only imports**: `import type { X } from "./x"` to avoid runtime cycles.
- **Derive, don't duplicate**: `z.infer`, `ReturnType`, `typeof CONST[number]`.
- **Overloads** only when return type depends on argument in a way unions cannot express; otherwise generics or unions.
- **Declaration merging/module augmentation** to type third-party extensions (e.g. `declare module "express-serve-static-core" { interface Request { user?: User } }`).

---

## 13. Pitfalls

| Pitfall | Why it bites | Fix |
|---|---|---|
| `as` casts on API data | Compiler trusts it; runtime crashes | Schema parse |
| `Omit<T, "typo">` | `Omit` does not check keys | Write a strict `StrictOmit<T, K extends keyof T>` |
| `Object.keys(obj)` returns `string[]` | Structural typing: object may have more keys | Cast deliberately or iterate a known key list |
| Array index without `noUncheckedIndexedAccess` | `arr[10]` typed as `T` | Enable the flag |
| Optional vs `undefined` | `{a?: X}` vs `{a: X \| undefined}` differ | `exactOptionalPropertyTypes` |
| Method bivariance | Methods declared with method syntax are checked bivariantly | Use property syntax `fn: (x: T) => void` in interfaces |
| Intersection conflicts | `{a: string} & {a: number}` → `a: never` silently | Use `interface extends` |
| `{}` type | Means "any non-nullish value", not "empty object" | `Record<string, never>` or `object` |
| `Function` type | Untyped callable | Explicit signature |
| Enums | Runtime code, numeric unsoundness | `as const` objects |
| Deep conditional types | Slow compile, unreadable errors | Keep type gymnastics in a few library files; test them |
| Barrel files (`index.ts` re-exports) | Slower builds, circular imports, larger bundles | Import from source modules in apps |
| `@ts-ignore` | Hides future errors too | `@ts-expect-error` with reason |
| Relying on types for security | Erased at runtime | Validate input |

### Testing types

```ts
import { expectTypeOf } from "vitest";
expectTypeOf(parseUser).returns.toEqualTypeOf<User>();
// or: // @ts-expect-error — negative tests
```

Tools: `vitest` type tests, `tsd`, `@arethetypeswrong/cli` (package export correctness), `tsc --noEmit` in CI, `tsc --generateTrace` for slow types.

---

## 14. When to use / when not

| Use TypeScript heavily | Be lighter |
|---|---|
| Shared libraries, SDKs, long-lived apps, teams > 2 | Throwaway scripts (JSDoc + `// @ts-check` is enough) |
| Domain modelling with many states | Prototypes where types churn hourly |
| Refactoring-heavy codebases | — |

Avoid "type astronautics" in application code: if a type needs a comment to be understood, a simpler type plus a runtime check is often better.

---

## 15. Checklist

- [ ] `strict` + `noUncheckedIndexedAccess` on
- [ ] No `any` in new code (lint enforced); `unknown` at boundaries
- [ ] Every external input parsed by a schema; types derived with `z.infer`
- [ ] Domain states as discriminated unions with `assertNever`
- [ ] IDs branded where mix-ups are plausible
- [ ] Config objects use `satisfies`
- [ ] `tsc --noEmit` runs in CI; type errors block merge
- [ ] Public library types tested (`expectTypeOf`, `attw`)

## References

- TypeScript Handbook — https://www.typescriptlang.org/docs/handbook/intro.html
- Narrowing — https://www.typescriptlang.org/docs/handbook/2/narrowing.html
- Generics — https://www.typescriptlang.org/docs/handbook/2/generics.html
- Conditional types — https://www.typescriptlang.org/docs/handbook/2/conditional-types.html
- Mapped types — https://www.typescriptlang.org/docs/handbook/2/mapped-types.html
- Template literal types — https://www.typescriptlang.org/docs/handbook/2/template-literal-types.html
- Utility types — https://www.typescriptlang.org/docs/handbook/utility-types.html
- TSConfig reference — https://www.typescriptlang.org/tsconfig
- Zod — https://zod.dev
- Valibot — https://valibot.dev
- Standard Schema — https://standardschema.dev
- typescript-eslint — https://typescript-eslint.io
- Type Challenges — https://github.com/type-challenges/type-challenges
- Total TypeScript (Matt Pocock) — https://www.totaltypescript.com
- *Effective TypeScript* (Dan Vanderkam, 2nd ed.) — https://effectivetypescript.com
- Structural type system — https://en.wikipedia.org/wiki/Structural_type_system
