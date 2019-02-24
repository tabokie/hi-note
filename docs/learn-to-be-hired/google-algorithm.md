# Google Algorithm Interview

## Data Structure

* Given K sorted list, each list contains many (timestamp, key, value) entries sorted by key. Merge them ordered by key and keep newest value only.
	* store list in min-heap
* ```
Say you have a web server and a logging component. The component has two functions as below. When a request come in to the web server, started(string, int) get called first, then completed(string, int) get called.
+started(reqId:string, timestamp:int64)
+completed(reqId:string, timestamp:int64)
You are required to print out a logging statement for each request in the following format. Sorted by the started time.
Request {ReqId1} started at {Y1} finished at {Z1}
Request {ReqId2} started at {Y2} finished at {Z2}
Request {ReqId3} started at {Y3} finished at {Z3}
Request {ReqId4} started at {Y4} finished at {Z4}
```
	* ```
	class Request {id, tStarted, tFinished} 
	Queue<Request> queue; 
	HashMap<String, Request> byId; 
	void started(timestamp, requestId) { 
	  Request req = new Request(id=requestId, tStarted=timestamp); 
	  queue.enqueue(req); 
	  byId[requestId] = req; 
	} 
	void completed(timestamp, requestId) { 
	  byId[requestId].tFinished = timestamp; 
	  while(queue.peek().tFinished != null) { 
	    byId.remove(queue.peek().id); 
	    LOGGING STATEMENT
	  } 
	}
	```

## System

* Text Editor
```
[Google] Design Text Editor (Doubly Linked List)
Build a text editor class with the following functions,

moveCursorLeft(),

moveCursorRight(),

insertCharacter(char) //insert the char right before cursor

backspace() //delete the char right before cursor

Follow-up
Implement undo() //undo the last edit. Can be called multiple times until all edits are cancelled.

All functions above should take O(1) time.

Example

( '|' denotes where the cursor locates. 'text' shows what's been written to the text editor. )

Start with empty text
text = "|"

insertCharacter('a')
text = "a|"

insertCharacter('b')
text = "ab|"

insertCharacter('c')
text = "abc|"

moveCursorLeft()
text = "ab|c"

moveCursorLeft()
text = "a|bc"

backspace()
text = "|bc"

moveCursorLeft()
text = "|bc" (nothing happens since cursor was on the leftmost position)

undo()
text = "a|bc"

undo()
text = "ab|c"

undo()
text = "abc|"

undo()
text = "ab|"

undo()
text = "a|"
```