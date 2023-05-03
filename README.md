Download Link: https://assignmentchef.com/product/solved-cs3210-lab-5-distributed-memory-programming-using-mpi
<br>
Lab 4 provided you with basic knowledge on MPI programming. This lab aims to provide a more detailed coverage of MPI libraries calls.

Part 1: Collective Communication

MPI provides collective communication functions which must involve all processes in the scope of a communicator. By default, all processes are members in the communicator MPI_COMM_WORLD.

<sup></sup><strong>Important:</strong>It is the programmer’s responsibility to ensure that all processes within a communicator participate in any collective operations! Failure to do so will deadlock the program. There are three types of collective communication:

<ol>

 <li>Synchronization communication</li>

 <li>Data movement operations</li>

 <li>Collective computations (data movement with reduction operations)</li>

</ol>

<h2>Part 1.1: Synchronization Communication</h2>

There is only one collective synchronization operation in MPI:

————————————————————————-

int————————————————————————-MPI_Barrier(MPI_Comm comm) All processes from MPI communicator comm will block until all of them reach the barrier. Failure to call this function from all the processes will result in deadlock.

<h2>Part 1.2: Data Movement Operations</h2>

The data-movement operations provided by MPI are:

1

/*MPI_Bcast – broadcasts (sends) a message from the process with rank root to all other processes in the group.*/

int MPI_Bcast(void *buffer, int count, MPI_Datatype datatype, int root, MPI_Comm comm)

————————————————————————/*MPI_Scatter – sends data from one process to all processes in a communicator.*/

int MPI_Scatter(void *sendbuf, int sendcount, MPI_Datatype sendtype, void *recvbuf, int recvcount, MPI_Datatype recvtype,

int root, MPI_Comm comm)

————————————————————————-

/*MPI_Gather – gathers data from a group of processes into one root

process.*/

int MPI_Gather(void *sendbuf, int sendcount, MPI_Datatype sendtype, void *recvbuf, int recvcount, MPI_Datatype recvtype, int root, MPI_Comm comm)

————————————————————————/*MPI_Allgather – gathers data from a group of processes into each of the process from that group.*/

int MPI_Allgather(void *sendbuf, int sendcount, MPI_Datatype sendtype, void *recvbuf, int recvcount,

MPI_Datatype recvtype, MPI_Comm comm)

————————————————————————-

————————————————————————/*MPI_Alltoall – each process in a group performs a scatter operation, sending a distinct message to all the processes in the group in order by index.*/

<sup></sup>int MPI_Alltoall(void *sendbuf, int sendcount,

MPI_Datatype sendtype, void *recvbuf, int recvcount,

MPI_Datatype recvtype, MPI_Comm comm)

————————————————————————MPI-3 standard introduced Nonblocking Collective Data Movement operations such as MPI_Iscatter. Check the mpi manual to learn more.

<h2>Part 1.3: Collective Computations</h2>

The MPI functions that achieve collective computations are:

/*MPI_Reduce – reduces values on all processes within a group.

The reduction operation must be one of the following:

MPI_MAX maximum | MPI_MIN minimum | MPI_SUM sum, MPI_PROD product | MPI_LAND logical AND | MPI_BAND bit-wise AND | MPI_LOR logical OR |

MPI_BOR bit-wise OR | MPI_LXOR logical XOR | MPI_BXOR bit-wise XOR */

int MPI_Reduce(void *sendbuf, void *recvbuf, int count,

MPI_Datatype datatype, MPI_Op op, int root, MPI_Comm comm)

————————————————————————/*MPI_Allreduce – applies a reduction operation and places the result in all processes in the communicator. This is equivalent to an MPI_Reduce

followed by an MPI_Bcast.*/

int MPI_Allreduce(void *sendbuf, void *recvbuf, int count,

MPI_Datatype datatype, MPI_Op op, MPI_Comm comm)

————————————————————————/*MPI_Reduce_scatter – first, it does an element-wise reduction on a vector across all processes in the group. Next, the result vector is split into disjoint segments and distributed across the processes. This is equivalent to an MPI_Reduce followed by an MPI_Scatter operation.*/

int MPI_Reduce_scatter(void *sendbuf, void *recvbuf, int *recvcounts,

MPI_Datatype datatype, MPI_Op op, MPI_Comm comm)

————————————————————————-

