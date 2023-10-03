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

## Github projects 

Using production code from the following projects to find examples-

**flask -**  The Python micro framework for building web applications.
flask.palletsprojects.com 
https://github.com/pallets/flask
\
\
**pydoit -** Task management & automation tool 
https://github.com/pydoit/doit

## 1. Decorators
**Example 1a**:
  
 **Link**: https://github.com/pydoit/doit/blob/master/doit/control.py

**Comments**: This code defines a decorator named no_none that's designed to filter out None values from a generator. 

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
  
 **Link**: samples/compile_pathlib.py

**Comments**: task_compile function uses the generator mechanism in Python to produce a series of tasks for compiling C source files. Each task is represented as a dictionary with details about how to compile the source file and its dependencies.
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

**Example 2b**:
  
 **Link**: doit/samples/subtasks.py

**Comments**: When you call this function, it will not execute the loop immediately. Instead, it will return a generator object. As you iterate over this generator (e.g., using a for loop or the next function), it will execute the loop and yield tasks one by one.

```python
def task_create_file():
    for i in range(3):
        filename = "file%d.txt" % i
        yield {'name': filename,
               'actions': ["touch %s" % filename]}
```

**Example 2c**:
  
 **Link**: doit/control.py

**Comments**: 'step' method is designed to retrieve the next item from a generator (presumably an attribute of the class).

```python
    def step(self):
        """get node's next step"""
        try:
            return next(self.generator)
        except StopIteration:
            return None
```

---

## 3. Type Hinting

**Example 3a**: Examples on this file from flask repo
  
 **Link**: https://github.com/pallets/flask/blob/main/src/flask/app.py

**Comments**: The function signature includes a type hint indicating that the function returns a dictionary (dict).

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

---

## 4. Extension on class basics
### (Class Attributes, Class Methods, Static Methods)

**Example 4a**: class attributes
  
 **Link**: flask/tests/test_cli.py

**Comments**: 

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
flask/examples/tutorial/tests/test_db.py-
```python
def test_init_db_command(runner, monkeypatch):
    class Recorder:
        called = False

    def fake_init_db():
        Recorder.called = True

    monkeypatch.setattr("flaskr.db.init_db", fake_init_db)
    result = runner.invoke(args=["init-db"])
    assert "Initialized" in result.output
    assert Recorder.called
```


**Example 4b**: class methods and static methods
  
 **Link**: doit/cmd_completion.py

**Comments**: 

