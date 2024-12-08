---
title: Position Locator
banner_img: img/maya/posicitionLocator.gif
index_img: /img/maya/posicitionLocator.gif
comment: 'remark42'
category: maya
tags: [maya, tool]
---

## usage
- 點擊要建立locator的位置，按下script

## demo
<video controls style="width: 100%; height: auto;">
<source src="https://lookdev.codeart3d.xyz/blog_video/maya/Position_Locator.mp4" type="video/mp4">
</video>

## scripts
{% note success %}
```python
import maya.cmds as cmds 

sel=cmds.ls(sl=1) 
bigbig = 20 
lc = cmds.spaceLocator()[0] 
sp = cmds.listRelatives( lc, s=1 )[0]

cmds.setAttr("%s.overrideEnabled" % sp, 1 )
cmds.setAttr("%s.overrideColor" % sp, 9 )

cmds.setAttr( "%s.localScale" % sp, bigbig, bigbig, bigbig ) 
cmds.xform( lc,ws=1, ro=cmds.xform(sel[0],q=1,ws=1,ro=1)) 
cmds.xform( lc,ws=1,t=cmds.xform(sel[0],q=1,ws=1,t=1))
```
{% endnote %}