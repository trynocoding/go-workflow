# Modern Go Syntax — Review Reference

Flag outdated patterns as **🔵 Minor / Idiomatic** during code review.
**Only flag patterns from Go versions at or below the project's detected version.**

---

## Go 1.13+

| Outdated | Modern | Flag message |
|---|---|---|
| `err == target` | `errors.Is(err, target)` | Use `errors.Is` — direct comparison breaks with wrapped errors |
| `err.(*T)` for type check | `errors.As(err, &target)` | Use `errors.As` — works through error wrapping chains |

---

## Go 1.18+

| Outdated | Modern | Flag message |
|---|---|---|
| `interface{}` | `any` | Use `any` — it's an alias for `interface{}` and is the modern convention |
| `bytes.Index` + slice to split | `bytes.Cut(b, sep)` | Use `bytes.Cut` — cleaner split into before/after/found |
| `strings.Index` + slice to split | `strings.Cut(s, sep)` | Use `strings.Cut` — cleaner split into before/after/found |

---

## Go 1.19+

| Outdated | Modern | Flag message |
|---|---|---|
| `[]byte(fmt.Sprintf(...))` | `fmt.Appendf(buf, ...)` | Use `fmt.Appendf` — avoids intermediate string allocation |
| `atomic.StoreInt32` / `atomic.LoadInt32` | `atomic.Bool` / `atomic.Int64` / `atomic.Pointer[T]` | Use typed atomics — type-safe and less error-prone |

---

## Go 1.20+

| Outdated | Modern | Flag message |
|---|---|---|
| `strings.HasPrefix` + slice | `strings.CutPrefix(s, "pre:")` | Use `strings.CutPrefix` — returns remainder and found bool |
| `strings.HasSuffix` + slice | `strings.CutSuffix(s, "suf")` | Use `strings.CutSuffix` — cleaner |
| `append(err1.Error(), ...)` style multi-error | `errors.Join(err1, err2)` | Use `errors.Join` — standard multi-error combination |
| `context.WithCancel` for cancellation with cause | `context.WithCancelCause(parent)` | Use `WithCancelCause` when callers need to know why ctx was cancelled |

---

## Go 1.21+

| Outdated | Modern | Flag message |
|---|---|---|
| `if a > b { x = a } else { x = b }` | `x = max(a, b)` | Use built-in `min`/`max` |
| Manual loop to find element in slice | `slices.Contains` / `slices.Index` / `slices.IndexFunc` | Use `slices` package |
| `sort.Slice` with less func | `slices.SortFunc(s, cmp.Compare)` | Use `slices.SortFunc` — type-safe, no reflection |
| Manual map iteration to clone | `maps.Clone(m)` | Use `maps.Clone` |
| `sync.Once` + wrapper function pattern | `sync.OnceFunc` / `sync.OnceValue` | Use `sync.OnceFunc` / `sync.OnceValue` — less boilerplate |
| `delete` loop to clear map | `clear(m)` | Use built-in `clear` |

---

## Go 1.22+

| Outdated | Modern | Flag message |
|---|---|---|
| `for i := 0; i < len(s); i++` | `for i := range len(s)` | Use `for i := range n` — more idiomatic |
| `for i := 0; i < n; i++` | `for i := range n` | Use `for i := range n` |
| Loop variable captured in goroutine with explicit copy | (no change needed) | In Go 1.22+, loop variables are per-iteration — explicit copy is no longer needed |
| `flag \|\| env \|\| config` chain with fallback | `cmp.Or(flag, env, config, "default")` | Use `cmp.Or` — returns first non-zero value |
| `reflect.TypeOf((*T)(nil)).Elem()` | `reflect.TypeFor[T]()` | Use `reflect.TypeFor` — cleaner generic type lookup |

---

## Go 1.23+

| Outdated | Modern | Flag message |
|---|---|---|
| `for k := range m { keys = append(keys, k) }` | `slices.Collect(maps.Keys(m))` | Use `slices.Collect` + `maps.Keys` iterator |
| Manual collect + sort of map keys | `slices.Sorted(maps.Keys(m))` | Use `slices.Sorted` — collect and sort in one step |
| `ticker := time.NewTicker(d); defer ticker.Stop()` when stop isn't needed | `time.Tick(d)` | In Go 1.23+, `time.Tick` is GC-safe — `NewTicker` only needed when explicit `Stop` is required |

---

## Go 1.24+

| Outdated | Modern | Flag message |
|---|---|---|
| `ctx, cancel := context.WithCancel(context.Background())` in tests | `ctx := t.Context()` | Use `t.Context()` in tests — automatically cancelled when test ends |
| `omitempty` on `time.Duration`, `time.Time`, structs, slices, maps in JSON tags | `omitzero` | Use `omitzero` — `omitempty` is unreliable for these types |
| `for i := 0; i < b.N; i++` in benchmarks | `for b.Loop()` | Use `b.Loop()` — avoids common benchmark setup/teardown mistakes |
| `for _, part := range strings.Split(s, sep)` | `for part := range strings.SplitSeq(s, sep)` | Use `strings.SplitSeq` — avoids intermediate slice allocation when iterating |
| `strings.Fields` + range | `strings.FieldsSeq` | Use `strings.FieldsSeq` — iterator form, no intermediate slice |
| `bytes.Split` + range | `bytes.SplitSeq` | Use `bytes.SplitSeq` |

---

## Go 1.25+

| Outdated | Modern | Flag message |
|---|---|---|
| `wg.Add(1); go func() { defer wg.Done(); ... }()` | `wg.Go(func() { ... })` | Use `wg.Go` — eliminates the Add/Done boilerplate |

---

## Go 1.26+

| Outdated | Modern | Flag message |
|---|---|---|
| `x := val; &x` to get pointer | `new(val)` | Use `new(val)` — Go 1.26 extends `new` to accept expressions |
| `var target *T; errors.As(err, &target)` | `errors.AsType[*T](err)` | Use `errors.AsType` — cleaner, no pre-declaration needed |
