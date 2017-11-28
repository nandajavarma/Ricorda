18/9, Monday

Homepage: https://beep.metid.polimi.it/web/486378/home

Books: http://dbbook.dia.uniroma3.it/

DBMS: Large, persistent, shared, reliable, private storages of data.

* Transactional systems.
    * Eg: Telephone
    * Transaction is an atomic unit of work. Example a bank transaction
    * The initial state and the final state of a transaction would be in
      consistency space. That is, a constraint should be satisfied. Eg:
      Sum of percentages = 100. The states in between can go out of the
      consistency space.
    * Commit work -> Success
    * Rollback -> Undo
    * Fault after commit -> Redo (But redo what? Redo from the final
      state?)

    * ACID properties
        * Atomicity
        * Consistency
        * Isolation
        * Durability
      Domino effect is when rollback of one of the transactional units
      rollback the otherones as well.

    * Concurrency control for isolation
        Our goal while designing a DBMS is to decrease the transaction
        per second(Density of execution)

        Transactions can happen in several ways:
            * Serial execution
            * Nested execution
            * Interleaved execution

            Update operation is when there is a modification to the
            initial state

        * Major concurrency problems are:
            * Data loss - r1, r2, w2, w1
            * Dirty read - r1, w1, r2, abort1, w2
            * Non repeatable read - r1, r2, w2, r1
            * Phantom read - r1, r2, w2, r1 reads happening too early
            * Phantom insert - r1, w2, r1

        * The number of ways a transaction with n operations can happen in a
          serial execution is n!
19/09, Tuesday

    * Number of distinct schedules of n transactions with ki operations each
      is: (k1+k2+...ki)!/k1!.k2!.k3!......ki!
    * The order of operations should be maintained in the same
      transaction, hence the denominator
    * The scheduler is the part of the DBMS that accepts or rejects an
      operation request
    * *serializable schedule* - A schedule that produce the same result
      as any serial schedule
    * We analyze schedules once it is all done, although in reality this
      is not the case
    * All schedules *contains* serializable *contains* serial

    * Equivalencies

        * View serializabilty
           We say two schedules are view equivalent iff:
            1. Same operations
            2. Same reads from. i. e. Same read operations reads a
               resource that was affected by the same write operation
            3. Same final writes.

           So if two schedules are view equivalent then that class of
           schedules are called view serializable (VSR)

           Eg:
            s3: w0(x) r2(x) r1(x) w2(x) w2(z)
                w0(x) r1(x) r2(x) w2(x) w2(z)


        * Conflict serializability
        * 2PL
        La merda swap files!!!

      Locks:
            Locks are used by implementing 2PL technique which is less
            costly compared to VSR and CSR but less accurate, of course.
            The main problems with locks are of course:
            * deadlock
            * starvation

            So to reduce the effect of this, we set isolation levels.
            writes are always strict 2pl to avoid update loss.
            * read uncommitted: no read lock
            * read committed: prevents dirty read, but nothing for non
              repeatable reads or phantoms
            * repeatable read: only allows phantom insert
            * serializable: 2PL with predicate locks. Locks on a
              predicate. for eg on A=1 in case of the phantom insert


            Deadlock
            --------


            If you draw a wait for graph, then there will be a cycle if
            there is a deadlock. This is how you detect a deadlock


            How to resolve deadlock:
            1. Timeout: this is the easiest. Set a timeout value and
               kill the transaction if it exceeds. But the problem is
               starvation.
            2. Deadlock prevention: Transaction killed when there could
               be a deadlock
               * resource based: all transaction request resources all
                 at once. And they ask it in a global order. Sort the
                 resources alphabetically, so no two transactions can
                 have conflicting waits.
               * transaction based: Give IDs to the transactions. Will
                 not let old transactions to wait for the new ones. can
                 kill the waiting transaction(pre-emptive) or the
                 requesting one(non pre-emptive)
                 --> too many killings, yo!

            3. Deadlock detection
               Transactions killed when they are in a deadlock. The
               deadlocks are detected using the wait for graph. In the
               local system this is easy, but when it comes to a
               distributed system this becomes very difficult. So we
               have Obermark's algorithm

               Obermark's algorithm
               --------------------


               We have to prevent a global deadlock. We also have to
               prevent all transactions being killed(overkill, heh!) by
               the nodes. So the Nodes should exchange messages.
               Messages are sent only in one order. ninjnk.. where
               i<j<k. So there won't be a circular message passing. All
               the messages will accumulate on the last machine. So
               efficient kiling.

               The implementation is run periodically on all nodes.
               There will be a local wait for graph. Get potential
               deadlocks from previous nodes. Search for deadlocks, if
               found it will kill one transaction. send updated
               potential deadlock to next node.

               Eg: refer slide for the diagram
               Node a: eb(external from b) -> t1 -> t2  ->
               eb(external to b) sent to node b
               at node be we already have: ea -> t2 -> t1 -> ea

               so when node b receives the message:
               it updates the wait for graph
               there forms a cycle. so deadlock detected. t1 or t2 is
               killed(rollbacked)

               it doesn't matter if i>j or i<j is the order

               it can send it to the following node or the preceesing
               node.


               Update lock:

               This can reduce the deadlock frequency. If there is an
               operation which is doing a read and then a write, it
               taking the exclusive lock from the beginning is resource
               consuming. But just keeping it shared and elevating to
               exclusive can cause deadlocks.

               So we have the concept of an update lock. In systems with
               this mechanism, read locks can never be elevated. But an
               update lock is a shared lock that can be elevated. So it
               would probably mean that, when an update lock is waiting
               for elevation, it is for sure that all the locks will be
               released at somepoint because those are just read locks.



           * Heirarchical locking:
                In several systems locks are designed in several
                granularities. It improved the efficiency of a big dbms.
                And might or might not decrease the deadlocks because,
                the locks now are upon very small piece of info. So
                multiple operations asking for that tiny one can be a
                small amount, let us hope?

                So levels of granularity: file ->  page -> tuple ->
                attribute(value)


                Objectives: Setting the minimum number of lockings,
                detecting conflicts asap.

                Request of resource happen in the top down approach,
                until the right level of granularity is reached. Release
                happens the other way around.


           * Enhanced locking schemes
                More locks in addition of SL and XL:
                1. ISL -> intention of locking a subelement in shared
                   mode
                2. IXL -> intention of locking a subelement in x mode
                3. SIXL -> SL + IXL: Lock the element in shared mode, in
                   addition intention of locking a subelement in x mode


                   to issue a lock on non element of a dbms:
                   * for SL or ISL - should issue ISL or IXL on the root
                   * for XL or SIXL or IXL -> IXL or SIXL on root
                   read the example of lock sequence from the slide.
