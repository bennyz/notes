# The Linux programming interface

## jobs
  Major shells provide a job control feature in which all processes in a pipeline
  are placed in a __process group__ or __job__.
  A process group has a process group identifier (PGI) which is the PID of the
  process group leader (one of the processes)

  __session__ - a collection of process groups, all processes in a session have
  the same __session identifier__. A __session leader__ is the process that created
  the session and its PID is the session ID.


---

# Data Structures
## SSTable
Hash table indexes have the limitation of ranged queries. If we want to fetch all keys between kitty0000 and kityy9999, each key
will have to be looked up individually.

__Sorted String Table__


