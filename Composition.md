
+ Object B as a field/property of A. A has B.
+ **has-a** relationship.
+ In composition, both entities are dependent on each other.
+ The composed object (B)  **cannot exist independently** without the composer object (A).
+ Ex: A house has rooms. A room cannot exist without a house, because even 1 room is a house.

# Composition over Inheritance

[Video on this topic](https://www.youtube.com/watch?v=y9CMO83hvpw&ab_channel=LiviuOprisan)

| Criteria          | Inheritance                                          | Composition                                       |
| ----------------- | ---------------------------------------------------- | ------------------------------------------------- |
| Relationship type | IS-A                                                 | HAS-A                                             |
| Code Reusability  | High, but tightly coupled                            | High, but loosely coupled                         |
| Flexible          | Low                                                  | High                                              |
| Encapsulation     | Less (child exposed to parent details)               | More (internal details hidden)                    |
| Use Case          | When subclass is truly specialized version of parent | When class needs functionality without inheriting |