<table width="601">

 <tbody>

  <tr>

   <td width="61"></td>

   <td width="540"><strong><u>Exercise 1</u></strong>Compile the program col_comm.c and run it. Explore the code and output to understand how Scatter works in this example. What can we do if we want to scatter the data only in a subset of the processes of the MPI communicator?</td>

  </tr>

 </tbody>

</table>

<h1>Part 2: Managing Communicators</h1>

One of the major disadvantages of using collective communications is that we must use all the processes in the MPI communicator. To overcome this, MPI allows us to create custom communicators, add / remove processes to / from communicator and destroy communicators.

The MPI functions for communicator management are:

/*MPI_Comm_group – returns the group associated with a communicator.*/

int MPI_Comm_group(MPI_Comm comm, MPI_Group *group)

————————————————————————/*MPI_Group_incl – produces a group by reordering an existing group and taking only listed members.*/

int MPI_Comm_group(MPI_Comm comm, MPI_Group *group)

————————————————————————-

/*MPI_Comm_create – creates a new communicator with a group of

processes.*/

int MPI_Comm_create(MPI_Comm comm, MPI_Group group, MPI_Comm *newcomm) ————————————————————————-

/*MPI_Group_rank – returns the rank of the calling process in the given group.*/

int MPI_Group_rank(MPI_Group group, int *rank)

————————————————————————-

<table width="601">

 <tbody>

  <tr>

   <td width="61"></td>

   <td width="540"><strong><u>Exercise 2</u></strong>Compile the program new_comm.c and run it. What does the program do?</td>

  </tr>

 </tbody>

</table>

<h1>Part 3: Cartesian Virtual Topologies</h1>

In MPI context, a virtual topology describes a mapping/ordering of MPI processes into a geometric space. Generally, there are two main types of topologies supported by MPI, namely, (i) Cartesian and (ii) Graph. In this lab we are only studying Cartesian virtual topology. Often the MPI processes access data in a regular structured pattern in Cartesian space. In those case, it is useful to arrange the logical MPI processes into a Cartesian virtual topology to facilitate coding and communication. Remember that there may be no relation between the physical structure of the parallel machines and the MPI virtual topology, and, the MPI topology must be programmed and managed by the programmer.

MPI has three functions to help us manage a Cartesian topology:

/*MPI_Cart_create – makes a new communicator to which Cartesian topology information has been attached.*/

int MPI_Cart_create(MPI_Comm comm_old, int ndims, int *dims,

int *periods, int reorder, MPI_Comm *comm_cart)

————————————————————————/*MPI_Cart_coords – determines process coordinates in the Cartesian topology, given its rank in the group.*/



int MPI_Cart_coords(MPI_Comm comm, int rank, int maxdims, int *coords) ————————————————————————/*MPI_Cart_shift – returns the shifted source and destination ranks, given a shift direction and amount.     */

int MPI_Cart_shift(MPI_Comm comm, int direction, int disp, int *rank_source, int *rank_dest)

————————————————————————-

<table width="601">

 <tbody>

  <tr>

   <td width="60"></td>

   <td width="541"><strong><u>Exercise 3</u></strong>cart.c is an example implementation of a Cartesian topology. Study the code and understand how it works. Compile program and run it. What does the program do?</td>

  </tr>

 </tbody>

</table>

More information and further reading:



<ul>

 <li>LLNL MPI Tutorial: <a href="https://computing.llnl.gov/tutorials/mpi/">https://computing.llnl.gov/tutorials/mpi/</a></li>

 <li>OpenMPI FAQ: <a href="https://www.open-mpi.org/faq/?category=running">https://www.open-mpi.org/faq/?category=running</a></li>

 <li>Another MPI Tutorial: <a href="http://mpitutorial.com/">http://mpitutorial.com/</a></li>

 <li>Short Video on Cartesian Topology: <a href="https://youtu.be/dyVOdlC0y7w">https://youtu.be/dyVOdlC0y7w</a></li>

 <li>Advanced Parallel Programming with MPI: <a href="https://htor.inf.ethz.ch/teaching/mpi_tutorials/ppopp13/2013-02-24-ppopp-mpi-advanced.pdf">https://htor.inf.ethz.ch/teaching/ </a><a href="https://htor.inf.ethz.ch/teaching/mpi_tutorials/ppopp13/2013-02-24-ppopp-mpi-advanced.pdf">mpi_tutorials/ppopp13/2013-02-24-ppopp-mpi-advanced.pdf</a></li>

</ul>