# Code Optimization Guide

A practical guide for identifying and improving slow or inefficient code patterns.

## Table of Contents

1. [Identifying Performance Issues](#identifying-performance-issues)
2. [Common Inefficiency Patterns](#common-inefficiency-patterns)
3. [Optimization Techniques](#optimization-techniques)
4. [Profiling Tools](#profiling-tools)
5. [Best Practices](#best-practices)

---

## Identifying Performance Issues

### Signs of Slow Code

- Long response times in API calls
- High CPU or memory usage
- Slow page load times
- Database query timeouts
- Application hangs or freezes

### Measurement First

Before optimizing, always measure:

1. **Profile your code** to find actual bottlenecks
2. **Use benchmarks** to establish baselines
3. **Monitor in production** with APM tools
4. **Set performance budgets** for critical paths

---

## Common Inefficiency Patterns

### 1. Inefficient Loops

**Problem:**
```python
# O(n²) - nested loops on large datasets
for item in large_list:
    if item in another_large_list:  # O(n) lookup each time
        process(item)
```

**Solution:**
```python
# O(n) - use a set for O(1) lookups
another_set = set(another_large_list)
for item in large_list:
    if item in another_set:
        process(item)
```

### 2. N+1 Query Problem

**Problem:**
```python
# Makes N+1 database queries
users = User.query.all()
for user in users:
    print(user.orders)  # Lazy loads orders for each user
```

**Solution:**
```python
# Single query with eager loading
users = User.query.options(joinedload(User.orders)).all()
for user in users:
    print(user.orders)
```

### 3. String Concatenation in Loops

**Problem:**
```python
# Creates new string object each iteration
result = ""
for word in words:
    result += word + " "
```

**Solution:**
```python
# Use join for efficient string building
result = " ".join(words)
```

### 4. Unnecessary Object Creation

**Problem:**
```java
// Creates new object in every loop iteration
for (int i = 0; i < 1000; i++) {
    DateTimeFormatter df = DateTimeFormatter.ofPattern("yyyy-MM-dd");
    process(df.format(date));
}
```

**Solution:**
```java
// Reuse object instance (DateTimeFormatter is thread-safe and reusable)
DateTimeFormatter df = DateTimeFormatter.ofPattern("yyyy-MM-dd");
for (int i = 0; i < 1000; i++) {
    process(df.format(date));
}
```

### 5. Synchronous Operations That Could Be Async

**Problem:**
```javascript
// Sequential API calls
const user = await fetchUser(id);
const orders = await fetchOrders(id);
const preferences = await fetchPreferences(id);
```

**Solution:**
```javascript
// Parallel API calls
const [user, orders, preferences] = await Promise.all([
    fetchUser(id),
    fetchOrders(id),
    fetchPreferences(id)
]);
```

---

## Optimization Techniques

### Algorithm Optimization

| Original | Optimized | Improvement |
|----------|-----------|-------------|
| Linear search O(n) | Binary search O(log n) | Use sorted data |
| Bubble sort O(n²) | Quick/Merge sort O(n log n) | Better algorithms |
| Recursive Fibonacci | Memoized/Iterative | Avoid redundant work |

### Caching Strategies

- **In-memory caching** for frequently accessed data
- **Redis/memcached** for distributed caching
- **HTTP caching** with proper headers
- **Database query caching** for expensive queries

### Database Optimization

- Add appropriate indexes
- Avoid SELECT * - specify needed columns
- Use EXPLAIN to analyze query plans
- Batch inserts/updates when possible
- Consider denormalization for read-heavy workloads

### Memory Management

- Release unused resources promptly
- Use streaming for large files
- Implement pagination for large datasets
- Consider object pooling for expensive objects

---

## Profiling Tools

### By Language

| Language | Profiling Tools |
|----------|-----------------|
| Python | cProfile, py-spy, memory_profiler |
| JavaScript | Chrome DevTools, Node.js --inspect, clinic.js |
| Java | JProfiler, VisualVM, async-profiler |
| Go | pprof, trace |
| .NET | dotTrace, PerfView |

### Application Performance Monitoring (APM)

- **New Relic** - Full-stack observability
- **Datadog** - Infrastructure and application monitoring
- **Dynatrace** - AI-powered performance management
- **Prometheus + Grafana** - Open-source monitoring

---

## Best Practices

### Do's

✅ Profile before optimizing  
✅ Focus on the critical path  
✅ Write benchmarks for comparisons  
✅ Use appropriate data structures  
✅ Cache expensive computations  
✅ Optimize database queries  
✅ Consider async/parallel processing  
✅ Monitor production performance  

### Don'ts

❌ Premature optimization  
❌ Optimizing without measuring  
❌ Sacrificing readability for marginal gains  
❌ Ignoring algorithmic complexity  
❌ Over-caching (stale data issues)  
❌ Blocking I/O in critical paths  

---

## Quick Reference Checklist

When reviewing code for performance:

- [ ] Are there nested loops that could be flattened?
- [ ] Are we using appropriate data structures (sets, maps)?
- [ ] Are database queries efficient (N+1 problem)?
- [ ] Can operations be parallelized?
- [ ] Is caching being used appropriately?
- [ ] Are resources being released properly?
- [ ] Have we profiled to find the actual bottleneck?

---

## Resources

- [Big O Cheat Sheet](https://www.bigocheatsheet.com/)
- [Web Performance Best Practices](https://web.dev/performance/)
- [Database Indexing Explained](https://use-the-index-luke.com/)

---

*Remember: "Make it work, make it right, make it fast" - Kent Beck*
