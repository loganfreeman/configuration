ManagementUtility
---
```py
#!/usr/bin/env python
from __future__ import print_function, absolute_import

import os

from optparse import OptionParser
from django.core.management import ManagementUtility


def create_project(parser, options, args):
    # Validate args
    if len(args) < 2:
        parser.error("Please specify a name for your wagtail installation")
    elif len(args) > 3:
        parser.error("Too many arguments")

    project_name = args[1]
    try:
        dest_dir = args[2]
    except IndexError:
        dest_dir = None

    # Make sure given name is not already in use by another python package/module.
    try:
        __import__(project_name)
    except ImportError:
        pass
    else:
        parser.error("'%s' conflicts with the name of an existing "
                     "Python module and cannot be used as a project "
                     "name. Please try another name." % project_name)

    print("Creating a wagtail project called %(project_name)s" % {'project_name': project_name})

    # Create the project from the wagtail template using startapp

    # First find the path to wagtail
    import wagtail
    wagtail_path = os.path.dirname(wagtail.__file__)
    template_path = os.path.join(wagtail_path, 'project_template')

    # Call django-admin startproject
    utility_args = ['django-admin.py',
                    'startproject',
                    '--template=' + template_path,
                    '--ext=html,rst',
                    project_name]

    if dest_dir:
        utility_args.append(dest_dir)

    utility = ManagementUtility(utility_args)
    utility.execute()

    print("Success! %(project_name)s is created" % {'project_name': project_name})


COMMANDS = {
    'start': create_project,
}


def main():
    # Parse options
    parser = OptionParser(usage="Usage: %prog start project_name [directory]")
    (options, args) = parser.parse_args()

    # Find command
    try:
        command = args[0]
    except IndexError:
        parser.print_help()
        return

    if command in COMMANDS:
        COMMANDS[command](parser, options, args)
    else:
        parser.error("Unrecognised command: " + command)

if __name__ == "__main__":
    main()
```
INSTALLED_APPS
---
```py
#!/usr/bin/env python
import sys

from django.conf import settings
from django.core import management


def create_project():
    # Put mezzanine.conf in INSTALLED_APPS so call_command can find
    # our command,
    settings.configure()
    settings.INSTALLED_APPS = list(settings.INSTALLED_APPS) + ['mezzanine.bin']
    argv = sys.argv[:1] + ['mezzanine_project'] + sys.argv[1:]
    management.execute_from_command_line(argv)


if __name__ == "__main__":
    create_project()
```
Writing custom django-admin commands¶
---
To do this, just add a `management/commands` directory to the application. Django will register a manage.py command for each Python module in that directory whose name doesn’t begin with an underscore. 
```
polls/
    __init__.py
    models.py
    management/
        __init__.py
        commands/
            __init__.py
            _private.py
            closepoll.py
    tests.py
    views.py
```
In this example, the closepoll command will be made available to any project that includes the polls application in `INSTALLED_APPS`.

The `_private.py` module will not be available as a management command.

The `closepoll.py` module has only one requirement – it must define a class `Command` that extends `BaseCommand` or one of its subclasses.
