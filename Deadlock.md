If two or more processes are waiting on an event which never happens, there exists a deadlock.

# Conditions
If all conditions are satisfied, system is in deadlock
## Mutual Exclusion
Shared resources must be used in mutual exclusive way i.e. if one process is using shared resource, other processes must wait till current process finishes using the resource.
## No preemption
If process P1 is holding a resource R1 and process P2 is holding resource R2, assume P1 wants R2 and P2 wants R1 i.e. both processes are holding onto resource the other wants.
Then no-preemption means that resources cannot be forcibly taken from processes i.e. **no lower priority thread yielding to higher priority thread**.
## Hold and wait
If process P1 is holding a resource R1 and process P2 is holding resource R2, assume P1 wants R2 and P2 wants R1 i.e. both processes are holding onto resource the other wants.
Then hold and wait means that **process holds onto resource while waiting** for other resource instead of releasing resources held since it cannot get them all.
## Circular wait
If process P1 is holding a resource R1 and process P2 is holding resource R2, assume P1 wants R2 and P2 wants R1 i.e. both processes are holding onto resource the other wants, this is circular wait.
