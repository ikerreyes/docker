
Docker details
==============


Docker uses **namespaces** for networking, root filesystem, users, pids,...;
but containers share the kernel with the host OS.
Namespaces make that the processes in the namespace have their own isolated instance of global resources
as PIDs (each container has its own PID 1), net (each container has its own network stack),
mnt (each container has its own view of the filesystem hierarchy),
ipc (each container has its own interprocess communication),
uts (each container has its own hostname and domainname) and
user (each container has its own user and group ID number spaces).


**Cgroups** are used for resource isolation. They organize processes and distribute system resources.
Cgoups are: blkio, cpu, cpuacct, cpuset, devices, freezer and memory.


Docker uses the `Union filesystem <https://en.wikipedia.org/wiki/UnionFS>`_
to allow allows files and directories of separate filesystems
(*layers*)  to be transparently overlaid on each other, to create a new virtual filesystem.
Docker images have multiple read-only layers and one final
read-write layer that only last as long as the container does.

To store data persistently in the host system, **bind mounts**
allow to mount any file or directory into a container, and
**docker volumes** are directly managed by Docker
(under :dir:`/var/lib/docker/volumes`).


.. glossary::

   Image
     application, dependencies and user-space libraries

   Container
     running instance of an image

