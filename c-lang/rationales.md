### evaluations
There are two kinds of
evaluations performed by the compiler for each expression or subexpression (both of which
are optional):

* Value computation: Calculation of the value that is returned by the expression.
This may involve determining an object's identity (e.g., when the expression returns a
reference to some object) or reading the value previously assigned to an object (e.g.,
when the expression returns a number or some other value)
* Side effect: Access (read or write) to a volatile object, modify (write) to a (nonvolatile)
object, calling a library I/O function, or calling a function that does any
of those operations. All such side eects are changes in the state of the execution
environment.

### sequence before
https://en.cppreference.com/w/cpp/language/eval_order

### Sequence points
https://en.cppreference.com/w/cpp/language/eval_order
