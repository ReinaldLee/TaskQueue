A simple and easy using queue for tasks(functions) running one by one according to their priorities.

First, new a TaskQueue like:
```javascript
const taskQueue = new TaskQueue();
```
If the task putting into the queue will return a promise, or a promise telling when the task will be finished can be provided, call the method:
```javascript
taskQueue.addTask(task, endPromise, priority);
```
Then, as long as the endPromise (considered first) or the promise returned from the task has resolved, the task is done and the queue starts to execute next task.

Whether the task function returns a promise is optional. If no endPromise provides and no promise returns, the task is considered done as long as the task function returns. Generally this probably is a misusing.

Alternatively, if a target object related to the task is added with the task, like:
```javascript
taskQueue.addTaskWithTarget(target, task, priority);
```
The queue will make the target hold a resolve function of an end promise of the task in an array. That is, as long as the end promise is resolved, the task is considered done. So, when the actual task completes, call
```javascript
taskQueue.endTaskWithTarget(target);
```

Then the queue will try to pick up the resolve function holding by the target, invoke the resolve function and make the end promise resolved. Remember to call endTaskWithTarget() at proper time or else the queue is stuck and all remain tasks in the queue will no longer be executing.

The target can be any kind of object. Since function is also one kind of object, the target and the task can be the same one.

The queue running can be paused by calling
```javascript
taskQueue.pause();
```

After that all the remaining tasks or any new task being added will not execute temporarily. Notice that the task having started running while pause() invoked will continue to execute without pause. Call
```javascript
taskQueue.resume();
```
to continue the rest tasks executing. Notice that the queue holds a counting score while calling pause(). That is, calling N times of pause(), calling N times of resume() is required.