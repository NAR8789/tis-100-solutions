### Definitions

- *minimal*: let us call a node "minimal" if the operation it performs cannot be performed faster
- *atomic*: let us call a node "atomic" if its operations cannot be made faster by splitting and pipelining

### Uneven Branch Problem

Consider the following:

``` TIS-100
0: MOV ANY ACC
JEZ B
A: MOV 1 ANY
JMP 0
B: MOV 0 ANY
```

The `A` branch takes 1 instruction longer, due to the "break"--the additional `JMP` instruction at the end of the `A` branch to prevent fall-through into the `B` branch. I call this the *Uneven Branch Problem*. The obvious implementaton of if-else logic as a 2-case "switch statement" results in the first branch taking longer to run.

#### `if` Unrolling

We can save the extra cycle at the cost of additional instructions, like so:

``` TIS-100
MOV ANY ACC
JEZ B
A: MOV 1 ANY
MOV ANY ACC
JNZ A
B: MOV 0 ANY
```

By duplicating the logic under label label `0` up through (a negated copy of) the branch instruction, we can make the `A` and `B` branches symmetric (antisymmetric?), and drop the `0` label and associated `JMP`. This saves the extra instruction on the `A` branch.

Note that in most cases this will only work correctly with `JEZ` and `JNZ`. Since we require a negated version of the branch logic, trying to do this with a `JLZ` or `JGZ` if-statement will usually result in inconsistent treatment of zeroes. (Of course, If the input guaranteeably contains no zeros, this is not a problem.)

This is really just a variation on loop unrolling--we are replacing `JMP 0` with a negated duplicate of the contents under label `0`, but by taking advantage of symmetry we avoid copying the original `JMP 0` command, and therefore avoid iterated copies.

The natural looping of the TIS node is important--without it, the `B` branch in the original would also need a `JMP 0`, and we'd have to deal with that by traditional loop-unrolling.