```python
class TabCompletion(DoitCmdBase):
    """generate scripts for tab-completion

    If hardcode-tasks options is chosen it will get the task
    list from the current dodo file and include in the completion script.
    Otherwise the script will dynamically call `doit list` to get the list
    of tasks.

    If it is completing a sub-task (contains ':' in the name),
    it will always call doit while evaluating the options.

    """
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


    def _generate_bash(self, opt_values, pos_args):
        # some applications built with doit do not use dodo.py files
        for opt in self.get_options():
            if opt.name == 'dodoFile':
                get_dodo_part = bash_get_dodo
                pt_list_param = '--file="$dodof"'
                break
        else:
            get_dodo_part = ''
            pt_list_param = ''

        # dict with template values
        pt_bin_name = os.path.split(sys.argv[0])[1]
        tmpl_vars = {
            'pt_bin_name': pt_bin_name,
            'pt_cmds': ' '.join(sorted(self.cmds)),
            'pt_list_param': pt_list_param,
        }

        # if hardcode tasks
        if opt_values['hardcode_tasks']:
            if getattr(self.loader, 'API', 1) == 2:
                self.loader.setup(opt_values)
                self.loader.load_doit_config()
                self.task_list = self.loader.load_tasks(cmd=self, pos_args=pos_args)
            else:
                self.task_list, _ = self.loader.load_tasks(
                    self, opt_values, pos_args)
            task_names = (t.name for t in self.task_list if not t.subtask_of)
            tmpl_vars['pt_tasks'] = '"{0}"'.format(' '.join(sorted(task_names)))
        else:
            tmpl_list_cmd = "$({0} list {1} --quiet 2>/dev/null)"
            tmpl_vars['pt_tasks'] = tmpl_list_cmd.format(pt_bin_name,
                                                         pt_list_param)

        # case statement to complete sub-commands
        cmds_args = []
        for name in sorted(self.cmds):
            cmd_class = self.cmds[name]
            cmd = cmd_class(**self.init_kwargs)
            cmds_args.append(self._bash_cmd_args(cmd))
        comp_subcmds = ("\n    case ${words[1]} in\n"
                        + "".join(cmds_args)
                        + "\n    esac\n")

        template = Template(
            bash_start + bash_opt_file + get_dodo_part
            + bash_task_list + bash_first_arg
            + comp_subcmds + bash_end)
        self.outstream.write(template.safe_substitute(tmpl_vars))


    @staticmethod
    def _zsh_arg_line(opt):
        """create a text line for completion of a command arg"""
        # '(-c|--continue)'{-c,--continue}'[continue executing tasks...]' \
        # '--db-file[file used to save successful runs]' \
        if opt.short and opt.long:
            tmpl = ('"(-{0.short}|--{0.long})"{{-{0.short},--{0.long}}}"'
                    '[{help}]" \\')
        elif not opt.short and opt.long:
            tmpl = '"--{0.long}[{help}]" \\'
        elif opt.short and not opt.long:
            tmpl = '"-{0.short}[{help}]" \\'
        else:  # without short or long options cant be really used
            return ''
        ohelp = opt.help.replace(']', r'\]').replace('"', r'\"')
        return tmpl.format(opt, help=ohelp).replace('\n', ' ')


    @classmethod
    def _zsh_arg_list(cls, cmd):
        """return list of arguments lines for zsh completion"""
        args = []
        for opt in cmd.get_options():
            args.append(cls._zsh_arg_line(opt))
        if 'TASK' in cmd.doc_usage:
            args.append("'*::task:(($tasks))'")
        if 'COMMAND' in cmd.doc_usage:
            args.append("'::cmd:(($commands))'")
        return args

    @classmethod
    def _zsh_cmd_args(cls, cmd):
        """create the content for "case" statement with all command options """
        arg_lines = cls._zsh_arg_list(cmd)
        tmpl = """
      ({cmd_name})
          _command_args=(
            {args_body}
            ''
        )
      ;;
"""
        args_body = '\n            '.join(arg_lines)
        return tmpl.format(cmd_name=cmd.name, args_body=args_body)


    # TODO:
    # detect correct dodo-file location
    # complete sub-tasks
    # task options
    def _generate_zsh(self, opt_values, pos_args):
        # deal with doit commands
        cmds_desc = []
        cmds_args = []
        for name in sorted(self.cmds):
            cmd_class = self.cmds[name]
            cmd = cmd_class(**self.init_kwargs)
            cmds_desc.append("    '{0}: {1}'".format(cmd.name, cmd.doc_purpose))
            cmds_args.append(self._zsh_cmd_args(cmd))

        template_vars = {
            'pt_bin_name': sys.argv[0].split('/')[-1],
            'pt_cmds': '\n    '.join(cmds_desc),
            'pt_cmds_args': '\n'.join(cmds_args),
        }

        if opt_values['hardcode_tasks']:
            if getattr(self.loader, 'API', 1) == 2:
                self.loader.setup(opt_values)
                self.loader.load_doit_config()
                self.task_list = self.loader.load_tasks(cmd=self, pos_args=pos_args)
            else:
                self.task_list, _ = self.loader.load_tasks(
                    self, opt_values, pos_args)
            lines = []
            for task in self.task_list:
                if not task.subtask_of:
                    lines.append("'{0}: {1}'".format(task.name, task.doc))
            template_vars['pt_tasks'] = '(\n{0}\n)'.format(
                '\n'.join(sorted(lines)))
        else:
            tmp_tasks = Template(
                '''("${(f)$($pt_bin_name list --template '{name}: {doc}')}")''')
            template_vars['pt_tasks'] = tmp_tasks.safe_substitute(template_vars)


        template = Template(zsh_start)
        self.outstream.write(template.safe_substitute(template_vars))

```

