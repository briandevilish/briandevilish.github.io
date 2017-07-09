---
title: Python - using os walk to navigate directory
layout: default
---

{{ page.title }}
================
<head>
<style>
table, th, td {
    border: 1px solid black;
    border-collapse: collapse;
    margin: 5px 0;
    text-align: left;
    vertical-align: top;
}
th { background-color: #99ccff; }
tr { background-color: #e6f2ff; }
pre {
    display: block;
    font-family: monospace;
    white-space: pre;
    margin: 1em 0;
}
</style>
</head>


I have been using home NAS (QNAP) configured with RAID-1 to backup my pictures, works, etc. RAID-1 provides resiliency with 2 disk drives data mirroring but no parity/striping. To backup my stuff, rather than using backup software or using rsync, I wanted to have something simple that I have control, hence I choosen to write my own back-up script in Python. Python because it is simple and I am on Ubuntu which is pre-installed.

For the backup to work I need to be able to specify the source (local machine) and destination (nas) directory. I will also needs the below functionality: 
<ul>
  <li>only back-up the top directory i.e. excluding subdirectory</li>
  <li>exclude some files extension</li>
  <li>exclude some sub-directory</li>
</ul>

To start I would need a function to be able to traverse the directory tree. Python os module provides an operating system dependent functionality. It has a handy function to do just that, aptly named [os.walk](https://docs.python.org/2/library/os.html).

os.walk accepts four arguments as shown in table below. 
<table border="1">
<tr>
 <th>Argument</th>
 <th>Optional/Mandatory</th>
 <th>Default Value</th>
 <th>Explanation</th>
</tr>
<tr>
 <td>top</td>
 <td>Mandatory</td>
 <td>Not applicable</td>
 <td>Path of the top level directory in which to start traversing.</td>
</tr>
<tr>
 <td>topdown</td>
 <td>Optional</td>
 <td>True</td>
 <td>If True, then the traversal happens from top-to-bottom otherwise it will be done from bottom-to-top. This is useful when using *os.rmdir* which only allow removal of empty directory. Hence, the bottom-to-top allows removal of all files/subdirectories before removal of parent directory.</td>
</tr>
<tr>
 <td>onerror</td>
 <td>Optional</td>
 <td>None</td>
 <td>If specified should be a function, it will be called with one argument, an *OSError* instance.</td>
</tr>
<tr>
 <td>followlinks</td>
 <td>Optional</td>
 <td>False</td>
 <td>If True, then it will traverse symlinks on systems that support them.</td>
</tr>
</table>

It returns a 3-tuple i.e. (dirpath, dirnames, filenames). 
<ol>
  <li>*dirpath* is a string of the currently traversed fully qualified path, as it moves to subdirectory the dirpath change to reflect it. </li>
  <li>*dirnames* is a list of names of the subdirectories in current *dirpath*. Note it is not fully qualified path of each subdirectories but only the subdirectory name itself. To get a fully qualified path for subdirectory do *os.path.join(dirpath, name)*. Handy to know that *walk()* will only recurse into the subdirectories whose names remain in *dirnames*. You will see example below on how I used this to ignore some subdirectories.</li>
  <li>*filenames* is a list of non-directory files in current *dirpath*.</li>
</ol>
Note: I explicitly used *current* dirpath, as the dirpath changes as we traverse the directory tree.

What is the order of entries listed using os.walk?
As per Python 2.7 source code [os.walk](https://github.com/python/cpython/blob/2.7/Lib/os.py) is relying on *os.listdir* for listing. *os.listdir* return a list of entries in the directory in an arbitrary order. It does not include the special entries '.' and '..' even if they are present in the directory. However, if you are using other version of Python e.g. 3.0, then the implementation might have been swapped with a [faster](https://www.python.org/dev/peps/pep-0471/#os-walk) function using *os.scandir*. So if you would like to have an ascending/descending order, you would need to implement sorting yourself.

**Let's dive into the code**

A sample directory structure as below:
<pre>
|-Top-Level-Dir-1
|--Sub-Directory-2-1
|---Sub-Directory-2-1-1
|---Sub-Directory-2-1-2
|----file-2-1-2.txt
|---file-2-1.txt
|--Sub-Directory-2-2
|--file-1.txt
</pre>

With a simple Python code:
<pre lang="ruby">
import os
for root, dirs, files in os.walk('/home/brian/sandbox/Top-Level-Dir-1'):
  print("root: %s\ndirs: %s\nfiles: %s" %(root, dirs, files))
  print("...next iteration...")
</pre>

Will have the following output:
<pre>
root: /home/brian/Top-Level-Dir-1
dirs: ['Sub-Directory-2-1', 'Sub-Directory-2-2']
files: ['file-1.txt']
...next iteration...
root: /home/brian/Top-Level-Dir-1/Sub-Directory-2-1
dirs: ['Sub-Directory-2-1-2', 'Sub-Directory-2-1-1']
files: ['file-2-1.txt']
...next iteration...
root: /home/brian/Top-Level-Dir-1/Sub-Directory-2-1/Sub-Directory-2-1-2
dirs: []
files: ['file-2-1-2.txt']
...next iteration...
root: /home/brian/Top-Level-Dir-1/Sub-Directory-2-1/Sub-Directory-2-1-1
dirs: []
files: []
...next iteration...
root: /home/brian/Top-Level-Dir-1/Sub-Directory-2-2
dirs: []
files: []
...next iteration...
</pre>

**Another sample - to walk through only directory not in the ignore list**
<pre>
def print_exc_ignore_dir(fullpath_dir, backup_dir, ignore_dir_patterns):
  for root, dirs, files in os.walk(fullpath_dir, topdown=True):
    dirs[:] = [dirname for dirname in dirs if dirname not in ignore_dir_patterns]
    print("root: %s\ndirs: %s\nfiles: %s" %(root, dirs, files))
</pre>

dirs[:] is the slice notation in which it refers to a copy of whole array.

In this example, a list of ignored directory name (not fully qualified path) is supplied to the function.
Ignored directory name is removed from the *dirnames*, in this case named *dirs*.

If ignore directory needs to be fully qualified, then you will have to use *os.path.join*. To ensure the comparison is case insenstive, the ignore directory list is converted to all lower case.

<pre>
from os.path import join
import os

ignore_dir_patterns=['/home/brian/Top-Level-Dir-1/Sub-Directory-2-1/Sub-Directory-2-1-2/java']
ignore_dir_patterns=[d.lower() for d in ignore_dir_patterns]
for root, dirs, files in os.walk('/home/brian/Top-Level-Dir-1'):
  dirs[:] = [dirname for dirname in dirs if os.path.join(root, dirname).lower() not in ignore_dir_patterns]
  print("root: %s\ndirs: %s\nfiles: %s" %(root, dirs, files))
  print("...next iteration...")
</pre>






