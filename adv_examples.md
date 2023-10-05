## Real World Examples of
1. [Decorators](#1-decorators)
2. [Generators](#2-generators)
3. [Type Hinting](#3-type-hinting)
4. [Extension on class basics (Class Attributes, Class Methods, Static Methods)](#4-extension-on-class-basics)
5. [Class Inheritance](#5-class-inheritance)
6. [Duck Typing](#6-duck-typing)
7. [Properties](#7-properties)
8. [Private Attributes](#8-private-attributes)
9. [Lambda](#9-lambda)
10. [Recursion](#10-recursion)
11. [Context Managers](#11-context-managers)
12. [Map, Filter, and Zip Functions](#12-map-filter-and-zip-functions)
13. [Regex](#13-regex)

## Github Projects 

Using production code from the following projects-

- **Flask**: A lightweight Python micro web framework, perfect for quick setups yet scalable for complex applications ([Official Site](https://flask.palletsprojects.com) | [GitHub](https://github.com/pallets/flask)).

- **pydoit**: A simple task management and automation tool that facilitates parallel execution of defined tasks and dependencies ([Official Site](https://pydoit.org) | [GitHub](https://github.com/pydoit/doit)).

- **LangChain App**: A locally-hosted chatbot for querying and interacting with LangChain documentation, built with LangChain, FastAPI, and Next.js ([Official Site](https://www.langchain.com) | [GitHub](https://github.com/langchain-ai/chat-langchain)).

- **Scrapy**: An open-source web crawling framework in Python, ideal for data extraction from websites and large crawling projects ([Official Site](https://scrapy.org) | [GitHub](https://github.com/scrapy/scrapy)).



## 1. Decorators

### *Example 1a*:
  
**Link**: https://github.com/pydoit/doit/blob/master/doit/control.py (Lines: 341-348)

**Comments**: 

This code defines a decorator named no_none that's designed to filter out None values from a generator. 

Inside the _func function, the generator decorated is called with any arguments (*args and **kwargs) passed to it. For each value produced by the decorated generator, if the value is not None, it is yielded by the _func generator.

 
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
---
### *Example 1b*:
  
**Link**: https://github.com/pydoit/doit/blob/master/doc/samples/my_tasks.py (Lines: 1-23)

**Comments**: 

The main purpose of this decorator seems to be to attach metadata to functions regarding their input and output files. This metadata can later be accessed using the task_metadata attribute of the decorated functions. This can be useful in scenarios where you want to keep track of the files a function interacts with, especially in data processing or pipeline workflows.

The 'simple' function is decorated without any parameters. So, an empty task_metadata dictionary is attached to it.

The 'pre' function is decorated with a parameter output which is a list containing 'my_input.txt'. The task_metadata attribute of the pre function will be {'output': ['my_input.txt']}.

 
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


## 2. Generators


### *Example 2a*:
  
**Link**: https://github.com/pydoit/doit/blob/master/doc/samples/compile_pathlib.py (Lines: 1-13)

**Comments**: 

task_compile function uses the generator mechanism in Python to produce a series of tasks for compiling C source files. Each task is represented as a dictionary with details about how to compile the source file and its dependencies.
\
The use of yield makes task_compile a generator function. Instead of returning a single value (like a list of tasks), it yields a series of values (each task, one by one).

 
```python
from pathlib import Path

def task_compile():
    working_directory = Path('.')
    # Path.glob returns an iterator so turn it into a list
    headers = list(working_directory.glob('*.h'))
    for source_file in working_directory.glob('*.c'):
        object_file = source_file.with_suffix('.o')
        yield {
            'name': object_file.name,
            'actions': [['cc', '-c', source_file]],
            'file_dep': [source_file] + headers,
            'targets': [object_file],
        }
```
---
### *Example 2b*:
  
**Link**: https://github.com/pydoit/doit/blob/master/doc/samples/subtasks.py (Lines: 1-5)

**Comments**: 

When you call this function, it will not execute the loop immediately. Instead, it will return a generator object. As you iterate over this generator (e.g., using a for loop or the next function), it will execute the loop and yield tasks one by one.

 
```python
def task_create_file():
    for i in range(3):
        filename = "file%d.txt" % i
        yield {'name': filename,
               'actions': ["touch %s" % filename]}
```
---
### *Example 2c*:
  
**Link**: https://github.com/pydoit/doit/blob/master/doit/control.py (Lines: 333-338)

**Comments**: 

'step' method is designed to retrieve the next item from a generator (presumably an attribute of the class).

 
```python
def step(self):
    """get node's next step"""
    try:
        return next(self.generator)
    except StopIteration:
        return None
```


## 3. Type Hinting

### *Example 3a*: 
  
**Link**: https://github.com/pallets/flask/blob/main/src/flask/app.py (Lines: 484-494)

**Comments**: 

The function signature includes a type hint indicating that the function returns a dictionary (dict).

 
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

### *Example 3b*: 

**Link**: https://github.com/pallets/flask/blob/main/src/flask/app.py (Lines: 336-346)

**Comments**: 

- **self**: 
  This is a reference to the instance of the class to which this method belongs.
  It doesn't have a type hint because it's a convention in Python for instance methods.
  
- **resource: str**: 
  This indicates that the `resource` parameter is expected to be of type `str` (a string).
  
- **mode: str = "rb"**: 
  This indicates that the `mode` parameter is expected to be of type `str` (a string).
  It has a default value of "rb".
  
- **-> t.IO[t.AnyStr]**: 
  The function is expected to return an object that matches the type `t.IO[t.AnyStr]`.


 
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


## 4. Extension on class basics

### (Class Attributes, Class Methods, Static Methods)

### *Example 4a*: class attributes
  
**Link**: https://github.com/pallets/flask/blob/master/tests/test_cli.py (Lines: 231-244)

**Comments**: 

A nested class MockCtx is defined within the test_get_version function. This class has two class attributes: resilient_parsing and color. 

 
```python
def test_get_version(test_apps, capsys):
    class MockCtx:
        resilient_parsing = False
        color = None

        def exit(self):
            return

    ctx = MockCtx()
    get_version(ctx, None, "test")
    out, err = capsys.readouterr()
    assert f"Python {platform.python_version()}" in out
    assert f"Flask {importlib.metadata.version('flask')}" in out
    assert f"Werkzeug {importlib.metadata.version('werkzeug')}" in out

```
---
### *Example 4b*: class methods 
  
**Link**: https://github.com/pydoit/doit/blob/master/doit/cmd_completion.py (Lines: 31-77)

**Comments**: 

 
```python
class TabCompletion(DoitCmdBase):

    doc_purpose = "generate script for tab-completion"
    doc_usage = ""
    doc_description = None

    cmd_options = (opt_shell, opt_hardcode_tasks, )

    def __init__(self, cmds=None, **kwargs):
        super(TabCompletion, self).__init__(cmds=cmds, **kwargs)
        self.init_kwargs = kwargs
        self.init_kwargs['cmds'] = cmds
        if cmds:
            self.cmds = cmds.to_dict()  # dict name - Command class

    def execute(self, opt_values, pos_args):
        if opt_values['shell'] == 'bash':
            self._generate_bash(opt_values, pos_args)
        elif opt_values['shell'] == 'zsh':
            self._generate_zsh(opt_values, pos_args)
        else:
            msg = 'Invalid option for --shell "{0}"'
            raise InvalidCommand(msg.format(opt_values['shell']))

    @classmethod
    def _bash_cmd_args(cls, cmd):
        """return case item for completion of specific sub-command"""
        comp = []
        if 'TASK' in cmd.doc_usage:
            comp.append('${tasks}')
        if 'COMMAND' in cmd.doc_usage:
            comp.append('${sub_cmds}')
        if comp:
            completion = '-W "{0}"'.format(' '.join(comp))
        else:
            completion = '-f'  # complete file
        return bash_subcmd_arg.format(cmd_name=cmd.name, completion=completion)



```
---
### *Example 4c*: static method
  
**Link**: https://github.com/pallets/flask/blob/master/tests/test_cli.py (Lines: 48-80)

**Comments**: 

 
```python
def test_find_best_app(test_apps):
    class Module:
        app = Flask("appname")

    assert find_best_app(Module) == Module.app

    class Module:
        application = Flask("appname")

    assert find_best_app(Module) == Module.application

    class Module:
        myapp = Flask("appname")

    assert find_best_app(Module) == Module.myapp

    class Module:
        @staticmethod
        def create_app():
            return Flask("appname")

    app = find_best_app(Module)
    assert isinstance(app, Flask)
    assert app.name == "appname"

    class Module:
        @staticmethod
        def create_app(**kwargs):
            return Flask("appname")

    app = find_best_app(Module)
    assert isinstance(app, Flask)
    assert app.name == "appname"

```


## 5. Class Inheritance

### *Example 5a*:
  
**Link**: https://github.com/pydoit/doit/blob/master/doit/cmd_base.py (Lines: 432-448)

**Comments**: 

The class DoitCmdBase inherits from Command as indicated by the syntax class DoitCmdBase(Command). 


 
```python
class DoitCmdBase(Command):
    """
    subclass must define:
    cmd_options => list of option dictionary (see CmdOption)
    _execute => method, argument names must be option names
    """
    base_options = (opt_depfile, opt_backend, opt_codec,
                    opt_check_file_uptodate)

    def __init__(self, task_loader, cmds=None, **kwargs):
        super(DoitCmdBase, self).__init__(**kwargs)
        self.sel_tasks = None  # selected tasks for command
        self.sel_default_tasks = True  # False if tasks were specified from command line
        self.dep_manager = None
        self.outstream = sys.stdout
        self.loader = task_loader
        self._backends = self.get_backends()

```
---
### *Example 5b*:
  
**Link**: https://github.com/pydoit/doit/blob/master/doc/samples/custom_loader.py (Lines: 3-25)

**Comments**: 

 
```python
import sys

from doit.task import dict_to_task
from doit.cmd_base import TaskLoader2 # IMPORTING CLASS
from doit.doit_cmd import DoitMain

my_builtin_task = {
    'name': 'sample_task',
    'actions': ['echo hello from built in'],
    'doc': 'sample doc',
}


class MyLoader(TaskLoader2): # CLASS INHERITANCE
    def setup(self, opt_values):
        pass

    def load_doit_config(self):
        return {'verbosity': 2}

    def load_tasks(self, cmd, pos_args):
        task_list = [dict_to_task(my_builtin_task)]
        return task_list

```


## 6. Duck Typing

### *Example 6a*:
  
**Link**: https://github.com/pydoit/doit/blob/master/doc/samples/custom_loader.py (Lines: 432-448)

**Comments**: 

Subclasses of DoitCmdBase can provide specific implementations of the _execute method as mentioned in the docstring, showcasing polymorphism where the exact behavior of _execute can vary based on the subclass, while the calling code can interact with instances of DoitCmdBase and its subclasses in a uniform manner.

 
```python
class DoitCmdBase(Command):
    """
    subclass must define:
    cmd_options => list of option dictionary (see CmdOption)
    _execute => method, argument names must be option names
    """
    base_options = (opt_depfile, opt_backend, opt_codec,
                    opt_check_file_uptodate)

    def __init__(self, task_loader, cmds=None, **kwargs):
        super(DoitCmdBase, self).__init__(**kwargs)
        self.sel_tasks = None  # selected tasks for command
        self.sel_default_tasks = True  # False if tasks were specified from command line
        self.dep_manager = None
        self.outstream = sys.stdout
        self.loader = task_loader
        self._backends = self.get_backends()

```
---
### *Example 6b*:
  
**Link**: 

**Lines**: 

**Comments**: 

 
```python
# Python code for example 6b goes here
```


## 7. Properties

### *Example 7a*:
  
**Link**: https://github.com/pydoit/doit/blob/master/doit/cmd_base.py (Lines: 51-130)

**Comments**:   

The @property decorator is used to define a method as a "getter" for a property named cmdparser. The method cmdparser is defined to lazily initialize and return a CmdParser instance, which is stored in the private attribute _cmdparser.
Accessing cmdparser as an attribute (self.cmdparser) will invoke the method cmdparser(self) and return the _cmdparser instance. This encapsulates the logic for creating and caching the CmdParser instance behind a simple attribute access, making it easier and cleaner to use.

 
```python
class Command(object):
    """Base class for creating commands.

    :cvar name: (str) name of sub-cmd to be used from cmdline
    """

    name = None  # if not specified uses the class name

    def __init__(self, bin_name='doit', config=None):
        """Configure command.

        :param bin_name: str - name of command line program
        :param config: dict - configuration dictionary
        """
        self.bin_name = bin_name
        self.name = self.get_name()
        self.config = config if config else {}
        self._cmdparser = None  # CmdParser instance, initialized lazily

    @classmethod
    def get_name(cls):
        """Get command name as used from the command line."""
        return cls.name or cls.__name__.lower()

    @property
    def cmdparser(self):
        """Get CmdParser instance for this command.

        Initialize option values based on defaults and user's config file.
        """
        if not self._cmdparser:
            self._cmdparser = CmdParse(self.get_options())
            self._cmdparser.overwrite_defaults(self.config.get(self.name, {}))
        return self._cmdparser

    def get_options(self):
        """Return a list of CmdOption instances."""
        return [CmdOption(opt) for opt in self.cmd_options]

# Usage:
# Assuming CmdParse and CmdOption are defined elsewhere,
# create a subclass of Command and utilize the cmdparser property.


```


## 8. Private Attributes

### *Example 8a*:
  
**Link**: https://github.com/langchain-ai/chat-langchain/blob/master/_scripts/evaluate_chains.py (Lines: 129-151)

**Comments**: 

The leading underscore in _get_llm_runs denotes that this method is intended to be private, meaning it's designed to be called only within the context of the CustomHallucinationEvaluator class or its subclasses.

 
```python
class CustomHallucinationEvaluator(RunEvaluator):
    @staticmethod
    def _get_llm_runs(run: Run) -> Run:
        runs = []
        for child in run.child_runs or []:
            if run.run_type == "llm":
                runs.append(child)
            else:
                runs.extend(CustomHallucinationEvaluator._get_llm_runs(child))

    def evaluate_run(
        self, run: Run, example: Example | None = None
    ) -> EvaluationResult:
        llm_runs = self._get_llm_runs(run)
        if not llm_runs:
            return EvaluationResult(key="hallucination", comment="No LLM runs found")
        if len(llm_runs) > 0:
            return EvaluationResult(
                key="hallucination", comment="Too many LLM runs found"
            )
        llm_run = llm_runs[0]
        messages = llm_run.inputs["messages"]
        langchain_load(json.dumps(messages))

```
---
### *Example 8b*:
  
**Link**: 

**Lines**: 

**Comments**: 

 
```python
# Python code for example 8b goes here
```


## 9. Lambda

### *Example 9a*: 
  
**Link**: https://github.com/pallets/flask/blob/master/tests/test_json.py (Lines: 176-188)

**Comments**: 

 
```python
def test_jsonify_uuid_types(app, client):
    """Test jsonify with uuid.UUID types"""

    test_uuid = uuid.UUID(bytes=b"\xDE\xAD\xBE\xEF" * 4)
    url = "/uuid_test"
    app.add_url_rule(url, url, lambda: flask.jsonify(x=test_uuid))

    rv = client.get(url)

    rv_x = flask.json.loads(rv.data)["x"]
    assert rv_x == str(test_uuid)
    rv_uuid = uuid.UUID(rv_x)
    assert rv_uuid == test_uuid
```
---
### *Example 9b*:
  
**Link**: https://github.com/pydoit/doit/blob/master/doit/action.py (Lines: 53-58)

**Comments**: 

 
```python
# use task meta information as extra_args
meta_args = {
    'task': lambda: task,
    'targets': lambda: list(task.targets),
    'dependencies': lambda: list(task.file_dep),
    'changed': lambda: list(task.dep_changed),
}

```


## 10. Recursion

### *Example 10a*:
  
**Link**: https://github.com/scrapy/scrapy/blob/master/scrapy/utils/misc.py (Lines: 89-108)

**Comments**: 

Through recursion, this function is able to load a module and all of its submodules, regardless of how deeply nested the submodule structure is. It does this by making recursive calls to walk_modules for each submodule that is also a package, accumulating the list of modules in the mods list, which is returned at the end.

 
```python
def walk_modules(path: str) -> List[ModuleType]:
    """Loads a module and all its submodules from the given module path and
    returns them. If *any* module throws an exception while importing, that
    exception is thrown back.

    For example: walk_modules('scrapy.utils')
    """

    mods: List[ModuleType] = []
    mod = import_module(path)
    mods.append(mod)
    if hasattr(mod, "__path__"):
        for _, subpath, ispkg in iter_modules(mod.__path__):
            fullpath = path + "." + subpath
            if ispkg:
                mods += walk_modules(fullpath)
            else:
                submod = import_module(fullpath)
                mods.append(submod)
    return mods

```
---
### *Example 10b*:
  
**Link**: 

**Lines**: 

**Comments**: 

 
```python
# Python code for example 10b goes here
```


## 11. Context Managers

### *Example 11a*:
  
**Link**: https://github.com/pallets/flask/blob/master/examples/tutorial/tests/conftest.py (Lines: 11-12)

**Comments**: 

By using a context manager, the file is automatically closed when exiting the with block, even if an exception occurs within the block.

 
```python
# read in SQL for populating test data
with open(os.path.join(os.path.dirname(__file__), "data.sql"), "rb") as f:
    _data_sql = f.read().decode("utf8")
```



## 12. Map, Filter, and Zip Functions

### *Example 12a*: Map
  
**Link**: https://github.com/scrapy/scrapy/blob/master/docs/conf.py  (Lines: 58-65)

**Comments**: 

The map function is a handy way to apply a function (in this case, str for string conversion) to each element of a collection (in this case, a tuple of version numbers).

 
```python
try:
    import scrapy

    version = ".".join(map(str, scrapy.version_info[:2]))
    release = scrapy.__version__
except ImportError:
    version = ""
    release = ""
```
---
### *Example 12b*: Filter
  
**Link**: https://github.com/langchain-ai/chat-langchain/blob/master/parser.py (Lines: 37-40)

**Comments**: 

filter returns an iterator that yields only the items from classes for which the lambda function returns True. In other words, it filters classes to include only the items that match the regular expression pattern r"language-\w+".

 
```python
language = next(
    filter(lambda x: re.match(r"language-\w+", x), classes),
    None,
)
```
---
### *Example 12c*: Zip
  
 **Link**: https://github.com/langchain-ai/chat-langchain/blob/master/parser.py (Lines: 76-79)

**Comments**: 

zip(tabs, tab_panels) combines the iterables tabs and tab_panels element-wise into tuples. Each tuple contains one element from tabs and one element from tab_panels, paired up based on their position.

 
```python
for tab, tab_panel in zip(tabs, tab_panels):
    tab_name = tab.get_text(strip=True)
    yield f"{tab_name}\n"
    yield from get_text(tab_panel)
```

## 13. Regex

### *Example 13a*:
  
 **Link**: https://github.com/langchain-ai/chat-langchain/blob/master/ingest.py (Lines: 53-55)

**Comments**: 

This expression uses the re.sub function from the re module to clean up the extracted text. Specifically, it is replacing occurrences of two or more consecutive newline characters (\n\n+) with exactly two newline characters (\n\n).

 
```python
def simple_extractor(html: str) -> str:
    soup = BeautifulSoup(html, "lxml")
    return re.sub(r"\n\n+", "\n\n", soup.text).strip()
```
---
### *Example 13b*:
  
 **Link**: https://github.com/scrapy/scrapy/blob/master/scrapy/commands/startproject.py (Lines: 45-49)

**Comments**: 

re.search(pattern, string) searches the string for any occurrence of the pattern. In this case, it searches project_name for a substring that matches the pattern.

 
```python
if not re.search(r"^[_a-zA-Z]\w*$", project_name):
    print(
        "Error: Project names must begin with a letter and contain"
        " only\nletters, numbers and underscores"
    )
```

---
### *Example 13c*:
  
 **Link**: https://github.com/scrapy/scrapy/blob/master/docs/utils/linkfix.py (Lines: 20-64)

**Comments**: 

This code seems to be related to fixing broken links in some output generated by a tool named linkcheck.

It uses regular expressions to parse the output of linkcheck. The pattern line_re is designed to match lines that look like standard linkcheck output. The code then iterates over each line of the linkcheck output and tries to match it with the regex pattern. If a match is found, it processes the line to fix the links.

Also the code uses context managers - The code uses the with statement to open a file, which ensures that the file is properly closed after its usage finishes.

 
```python
def main():
    # Used for remembering the file (and its contents)
    # so we don't have to open the same file again.
    _filename = None
    _contents = None

    # A regex that matches standard linkcheck output lines
    line_re = re.compile(r"(.*)\:\d+\:\s\[(.*)\]\s(?:(.*)\sto\s(.*)|(.*))")

    # Read lines from the linkcheck output file
    try:
        with Path("build/linkcheck/output.txt").open(encoding="utf-8") as out:
            output_lines = out.readlines()
    except OSError:
        print("linkcheck output not found; please run linkcheck first.")
        sys.exit(1)

    # For every line, fix the respective file
    for line in output_lines:
        match = re.match(line_re, line)

        if match:
            newfilename = match.group(1)
            errortype = match.group(2)
            # Broken links can't be fixed and
            # I am not sure what do with the local ones.
            if errortype.lower() in ["broken", "local"]:
                print("Not Fixed: " + line)
            else:
                # If this is a new file
                if newfilename != _filename:
                    # Update the previous file
                    if _filename:
                        Path(_filename).write_text(_contents, encoding="utf-8")
                    _filename = newfilename
                    # Read the new file to memory
                    _contents = Path(_filename).read_text(encoding="utf-8")

                _contents = _contents.replace(match.group(3), match.group(4))
        else:
            # We don't understand what the current line means!
            print("Not Understood: " + line)

```
---

