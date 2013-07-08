Program Transformations
=======================

CPS
---

  * Gateway drug of program transformations.

  * Need to describe what a continuation is first.

  * Other necessary concepts: tail position/tail call, serious vs
    trivial expressions.

  * Give rules for CPSing.  Cases that trip people up include nested
    conds/conditionals with serious tests.  Lambdas also often cause
    problems.  M (curried map) is a good example for showing how to
    CPS the call.
    
  * Should point out that evaluation order of subexpressions in a
    Scheme call is unspecified; as a result, there may be more than
    one way to CPS.   
    
  * Point out properties that CPS gives you (and properties of other
    transformations as well, as we cover them): all serious calls are
    tail calls, all arguments to calls are simple, fix order of
    evaluation.

Trampolining
------------

  * We cannot trampoline without CPSing first, because the non-tail
    calls must return the expected type, not a procedure. For example,
    with Fibonacci, the + call would get confused getting procedures.

  * Show: when compiling to C, CPS by itself does not guarantee a
    bounded stack. Trampolining is one solution.

  * Show: just wrap a thunk around the body of the CPSed function.
    Now, the driver needs to keep applying the returned thunk until
    it's not a procedure anymore.

  * There are many other ways to drive, e.g. call/cc.

  * Instead of grossly thunking the whole body, we can be more
    fine-grained about it. Only need to thunk parts that consume
    unbounded stack.

  * Later: first-order representation for the thunk, registerization
    of thunk as an explicit PC counter.

Representation Independent and Defunctionalization
--------------------------------------------------

  * RI wrt to continuations. Other example: in interpreter, RI wrt to
    the env.
  * Step-by-step RI wrt to continuations:
    * explicit apply-k
    * abstract constructors one at a time, inside-out
  * Now, on to defunctionalization: we shift the smarts from the
    constructors to the destructor (apply-k).
  * Step-by-step defunctionalization:
    * Start by changing apply-k to a matcher. Falls through procedure
      case until all constructors are translated.
    * Per constructor:
      * Rename 'the' v to v.
      * Grab the arguments, newline, paste them, with the name,
        quasiquote.
      * Grab the body with the v, push it to apply-k.
  * First-order representation is nice for tracing. Try trace-define
    instead of define on CPSed first-order function.
  * Interpreter example. RI wrt to env. As an exercise: RI wrt to
    closures.

Registerization
---------------

  * Start with CPS, once more the gateway drug of program
    transformations.
  * If only registerizing the main function and not the continuations,
    without representation independence, need to backup registers.
  * With RI, all good. Tricky thing is understanding what is "serious"
    and needs registerizing, and what is not. Once more, the goal is
    to avoid stack use: procedures are just goto-labels.
  * Explicit PC counter.
      