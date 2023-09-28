## Real World Examples of
1. [Decorators](#1-decorators)
2. [Generators](#2-generators)
3. [Type Hinting](#3-type-hinting)
4. [Extension on class basics](#4-extension-on-class-basics)
5. [Class Inheritance](#5-class-inheritance)
6. [Duck Typing](#6-duck-typing)
7. [Properties](#7-properties)
8. [Private Attributes](#8-private-attributes)


## 1. Decorators
**Example 1a**:
  
 **Link**: https://github.com/pydoit/doit/blob/master/doit/control.py

**Comments**: 

```python
def no_none(decorated):
    """decorator for a generator to discard/filter-out None values"""
    def _func(*args, **kwargs):
        """wrap generator"""
        for value in decorated(*args, **kwargs):
            if value is not None:
                yield value
    return _func


```

**Example 1b**:
  
 **Link**: https://github.com/pydoit/doit/blob/master/doc/samples/my_tasks.py

**Comments**: The main purpose of this decorator seems to be to attach metadata to functions regarding their input and output files. This metadata can later be accessed using the task_metadata attribute of the decorated functions. This can be useful in scenarios where you want to keep track of the files a function interacts with, especially in data processing or pipeline workflows.

the 'simple' function is decorated without any parameters. So, an empty task_metadata dictionary is attached to it.

the 'pre' function is decorated with a parameter output which is a list containing 'my_input.txt'. The task_metadata attribute of the pre function will be {'output': ['my_input.txt']}.

```python
def task(*fn, **kwargs):
    # decorator without parameters
    if fn:
        function = fn[0]
        function.task_metadata = {}
        return function

    # decorator with parameters
    def wrap(function):
        function.task_metadata = kwargs
        return function
    return wrap


@task
def simple():
    print("thats all folks")

@task(output=['my_input.txt'])
def pre(to_create):
    with open(to_create[0], 'w') as fp:
        fp.write('foo')
```

---

## 2. Generators

**Example 2a**:
  
 **Link**: 

**Comments**: 

```python
# Python code for example 2a goes here
```

**Example 2b**:
  
 **Link**: 

**Comments**: 

```python
# Python code for example 2b goes here
```

---

## 3. Type Hinting

**Example 3a**: Examples on this file from flask repo
  
 **Link**: https://github.com/pallets/flask/blob/main/src/flask/app.py

**Comments**: 

```python
def make_shell_context(self) -> dict:
    """Returns the shell context for an interactive shell for this
    application.  This runs all the registered shell context
    processors.

    .. versionadded:: 0.11
    """
    rv = {"app": self, "g": g}
    for processor in self.shell_context_processors:
        rv.update(processor())
    return rv
```
```python
def open_instance_resource(self, resource: str, mode: str = "rb") -> t.IO[t.AnyStr]:
    """Opens a resource from the application's instance folder
    (:attr:`instance_path`).  Otherwise works like
    :meth:`open_resource`.  Instance resources can also be opened for
    writing.

    :param resource: the name of the resource.  To access resources within
                        subfolders use forward slashes as separator.
    :param mode: resource file opening mode, default is 'rb'.
    """
    return open(os.path.join(self.instance_path, resource), mode)

```


**Example 3b**:
  
 **Link**: 

**Comments**: 

```python
# Python code for example 3b goes here
```

---

## 4. Extension on class basics

**Example 4a**:
  
 **Link**: 

**Comments**: 

```python
# Python code for example 4a goes here
```

**Example 4b**:
  
 **Link**: 

**Comments**: 

```python
# Python code for example 4b goes here
```

---

## 5. Class Inheritance

**Example 5a**:
  
 **Link**: 

**Comments**: 

```python
# Python code for example 5a goes here
```

**Example 5b**:
  
 **Link**: 

**Comments**: 

```python
# Python code for example 5b goes here
```

---

## 6. Duck Typing

**Example 6a**:
  
 **Link**: 

**Comments**: 

```python
# Python code for example 6a goes here
```

**Example 6b**:
  
 **Link**: 

**Comments**: 

```python
# Python code for example 6b goes here
```

---

## 7. Properties

**Example 7a**:
  
 **Link**: 

**Comments**: 

```python
# Python code for example 7a goes here
```

**Example 7b**:
  
 **Link**: 

**Comments**: 

```python
# Python code for example 7b goes here
```

---

## 8. Private Attributes

**Example 8a**:
  
 **Link**: 

**Comments**: 

```python
# Python code for example 8a goes here
```

**Example 8b**:
  
 **Link**: 

**Comments**: 

```python
# Python code for example 8b goes here
```

---

## 9. Lambda

**Example 9a**:
  
 **Link**: 

**Comments**: 

```python
# Python code for example 9a goes here
```

**Example 9b**:
  
 **Link**: 

**Comments**: 

```python
# Python code for example 9b goes here
```

---

## 10. Recursion

**Example 10a**:
  
 **Link**: 

**Comments**: 

```python
# Python code for example 10a goes here
```

**Example 10b**:
  
 **Link**: 

**Comments**: 

```python
# Python code for example 10b goes here
```

---

## 11. Context Managers

**Example 11a**:
  
 **Link**: 

**Comments**: 

```python
# Python code for example 11a goes here
```

**Example 11b**:
  
 **Link**: 

**Comments**: 

```python
# Python code for example 11b goes here
```

---

## 12. Map, Filter, and Zip Functions

**Example 12a**:
  
 **Link**: 

**Comments**: 

```python
# Python code for example 12a goes here
```

**Example 12b**:
  
 **Link**: 

**Comments**: 

```python
# Python code for example 12b goes here
```

---

## 13. Regex

**Example 13a**:
  
 **Link**: 

**Comments**: 

```python
# Python code for example 13a goes here
```

**Example 13b**:
  
 **Link**: 

**Comments**: 

```python
# Python code for example 13b goes here
```

---

