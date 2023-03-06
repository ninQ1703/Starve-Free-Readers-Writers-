# Starve Free Reader Writer Solution 
## Readers Writers Problem
Given a file and a set of writers and readers.
Writers want to write into file and readers want to read from the file when they are ready.
But readers and writer cannot be doing their respective job at same time or it will cause to race condition.

There are two approachs to this problem:
1) First readers writers
2) second readers writers
These solution give readers priority over writers or vice verca which leads to starvation of the latter.

Hence a starve free readers writers solution has been proposed.

## Starve Free readers writers solution explanation
Many readers can read at a time while only one writer can write at a time.
Let us call this collection of readers a pool.

A pool as a whole has two permissions
1) Read/Write permission - It allows any reader of the pool to read the file
2) Entry permission - It permits the pool to allow new readers to enter into it

Whenever a writer is ready to enter the critical section, it takes away the Entry permission.
And whenever a writer is writing, it takes away Read permisson, and only one member is allowed to enter the pool, which checks for the read permission.

After the writer finishes writing, it gives back these permissions to the pool.
Hence we can use two semaphores for this purpose - write, turn.
Also we need to maintain count of readers inside of pool, so we use a variable readcount and a semaphore to achieve mutual exclusion for readcount among readers.

## Starve Free Reader Writers code
VARIABLES<ul>
	<li>readcount = 0;</li></ul>
SEMAPHORES<ul>
	<li>mutex = 1; //mutual exclusion for readers</li>
	<li>write = 1; //read and write permission</li>
	<li>turn  1; // entry permission</li></ul>

Readers Code:
```
	while(true){
		wait(turn);
		wait(mutex);

		readcount++;
		if(readcount==1) wait(write);

		signal(turn);
		signal(mutex);

		// critical section

		wait(mutex);

		readcount--;
		if(readcount==0) signal(write);

		signal(wrt);
	}
```

Writers Code:
```
	while(true){

		wait(turn);
		wait(write);
		signal(turn);

		//critical section

		signal(write);
	}
```

## How Solution Satisfies all the Reqiurements

<b>1) Mutual Exclusion</b> - The semaphore 'write' makes sure that there is mutual exclusion between readers and writers and among writers.

<b>2) Progress</b> - If a writer/reader is ready to write/read and no other reader/writer is there, then it will enter the critical section definitely.

<b>3) Bounded Waiting</b> - If a writer is ready, then the pool stops collecting readers. Writers then wait for pool to get empty which takes finite time, Similarly a reader can get access to file when writer finishes writing with equal fairness. This ensures Starve free Solution.
