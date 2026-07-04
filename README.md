# System_Design_Overvieew


1 - [CAP Theorem](#cap-theorem-section)


<a name="cap-theorem-section"><a/>
# 1 - CAP Theorem

<img width="2048" height="1933" alt="image" src="https://github.com/user-attachments/assets/a1d80d2e-48a5-43e6-9e76-96b8b373ee94" />

The CAP theorem is a fundamental rule in system design. It states that a distributed data store (a system made of multiple machines working together) can only simultaneously provide two out of three guarantees: Consistency, Availability, and Partition Tolerance.

Here is what those three letters actually mean in plain English:

- Consistency (C): All nodes display identical data, guaranteeing that reads always reflect the most recent write.
Every read receives the most recent write or an error. Imagine updating your profile picture; if the system is consistent, anyone checking your profile a millisecond later will see the new picture, not the old one.

- Availability (A): Every request receives a response, without guarantee that it contains the most recent write. Every non-failing node returns a non-error response, without the guarantee that it contains the most recent write. The system is always up and responsive, even if it occasionally serves stale data.

- Partition Tolerance (P): The system continues to operate despite network partitions.
The system continues to operate despite an arbitrary number of messages being dropped or delayed by the network between nodes.

<img width="800" height="487" alt="image" src="https://github.com/user-attachments/assets/76ab1927-d556-41fd-8e26-8842661de877" />
