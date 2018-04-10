# PDB Notes

`pdb` refers to the interactive **P**ython **D**e**B**ugger.

## Starting/Setup 

### Starting program under debugger control

```
python -m pdb foo.py
```

Then it stops on the first line of the code.

Automatically enter post-mortem debugging if the program being debugged exits abnormally.

### Within python interpreter

Import both `pdb` and the targeting module, and call module functions in python interpreter.

```
>>> import pdb
>>> import foo
>>> pdb.run('foo.bar()')
```

### Set a hard-coded breakpoint within code

A.k.a, break into the debugger from a running program.

Setup by (1) add the import and (2) call the method both in the line you want to stop the code:

```python
import pdb; pdb.set_trace()
```

When executed to this line, the shell turns

```
(Pdb)
```

## Commands

### Basic navigation methods

- `l(ist)`: Displays 11 lines around the current line or continue the previous listing.
- `s(tep)`: Execute the current line, stop at the first possible occasion.
- `n(ext)`: Continue execution until the next line in the current function is reached or it returns.
- `b(reak) line_number`: Set a breakpoint (depending on the argument provided).
	- `b` lists all breakpoints.
	- Breakpoints can be cleared by `cl(ear) break_point_num`.
- `c(ont(inue))`
- `a(rgs)`: prints the arguments with their values of the current function.
- `r(eturn)`: Continue execution until the current function returns.

May check the values of the variables by simply type their names, (e.g., `foo`). May also have functions on the variables (e.g. `len(foo)`). When name crashes with `pdb` key words, do `!foo`.

### Auxiliary methods

- `h(elp)`: Without argument, print the list of available commands. With a command as an argument, print help about that command.
- `q(uit)`: Exit the debugger.

### Post-mortem debugging

For debugging a dead program using its traceback object, by checking the state of the program at the time when it died.

- `pdb.pm()`
- `pdb.post_mortem()`

Usage:

- In interpreter after an exception is thrown out.
- `python -m pdb foo.py` directly goes there if the code exits abnormally.

## References

1. [`pdb` official document for python 2.7](https://docs.python.org/2/library/pdb.html).
1. [`pdb` tutorial](https://github.com/spiside/pdb-tutorial) by spiside.
1. [Become a pdb power-user](https://medium.com/instamojo-matters/become-a-pdb-power-user-e3fc4e2774b2)