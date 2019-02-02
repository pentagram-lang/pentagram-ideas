# Tacit ideas / Plans / Closures

## Reference copies

If Tacit can prove that a closure is completed before the stack goes down, then the closure is allowed to reference stack data without copying. If not, all referenced stack data must be copied into the closure

- Running `map` (light closure)
- Running an async coroutine and waiting for its result (light closure)
- Spawning a completely independent coroutine (full closure)
