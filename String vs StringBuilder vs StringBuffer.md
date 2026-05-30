
| Feature           | String                               | StringBuffer                       | StringBuilder                 |
| ----------------- | ------------------------------------ | ---------------------------------- | ----------------------------- |
| Mutability        | Immutable                            | Mutable                            | Mutable                       |
| Thread Safety     | Thread safe due to immutability      | Thread safe due to synchronization | Not thread safe               |
| Memory Efficiency | High                                 | Least efficient                    | Efficient                     |
| Performance       | Slowest due to new memory allocation | Slow due to synchronization        | High since no synchronization |
| Storage Area      | String Pool                          | Heap                               | Heap                          |
