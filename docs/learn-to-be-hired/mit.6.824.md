# MIT - 6.824 Course Note

MIT 6.824 2017 [(site)](http://nil.csail.mit.edu/6.824/2017/schedule.html)

## Lecture 1

-	Why distributed?
  -	to connect physically separate entities
  -	to achieve security via isolation
  -	to tolerate faults via replication
  -	to scale up throughput via parallel CPUs/mem/disk/net
-	Topics
	-	Implementation
	-	Performance
	-	Fault-Tolerance
	-	Consistency
-	MapReduce
	-	context: multi-hour computations on multi-terabyte data-sets
	-	Feature
		-	Simplicity
		-	Scalability
	-	Detail
		-	Assume `fail-stop` server
		-	User-defined `map` and `reduce` are state-less
			-	easy to recover locally
		-	Input Data is partitioned into tasks, number >> server
			-	good for balance
		-	Input Data stored in GFS with 3 replicas
		-	Mapper read replica from local disk
		-	Mapper computes
			-	Master will ping to check liveness
			-	re-run if reducer hasn't fetch all mapped data
		-	Mapper write output files into local disk
		-	Reducer request intermediate file from all Mappers
		-	Reducer computes
			-	atomic rename to avoid partial failure and duplicate reducers

## Lecture 2

-	RPC
	-	Marshalling
	-	Binding
	-	Failure
		-	`at least once`
		-	`at most once`: detect duplicate request
			-	XID generation
			-	one at a time: server discard # < seq
			-	retry limit
		-	`exactly once`: at-most-once + unbounded retry + fault-tolerance

## Lecture 3

-	GFS
	-	Design
		-	Chunk: 64 MB with 3 replicas and 64 byte metadata
			-	fault-tolerant
			-	load balance
			-	size is big
		- Master: in-memory metadata of dir and file with standby shadow (stale)
			-	fast response
	-	Operation
		-	read:
			-	get server list (addr, version) from Master
			-	ask nearest server
			-	retry if version number is wrong
		-	append:
			-	get servers (3 replica) from Master
			-	push to chained replicas
			-	contact primary:
				-	seq ++
				-	apply change
				-	reqeust replicas
				-	reply to client after all acks
				-	contect master
	-	Consistency
		-	directory is strong consistent, but master failure will result in stale directory
		-	file is weak consistent, hole or duplicate when retry, concurrent write if not atomic append
			-	use file rename for atomic append ??
			-	identifier for valid record
			-	read whole file instead of read by offset
	-	Misc
		-	COW Snapshot by reference count

## Lecture 4

-	Primary-Backup Replication
	-	Synchronize Method
		-	State Transfer
		-	State Machine
	-	State = uniprocessor machine
		-	Model
			-	share disk
			-	client-server and log channel
			-	input includes clock/disk/network interrupt
		- Deterministic Replay
			-	event logging with volatile register value stored
			-	output is idempotent
		-	FT protocol
			-	primary buffer output until backup's output logged and ack
		-	Shared disk as reliable log channel

## Lecture 5-6

-	Overview
	-	Procedure
		-	leader forward request to replicas
		-	if majority reply, leader commit
		-	all servers commit
		-	leader replies
	-	Election
		-	term sequence
		-	leader heartbeat and random timeout
	-	Log Synchronization
		-	
-	Tradeoff
	-	persistent log
	- ...