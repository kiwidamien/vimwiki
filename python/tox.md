# Using tox

This blog post I wrote a while ago is a pretty good intro:
https://kiwidamien.github.io/making-a-python-package-v-testing-with-tox.html

An example tox file:
```tox
[tox]
envlist=py36

[testenv]
deps = 
  pydocstyle
  pycodestyle
  pytest
commands =
  - pydocstyle --ignore=D100,104 roman/ 
  - pycodestyle roman/ 
  pytest
```

i.e. When typing tox, load (either conda or virtualenv) environment `py36`, install the packages listed in `deps`, and then run the commands `pydocstyle --ignore=D100,104 roman/` and `pycdoestyle roman/`, and finally `pytest`
. The dashes ignore the exit code of  the functoin.

You can also include a requirements file:
```tox
[tox]
envlist=py36

[testenv]
deps = 
    pytest
    -rrequirements.txt
commands =
  - pydocstyle --ignore=D100,104 roman/ 
  - pycodestyle roman/ 
  pytest
```

Note there is no space between the `-r` and `requirements.txt`
