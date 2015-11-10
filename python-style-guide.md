# An Opinionated Style Guide for Python

An evolving style guide to writing Python code in a clear and effective manner. I use these guidelines throughout the projects and coursework I produce.


## Overall Styles

### General Development Guidelines

- "Explicit is better than implicit" - [PEP 20][]
- "Readability counts." - [PEP 20][]
- "Now is better than never." - [PEP 20][]
- These guidelines are always evolving.


## In Particular

### Style

Follow [PEP 8][], whenever sensible.

#### Naming

- Variables, functions, methods, packages, modules
    + `lower_case_with_underscores`
- Classes and Exceptions
    + `CapWords`
- Protected methods and internal functions
    + `_single_leading_underscore(self, ...)`
- Private methods
    + `__double_leading_underscore(self, ...)`
- Constants
    + `ALL_CAPS_WITH_UNDERSCORES`

##### Best Practices

- Try to avoid one-letter variables. 
*Noted exception*: In very short blocks, when the meaning is clearly visible from the immediate context (e.g. loops)

- Avoid the characters 'l' (lowercase letter el), 'O' (uppercase letter oh), or 'I' (uppercase letter eye) as single character variable names.
- Modules should have short, all-lowercase names. Underscores can be used in the module name if it improves readability. 
- Class names should normally use the CapWords convention.


#### Code Style

##### Explicit code
While any kind of black magic is possible with Python, the most explicit and straightforward manner is preferred.

**Good**
```python
def make_complex(x, y):
    return {'x': x, 'y': y}
```

**Not so good**
```python
def make_complex(*args):
    x, y = args
    return dict(**locals())
```

In the good code above, x and y are explicitly received from the caller, and an explicit dictionary is returned. The developer using this function knows exactly what to do by reading the first and last lines, which is not the case with the bad example.

##### One statement per line
While some compound statements such as list comprehensions are allowed and appreciated for their brevity and their expressiveness, it is bad practice to have two disjoint statements on the same line of code.

**Good**
```python
print 'one'
print 'two'

if x == 1:
    print 'one'

cond1 = <complex comparison>
cond2 = <other complex comparison>
if cond1 and cond2:
    # do something
```

**Not so good**
```python
print 'one'; print 'two'

if x == 1: print 'one'

if <complex comparison> and <other complex comparison>:
    # do something
```

#### Indentation

Use 4 spaces -- not tabs.

##### Imports

Import entire modules instead of individual symbols within a module. For example, for a top-level module `canteen` that has a file `canteen/sessions.py`,

**Good**

```python
import pandas
import pandas.read_excel;
from pandas import read_excel
```

**Not so much**

```python
from pandas import _np_version  # Symbol from pandas/__init__.py
```

*Exception*: For third-party code where documentation explicitly says to import individual symbols.

