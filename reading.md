# The Linux programming interface

```
  Major shells provide a job control feature in which all processes in a pipeline
  are placed in a __process group__ or __job__.
  A process group has a process group identifier (PGI) which is the PID of the
  process group leader (one of the processes)

  __session__ - a collection of process groups, all processes in a session have
  the same __session identifier__. A __session leader__ is the process that created
  the session and its PID is the session ID.

```
