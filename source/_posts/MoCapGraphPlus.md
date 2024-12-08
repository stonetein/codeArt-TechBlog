---
title: MoCap GraphPlus
banner_img: img/maya/MoCapGraphPlus.gif
index_img: /img/maya/curve_editor_tool.png
category: maya
tags: [maya, tool]
---

## usage function
- oaSmoothKeys：選取軸向進行平滑
- Get value：取得目前軸向的數值
- Translate(Rotate)X,Y,Z：選擇anim curve
- Set Keyframe：取得目前軸向峰值keypose
- Value Zero：取得目前軸向值將 anim curver 第一格歸

## Gui

<img src="/codeArt-TechBlog/img/maya/curve_editor_tool.png" width="500" style="display: block; margin-left: 0;">

## demo
<video controls style="width: 100%; height: auto;">
<source src="https://lookdev.codeart3d.xyz/blog_video/maya/Graph_Editor_tool.mp4" type="video/mp4">
</video>

## scripts

### python2
{% note info %}
```python
import pymel.core as pm
import maya.cmds as cmds


if cmds.window("GE_ui_window", exists=True): #If the window exists
    cmds.deleteUI("GE_ui_window") #Delete it

cmds.window("GE_ui_window", title="My custom Graph Editor") #Create your custom win
cmds.frameLayout("GE_ui_frameLayout", p="GE_ui_window", lv=False, bv=False ) 


if cmds.scriptedPanel("GE_ui_scriptedPanel", exists=True): #If the scriptel panel already exists
    cmds.deleteUI("GE_ui_scriptedPanel") #Delete it
cmds.scriptedPanel("GE_ui_scriptedPanel", unParent=True, type="graphEditor")
cmds.scriptedPanel( "GE_ui_scriptedPanel", e=True, parent="GE_ui_window|GE_ui_frameLayout") #parent the scripted panel to your frame layout


TranslateX = 0
TranslateY = 0
TranslateZ = 0
RotateX = 0
RotateY = 0
RotateZ = 0
    
minV = False
maxV = False

kf_list = []

def sl():
    depNodeName = cmds.ls(sl = True)
    if depNodeName:
        depNodeName = cmds.ls(sl = True)[0]
    else:
        depNodeName = cmds.ls(sl = True)
    return depNodeName

def Get():
    TranslateX = cmds.getAttr(sl() + '.translateX')
    cmds.floatField('TranslateX_V', e=1,v = TranslateX )
    TranslateY = cmds.getAttr(sl() + '.translateY')
    cmds.floatField('TranslateY_V', e=1,v = TranslateY )
    TranslateZ = cmds.getAttr(sl() + '.translateZ')
    cmds.floatField('TranslateZ_V', e=1,v = TranslateZ )
    RotateX = cmds.getAttr(sl() + '.rotateX')
    cmds.floatField('RotateX_V', e=1,v = RotateX )
    RotateY = cmds.getAttr(sl() + '.rotateY')
    cmds.floatField('RotateY_V', e=1,v = RotateY )
    RotateZ = cmds.getAttr(sl() + '.rotateZ')
    cmds.floatField('RotateZ_V', e=1,v = RotateZ )
    
    time_v()
    global TranslateX, TranslateY, TranslateZ, RotateX, RotateY, RotateZ


def oaSmoothkeys():
    curves=cmds.keyframe(q=1, name=1)
    #Get the selected curves
    if len(curves) == 0:
        cmds.mel.error("Select at least 3 keys in the Graph Editor.")
        #Go
        
    prevVal = []
    currVal = []
    nextVal = []
    average = 0.0
    keys = []
    #frame numbers
    sizeOfKeys = 0
    dupCurve = []
    dupCurveVal = []
    for curve in curves:
        keys=cmds.keyframe(curve, q=1, sl=1)
        #Get the selected keys
        sizeOfKeys=len(keys)
        #The first and last key will not be touched.
        #Therefore there must be at least 3 keys selected.
        if sizeOfKeys<3:
            continue
            #Duplicate the curve to store values in
            
        dupCurve=cmds.duplicate(curve)
        #Start with frame 2, don't touch last frame
        for i in range(1,sizeOfKeys - 1):
            prevVal=cmds.keyframe(curve, q=1, vc=1, time=(keys[i - 1],keys[i - 1]))
            currVal=cmds.keyframe(curve, q=1, vc=1, time=(keys[i],keys[i]))
            nextVal=cmds.keyframe(curve, q=1, vc=1, time=(keys[i + 1],keys[i + 1]))
            #Calculate average
            average= (prevVal[0] + currVal[0] + nextVal[0]) / 3
            #Store the value in the dup curve
            cmds.keyframe(dupCurve[0], valueChange=average, absolute=1, time=(keys[i],keys[i]))
            
        #Apply the new values
        for i in range(1,sizeOfKeys - 1):
            dupCurveVal=cmds.keyframe(dupCurve[0], q=1, vc=1, time=(keys[i],keys[i]))
            cmds.keyframe(curve, valueChange=dupCurveVal[0], absolute=1, time=(keys[i],keys[i]))
            
        cmds.delete(dupCurve[0])

        
#if have a animLayer or not animLayer attr change name
# sample  1.original:   [ pSphere1_translateX ]
#         2.layer:      [ pSphere1_translateX_AnimLayer1_inputB ]
#         3.merge layer:[ pSphere1_translateX_Merged_Layer_inputB ]
def Sel_Attr():
    crvAll = []
    for atr in cmds.listAttr(sl(),k=1):
        # print atr
        connectAtrs = cmds.listConnections('%s.%s'%(sl(),atr))
        # print connectAtrs
        getAniCrv = cmds.ls(connectAtrs,typ='animCurve')
        crvAll += getAniCrv
        
        if getAniCrv:
            crvAll += getAniCrv
        if connectAtrs:
            for cta in connectAtrs:
        # print '----', cta
                crvAll += cmds.listConnections(cta,type='animCurve')or []
        else:
            pass
        # print connectAtrs
        Tx = [v for v in crvAll if "translateX" in v or "TranslateX1" in v]
        Ty = [v for v in crvAll if "translateY" in v or "TranslateY1" in v]
        Tz = [v for v in crvAll if "translateZ" in v or "TranslateZ1" in v]
        Rx = [v for v in crvAll if "rotate" in v and "X" in v or "inRotateX" in v]
        Ry = [v for v in crvAll if "rotate" in v and "Y" in v or "inRotateY" in v]
        Rz = [v for v in crvAll if "rotate" in v and "Z" in v or "inRotateZ" in v]
    return Tx, Ty, Tz, Rx, Ry, Rz
  
	
#Combine 2 lists into 1 dictionary
def Opt_Tx():
    cmds.selectKey(Sel_Attr()[0])
    cmds.FrameSelected()	
    Get()

def Opt_Ty():
    cmds.selectKey(Sel_Attr()[1])
    cmds.FrameSelected()
    Get()
		
def Opt_Tz():
    cmds.FrameSelected()
    cmds.selectKey(Sel_Attr()[2])
    Get()
	
def RotateX_Doit():
    cmds.FrameSelected()
    cmds.selectKey(Sel_Attr()[3])	

def RotateY_Doit():
    cmds.FrameSelected()
    cmds.selectKey(Sel_Attr()[4])	

def RotateZ_Doit():
    cmds.FrameSelected()
    cmds.selectKey(Sel_Attr()[5])	


def time_v():
    n1 = cmds.keyframe(sl(), q =True)
    minV = min(n1)
    maxV = max(n1)
    cmds.playbackOptions(min =minV, max =  maxV)
    
    global minV , maxV


#Combine 2 lists into 1 dictionary

def setkeyframe():
    selobj = cmds.keyframe(q=1,n=1)
    min = minV
    max = maxV
    
    axial_V = []
    for i in selobj:
        if 'translateX' in i:
            axial_V = '.tx'
            print axial_V
        elif 'translateY' in i:
            axial_V = '.ty'
            print axial_V
        elif 'translateZ' in i:
            axial_V = '.tz'
            print axial_V
        elif 'rotate' and 'X' in i:
            axial_V = '.rx'
            print axial_V
        elif 'rotate' and 'Y' in i:
            axial_V = '.ry'
            print axial_V
        elif 'rotate' and 'Z' in i:
            axial_V = '.rz'
            print axial_V
            
    val_list = cmds.keyframe(sl() + axial_V, q = True, vc= True)
    key_list = cmds.keyframe(sl() + axial_V, q = True)
    
    # print key_list, val_list
    kv_list = zip(key_list,val_list)
    kv_list = sorted(kv_list,key=lambda x:x[0])
    
    if kf_list:
        kf_list = []
    else:
        pass
           
    for i in range(len(kv_list)):
        key, value = kv_list[i]
        bi, ai = i-1, i+1
        #If first is equal to -1 or the latter value is greater than the length -1 to skip 
        if bi == -1 or ai > (len(kv_list)-1):continue
        
        if kv_list[i][0] == min +1 or kv_list[i][0] == max -1:
            # Get the smallest keyframe of the length
            if kv_list[i][0] == min +1:
                    kf_list.append(min)      
            # Get the largest keyframe in length
            if kv_list[i][0] == max -1:            
                    kf_list.append(max)
        #Get the most extreme keyframe in length
        if kv_list[i][1] > kv_list[bi][1] and kv_list[i][1] > kv_list[ai][1]:
                kf_list.append(key)
        if kv_list[i][1] < kv_list[bi][1] and kv_list[i][1] < kv_list[ai][1]:
                kf_list.append(key)
            
    global kf_list
    
    pm.mel.animLayerEditorOnSelect("BaseAnimation", 0)
    cmds.animLayer((sl()), at = sl() ,aso =1 ) 
    for i in kf_list:
        cmds.setKeyframe(id = True, t= (i), bd = True)        
              
#Value zero
def ValueZero_Doit():
    selobj = cmds.keyframe(q=1,n=1)
    
    axial_V = []
    for i in selobj:
        if 'X' in i:
            axial_V = '.tx'
            cmds.currentTime(minV)
            TranslateX = cmds.getAttr(sl() + '.translateX')
            cmds.keyframe(an='keys',r=1, vc =(0 -TranslateX))
            Get()
            print axial_V
        elif 'Y' in i:
            axial_V = '.ty'
            TranslateY = cmds.getAttr(sl() + '.translateY')
            cmds.keyframe(an='keys',r=1, vc =(0-TranslateY))
            Get()
            print axial_V
        elif 'Z'  in i:
            axial_V = '.tz'
            cmds.currentTime(minV)
            TranslateZ = cmds.getAttr(sl() + '.translateZ')
            cmds.keyframe(an='keys',r=1, vc =(0-TranslateZ))
            Get()
            print axial_V
        elif 'rotate' and 'X' in i:
            axial_V = '.rx'
            print axial_V
        elif 'rotate' and 'Y' in i:
            axial_V = '.ry'
            print axial_V
        elif 'rotate' and 'Z' in i:
            axial_V = '.rz'
            print axial_V

cmds.showWindow("GE_ui_window")

channelLayout = cmds.formLayout("GE_ui_scriptedPanelOutlineEdForm", query=True, ca=True)[0] #Get the channel box's layout
filterLayout = cmds.formLayout("GE_ui_scriptedPanelOutlineEdForm", query=True, ca=True)[1] #Get the filter's layout

myRowLayout =cmds.rowColumnLayout(numberOfColumns=2 ,cw=[(1,105),(2,105)],bgc=(0.2,0.2,0.2), p="GE_ui_scriptedPanelOutlineEdForm") #Create a row layout 
cmds.button(l="oaSmoothKeys",bgc=(0.0,0.0,0.3) , command=('oaSmoothkeys()'))
cmds.button(l="Get value", command=('Get()'))
cmds.button(l="TranslateX",bgc=(0.6,0.6,0.6),command=('Opt_Tx()'))
cmds.floatField('TranslateX_V',bgc = (0.1,0.1,0.1), v =TranslateX)

cmds.button(l="TranslateY",bgc=(0.6,0.6,0.6),command=('Opt_Ty()'))
cmds.floatField('TranslateY_V',bgc = (0.1,0.1,0.1), v = TranslateY)

cmds.button(l="TranslateZ",bgc=(0.6,0.6,0.6),command=('Opt_Tz()'))
cmds.floatField('TranslateZ_V',bgc = (0.1,0.1,0.1), v = TranslateZ)

cmds.button(l="RotateX",bgc=(0.6,0.6,0.6),command=('RotateX_Doit()'))
cmds.floatField('RotateX_V',bgc = (0.1,0.1,0.1), v = RotateX)

cmds.button(l="RotateY",bgc=(0.6,0.6,0.6),command=('RotateY_Doit()'))
cmds.floatField('RotateY_V',bgc = (0.1,0.1,0.1), v = RotateY)

cmds.button(l="RotateZ",bgc=(0.6,0.6,0.6),command=('RotateZ_Doit()'))
cmds.floatField('RotateZ_V',bgc = (0.1,0.1,0.1), v = RotateZ)

cmds.button(l="Set_Keyframe",bgc = (0.0,0.8,0), command=('setkeyframe()'))
cmds.button(l="Value Zero",bgc = (0.8,0,0), command=('ValueZero_Doit()'))

# cmds.text("Time Slider")
# cmds.separator ( h=20, style='none') 

# cmds.intField('startTimeField',bgc = (0.1,0.1,0.1), v = cmds.playbackOptions(ast = minV ,min = minV))
# cmds.intField('endTimeField',bgc = (0.1,0.1,0.1), v = cmds.playbackOptions(aet =maxV, max =  maxV))
cmds.separator ( h=2, style='none',bgc = (0.8,0.0,0.0)) 
cmds.separator ( h=2, style='none',bgc = (0.8,0.0,0.0)) 

#This will reorder the content of the left formLayout
cmds.formLayout("GE_ui_scriptedPanelOutlineEdForm", edit=True, af=[ \
            (channelLayout, "bottom", 0), \
            (channelLayout, "right", 0),  \
            (filterLayout, "top", 0),  \
            (myRowLayout, "left", 0),  \
            (myRowLayout, "right", 0)],  \
            ac=[(myRowLayout, "top", 0, filterLayout), \
            (channelLayout, "top", 0, myRowLayout)])
```
{% endnote %}

