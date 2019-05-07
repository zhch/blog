### Buffer Pool

Programs in a database system make requests (that is, calls) on the buffer manager when they need a block from disk:

- If the block is already in the buffer, the buffer manager passes the address of the block in main memory to the requester.

- If the block is not in the buffer, the buffer manager first allocates space in the buffer for the block, throwing out some other block, if necessary, to make space for the new block. The thrown-out block is written back to disk only if it has been modified since the most recent time that it waswritten to the disk. Then, the buffer manager reads in the requested block from the disk to the buffer, and passes the address of the block in main memory to the requester. 

  **The internal actions of the buffer manager are transparent to the programs that issue disk-block requests.**

