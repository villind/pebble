.. Pebble documentation master file, created by
   sphinx-quickstart on Thu Oct 17 23:52:22 2013.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

Welcome to Pebble's documentation!
==================================

Modern languages should natively support concurrency, threading and synchronization primitives. Their usage should be the most intuitive possible, yet allowing all the required flexibility.

Pebble aims to help managing threads and processes in an easier way; it wraps Python's standard libray threading and multiprocessing objects.


:mod:`process`
-----------------

.. function:: spawn(target=None, args=(), kwargs={}, name=None, daemon=False)

   Spawns a concurrent process and runs a function within it.

   The *spawn* function works as well as a decorator.

   *target* is the desired function to be run with the given *args* and *kwargs* parameters; if *daemon* is True, the process will be stopped if the parent exits (default False).
   *name* is a string, if assigned will be forwarded to the process object.

   The *spawn* function returns the Process object which is running the *target* or decorated one.

   .. note::

       The decorator accepts the keywords *daemon* and *name* only.
       If *target* keyword is not specified, the function will act as a decorator.

.. function:: concurrent(target=None, args=(), kwargs={}, callback=None, identifier=None, timeout=None)

   Runs the given function in a concurrent process, taking care of the results and error management.

   The *concurrent* function works as well as a decorator.

   *target* is the desired function to be run with the given *args* and *kwargs* parameters; if *timeout* is set, the process will be stopped once expired returning TimeoutError as results.
   If a *callback* is passed, it will be executed after the job has finished with the returned *Task* as parameter.
   If *identifier* is not None, it will be assigned as the *Task.id* value.

   The *concurrent* function returns a *Task* object.

   .. note::

       The decorator accepts the keywords *timeout* and *callback* only.
       If *target* keyword is not specified, the function will act as a decorator.

.. class:: Pool(workers=1, task_limit=0, queue=None, queueargs=None, initializer=None, initargs=None)

   A Pool allows to schedule jobs into a Pool of Processes which will perform them concurrently.
   Process pools work as well as a *context manager*.

   *workers* is an integer representing the amount of desired process workers managed by the pool. If *worker_task_limit* is a number greater than zero each worker will be restarted after performing an equal amount of tasks.
   *initializer* must be callable, if passed, it will be called every time a worker is started, receiving *initargs* as arguments.
   *queue* represents a Class which, if passed, will be constructed with *queueargs* as parameters and used internally as a task queue. The *queue* object resulting from its construction must expose same functionalities of Python standard *Queue* object, especially for what concerns the *put()*, *get()* and *join()* methods.

   .. data:: active

      True if the Pool is running, false otherwise.

   .. function:: schedule(function, args=(), kwargs={}, callback=None, timeout=0, identifier=None)

      Schedule a job within the Pool.

      *function* is the function which is about to be scheduled.
      *args* and *kwargs* will be passed to the function respectively as its arguments and keyword arguments.
      *callback* must be callable, if passed, it will be called once the task has ended with the *Task* object as parameter.
      *timeout* is an integer, if greater than zero, once expired will force the timed out task to be interrupted and the worker will be restarted; *Task.get()* will raise *TimeoutError*, callbacks will be executed.
      If *identifier* is not None, it will be assigned as the *Task.id* value.

   .. function:: close()

      No more job will be allowed into the Pool, queued jobs will be consumed.
      To ensure all the jobs are performed call *ThreadPool.join()* just after closing the Pool.

   .. function:: stop()

      The ongoing jobs will be performed, all the enqueued ones dropped; this is a fast way to terminate the Pool.
      To ensure the Pool to be released call *ThreadPool.join()* after stopping the Pool.

   .. function:: join(timeout=None)

      Waits for all workers to exit, must not be called before calling either *close()*, *stop()* or *kill()*.
      If *timeout* is set and some worker is still running after it expired a TimeoutError will be raised, a timeout of 0 will return immediately.


:mod:`thread`
-----------------

.. function:: spawn(target=None, args=(), kwargs={}, name=None, daemon=False)

   Spawns a concurrent thread and runs a function within it.

   The *spawn* function works as well as a decorator.

   *target* is the desired function to be run with the given *args* and *kwargs* parameters; if *daemon* is True, the thread will be stopped if the parent exits (default False).
   *name* is a string, if assigned will be forwarded to the thread object.

   The *spawn* function returns the Thread object which is running the *target* or decorated one.

   .. note::

       The decorator accepts the keywords *daemon* and *name* only.
       If *target* keyword is not specified, the function will act as a decorator.

