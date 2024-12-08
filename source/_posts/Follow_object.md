---
title: Follow Object
banner_img: img/maya/followObject.gif
index_img: img/maya/followObject.gif
category: maya
tags: [maya, tool]
---

## Follow object

<video controls style="width: 100%; height: auto;">
<source src= "/codeArt-TechBlog/video/maya/followObject.mp4" type="video/mp4">
</video>

### GUI
<img src="/codeArt-TechBlog/img/maya/followObj.png" width="300" style="display: block; margin-left: 0;">

### Usage
```python
step1. Select your A_object

step2. Check the B_object you want to follow

step3. Check your time range

step4. Apply
```

### Code  
```python
#~~ IMPORT - Area ~~#

import maya.cmds as cmds
import maya.mel as mel

#~~ End of IMPORT Area ~~#

#~~ Script - Area ~~#

WindowWinUi="TargetFollow" #Here must to add ' and ' to be 'xxxxxxxx'

def zarmWppsWinExe(*args):
    if cmds.window(WindowWinUi, exists = True):
        cmds.deleteUI(WindowWinUi)

        

def Doit():
    stime = cmds.intField( 'startTimeField', q=1, v=1 )
    etime = cmds.intField( 'endTimeField', q=1, v=1 ) + 1
    time = stime	
    sel = cmds.ls(sl = True)
    for i in range(stime,etime):
        cmds.currentTime(i)
        cmds.xform( sel[0],ws=1, m=cmds.xform(sel[1],q=1,ws=1,m=1) )
        cmds.setKeyframe(sel[0])

#~~ End of Script Area ~~#q

#~~ UI - Area ~~#
zarmWppsWinExe()

cmds.window(WindowWinUi,bgc=(0.5,0.5,0.5) )

#~~ Contents ~~#

cmds.columnLayout("A")

cmds.rowColumnLayout(p="A",nc=1,cw=[(1,100)])

cmds.text( label='Target Object:')

cmds.separator ( h=5, style='none')

cmds.button(l="Apply",bgc=(0.5,0.8,0.5), command=('Doit()'))

cmds.separator ( h=5, style='none')

cmds.text( label='Time Range:')

cmds.rowColumnLayout(p="A",nc=3,cw=[(1,100),(2,100)])

cmds.intField('startTimeField', v=cmds.playbackOptions( q=1, min=1))

cmds.intField('endTimeField', v=cmds.playbackOptions( q=1, max=1 ))


#~~ Contents ~~#

cmds.showWindow(WindowWinUi)
cmds.window(WindowWinUi,e=1,wh=(204,102),s=1 )
cmds.window(WindowWinUi,q=1,wh=1)
```