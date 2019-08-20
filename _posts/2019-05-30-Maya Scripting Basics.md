---
layout: post
title:  "Maya Scripting Basics"
date:   2019-05-30
categories: [Python]
tags: [Python]
icon: fa-code
---

# Table of Contents
### 1. [**Create, Query, and Edit Flags in Maya**](#1)
### 2. [**Get System Path of Maya**](#2)
### 3. [**Writing and Running an External Script**](#3)
### 4. [**Calling MEL Script with Python**](#4)

<a name="1"></a>

<br/><br/>

---

<br/><br/>

# 1. Create, Query, and Edit Flags in Maya

<br/>

* `"Create"` flag is only relevant when first running the command, such as when you initially create a polygonal primitive
eg: Create a Cube Command
```
myCube = cmds.polyCube()
```

* `"Query"` flag can be queried after the command has been run and can be used to retrieve information about something in the scene
eg: Use the query mode to store the new number of divisions to a variable
```
numOfDivisions = cmds.polyCube(myCube, query=True, subdivisionsX=True)
print(numOfDivisions)
# You should see "5.0" as the output.
```
* `"Edit"` flag can be edited after the fact
eg: Change the X subdivisions from the cube we created
```
cmds.polyCube(myCube, edit=True, subdivisionsX = 5) 
```

* `"Multiple"` flag can be used more than once in a single instance of the command (for example, to create specify multiple points when creating a curve)

<a name="2"></a>


<br/><br/>

--- 

<br/><br/>

# 2. Get System Path of Maya(Can store scripts)

<br/>

```python
import sys
pathList = sys.path
for path in pathList:
	print(path)
```

---

## if we want to store scripts in our own directory

1. create a python script

2. ```python
   import sys
   sys.path.append('directory path')
   ```

3. Let maya **execute script everytime when runing the program**, what we need to do is to save this file in 

   > Mac:

   ```
   ~/Library/Preference/Autodesk/maya/<version>/scripts
   ```

   > Windows:

   ```
   <drive>:\Documents and Settings\<username>\My Documents\maya<version>\scripts
   ```

<a name="3"></a>

<br/><br/>

---

<br/><br/>

# 3. Writing and Running an External Script

<br/>

When we want to runing an external script in maya, First thing is to make sure the script is in maya script folder, on **Windows**， it can be found in ```<drive>:\Documents and Settings\<username>\My Documents\maya<version>\scripts```, on **Mac** can be found in ```~/Library/Preference/Autodesk/maya/<version>/scripts```

when import the script make sure to use reload() function to clear the caches.

### For example:

### if we have a python file with following code:
```
import maya.cmds as cmds
print("Imported the script!")
def makeObject():
    cmds.polyCube()
    print("Made a cube!")
```

### We can call the function by running the following in maya
```
import myScript
reload(myScript)
myScript.makeObject()
```

<a name="4"></a>

<br/><br/>

--- 

<br/><br/>

# 4. Calling MEL Script with Python

<br/>

Maya offers **two** different languages with which to create custom functionality via scripting—both Maya Embedded Language (`MEL`) scripts and `Python`. Normally we only want to use one of them, but some times we can call the mel script through python.

<br>
### Code Example:


**1.Create a Mel File add following:**

```
global proc myMELScript()
{
    polyCube;
    print("Hello from MEL!");
}
```

**2.Create a Python File add following:**

```
import maya.cmds as cmds
import maya.mel as mel

def runMEL():
    print("Running MEL from Python")
    mel.eval("source myMELScript;")
    mel.eval("myMELScript;")

runMEL()
```

**3.Run it in maya with**

```
import runMEL
reload(runMEL)
```

---

>**End --Cheng Gu**