.. function:: concurrent(target=None, args=(), kwargs={}, callback=None, identifier=None)

   Runs the given function in a concurrent thread, taking care of the results and error management.

   The *concurrent* function works as well as a decorator.

   *target* is the desired function to be run with the given *args* and *kwargs* parameters; if *timeout* is set, the thread will be stopped once expired returning TimeoutError as results.
   If a *callback* is passed, it will be executed after the job has finished with the returned *Task* as parameter.
   If *identifier* is not None, it will be assigned as the *Task.id* value.

   The *concurrent* function returns a *Task* object.

   .. note::

       The decorator accepts the *callback* keyword only.
       If *target* keyword is not specified, the function will act as a decorator.

.. class:: Pool(workers=1, task_limit=0, queue=None, queueargs=None, initializer=None, initargs=None)

   A Pool allows to schedule jobs into a Pool of Threads which will perform them concurrently.
   Thread pools work as well as a *context manager*.

   *workers* is an integer representing the amount of desired thread workers managed by the pool. If *worker_task_limit* is a number greater than zero each worker will be restarted after performing an equal amount of tasks.
   *initializer* must be callable, if passed, it will be called every time a worker is started, receiving *initargs* as arguments.
   *queue* represents a Class which, if passed, will be constructed with *queueargs* as parameters and used internally as a task queue. The *queue* object resulting from its construction must expose same functionalities of Python standard *Queue* object, especially for what concerns the *put()*, *get()* and *join()* methods.

   .. data:: active

      True if the Pool is running, false otherwise.

   .. function:: schedule(function, args=(), kwargs={}, callback=None, identifier=None)

      Schedule a job within the Pool.

      *function* is the function which is about to be scheduled.
      *args* and *kwargs* will be passed to the function respectively as its arguments and keyword arguments.
      *callback* must be callable, if passed, it will be called once the task has ended with the *Task* object as parameter.
      If *identifier* is not None, it will be assigned as the *Task.id* value.

   .. function:: close()

      No more job will be allowed into the Pool, queued jobs will be consumed.
      To ensure all the jobs are performed call *ThreadPool.join()* just after closing the Pool.

   .. function:: stop()

      The ongoing jobs will be performed, all the enqueued ones dropped; this is a fast way to terminate the Pool.
      To ensure the Pool to be released call *ThreadPool.join()* after stopping the Pool.

   .. function:: join(timeout=None)

      Waits for all workers to exit, must not be called before calling either *close()*, *stop()* or *kill()*.
      If *timeout* is set and some worker is still running after it expired a TimeoutError will be raised, a timeout of 0 will return immediately.


:mod:`pebble`
-------------

.. decorator:: synchronized([lock])

    A synchronized function prevents two or more callers to interleave its execution preventing race conditions.

    The *synchronized* decorator accepts as optional parameter a *Lock*, *RLock* or *Semaphore* from *threading* and *multiprocessing* modules.

    If no synchronization object is given, a single *threading.Lock* will be employed. This implies that between different decorated functions only one at a time will be executed.

.. decorator:: sighandler(signals)

   Convenience decorator for setting the decorated *function* as signal handler for the specified *signals*.

   *signals* can either be a single signal or a list/tuple of signals.

.. function:: waitfortasks(tasks, timeout=None)

   Waits for one or more *Task* to be ready or until *timeout* expires.

   *tasks* is a list containing one or more *pebble.Task* objects.
   If *timeout* is not None the function will block for the specified amount of seconds returning an empty list if no *Task* is ready.

   The function returns a list containing the ready *Tasks*.

.. function:: waitforthreads(threads, timeout=None)

   Waits for one or more *Thread* to exit or until *timeout* expires.

   *threads* is a list containing one or more *threading.Thread* objects.
   If *timeout* is not None the function will block for the specified amount of seconds returning an empty list if no *Thread* is ready.

   The function returns a list containing the ready *Threads*.

   .. note::

      Expired *Threads* are not joined by *waitforthreads*.

.. function:: waitforqueues(queues, timeout=None)

   Waits for one or more *Queue* to be ready or until *timeout* expires.

   *queues* is a list containing one or more *Queue.Queue* objects.
   If *timeout* is not None the function will block for the specified amount of seconds returning an empty list if no *Queue* is ready.

   The function returns a list containing the ready *Queues*.

.. exception:: TimeoutError

   Raised when a given timeout expires.

.. exception:: TaskCancelled

   Raised by *Task.get()* functions if *Task.cancel()* has been called.

.. exception:: PoolError

   Raised if a condition lead to an instability of the Pool.
   This can be caused by, for example, an unmanaged exception raised in the *callback* functions.

.. exception:: ProcessExpired

   Raised by *Task.get()* functions if the related process died unexpectedly during the *Task* execution.

   .. data:: exitcode

      Integer representing the process' exit code.

