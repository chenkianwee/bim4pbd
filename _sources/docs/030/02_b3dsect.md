# Sectional perspective with Blender3D and LibreCAD
I will attempt to model a space using LibreCAD and Blender3D and cut a sectional perspective. For a more comprehensive tutorial, refer to <a href="https://learning.oreilly.com/library/view/introduction-to-blender/9781484279540/" target="_blank">Introduction to Blender 3.0 : learn organic and architectural modeling, lighting, materials, painting, rendering, and compositing with Blender Chapter 3 Building a 3D Environment </a>

1. Draw the following with the rectangle command and offset command in librecad ({doc}`../030/01_tips`). Dimensions are in meters.
    <br/><br/>
    ```{image} ../../_static/librecad/librecad1.png
    :width: 100%
    :align: center
    ```
    <br/><br/>

2. Import it into Blender3D. Go to Edit -> Preferences -> Add-ons. Search for dxf. Activate the Import-Export: Import AutoCAD DXF Format (.dxf) add-on. Turn both import and export on.
3. Make a collection for walls at the outliner. Add a plane. Resize the plane ({doc}`here <../030/03_b3dplane>`) and snap it to the outline to make the walls.
    <br/><br/>
    ```{image} ../../_static/blender1/blender7.png
    :width: 100%
    :align: center
    ```
    <br/><br/>

4. Extrude the walls following instructions [here](03_b3dplane.md#extruding-the-face-of-a-plane).
    <br/><br/>
    ```{image} ../../_static/blender1/blender8.png
    :width: 100%
    :align: center
    ```
    <br/><br/>
    
5. In the object mode. Select all the walls. Click the Move icon at the left side bar. Move them in the Z direction. While dragging the walls key in 0.3m. This is to make way for the floor slab.
    <br/><br/>
    ```{image} ../../_static/blender1/blender9.png
    :width: 100%
    :align: center
    ```
    <br/><br/>
6. Copy and paste the floor slab. Move the slab 4.3m up. It will be the roof.
    <br/><br/>
    ```{image} ../../_static/blender1/blender10.png
    :width: 100%
    :align: center
    ```
    <br/><br/>    

7. As Blender3d do not have a function to do sectional cuts. We turn off the visibility of one of the walls so we can see into the space. Set the camera interactively by going to the right hand side bar -> View -> View Lock -> tick Camera to View.
    <br/><br/>
    ```{image} ../../_static/blender1/blender11.png
    :width: 100%
    :align: center
    ```
    <br/><br/>

8. Add line rendering by ticking Freestyle in the Rendering Properties tab. Render the image by going to Render -> Render Image. You can make the background transparent by going to the Render Properties -> Film -> Transparent.
    <br/><br/>
    ```{image} ../../_static/blender1/blender13.png
    :width: 100%
    :align: center
    ```
    <br/><br/>