---
layout: post
title:  "MayaScripting: Working with Geometry"
date:   2019-06-01
categories: [Python]
tags: [Python]
icon: fa-code
---

# Table of Contents
### 1. [**Check Selections & Type**](#1)

### 2. [**Access Geometry Data**](#2)

### 3. [**Creating Curve & Primitive**](#3)



<a name="1"></a>

<br/><br/>

---

<br/><br/>

# 1. Check Selections & Type

One of the most commonly used command in maya.cmds API is [cmds.ls()](http://help.autodesk.com/cloudhelp/2017/ENU/Maya-Tech-Docs/Commands/ls.html), this command will return a list that contains all objects in the scene. 

We can list the selected object by set the `selection` flag to True, as belows:

```py
cmds.ls(selection=True)
```

When we want to check the type of the object. For example, we want to know if the object is a `mesh` type, we can do by using [cmds.listRelatives()](https://download.autodesk.com/us/maya/2011help/CommandsPython/listRelatives.html) and [cmds.nodeType()](https://help.autodesk.com/cloudhelp/2018/ENU/Maya-Tech-Docs/Commands/nodeType.html) as following:

```py
# get the shape of the object
shapeNode = cmds.listRelatives(obj, shapes=True)

# get the node type through shape
nodeType = cmds.nodeType(shapeNode)

# if the type is "mesh", return true
if nodeType == "mesh":
  return True
```
	

<a name="2"></a>


<br/><br/>

--- 

<br/><br/>

# 2. Access Geometry Data
<br/>

## Data in Polygon Model

If we want to get the counts on the specified object [cmds.polyEvaluate()](http://download.autodesk.com/us/maya/2011help/CommandsPython/polyEvaluate.html). For example, do as following to get the number of vertices, edges, and faces:

```py
vertNum = cmds.polyEvaluate(obj,vertex=True)

edgeNum = cmds.polyEvaluate(obj,edge=True)

faceNum = cmds.polyEvaluate(obj,face=True)
```

Sometimes we want to get the reference from the specific vertex, edge, or face. The following code shows to select the specific vertex, edge, and face by using [cmds.select](http://help.autodesk.com/cloudhelp/2015/ENU/Maya-Tech-Docs/CommandsPython/select.html)

```py
cmds.select(obj+".vtx[0]",replace=True)
cmds.select(obj+".e[1:5]",replace=True)
cmds.select(obj+".f[-1]",replace= True)
```

## Data in Nurbs Object

Using [cmds.getAttr()](http://help.autodesk.com/cloudhelp/2017/ENU/Maya-Tech-Docs/CommandsPython/getAttr.html) to retrieve the value of specified attribute on a given node.

```py
  degU = cmds.getAttr(obj+".degreeU")
  spansU = cmds.getAttr(obj+".spansU")
  cvsU = degU+spansU
  print("CVs (U): %s"%cvsU)
  
  degV = cmds.getAttr(obj+".degreeV")
  spansV = cmds.getAttr(obj+".spansV")
  cvsV = degV+spansV
  print("CVs (V): %s"%cvsV)
```


<a name="3"></a>
<br/><br/>

---

# 3. Creating Curve & Primitive

Using [cmds.curve()](https://help.autodesk.com/cloudhelp/2018/ENU/Maya-Tech-Docs/CommandsPython/curve.html) to create curve.

```py
cmds.curve(degree=1,p=[(-0.5,-0.5,0),(0.5,-0.5,0),(0.5,0.5,0),(-0.5,0.5,0),(-0.5,-0.5,0)])
```

Using [cmds.polyCreateFacet()](https://download.autodesk.com/global/docs/maya2012/en_us/CommandsPython/polyCreateFacet.html) to create polygon faces.

```py
# Create a triangular facet
cmds.polyCreateFacet( p=[(0.0, 0.0, 0.0), (10.0, 0.0, 0.0), (10.0, 10.0, 0.0)] )

# Create a quadrangular facet with two triangular holes inside
# Note that, in Python, specifying an empty tuple in the point list marks the
# beginning of a hole specification
cmds.polyCreateFacet( p=[(0, 0, 0), (10, 0, 0), (10, 10, 0), (0, 10, 0), (), (4, 2, 0), (5, 4, 0), (6, 2, 0), (), (5, 6, 0), (4, 8, 0), (6, 8, 0)] )
cmds.move( 0, 0, 10, r=True )

```

An example of create a tetrahedron by using [cmds.append()](https://download.autodesk.com/us/maya/2011help/CommandsPython/polyAppend.html) to append faces, [cmds.polyUnite()](https://download.autodesk.com/us/maya/2011help/CommandsPython/polyUnite.html) to combine faces, [cmd.polyMergeVertex()] to merge vertices, [cmds.move()](https://download.autodesk.com/us/maya/2011help/CommandsPython/move.html) to move the points, [cmds.xform()] to transform the pivot of  the object, finally using [cmds.makeIdentity()](https://download.autodesk.com/us/maya/2011help/CommandsPython/makeIdentity.html) to freeze transformation, and using [cmds.delete()](https://download.autodesk.com/us/maya/2009help/CommandsPython/delete.html) to delete construction history.

<br/>

### Full Code:

```py
from maya import cmds
import math

def makeTetra(size):
  pointA = [0,0,0]
  pointB = [size,0,0]
  
  pointC = [size/2,0,0]
  
  # set the z position for C
  pointC[2] = math.sqrt((size*size)-(size/2*size/2))
  
  pointE = [0,0,0]
  #average the A, B, and C to get E
  # add all the values
  for i in range(0,3):
    pointE[i]+=pointA[i]
    pointE[i]+=pointB[i]
    pointE[i]+=pointC[i]
  # divide by 3
  for i in range(0,3):
    pointE[i] = pointE[i]/3.0
 
	# start point D with the X and Z coordinates of pointE
  pointD = [0,0,0]
  pointD[0] = pointE[0]
  pointD[2] = pointE[2]
  
  distanceAE = math.sqrt((pointE[0]*pointE[0])+(pointE[2]*pointE[2]))
  
  # set the Y coordinate of point D
  pointD[1] = math.sqrt(size*size-distanceAE*distanceAE)
  
  faces=[]
  faces.append(cmds.polyCreateFacet(p=[pointA,pointB,pointC],texture=1))
  faces.append(cmds.polyCreateFacet(p=[pointA,pointD,pointB],texture=1))
  faces.append(cmds.polyCreateFacet(p=[pointB,pointD,pointC],texture=1))
  faces.append(cmds.polyCreateFacet(p=[pointC,pointD,pointA],texture=1))
  
  cmds.select(faces[0],replace=True)
  for i in range(1,len(faces)):
    cmds.select(faces[i],add=True)
  
  # combine all the faces into a single object
  obj = cmds.polyUnite()
  
  # merge duplicated vertices
  cmds.select(obj[0]+".vtx[:]")
  cmds.polyMergeVertex(distance=0.0001)
  cmds.select(obj[0])
  
  # move the object to the center
  cmds.move(-pointE[0],0,-pointE[2])
  # move the pivot of the object
  cmds.xform(pivots=(pointE[0],0,pointE[2]))
  # freeze transformation
  cmds.makeIdentity(apply=True)
  
  # delete construction history
  cmds.delete(ch=True)

makeTetra(5)
```

### Final Result:


<p align="center">     
<img src="/static/assets/img/blog/tetrahedron.png" width="50%">
</p>

---

>**End --Cheng Gu**

