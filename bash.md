# bash notes

## bashrc

Adding a variable permanently to your terminal

## Append new value to the current value of a variable

For example, if the current value of **PYTHONPATH** is `/usr/local/lib/python3.9/site-packages`, and you set
`PYTHONPATH=/path/to/your/project:$PYTHONPATH`, the resulting value of **PYTHONPATH** will be `/path/to/your/project:
/usr/local/lib/python3.9/site-packages`. This will ensure that both directories are on the PYTHONPATH.

```bash
export PYTHONPATH=/path/to/your/project:$PYTHONPATH
```

## mkdir -p

The `mkdir -p` command is used in the Unix/Linux operating system to create a new directory or a new set of directories.
The `-p` flag tells the command to create any missing directories in the path.

Example: The `-p` flag ensures that both the "documents" directory and the "my_project" directory are created, even if 
"documents" directory does not exist.

```bash
mkdir -p documents/my_project
```