**Example 4c**: static method
  
 **Link**: flask/tests/test_cli.py

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

    class Module:
        @staticmethod
        def make_app():
            return Flask("appname")

    app = find_best_app(Module)
    assert isinstance(app, Flask)
    assert app.name == "appname"

    class Module:
        myapp = Flask("appname1")

        @staticmethod
        def create_app():
            return Flask("appname2")

    assert find_best_app(Module) == Module.myapp

    class Module:
        myapp = Flask("appname1")

        @staticmethod
        def create_app():
            return Flask("appname2")

    assert find_best_app(Module) == Module.myapp

    class Module:
        pass

    pytest.raises(NoAppException, find_best_app, Module)

    class Module:
        myapp1 = Flask("appname1")
        myapp2 = Flask("appname2")

    pytest.raises(NoAppException, find_best_app, Module)

    class Module:
        @staticmethod
        def create_app(foo, bar):
            return Flask("appname2")

    pytest.raises(NoAppException, find_best_app, Module)

    class Module:
        @staticmethod
        def create_app():
            raise TypeError("bad bad factory!")

    pytest.raises(TypeError, find_best_app, Module)
```

---

## 5. Class Inheritance

**Example 5a**:
  
 **Link**: 

**Comments**: 

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

**Example 5b**:
  
 **Link**: 

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
  
 **Link**: doit/cmd_base.py

**Comments**: 

```python
class Command(object):
    """third-party should subclass this for commands that do no use tasks

    :cvar name: (str) name of sub-cmd to be use from cmdline
    :cvar doc_purpose: (str) single line cmd description
    :cvar doc_usage: (str) describe accepted parameters
    :cvar doc_description: (str) long description/help for cmd
    :cvar cmd_options:
          (list of dict) see cmdparse.CmdOption for dict format
    """

    # if not specified uses the class name
    name = None

    # doc attributes, should be sub-classed
    doc_purpose = ''
    doc_usage = ''
    doc_description = None  # None value will completely omit line from doc

    # sequence of dicts
    cmd_options = tuple()

    # `execute_tasks` indicates whether this command execute task's actions.
    # This is used by the loader to indicate when delayed task creation
    # should be used.
    execute_tasks = False

    def __init__(self, config=None, bin_name='doit', opt_vals=None, **kwargs):
        """configure command

        :param bin_name: str - name of command line program
        :param config: dict

        Set extra configuration values, this vals can come from:
         * directly passed when using the API - through DoitMain.run()
         * from an INI configuration file
        """
        self.bin_name = bin_name
        self.name = self.get_name()
        # config includes all option values and plugins
        self.config = config if config else {}
        self._cmdparser = None
        # option values (i.e. loader options)
        self.opt_vals = opt_vals if opt_vals else {}

        # config_vals contains cmd option values
        self.config_vals = {}
        if 'GLOBAL' in self.config:
            self.config_vals.update(self.config['GLOBAL'])
        if self.name in self.config:
            self.config_vals.update(self.config[self.name])

        # Use post-mortem PDB in case of error loading tasks.
        # Only available for `run` command.
        self.pdb = False


    @classmethod
    def get_name(cls):
        """get command name as used from command line"""
        return cls.name or cls.__name__.lower()

    @property
    def cmdparser(self):
        """get CmdParser instance for this command

        initialize option values:
          - Default are taken from harded option definition
          - Defaults are overwritten from user's cfg (INI) file
        """
        if not self._cmdparser:
            self._cmdparser = CmdParse(self.get_options())
            self._cmdparser.overwrite_defaults(self.config_vals)
        return self._cmdparser


    def get_options(self):
        """@reutrn list of CmdOption
        """
        return [CmdOption(opt) for opt in self.cmd_options]


    def execute(self, opt_values, pos_args):  # pragma: no cover
        """execute command
        :param opt_values: (dict) with cmd_options values
        :param pos_args: (list) of cmd-line positional arguments
        """
        raise NotImplementedError()


    def parse_execute(self, in_args):
        """helper. just parse parameters and execute command

        @args: see method parse
        @returns: result of self.execute
        """
        params, args = self.cmdparser.parse(in_args)
        self.pdb = params.get('pdb', False)
        params.update(self.opt_vals)
        return self.execute(params, args)

    def help(self):
        """return help text"""
        text = []
        text.append("PURPOSE")
        text.extend(_wrap(self.doc_purpose, 4))

        text.append("\nUSAGE")
        usage = "{} {} {}".format(self.bin_name, self.name, self.doc_usage)
        text.extend(_wrap(usage, 4))

        text.append("\nOPTIONS")
        options = defaultdict(list)
        for opt in self.cmdparser.options:
            options[opt.section].append(opt)
        for section, opts in sorted(options.items()):
            section_name = '\n{}'.format(section or self.name)
            text.extend(_wrap(section_name, 4))
            for opt in opts:
                # ignore option that cant be modified on cmd line
                if not (opt.short or opt.long):
                    continue
                text.extend(_wrap(opt.help_param(), 6))
                # TODO It should always display option's default value
                opt_help = opt.help % {'default': opt.default}
                opt_choices = opt.help_choices()
                opt_config = 'config: {}'.format(opt.name)
                if opt.env_var:
                    opt_env = ', environ: {}'.format(opt.env_var)
                else:
                    opt_env = ''
                desc = '{} {} ({}{})'.format(opt_help, opt_choices,
                                             opt_config, opt_env)
                text.extend(_wrap(desc, 12))

                # print bool inverse option
                if opt.inverse:
                    text.extend(_wrap('--{}'.format(opt.inverse), 6))
                    text.extend(_wrap('opposite of --{}'.format(opt.long), 12))

        if self.doc_description is not None:
            text.append("\n\nDESCRIPTION")
            text.extend(_wrap(self.doc_description, 4))
        return "\n".join(text)

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

