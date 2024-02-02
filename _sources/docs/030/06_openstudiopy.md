# Understanding the Openstudio Workflow
- OpenStudio Workflow (.osw file) - describes the workflow steps of OpenStudio <a href="https://nrel.github.io/OpenStudio-user-documentation/reference/command_line_interface/#osw-structure" target="_blank">official explanation here.</a>
    - <a href="https://raw.githubusercontent.com/NREL/OpenStudio-workflow-gem/develop/spec/schema/osw_output.json" target="_blank">.osw schema</a>
- In an OpenStudio Workflow this happens:
    1. An OpenStudio Model (.osm file) is loaded.
        - OpenStudio Model - a model describing the building. This include the construction, thermal zones and simulation configurations etc (https://s3.amazonaws.com/openstudio-sdk-documentation/cpp/OpenStudio-3.7.0-doc/model/html/index.html#introduction_model).
    2. OpenStudio Measure are applied to the osm.
        - A set of programmable instructions that make changes to the osm model (https://nrel.github.io/OpenStudio-user-documentation/getting_started/about_measures/).
        - A measure must consists of all the files listed <a href="https://nrel.github.io/OpenStudio-user-documentation/reference/measure_writing_guide/#measure-file-structure" target="_blank">here.</a> 
        - You can find a library of Measures at the Building Component Library webpage (https://bcl.nrel.gov/)
    3. The osm model is then translated to either IDF for energyplus simulation or .RAD for radiance simulation depending on the measure. The measure will specify how the data from each simulation results feed into each other.
        - for example "Radiance Daylighting Measure" (https://bcl.nrel.gov/api/download?uids=1e3cfef8-b051-4e60-8bb0-ed2d29d4f45f) - The OpenStudio model is converted to Radiance format. All spaces containing daylighting objects (illuminance map, daylighting control point, and optionally glare sensors) will have annual illuminance calculated using Radiance, and the OS model's lighting schedules can be overwritten with those based on daylight responsive lighting controls.
    4. Once the simulations are completed. The OpenStudio Reporting Measures are applied to generate reports of the simulation result.
        - An error at any point will stop the execution. Whether successful or not an output OSW file is written to show the output of the workflow. 

## Weather Files
- https://climate.onebuilding.org/
- https://energyplus.net/weather

## Running a simple simulation with OpenStudio Application
1. Download and install OpenStudio Application [here](https://github.com/openstudiocoalition/OpenStudioApplication/releases)
2. Open OpenStudio. Go to File -> Examples -> Example Model
3. In the site tab, specify the weather file. You can download weather file from [here](https://www.ladybug.tools/epwmap/)
4. Save the file. A structure of files are created for OpenStudio Workflow run.
5. Go to the Run Simulation tab and click run. The file structure will be populated with result file.

## Building Energy Modeling with Python OpenStudio SDK
1. Install the python binding of Openstudio
    ```
    pip install openstudio
    ```
2. Example script
    ```
    import openstudio
    from openstudio import model as osmod

    m = osmod.Model()
    mat = osmod.StandardOpaqueMaterial(m)
    mat.setThickness(0.3)
    print(mat)

    epw_path = '/epw/SGP_SG_Changi.Intl.AP.486980_TMYx.2007-2021.epw'
    epwfile = openstudio.openstudioutilitiesfiletypes.EpwFile(epw_path)

    m = osmod.exampleModel()
    oswf = m.getWeatherFile()
    oswf.setWeatherFile(m, epwfile)

    things = osmod.getSurfaces(m)
    for thing in things:
        print('construction', thing.isConstructionDefaulted())
        print('ufactor', thing.uFactor())
        print('type', thing.surfaceType())
        
        vs = thing.vertices()
        for v in vs:
            print(type(v))
            print(v.x(), v.y(), v.z())
        
        things = osmod.getBuildingStorys(m)
        for thing in things:
            print(thing)
        
    ft = openstudio.energyplus.ForwardTranslator()
    idf = ft.translateModel(m)
    path = 'out.idf'
    idf.save(path, True)

    path = 'test.osm'
    m.save(path, True)
    ```
## IFC to OSM
1. Model a IFC building model [You can do it with FreeCAD](05_fcbim.md) and then [export it IFC](05_fcbim.md#export-to-ifc-and-read-with-ifcopenshell).
2. Using the IFCOpenshell and the OpenStudio library convert the IFC model to OSM. 

## Resources
- reference document (https://s3.amazonaws.com/openstudio-sdk-documentation/index.html)
- openstudio resources (https://github.com/NREL/OpenStudio-resources)
- example script 
    - create material (https://unmethours.com/question/97305/create-a-new-material-with-python-bindings/)
    - run energyplus + radiance simulation (https://unmethours.com/question/84025/co-simulation-with-e-and-radiance-in-openstudio/)
    - python example
        - https://github.com/jmarrec/OpenStudio_to_EnergyPlusAPI/blob/main/OpenStudio_to_EnergyPlusAPI.ipynb
        - https://snyk.io/advisor/python/openstudio/functions/openstudio.model.getSpaces
        - https://github.com/GFlechas/Gabes_OpenStudio_shared_resources/blob/main/load_model_add_ruleset/load_model_add_ruleset_notebook.ipynb