### python3
{% note info %}
```python
import pymel.core as pm
import maya.cmds as cmds


if cmds.window("GE_ui_window", exists=True): #If the window exists
    cmds.deleteUI("GE_ui_window") #Delete it

cmds.window("GE_ui_window", title="My custom Graph Editor") #Create your custom win
cmds.frameLayout("GE_ui_frameLayout", p="GE_ui_window", lv=False, bv=False ) 


if cmds.scriptedPanel("GE_ui_scriptedPanel", exists=True): #If the scriptel panel already exists
    cmds.deleteUI("GE_ui_scriptedPanel") #Delete it
cmds.scriptedPanel("GE_ui_scriptedPanel", unParent=True, type="graphEditor")
cmds.scriptedPanel( "GE_ui_scriptedPanel", e=True, parent="GE_ui_window|GE_ui_frameLayout") #parent the scripted panel to your frame layout


TranslateX = 0
TranslateY = 0
TranslateZ = 0
RotateX = 0
RotateY = 0
RotateZ = 0
    
minV = False
maxV = False

kf_list = []

def sl():
    depNodeName = cmds.ls(sl = True)
    if depNodeName:
        depNodeName = cmds.ls(sl = True)[0]
    else:
        depNodeName = cmds.ls(sl = True)
    return depNodeName

def Get():
    TranslateX = cmds.getAttr(sl() + '.translateX')
    cmds.floatField('TranslateX_V', e=1,v = TranslateX )
    TranslateY = cmds.getAttr(sl() + '.translateY')
    cmds.floatField('TranslateY_V', e=1,v = TranslateY )
    TranslateZ = cmds.getAttr(sl() + '.translateZ')
    cmds.floatField('TranslateZ_V', e=1,v = TranslateZ )
    RotateX = cmds.getAttr(sl() + '.rotateX')
    cmds.floatField('RotateX_V', e=1,v = RotateX )
    RotateY = cmds.getAttr(sl() + '.rotateY')
    cmds.floatField('RotateY_V', e=1,v = RotateY )
    RotateZ = cmds.getAttr(sl() + '.rotateZ')
    cmds.floatField('RotateZ_V', e=1,v = RotateZ )
    
    time_v()
    #global TranslateX, TranslateY, TranslateZ, RotateX, RotateY, RotateZ


def oaSmoothkeys():
    curves=cmds.keyframe(q=1, name=1)
    #Get the selected curves
    if len(curves) == 0:
        cmds.mel.error("Select at least 3 keys in the Graph Editor.")
        #Go
        
    prevVal = []
    currVal = []
    nextVal = []
    average = 0.0
    keys = []
    #frame numbers
    sizeOfKeys = 0
    dupCurve = []
    dupCurveVal = []
    for curve in curves:
        keys=cmds.keyframe(curve, q=1, sl=1)
        #Get the selected keys
        sizeOfKeys=len(keys)
        #The first and last key will not be touched.
        #Therefore there must be at least 3 keys selected.
        if sizeOfKeys<3:
            continue
            #Duplicate the curve to store values in
            
        dupCurve=cmds.duplicate(curve)
        #Start with frame 2, don't touch last frame
        for i in range(1,sizeOfKeys - 1):
            prevVal=cmds.keyframe(curve, q=1, vc=1, time=(keys[i - 1],keys[i - 1]))
            currVal=cmds.keyframe(curve, q=1, vc=1, time=(keys[i],keys[i]))
            nextVal=cmds.keyframe(curve, q=1, vc=1, time=(keys[i + 1],keys[i + 1]))
            #Calculate average
            average= (prevVal[0] + currVal[0] + nextVal[0]) / 3
            #Store the value in the dup curve
            cmds.keyframe(dupCurve[0], valueChange=average, absolute=1, time=(keys[i],keys[i]))
            
        #Apply the new values
        for i in range(1,sizeOfKeys - 1):
            dupCurveVal=cmds.keyframe(dupCurve[0], q=1, vc=1, time=(keys[i],keys[i]))
            cmds.keyframe(curve, valueChange=dupCurveVal[0], absolute=1, time=(keys[i],keys[i]))
            
        cmds.delete(dupCurve[0])

        
#if have a animLayer or not animLayer attr change name
# sample  1.original:   [ pSphere1_translateX ]
#         2.layer:      [ pSphere1_translateX_AnimLayer1_inputB ]
#         3.merge layer:[ pSphere1_translateX_Merged_Layer_inputB ]
def Sel_Attr():
    crvAll = []
    for atr in cmds.listAttr(sl(),k=1):
        # print atr
        connectAtrs = cmds.listConnections('%s.%s'%(sl(),atr))
        # print connectAtrs
        getAniCrv = cmds.ls(connectAtrs,typ='animCurve')
        crvAll += getAniCrv
        
        if getAniCrv:
            crvAll += getAniCrv
        if connectAtrs:
            for cta in connectAtrs:
        # print '----', cta
                crvAll += cmds.listConnections(cta,type='animCurve')or []
        else:
            pass
        # print connectAtrs
        Tx = [v for v in crvAll if "translateX" in v or "TranslateX1" in v]
        Ty = [v for v in crvAll if "translateY" in v or "TranslateY1" in v]
        Tz = [v for v in crvAll if "translateZ" in v or "TranslateZ1" in v]
        Rx = [v for v in crvAll if "rotate" in v and "X" in v or "inRotateX" in v]
        Ry = [v for v in crvAll if "rotate" in v and "Y" in v or "inRotateY" in v]
        Rz = [v for v in crvAll if "rotate" in v and "Z" in v or "inRotateZ" in v]
    return Tx, Ty, Tz, Rx, Ry, Rz
  
	
#Combine 2 lists into 1 dictionary
def Opt_Tx():
    cmds.selectKey(Sel_Attr()[0])
    cmds.FrameSelected()	
    Get()

def Opt_Ty():
    cmds.selectKey(Sel_Attr()[1])
    cmds.FrameSelected()
    Get()
		
def Opt_Tz():
    cmds.FrameSelected()
    cmds.selectKey(Sel_Attr()[2])
    Get()
	
def RotateX_Doit():
    cmds.FrameSelected()
    cmds.selectKey(Sel_Attr()[3])	

def RotateY_Doit():
    cmds.FrameSelected()
    cmds.selectKey(Sel_Attr()[4])	

def RotateZ_Doit():
    cmds.FrameSelected()
    cmds.selectKey(Sel_Attr()[5])	


def time_v():
    n1 = cmds.keyframe(sl(), q =True)
    minV = min(n1)
    maxV = max(n1)
    cmds.playbackOptions(min =minV, max =  maxV)
    
    #global minV , maxV


#Combine 2 lists into 1 dictionary

def setkeyframe():
    selobj = cmds.keyframe(q=1,n=1)
    min = minV
    max = maxV
    
    axial_V = []
    for i in selobj:
        if 'translateX' in i:
            axial_V = '.tx'
            print (axial_V)
        elif 'translateY' in i:
            axial_V = '.ty'
            print (axial_V)
        elif 'translateZ' in i:
            axial_V = '.tz'
            print (axial_V)
        elif 'rotate' and 'X' in i:
            axial_V = '.rx'
            print (axial_V)
        elif 'rotate' and 'Y' in i:
            axial_V = '.ry'
            print (axial_V)
        elif 'rotate' and 'Z' in i:
            axial_V = '.rz'
            print (axial_V)
            
    val_list = cmds.keyframe(sl() + axial_V, q = True, vc= True)
    key_list = cmds.keyframe(sl() + axial_V, q = True)
    
    # print key_list, val_list
    kv_list = zip(key_list,val_list)
    kv_list = sorted(kv_list,key=lambda x:x[0])
    
    if kf_list:
        kf_list = []
    else:
        pass
           
    for i in range(len(kv_list)):
        key, value = kv_list[i]
        bi, ai = i-1, i+1
        #If first is equal to -1 or the latter value is greater than the length -1 to skip 
        if bi == -1 or ai > (len(kv_list)-1):continue
        
        if kv_list[i][0] == min +1 or kv_list[i][0] == max -1:
            # Get the smallest keyframe of the length
            if kv_list[i][0] == min +1:
                    kf_list.append(min)      
            # Get the largest keyframe in length
            if kv_list[i][0] == max -1:            
                    kf_list.append(max)
        #Get the most extreme keyframe in length
        if kv_list[i][1] > kv_list[bi][1] and kv_list[i][1] > kv_list[ai][1]:
                kf_list.append(key)
        if kv_list[i][1] < kv_list[bi][1] and kv_list[i][1] < kv_list[ai][1]:
                kf_list.append(key)
            
    #global kf_list
    
    pm.mel.animLayerEditorOnSelect("BaseAnimation", 0)
    cmds.animLayer((sl()), at = sl() ,aso =1 ) 
    for i in kf_list:
        cmds.setKeyframe(id = True, t= (i), bd = True)        
              
#Value zero
def ValueZero_Doit():
    selobj = cmds.keyframe(q=1,n=1)
    
    axial_V = []
    for i in selobj:
        if 'X' in i:
            axial_V = '.tx'
            cmds.currentTime(minV)
            TranslateX = cmds.getAttr(sl() + '.translateX')
            cmds.keyframe(an='keys',r=1, vc =(0 -TranslateX))
            Get()
            print (axial_V)
        elif 'Y' in i:
            axial_V = '.ty'
            TranslateY = cmds.getAttr(sl() + '.translateY')
            cmds.keyframe(an='keys',r=1, vc =(0-TranslateY))
            Get()
            print (axial_V)
        elif 'Z'  in i:
            axial_V = '.tz'
            cmds.currentTime(minV)
            TranslateZ = cmds.getAttr(sl() + '.translateZ')
            cmds.keyframe(an='keys',r=1, vc =(0-TranslateZ))
            Get()
            print (axial_V)
        elif 'rotate' and 'X' in i:
            axial_V = '.rx'
            print (axial_V)
        elif 'rotate' and 'Y' in i:
            axial_V = '.ry'
            print (axial_V)
        elif 'rotate' and 'Z' in i:
            axial_V = '.rz'
            print (axial_V)

cmds.showWindow("GE_ui_window")

channelLayout = cmds.formLayout("GE_ui_scriptedPanelOutlineEdForm", query=True, ca=True)[0] #Get the channel box's layout
filterLayout = cmds.formLayout("GE_ui_scriptedPanelOutlineEdForm", query=True, ca=True)[1] #Get the filter's layout

myRowLayout =cmds.rowColumnLayout(numberOfColumns=2 ,cw=[(1,105),(2,105)],bgc=(0.2,0.2,0.2), p="GE_ui_scriptedPanelOutlineEdForm") #Create a row layout 
cmds.button(l="oaSmoothKeys",bgc=(0.0,0.0,0.3) , command=('oaSmoothkeys()'))
cmds.button(l="Get value", command=('Get()'))
cmds.button(l="TranslateX",bgc=(0.6,0.6,0.6),command=('Opt_Tx()'))
cmds.floatField('TranslateX_V',bgc = (0.1,0.1,0.1), v =TranslateX)

cmds.button(l="TranslateY",bgc=(0.6,0.6,0.6),command=('Opt_Ty()'))
cmds.floatField('TranslateY_V',bgc = (0.1,0.1,0.1), v = TranslateY)

cmds.button(l="TranslateZ",bgc=(0.6,0.6,0.6),command=('Opt_Tz()'))
cmds.floatField('TranslateZ_V',bgc = (0.1,0.1,0.1), v = TranslateZ)

cmds.button(l="RotateX",bgc=(0.6,0.6,0.6),command=('RotateX_Doit()'))
cmds.floatField('RotateX_V',bgc = (0.1,0.1,0.1), v = RotateX)

cmds.button(l="RotateY",bgc=(0.6,0.6,0.6),command=('RotateY_Doit()'))
cmds.floatField('RotateY_V',bgc = (0.1,0.1,0.1), v = RotateY)

cmds.button(l="RotateZ",bgc=(0.6,0.6,0.6),command=('RotateZ_Doit()'))
cmds.floatField('RotateZ_V',bgc = (0.1,0.1,0.1), v = RotateZ)

cmds.button(l="Set_Keyframe",bgc = (0.0,0.8,0), command=('setkeyframe()'))
cmds.button(l="Value Zero",bgc = (0.8,0,0), command=('ValueZero_Doit()'))

# cmds.text("Time Slider")
# cmds.separator ( h=20, style='none') 

# cmds.intField('startTimeField',bgc = (0.1,0.1,0.1), v = cmds.playbackOptions(ast = minV ,min = minV))
# cmds.intField('endTimeField',bgc = (0.1,0.1,0.1), v = cmds.playbackOptions(aet =maxV, max =  maxV))
cmds.separator ( h=2, style='none',bgc = (0.8,0.0,0.0)) 
cmds.separator ( h=2, style='none',bgc = (0.8,0.0,0.0)) 

#This will reorder the content of the left formLayout
cmds.formLayout("GE_ui_scriptedPanelOutlineEdForm", edit=True, af=[ \
            (channelLayout, "bottom", 0), \
            (channelLayout, "right", 0),  \
            (filterLayout, "top", 0),  \
            (myRowLayout, "left", 0),  \
            (myRowLayout, "right", 0)],  \
            ac=[(myRowLayout, "top", 0, filterLayout), \
            (channelLayout, "top", 0, myRowLayout)])
```
{% endnote %}