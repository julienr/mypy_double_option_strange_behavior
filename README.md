# Repro repository for a strange behavior in mypy

This is an example project with a 'mod1' module and a 'mod2' package which both have functions
that should fail typechecking if `no_implicit_optional=True` is specified.

There are two config files. They both specify `no_implicit_optional=False` for mod2, so running
mypy with either, we would except an error only for mod1.

If you run `mypy --config-file mypy_1.ini .`, you get - as expected - only an error for mod1:

```
(venv) mypy_double_option_strange_behavior $ mypy --config-file mypy_1.ini .
mod1.py:1: error: Incompatible default for argument "x" (default has type "None", argument has type "int")  [assignment]
mod1.py:1: note: PEP 484 prohibits implicit Optional. Accordingly, mypy has changed its default to no_implicit_optional=True
mod1.py:1: note: Use https://github.com/hauntsaninja/no_implicit_optional to automatically upgrade your codebase
Found 1 error in 1 file (checked 2 source files)
```

If you run `mypy --config-file mypy_2.ini`, you get an error for mod2 as well. That's unexpected:

```
venv) mypy_double_option_strange_behavior $ mypy --config-file mypy_2.ini .
mypy_2.ini: While reading from 'mypy_2.ini' [line  5]: option 'no_implicit_optional' in section 'mypy' already exists
mod1.py:1: error: Incompatible default for argument "x" (default has type "None", argument has type "int")  [assignment]
mod1.py:1: note: PEP 484 prohibits implicit Optional. Accordingly, mypy has changed its default to no_implicit_optional=True
mod1.py:1: note: Use https://github.com/hauntsaninja/no_implicit_optional to automatically upgrade your codebase
mod2/__init__.py:1: error: Incompatible default for argument "x" (default has type "None", argument has type "int")  [assignment]
mod2/__init__.py:1: note: PEP 484 prohibits implicit Optional. Accordingly, mypy has changed its default to no_implicit_optional=True
mod2/__init__.py:1: note: Use https://github.com/hauntsaninja/no_implicit_optional to automatically upgrade your codebase
Found 2 errors in 2 files (checked 2 source files)
```

The only difference between the two configs is that `mypy_2.ini` has `no_implicit_optional = True` duplicated in the
`[mypy]` section.
