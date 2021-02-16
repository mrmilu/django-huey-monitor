# django-huey-monitor

Django based tool for monitoring [huey task queue](https://github.com/coleifer/huey)

Current implementation will just store all Huey task signals into the database
and display them in the Django admin.


## Quickstart

```bash
pip install django-huey-monitor
```

```python
INSTALLED_APPS = [
    #...
    'huey_monitor',
    #...
]
```

Note: You must normally not change your Huey tasks.

### Collect main-/sub-tasks

Huey monitor model can store information about task hierarchy. But this information can't be collected automatically.
You have to store these information in your Task code.

e.g.:

```python
@task(context=True)
def sub_task(task, parent_task_id, chunk_data):
    # Save the task hierarchy by:
    TaskModel.objects.set_parent_task(
        main_task_id=parent_task_id,
        sub_task_id=task.id,
    )
    # ... do something with e.g.: chunk_data ...


@task(context=True)
def main_task(task):
    for chunk_data in something:
        sub_task(parent_task_id=task.id, chunk_data=chunk_data)
```

Working example can be found in the test app here: [huey_monitor_tests/test_app/tasks.py](https://github.com/boxine/django-huey-monitor/blob/master/huey_monitor_tests/test_app/tasks.py)


## run test project

Note: You can quickly test Huey Monitor with the test project, e.g:

```bash
~/django-huey-monitor$ ./manage.sh run_testserver
```
or in an isolated Docker container:
```bash
~/django-huey-monitor$ make up
```
More info see below.

# Screenshots

### 2020-12-16-v002-change-list.png

![2020-12-16-v002-change-list.png](https://raw.githubusercontent.com/boxine/django-huey-monitor/gh-pages/2020-12-16-v002-change-list.png)

### 2020-12-16-v002-task-details1.png

![2020-12-16-v002-task-details1.png](https://raw.githubusercontent.com/boxine/django-huey-monitor/gh-pages/2020-12-16-v002-task-details1.png)

### 2020-12-16-v002-task-details2.png

![2020-12-16-v002-task-details2.png](https://raw.githubusercontent.com/boxine/django-huey-monitor/gh-pages/2020-12-16-v002-task-details2.png)



## developing

* install docker
* clone the project
* start the container

To start developing e.g.:

```bash
~$ git clone https://github.com/boxine/django-huey-monitor.git
~$ cd django-huey-monitor
~/django-huey-monitor$ make
help                 List all commands
install-poetry       install or update poetry via pip
install              install via poetry
update               Update the dependencies as according to the pyproject.toml file
lint                 Run code formatters and linter
fix-code-style       Fix code formatting
tox-listenvs         List all tox test environments
tox                  Run pytest via tox with all environments
pytest               Run pytest
pytest-ci            Run pytest with CI settings
publish              Release new version to PyPi
makemessages         Make and compile locales message files
clean                Remove created files from the test project (e.g.: SQlite, static files)
build                Update/Build docker services
up                   Start docker containers
down                 Stop all containers
shell_django         go into a interactive bash shell in Django container
shell_huey           go into a interactive bash shell in Huey worker container
logs                 Display and follow docker logs
reload_django        Reload the Django dev server
reload_huey          Reload the Huey worker
restart              Restart the containers
fire_test_tasks      Call "fire_test_tasks" manage command to create some Huey Tasks

~/django-huey-monitor$ make install-poetry
~/django-huey-monitor$ make install
~/django-huey-monitor$ make up
```


It's also possible to run the test setup with SQLite and Huey immediate setup
without docker:

```bash
~$ git clone https://github.com/boxine/django-huey-monitor.git
~$ cd django-huey-monitor
~/django-huey-monitor$ ./manage.sh run_testserver
```


## License

[GPL](LICENSE). Patches welcome!


## Links

* https://pypi.org/project/django-huey-monitor/
