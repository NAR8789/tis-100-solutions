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

The `A` branch takes 1 instruction longer, due to "break"--the additional `JMP` instruction at the end to prevent execution of the `B` branch. This is the *Uneven Branch Problem*. The obvious implementaton of if-else logic generally results in the first branch taking longer to run.

#### Solution to the Uneven Branch Problem

We can save the extra cycle at the cost of additional instructions, like so:

``` TIS-100
MOV ANY ACC
JEZ B
A: MOV 1 ANY
MOV ANY ACC
JNZ A
B: MOV 0 ANY
```

By duplicating the logic up to the branch point, we can make the `A` and `B` branches symmetric, and drop the `0` label and associated `JMP`. This will usually take more instructions, due to the duplication, but it saves us extra cycle of the `JMP`.

Note also that the above requires a negative version of the test--this solution works with `JEZ` and `JNZ`, but will not be universally applicable when using `JLZ` or `JGZ`
