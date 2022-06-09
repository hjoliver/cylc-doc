.. _spawn-on-demand:

Spawn On Demand Scheduler
=========================

This reference documents how the new event-driven scheduling algorithm in
Cylc 8 works.

.. note::

   For comparisons with the old *Spawn On Submit* scheduler and discussion of
   potential variations on the algorithm, see the original
   `proposal document
   <https://cylc.github.io/cylc-admin/proposal-spawn-on-d.html>`_


The Task Pool
-------------

The scheduler manages an evolving *task pool* of *task proxy objects*
representing the current subset of tasks in the graph that it has to be aware
of to do its job. Together these define the ``n=0`` workflow window:
active tasks (submitted or running) active-waiting tasks (task-prerequisites
satisfied but waiting on an external trigger) incomplete tasks (finished
without completing expected outputs) 


The Hidden Task Pool
--------------------

The hidden pool contains task proxies spawned with partially satisfied
task-prerequisites, that are kept hidden until fully satisfied.

This is a convenient way to keep track of task prerequisites until they become
fully satisfied, and it enables Cylc to present partially and fully unsatisfied
tasks equally - as simply waiting for their prerequisites to be satisfied.

The presence of any remaining tasks in the hidden pool is flagged as an error
at shutdown, if the workflow is otherwise complete.

For example, below, if task ``foo`` succeeds ``baz`` will spawn into the hidden
pool while it waits on ``bar`` to run and succeed, at which point it will move
into the main pool: 


.. code-block:: cylc-graph

   foo & bar => baz


Blah blah



