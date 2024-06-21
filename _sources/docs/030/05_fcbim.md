# BIM with FreeCAD
This entry is based on these resources:
- https://wiki.freecad.org/Manual:BIM_modeling
- https://wiki.freecad.org/Draft_Workbench
- https://wiki.freecad.org/Tutorials#Architecture_and_BIM

## Native IFC FreeCAD
- https://github.com/yorikvanhavre/FreeCAD-NativeIFC?tab=readme-ov-file



## Installing IFCOpenShell if necessary
- check the python console what version is FreeCAD using, check where is the path by typing
    - sys.path
- install ifcopenshell by downloading the prebuilt version https://blenderbim.org/docs-python/ifcopenshell-python/installation.html
- unzip and put it in the freecad python directory, you can find it by running sys.path and paying attention to the path printed.

## Importing images for tracing
1. PDF does not import well. Instead convert the PDF to either PNG or JPG.
2. Depending on the scale of your drawing scale the drawing with the Xsize and YSize parameters. If your drawing is 1:250, You can scale it to actual size by doing e.g. 210mm * 250
3. Double click on the image in the side-bar and adjust the transparency as accordingly.

## Change transparency of 3D objects through appearance options
1. Right click on the object -> Appearance and adjust the transparent and materials as accordingly.
2. These options will be reflected in the GLTF export.
3. For windows and doors:
    - first change the appearance
    - double click into the window/door
    - edit one of the component and update
    - that should update the appearance of the component.

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
## Setting property set of IFC elements
1. Install the BIM workbench.
2. In the workbench go to Manage IFC Properties -> choose the ifc element -> add property set or add property
3. Input the value accordingly.

## Export to IFC and read with IFCOpenshell
1. Select the component you want to export. If you choose BIM building component. The export will include all the components in it. If you only choose only "Wall", only that component will be exported.
    - currently materials are only exported if they are assigned to a BIM entity.
    - MultiMaterials are not exported properly into IFC format.
    ```{image} ../../_static/freecad_bim/freecad_bim6.jpg
    :width: 100%
    :align: center
    ```
2. Once exported you can read the IFC file with IFCOpenshell.
    - https://blenderbim.org/docs-python/ifcopenshell-python/code_examples.html
    - https://wiki.osarch.org/index.php?title=IfcOpenShell_code_examples
    ```
    import ifcopenshell

    ifc_path = '/home/chenkianwee/kianwee_work/get/projects/grundfos/model/ifc/grundfos-Building.ifc'

    model = ifcopenshell.open(ifc_path)

    print(model.schema) # May return IFC2X3, IFC4, or IFC4X3.
    print(model.by_id(1))

    walls = model.by_type('IfcWall')
    mats = model.by_type('IfcSurfaceStyle')
    print(mats)
    for w in walls:
         print(w.get_info()['Name'])
         psets = ifcopenshell.util.element.get_psets(w)
         print(psets)

         container = ifcopenshell.util.element.get_container(w)
         print(container)

         mat = ifcopenshell.util.element.get_materials(w)
         print(mat)
    ```
## Sectional Cuts with FreeCAD
1. Choose a working plane and then Go to BIM > Section Plane. A section plane will be created on the selected plane. Move the plan accordingly to where you want your cut to be. Double click on the section plane to add new objects into the section plane. 
    - double click on the section plane, then go to the model tab where you can choose all the objects you want to be included in the section cut.
    - You can trigger the sectional cut by right-click -> Toggle Cutview

2. In the BIM workbench go to Annotation -> Shape-based view. This will project the section onto the xy plane. You might have to turn off the visibility of your 3d model to see the projected lines below it.

3. Go to the TechDraw workbench. Go to TechDraw -> Page -> Insert Default Page. You will see a new view opened called a Page.

4. On the model view, select the section you want to insert into the page. Go to TechDraw -> Views from Other Workbenches -> Insert Arch Workbench Object. 
    - In the data tab, make sure the "All On" parameter is set to true
    - scale your section. e.g. if you want a 1:100 scale change it to '=1/100'
    - You can move your section anywhere on the drawing. Click on the view and adjust the lineweights etc accordingly.

5. To annotate the drawing. Go to TechDraw -> Annotations
6. To draw lines, Go to TechDraw -> Add Lines 

## Views with TechDraw
1. Go to TechDraw workbench -> TechDraw -> Page -> Insert Page using Tempate
    - choose the right template and create the page

2. In the BIM workbench insert view by first selecting the section cut object then go to Annotation -> View and it will insert that view onto that page.

3. Choose the Part you want to be in the TechDraw page. Go to TechDraw -> TechDraw Views -> Insert View
    - scale the view as accordingly

4. Once that you have a view, you can cut section using that view in TechDraw.

5. You can do dimension with the BIM workbench. You can customize the dimension by applying annotation styles onto the dimensions.

### TechDraw Templates
1. customize your own template https://blog.freecad.org/2023/12/19/tutorial-create-custom-techdraw-templates/

## Rendering with the Render Workbench
- https://github.com/FreeCAD/FreeCAD-render?tab=readme-ov-file#usage
- https://github.com/FreeCAD/FreeCAD-render/blob/master/docs/EngineInstall.md#povray

1. Install povray with the command 
    ```
    sudo apt install povray
    ```

2. Install the Render workbench using the workbench manager

3. Go to the Render Workbench, go to Render -> Render
    - In workbench settings, enter /usr/bin/povray in 'PovRay executable path'. 

4. Create a rendering project go to Render -> Projects -> Povray Projects. Choosen the povray_studio_light.pov

5. Select the objects you want to render and ctrl+click the povray project. Then click on the Rendering View icon. A render view will be created in the project.

6. Change to perspective view and to the scene you want to render. Click on the Project on the tree view and click Render.

## Print from 3D view
1. Go to Edit -> Preferences -> Display -> Camera type -> Perspective rendering
2. Minimize the window. The print preview will be accurate.

