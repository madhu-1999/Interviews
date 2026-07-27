A **hashable** object is one that has a __fixed hash value__ which __never changes__ during its entire lifetime.

For an object to be considered hashable, it usually must meet these three conditions
+ **It must provide a hash value** using a specific function (e.g., `__hash__()` in Python).
- **It must support equality comparisons** (e.g., `__eq__()` ), allowing the system to check if two objects are the same.
- **It must be immutable** (unchangeable). If an object's internal data changes, its hash value would also change, breaking its lookup location inside a dictionary or set.