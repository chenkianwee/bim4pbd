# BIM with FreeCAD
This entry is based on these resources:
- https://wiki.freecad.org/Manual:BIM_modeling
- https://wiki.freecad.org/Draft_Workbench
- https://wiki.freecad.org/Tutorials#Architecture_and_BIM

## Installing IFCOpenShell if necessary
- check the python console what version is FreeCAD using, check where is the path by typing
    - sys.path
- install ifcopenshell by downloading the prebuilt version https://blenderbim.org/docs-python/ifcopenshell-python/installation.html
- unzip and put it in the freecad python directory, you can find it by running sys.path and paying attention to the path printed.

## Modeling with FreeCAD
1. Go to the Arch workbench.
2. First draw the 500mm x 500mm rectangle. Turn it into a column by clicking on the structure button.
    <br/><br/>
    ```{image} ../../_static/freecad_bim/freecad_bim1.jpg
    :width: 100%
    :align: center
    ```
3. Array it into a column grid using the array tool. Set the x interval to 9m and y interval to 9m, with number of array at x=3,y=2,z=1
    <br/><br/>
    ```{image} ../../_static/freecad_bim/freecad_bim2.jpg
    :width: 100%
    :align: center
    ```
4. Draw lines to represent the walls. Then click on the line and click on the walls button to make the line a wall.
    <br/><br/>
    ```{image} ../../_static/freecad_bim/freecad_bim3.jpg
    :width: 100%
    :align: center
    ```
5. To combine two walls. Ctrl click on both of the walls and click on the plus sign on the tool bar to merge them. 
    - You can separate them by using the minus sign. Open the tree and choose the wall you want to separate and click on the minus sign.
    <br/><br/>
    ```{image} ../../_static/freecad_bim/freecad_bim4.jpg
    :width: 100%
    :align: center
    ```
6. To model windows. Draw the windows on 2d using the drafting tools. Then change the 2d shapes into a sketch by using the draft to sketch tool. Choose the sketch and click on the window tool.
    - further configure the window by double clicking on the window and creating the frame and each glass panel component.
    <br/><br/>
    ```{image} ../../_static/freecad_bim/freecad_bim5.jpg
    :width: 100%
    :align: center
    ```