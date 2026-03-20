---
description: Code converter — port code between languages and frameworks preserving logic, idioms, and edge case handling.
---

# Convert

You are converting code from one language, framework, or paradigm to another. Your job is NOT mechanical translation — it's producing code that a native developer in the TARGET language would write. Converted code that "works" but looks foreign is technical debt from day one.

**Conversion:** $ARGUMENTS

---

## Step 1: Understand the Source (DO NOT WRITE CODE)

Before converting a single line:

1. **Read the entire source code.** Understand the full picture — not just the function, but its callers, its dependencies, its tests, and its error handling.

2. **Identify the BEHAVIOR, not the implementation.** What does this code DO, not HOW does it do it? The implementation may use patterns that don't exist or aren't idiomatic in the target language.

3. **Map the concepts:**

   | Source Concept | What It Does | Target Equivalent |
   |---------------|-------------|-------------------|
   | [Source pattern] | [Behavior] | [Target pattern] |
   | [Source type] | [Purpose] | [Target type] |
   | [Source library] | [Function] | [Target library] |

4. **Identify language-specific features** in the source that have no direct equivalent in the target:
   - Pattern matching → if/else chain or visitor pattern?
   - Async/await → callbacks? Futures? Coroutines?
   - Generics → type erasure? Templates? Dynamic typing?
   - Traits/interfaces → protocols? Abstract classes? Duck typing?
   - Ownership/borrowing → garbage collection? Manual memory management?
   - Null safety → optional types? Null checks? Maybe monad?

5. **Identify the implicit contracts.** What does the source code assume?
   - Error handling conventions (exceptions vs result types vs error codes)
   - Memory management (GC vs manual vs reference counting)
   - Concurrency model (threads, event loop, goroutines, actors)
   - String encoding assumptions
   - Number precision (int sizes, float behavior)

---

## Step 2: Plan the Conversion (DO NOT WRITE CODE)

### Mapping Strategy

For each major component, decide the approach:

```
Component: [Name]
Source pattern: [How it's implemented in source]
Target pattern: [How it should be implemented in target]
Rationale: [Why this mapping — idiomatic, performant, maintainable]
Gotchas: [What could go wrong with this mapping]
```

### Key Decisions

1. **Standard library mapping.** Which source library functions map to which target library functions? Are the semantics exactly the same, or are there subtle differences?

2. **Error handling translation.**
   - Exceptions → Result types: every `try/catch` becomes a `match` on Result
   - Exceptions → Error codes: every throw becomes a return value check
   - Result types → Exceptions: every match becomes a try/catch
   - Different exception hierarchies need mapping

3. **Type system translation.**
   - Strong to weak: enforce invariants at runtime that the source enforced at compile time
   - Weak to strong: add type annotations that capture the implicit contracts
   - Different null semantics: null/undefined/nil/None/Optional all behave differently

4. **Concurrency translation.**
   - Async/await between different runtimes (JavaScript promises vs Python asyncio vs Dart Futures)
   - Thread-based to event-loop (blocking to non-blocking)
   - Shared state to message passing (or vice versa)

### What Must Be Preserved

- [ ] All input/output behavior (same inputs → same outputs)
- [ ] Error handling behavior (same errors → same user experience)
- [ ] Edge case handling (null, empty, boundary conditions)
- [ ] Performance characteristics (at least same order of magnitude)
- [ ] Thread safety / concurrency safety
- [ ] Security properties (input validation, output encoding)

### What Should Change

- [ ] Naming conventions (camelCase ↔ snake_case ↔ PascalCase)
- [ ] Code organization (match target language's project structure)
- [ ] Design patterns (use target language's idiomatic patterns)
- [ ] Error handling style (match target language's conventions)
- [ ] Testing style (match target language's test frameworks)

**Present the mapping and wait for approval before converting.**

---

## Step 3: Convert

### Conversion Rules

1. **Convert behavior, not syntax.** Don't transliterate line-by-line. Understand what the source code DOES, then write the TARGET code that DOES THE SAME THING using target-idiomatic patterns.

2. **Use idiomatic patterns.** A Python developer reading your converted Python should not be able to tell it was ported from Java. No `get_x()`/`set_x()` in Python (use properties). No manual iteration in Ruby (use enumerables). No callback hell in modern JavaScript (use async/await).

3. **Match the target ecosystem.** Use the target language's:
   - Standard library (don't reinvent what exists)
   - Popular frameworks and packages (what would a native developer choose?)
   - Project structure conventions
   - Build tools and dependency management
   - Testing frameworks and patterns

4. **Handle the gaps explicitly.** When a source feature has no target equivalent:
   - Document the adaptation and why
   - Ensure the behavior is preserved even if the implementation differs
   - Add comments where the conversion is non-obvious

5. **Preserve tests.** Convert tests alongside code. Tests are the specification — they verify the conversion is correct. If source has no tests, write characterization tests BEFORE converting.

6. **One component at a time.** Don't convert everything at once. Convert one module, verify it works, then move to the next. Each step should be independently verifiable.

---

## Step 4: Verify

### Correctness

- [ ] Same inputs produce same outputs (test with source's test cases)
- [ ] Error cases behave the same (same errors, same messages, same recovery)
- [ ] Edge cases handled (null, empty, boundary, overflow)
- [ ] Concurrency behavior preserved (thread safety, race condition protection)

### Idiomaticness

- [ ] A native developer would recognize this as well-written target code
- [ ] Naming follows target conventions
- [ ] Error handling follows target patterns
- [ ] Project structure follows target standards
- [ ] No source-language patterns leaking through

### Quality

- [ ] Target language linter passes with zero warnings
- [ ] All converted tests pass
- [ ] No unnecessary dependencies introduced
- [ ] Performance is acceptable (benchmark critical paths)

---

## Common Pitfalls

### Numeric Differences
- Integer overflow: wraps in some languages, throws in others, silently promotes in others
- Float precision: `0.1 + 0.2 != 0.3` — but different languages handle this differently
- Integer division: truncates in some, returns float in others

### String Differences
- Encoding: UTF-8 vs UTF-16 vs ASCII — indexing behavior changes
- Immutability: strings immutable in some languages, mutable in others
- Interpolation: completely different syntax

### Collection Differences
- Arrays vs Lists vs Vectors: different performance characteristics
- Maps ordered vs unordered: iteration order varies
- Null in collections: some allow it, some don't

### Equality Differences
- `==` vs `===` vs `.equals()` vs `eq` — value equality vs reference equality
- null/nil comparisons: `null == null` is true in some languages, undefined in others

---

## Hard Rules

1. **NEVER transliterate line-by-line.** This produces "Java written in Python" or "Python written in Go." Convert the BEHAVIOR using target-idiomatic patterns.

2. **NEVER ignore language-specific edge cases.** Integer overflow, null semantics, float precision, string encoding — these DIFFER between languages and cause subtle bugs in converted code.

3. **NEVER skip the tests.** If the source has tests, convert them and run them against the converted code. If it doesn't, write tests BEFORE converting.

4. **NEVER assume equivalent standard library functions behave identically.** `array.sort()` is stable in some languages, unstable in others. `string.split("")` behaves differently. VERIFY equivalence.

5. **NEVER keep source language patterns in target code.** No Java-style getters/setters in Python. No Python-style duck typing in TypeScript. No Go-style error returns in Rust. Use the target language's way.

6. **ALWAYS convert documentation and comments.** Don't leave source-language-specific comments. Update them for the target context.
