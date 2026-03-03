---
name: go-code-review
description: Use when reviewing Go code changes — checks idiomatic Go patterns, error handling, concurrency safety, interface design, and performance. Works across any Go project type.
license: MIT
compatibility: claude-code, opencode
metadata:
  workflow: code-review
  language: go
---

# Go Code Review

## Review Priorities (in order)

1. **Correctness** — Does it do what it claims?
2. **Error handling** — Are all errors handled explicitly?
3. **Concurrency safety** — Any races, deadlocks, or goroutine leaks?
4. **Idiomatic Go** — Does it follow Go conventions?
5. **Performance** — Any unnecessary allocations or blocking calls?
6. **Maintainability** — Is it clear, testable, minimal?

## Critical Checks

### Error Handling

```go
// BAD: ignored error
result, _ := someFunc()

// BAD: generic wrapping loses context
return fmt.Errorf("error: %v", err)

// GOOD: wrap with context using %w
return fmt.Errorf("loading config from %s: %w", path, err)

// GOOD: sentinel errors for callers to check
var ErrNotFound = errors.New("not found")
if errors.Is(err, ErrNotFound) { ... }
```

Flag:
- Any `_` discarding an `error`
- `fmt.Errorf` without `%w` when callers might need to unwrap
- Returning `nil` error after logging (swallowed errors)
- `panic` in non-main code without documented recovery contract

### Concurrency

```go
// BAD: unsynchronized map write from goroutine
go func() { m[key] = val }()

// BAD: goroutine leak — no way to stop it
go func() {
    for { process() }
}()

// GOOD: respect context cancellation
go func() {
    for {
        select {
        case <-ctx.Done():
            return
        default:
            process()
        }
    }
}()
```

Flag:
- Goroutines without a stop mechanism (`ctx.Done()`, channel, WaitGroup)
- `sync.Mutex` used when `sync.RWMutex` would be better for read-heavy paths
- `sync.Map` used when a regular map + mutex is clearer
- Channel operations without `select` + timeout/cancellation
- Closing a channel from the reader side

### Interface Design

```go
// BAD: fat interface, hard to mock
type Store interface {
    GetUser(id string) (*User, error)
    ListUsers() ([]*User, error)
    CreateUser(u *User) error
    DeleteUser(id string) error
    UpdateUser(u *User) error
    // ... 20 more methods
}

// GOOD: small, focused interfaces at point of use
type UserGetter interface {
    GetUser(id string) (*User, error)
}
```

Flag:
- Interfaces with >5 methods (unless it's a well-known external interface)
- Interfaces defined in the same package as their implementation (usually wrong direction)
- Accepting concrete structs where an interface would decouple better
- Returning interfaces instead of concrete types from constructors

### Context Propagation

```go
// BAD: context created mid-call, ignores parent
func (s *Service) DoWork() error {
    ctx := context.Background() // loses cancellation chain
    return s.db.Query(ctx, ...)
}

// GOOD: context flows from caller
func (s *Service) DoWork(ctx context.Context) error {
    return s.db.Query(ctx, ...)
}
```

Flag:
- `context.Background()` or `context.TODO()` in non-main code (outside tests)
- Context not passed to I/O operations (HTTP, DB, gRPC calls)
- Context stored in structs (should be passed per call)

### Resource Management

```go
// BAD: resource leak on error path
resp, err := http.Get(url)
body, _ := io.ReadAll(resp.Body)
// forgot defer resp.Body.Close()

// GOOD
resp, err := http.Get(url)
if err != nil { return err }
defer resp.Body.Close()
```

Flag:
- Missing `defer` for `Close()`, `Unlock()`, `Done()`
- `defer` inside a loop (use a helper function instead)
- File/connection opens without corresponding close in same scope

## Idiomatic Go Patterns

### Named Return Values

Use sparingly. Only when they clarify a complex function signature:
```go
// OK for clarity
func divide(a, b float64) (result float64, err error) {
// BAD: named returns + defer modification is confusing
```

### Slice/Map Initialization

```go
// GOOD: pre-allocate when size is known
items := make([]Item, 0, len(input))

// BAD: repeated append without capacity hint on large slices
var items []Item
for _, v := range input {
    items = append(items, process(v))
}
```

### Struct Tags

```go
// Check json tags match actual field intent
type Config struct {
    Timeout   int    `json:"timeout_ms"`          // clear unit
    Debug     bool   `json:"debug,omitempty"`      // correct omitempty usage
    Internal  string `json:"-"`                    // correctly excluded
}
```

## Output Format

Report findings grouped by severity:

```
## Code Review

### 🔴 Critical (must fix)
- [file:line] Description
  Risk: what can go wrong
  Fix: concrete suggestion

### 🟡 Important (should fix)
- [file:line] Description
  Suggestion: ...

### 🔵 Minor / Idiomatic
- [file:line] Description
  Suggestion: ...

### ✅ Good patterns noticed
- [file:line] Worth noting as good practice

### Summary
- Critical: N | Important: N | Minor: N
- Overall: Ready to merge / Needs fixes / Major revision needed
```

## Red Flags (automatic Critical)

- Any use of `unsafe` package without comment explaining why
- `os.Exit()` called outside of `main()`
- `log.Fatal` / `log.Panic` in library code
- Unbounded goroutine creation in a loop
- SQL/command injection via string concatenation
- Hardcoded credentials or secrets