*Rationale*: Avoids circular imports. See [here](https://sites.google.com/a/khanacademy.org/forge/for-developers/styleguide/python#TOC-Imports).

Put all imports at the top of the page with three sections, each separated by a blank line, in this order:

1. System imports
2. Third-party imports
3. Local source tree imports

*Rationale*: Makes it clear where each module is coming from.

#### Documentation

Follow [PEP 257][]'s docstring guidelines. [reStructured Text](http://docutils.sourceforge.net/docs/user/rst/quickref.html) and [Sphinx](http://sphinx-doc.org/) can help to enforce these standards.

Use one-line docstrings for obvious functions.

```python
"""Return the pathname of ``foo``."""
```

Multiline docstrings should include

- Summary line
- Use case, if appropriate
- Args
- Return type and semantics, unless ``None`` is returned

```python
"""Train a model to classify Foos and Bars.

Usage::

    >>> import klassify
    >>> data = [("green", "foo"), ("orange", "bar")]
    >>> classifier = klassify.train(data)

:param train_data: A list of tuples of the form ``(color, label)``.
:rtype: A :class:`Classifier <Classifier>`
"""
```

Notes

- Use action words ("Return") rather than descriptions ("Returns").
- Document `__init__` methods in the docstring for the class.

```python
class Person(object):
    """A simple representation of a human being.

    :param name: A string, the person's name.
    :param age: An int, the person's age.
    """
    def __init__(self, name, age):
        self.name = name
        self.age = age
```

##### On comments

Use them sparingly if you are are able to write code that already maintains a high level of readability. If code is not overly explicit as far as code goes, use comments. Prefer code readability to writing a lot of comments. Often, small methods are more effective than comments.

*May be a bit much*

```python
# If the sign is a stop sign
if sign.color == 'red' and sign.sides == 8:
    stop()
```

*Good*

```python
def is_stop_sign(sign):
    return sign.color == 'red' and sign.sides == 8

if is_stop_sign(sign):
    stop()
```

When you do write comments, remember: "Strunk and White". - [PEP 8][]

#### Line lengths

No need to stress. 80-100 characters is fine.

Use parentheses for line continuations.

```python
wiki = (
    "The Colt Python is a .357 Magnum caliber revolver formerly manufactured "
    "by Colt's Manufacturing Company of Hartford, Connecticut. It is sometimes "
    'referred to as a "Combat Magnum". It was first introduced in 1955, the '
    "same year as Smith & Wesson's M29 .44 Magnum."
)
```

### Testing

#### General testing guidelines

- Use long, descriptive names. This often obviates the need for doctrings in test methods.
- Tests should be isolated. Don't interact with a real database or network. Use a separate test database that gets torn down or use mock objects.
- Prefer [factories](https://github.com/rbarrois/factory_boy) to fixtures.
- Never let incomplete tests pass, else you run the risk of forgetting about them. Instead, add a placeholder like `assert False, "TODO: finish me"`.

#### Unit Tests

- Focus on one tiny bit of functionality.
- Should be fast, but a slow test is better than no test.
- It often makes sense to have one testcase class for a single class or model.

```python
import unittest
import factories

class PersonTest(unittest.TestCase):
    def setUp(self):
        self.person = factories.PersonFactory()

    def test_has_age_in_dog_years(self):
        self.assertEqual(self.person.dog_years, self.person.age / 7)
```

#### Functional Tests

Functional tests are higher level tests that are closer to how an end-user would interact with your application. They are typically used for web and GUI applications.

- Write tests as scenarios. Testcase and test method names should read like a scenario description.
- Use comments to write out stories, *before writing the test code*.

```python
import unittest

class TestAUser(unittest.TestCase):

    def test_can_write_a_blog_post(self):
        # Goes to the her dashboard
        ...
        # Clicks "New Post"
        ...
        # Fills out the post form
        ...
        # Clicks "Submit"
        ...
        # Can see the new post
        ...
```

Notice how the testcase and test method read together like "Test A User can write a blog post".


## Inspired by...

- [PEP 20 (The Zen of Python)][PEP 20]
- [PEP 8 (Style Guide for Python)][PEP 8]
- [The Hitchiker's Guide to Python][python-guide]
- [Khan Academy Development Docs][]
- [Python Best Practice Patterns][]
- [Pythonic Sensibilities][]
- [The Pragmatic Programmer][]
- and many other bits and bytes

[Pythonic Sensibilities]: http://www.nilunder.com/blog/2013/08/03/pythonic-sensibilities/
[Python Best Practice Patterns]: http://youtu.be/GZNUfkVIHAY
[python-guide]: http://docs.python-guide.org/en/latest/
[PEP 20]: http://www.python.org/dev/peps/pep-0020/
[PEP 257]: http://www.python.org/dev/peps/pep-0257/
[PEP 8]: http://www.python.org/dev/peps/pep-0008/
[Khan Academy Development Docs]: https://sites.google.com/a/khanacademy.org/forge/for-developers
[The Pragmatic Programmer]: http://www.amazon.com/The-Pragmatic-Programmer-Journeyman-Master/dp/020161622X/ref=sr_1_1?ie=UTF8&qid=1381886835&sr=8-1&keywords=pragmatic+programmer
