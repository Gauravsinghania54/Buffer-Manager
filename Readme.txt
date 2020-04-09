-Buffer Manager-

Project Modules
C source files: dberror.c, test_assign2_1.c,buffer_mgr_stat.c,test_assign2_1.c Header files: storage_mgr.h, dberror.h, test_helper.h, buffer_mgr.h, buffer_mgr_stat.h, dt.h.

Team Members: (Group 4)

1. Gaurav Singhania(A20445477)

2. Chaitanya Nagaram(A20450288)

3. Sachin Devatar(A20443522)


Abstract:
The buffer manager manages a fixed number of pages in memory that represent pages from a page file managed by the storage manager implemented in assignment 1. The memory pages managed by the buffer manager are called page frames or frames for short. We call the combination of a page file and the page frames storing pages from that file a Buffer Pool. The Buffer manager should be able to handle more than one open buffer pool at the same time. However, there can only be one buffer pool for each page file. Each buffer pool uses one page replacement strategy that is determined when the buffer pool is initialized. You should at least implement two replacement strategies FIFO and LRU. 

To Run:

With given default test cases:
Command: make test_assign2_1.c
Run: ./test_assign2_1.c
To remove object files use this command: make clean

1. Buffer Manager Functions
---------------------------------

The Buffer Manager has the following functionalities:

The functions of Buffer pool are used to create a new buffer pool in the memory which is later on used to 
fetch an existing page file from disk onto the memory for reading/writing purposes. 
The functionality of reading and writing onto file is already implemented in the Storage_Manager i.e. Assignment 1

initBufferPool(parameters)
This function creates and initializes a new buffer pool in the memory. Following are the parameters used in the function:
pageFile - is the page that is present on the disk and fetched to the buffer pool on request.
numPages - is the buffer size that is number of page frames that are allowed to store in the buffer pool.
Replacement Strategy - is used to provide the type of replacement algorithm (such as LRU, FIFO, CLOCK) to be performed on the buffer pool to replace page frames.

Returns RC_OK, once the buffer pool is initialised.


shutdownBufferPool(parameter)
This function is used to destroy the buffer pool i.e. it will free the memory/space used by the buffer pool created using initializeBufferPool() function.
Before freeing the buffer pool, forceFlushPool is called, which writes all dirty pages back to disk.

forceFlushPool(...)
This is used to write all the modified/dirty pages i.e. whose dirtyBit = 1 to the disk. It iterates through all the frames of the buffer pool and checks if any of the page's dirty bit is equal to 1 and no client is accessing it.
If both the above conditions satisfy, then the modified page is written back to the disk.


2. PAGE MANAGEMENT FUNCTIONS
==========================

The functions of page management are used to get pages from disk, remove pages from buffer pool, mark a page dirty if modified by client and force a page back to disk. 

pinPage(parameters)
This function pins the page holding page number in buffer pool i.e, it fetches the pages from the pagefile for the buffer pool. Before Pinning a page it checks whether the buffer pool has a empty space or not and if not then we need to perform Page Replacement Strategy in order to insert our new page to the buffer pool and we have implemented LRU, FIFO and Clock strategies for the page replacement it reads the page from the page file present on disk and stores it in the buffer pool. 

unpinPage(parameters)
This function unpins the given page. For a page to be unpinned, PageNumber has to be specified.

markDirty(parameters)
This is used to set the dirty bit = 1 for a modified page which is changed by the client.

forcePage(....)
This function is used to write back the contents of modified page (whose dirty bit = 1) from the buffer pool to disk. Once the writing of page contents is done, the dirty bit is modified to 0 and the write count is incremented.

3. STATISTICS FUNCTIONS
===========================

The statistics related functions are used to gather some information about the buffer pool. So it provides various statistical information about the buffer pool.

getFrameContents(...)
This function returns an array of PageNumbers. The array size = buffer size (numPages).
Repeats through all the pages in the buffer pool and setting frameContents' value to pageNum of the page


getDirtyFlags(...)
This function returns an array of bools.
The array size = buffer size (numPages).
Repeats through all the pages in the buffer pool and setting dirtyFlags' value to TRUE if page is dirty else FALSE


getFixCounts(...) 
This function returns an array of ints. 
The array size = buffer size (numPages). 
repeats through all the pages in the buffer pool and setting fixCounts' value to page's fixCount


getNumReadIO(...)
 This function returns the count of total number of IO reads performed by the buffer pool i.e. number of pages read from the disk. We maintain this data using the rearIndex variable.

getNumWriteIO(...)
 This function returns the count of total number of IO writes performed by the buffer pool i.e. number of pages written to the disk. We maintain this data using the writeCount variable. We initialize writeCount to 0 when buffer pool is initialized and increment it whenever a page frame is written to disk.


4. PAGE REPLACEMENT ALGORITHM FUNCTIONS
=========================================

The page replacement strategy functions implement FIFO, LRU, LFU, CLOCK algorithms which are used while pinning a page. If the buffer pool is full and a new page has to be pinned, then a page should be replaced from the buffer pool. These page replacement strategies determine which page has to be replaced from the buffer pool.

FIFO(...)
First In First Out (FIFO) is the most basic page replacement strategy used. 
FIFO is generally like a queue where the page which comes first in the buffer pool is in front and that page will be replaced first if the buffer pool is full.Once the page is located, 
we write the content of the page frame to the page file on disk and then add the new page at that location.

LRU(...)
Least Recently Used (LRU) removes the page frame which hasn't been used for a long time (least recent) amongst the other page frames in the buffer pool.
 The variable (field) hitNum in each page frame serves this purpose. hitNum keeps a count of of the page frames being accessed and pinned by the client. Also a global variable "hit" is used for this purpose.
So when we are using LRU, we just need to find the position of the page frame having the lowest value of hitNum. We then write the content of the page frame to the page file on disk and then add the new page at that location.

CLOCK(...)
CLOCK algorithm keeps a track of the last added page frame in the buffer pool. Also, we use a clockPointer which is a counter to point the page frames in the buffer pool.When a page has to be replaced we check the "clockPointer"s position. If that position's page's hitNum is not 1 (i.e. it wasn't the last page added), then replace that page with the new page.In case, hitNum = 1, then we set it's hitNum = 0, increment clockPointer i.e. we go to the next page frame to check the same thing. This process goes on until we find a position to replace the page. We set hitNum = 0 so that we don't enter into an infinite loop.












 







