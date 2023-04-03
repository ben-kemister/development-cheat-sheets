---
title: File operations
tags:
- python
---

This page contains information and simple code examples about using Python to interact with the file system.

## File exists()

The `OS` module in Python lets us interact with the operating system. This comes under Python’s standard utility modules and gives a portable way to use the dependent functions of the operating system. The `exists()` function in Python exists in the `os.path` module, which is a submodule of the python’s OS module and is used to check if a particular file exists or not.

### Syntax

```python
from os.path import exists

file_exists = exists(path_to_file)
```

## Delete a file

To delete a file, you must import the `OS` module, and run its `os.remove()` function:

### Simple Example

Remove the file "demofile.txt":

```python
import os
os.remove("demofile.txt")
```

### Check if File exist

To avoid getting an error, you might want to check if the file exists before you try to delete it:

#### Example

Check if file exists, then delete it:

```python
import os
if os.path.exists("demofile.txt"):
  os.remove("demofile.txt")
else:
  print("The file does not exist")
```

## Create a directory

`os.mkdir()` method in Python is used to create a directory named path with the specified numeric mode. This method raise `FileExistsError` if the directory to be created already exists.

```python
# importing os module
import os
  
# Directory to create
directory = "test"
  
# Parent Directory path
parent_dir = "D:/projects/"
  
# Path
path = os.path.join(parent_dir, directory)
  
# Create the directory
# 'test' in
# 'D:/projects/'
os.mkdir(path) # unless a mode is supplied the file permissions will default to mode = 0o777
print("Directory '% s' created" % directory)
```

## Copy a file

The `shutil.copy()` method in Python is used to copy the files or directories from the source to the destination. The source must represent the file, and the destination may be a file or directory.

```python
import shutil
import os
src = r'C:\Work\office\employee.txt'
dst = r'C:\Work\employee.txt'
shutil.copyfile(src, dst)
```