.. class:: Task

   Functions decorated by *thread*, *process* and *thread_pool* decorators, as well as the ThreadPool.schedule method, once called, will return a *Task* object.
   *Task* objects are handlers to the ongoing jobs within spawned threads and processes.

   .. data:: id

      Contains None as long as an identifier string is not specified at creation time.

   .. data:: number

      An integer representing the task number.

   .. data:: ready

      A boolean, False if *Task* is still ongoing, True if results are ready.

   .. data:: successful

      A boolean, False if *Task* is still ongoing or an exception occurred whithin, True if results were successfully delivered.

   .. data:: started

      A boolean, True if *Task* has been started, False if it is still in the queue.

   .. function:: wait(timeout=None)

      Wait until the results are ready or a *timeout* occurrs.
      Returns True if *Task* completed within the timeout period, False otherwise.

   .. function:: get(timeout=None, cancel=False)

      Returns the values given back by the decorated *function*.
      If an exception has been raised within it, it will be re-raised by the *get()* method with the traceback appended as attribute.
      The *get()* method blocks until the thread or process has not finished.

      If *timeout* is a number greater than 0 it will block for the specified amount of seconds, raising a *TimeoutError* if the results are not ready yet; a value equal or smaller than 0 will force the method to return immediately.
      If *cancel* is True the *task* will be cancelled in case of timeout; the function will raise *TimeoutError*, subsequent calls to *Task.get()* will raise *TaskCancelled*, the parameter has no effect if *timeout* has not been set.

   .. function:: cancel()

      Cancels the *Task*, results will be dropped, *callbacks* will be executed and any *Task.get()* blocking will raise *TaskCancelled* exception.
      If the task is running into a process it will be terminated, as thread cannot be stopped, its results will simply be ignored but the function itself will keep running.

      *cancel()* should not be called on *Tasks* which logic is using shared resources as *Pipes*, *Locks* or *Files*.


General notes
-------------

Callbacks
+++++++++

Callbacks are run in the parent process in separate threads, therefore any shared state must be protected accordingly.

Processes
+++++++++

The Python's multiprocessing guidelines apply as well for all functionalities within the *process* namespace.

Examples
--------

Use of spawn
++++++++++++

The spawn function is a simple convenience method for spawning Processes and Threads, the following code snippets are all equivalent.

::

     from threading import Thread

     def function(arg, keyarg=0):
         print arg + keyarg

     thrd = Thread(target=function, args=[1], kwargs={'keyarg': 1})
     thrd.daemon = True
     thrd.start()

::

     from pebble import thread

     def function(arg, keyarg=0):
         print arg + keyarg

     thrd = thread.spawn(target=function, args=[1], kwargs={'keyarg': 1}, daemon=True)

::

     from pebble import thread

     @thread.spawn(daemon=True)
     def function(arg, keyarg=0):
         print arg + keyarg

     thrd = function(1, keyarg=1)

Producer and consumer example.

::

     from pebble import thread
     from queue import Queue

     @thread.spawn
     def producer(queue):
         for product in range(10):
             print("I'm the producer, I produced: %d" % product)
             queue.put(product)
         print("I'm the producer and I'm leaving.")
         queue.put(None)

     @thread.spawn
     def consumer(queue):
         while True:
             product = queue.get()
             if product is not None:
                 print("I'm the consumer, I consume: %d" % product)
             else:
                 print("I'm the consumer and I'm leaving.")
                 return


     queue = Queue()
     cons = consumer(queue)
     prod = producer(queue)

     cons.join()
     prod.join()


Concurrent functions
++++++++++++++++++++

The concurrent functions represent what used to be the thread and process decorators in Pebble2.

::

     from pebble import process

     @process.concurrent
     def function(arg, keyarg=0):
         return arg + keyarg

     task = function(1, keyarg=1)
     print(task.get())

Quite often developers need to integrate in their projects third party code which appears to be unstable, to leak memory or to hang. The concurrent function allows to easily take advantage of the isolation offered by processes without the need of handling any multiprocessing primitive.

::

    from pebble import process

    from third_party_lib import unstable_function

    task = process.concurrent(target=unstable_function, args=[1, 2], timeout=5)

    try:
        results = task.get()
    except TimeoutError:
        print("unstable_function took more than 5 seconds to complete")
    except ProcessExpired as error:
        print("%s. Exit code: %d" % (error, error.exitcode))
    except Exception as error:
        print("unstable_function raised %s" % error)
        print(error.traceback)  # Python's traceback of remote process

Sighandler decorator
++++++++++++++++++++

The syntax ::

    import signal
    from pebble import sighandler

    @sighandler((signal.SIGINT, signal.SIGTERM))
    def signal_handler(signum, frame):
        print("Termination request received!")

Is equivalent to ::

    import signal

    def signal_handler(signum, frame):
        print("Termination request received!")

    signal.signal(signal.SIGINT, signal_handler)
    signal.signal(signal.TERM, signal_handler)


.. toctree::
   :maxdepth: 2