**Comments**: flask/tests/test_json.py

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

**Example 9b**:
  
 **Link**: doit/action.py

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

**Example 12c**: zip, 
  
 **Link**: 

**Comments**: 

```python
                    for tab, tab_panel in zip(tabs, tab_panels):
                        tab_name = tab.get_text(strip=True)
                        yield f"{tab_name}\n"
                        yield from get_text(tab_panel)
```
---

## 13. Regex

**Example 13a**:
  
 **Link**: langchain/ingest.py

**Comments**: 

```python

def simple_extractor(html: str) -> str:
    soup = BeautifulSoup(html, "lxml")
    return re.sub(r"\n\n+", "\n\n", soup.text).strip()
```

**Example 13b**:
  
 **Link**: scrapy/commands/startproject.py

**Comments**: 

```python
        if not re.search(r"^[_a-zA-Z]\w*$", project_name):
            print(
                "Error: Project names must begin with a letter and contain"
                " only\nletters, numbers and underscores"
            )
```


**Example 13b**:
  
 **Link**: scrapy/docs/utils/linkfix.py

**Comments**: This code seems to be related to fixing broken links in some output generated by a tool named linkcheck.

The code uses regular expressions to parse the output of linkcheck. The pattern line_re is designed to match lines that look like standard linkcheck output. The code then iterates over each line of the linkcheck output and tries to match it with the regex pattern. If a match is found, it processes the line to fix the links.

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

