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

### Generating Annual Meteorological Year (AMY) EPW file
- Ladybug tools forum: https://discourse.ladybug.tools/t/generate-epw-file-for-any-location-any-year-from-1940/21263
- https://github.com/IMMM-SFA/diyepw


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
    ```{dropdown} Script to Examine the OSModel
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

2. Using the IFCOpenshell and the OpenStudio library convert the IFC model to OSM. Here is an example script that converts a IFC file to OSM and uses an ideal air loads calculation to get the cooling load.
    ```{dropdown} Script to Convert IFC to OSModel
        import json
        import pathlib
        import subprocess
        from distutils.dir_util import copy_tree

        import numpy as np

        import ifcopenshell
        import ifcopenshell.geom

        import geomie3d

        import openstudio
        from openstudio import model as osmod

        #===================================================================================================
        # region: PARAMETERS
        #===================================================================================================
        ifc_path = '/example.ifc'
        res_path = '/osm/res_folder'
        epw_path = '/epw/SGP_Singapore.486980_IWEC.epw'
        # set measure type 0=ModelMeasure, 1=EnergyPlusMeasure, 2=UtilityMeasure, 3=ReportingMeasure
        measure_folder_list = [
                {'dir':'/measures/ideal_air_loads_zone_hvac/', 'type': 0,
                'arguments': [{'argument': 'include_outdoor_air', 'value': True}],
                'description': 'This OpenStudio measure will replace the existing HVAC system with ideal air loads objects for each conditioned zone and allow the user to specify input fields including availability schedules, humidity controls, outdoor air ventilation, demand controlled ventilation, economizer operation, and heat recovery.  The measure optionally creates custom meter and output meter objects that sum all ideal loads output variables for further analysis.',
                'modeler_description': 'This measure creates ZoneHVACIdealLoadsAirSystem objects for each conditioned zone using the model_add_ideal_air_loads method in the openstudio-standards gem.  If the Include Outdoor Air Ventilation? option is set to false, the measure will remove all Design Specification Outdoor Air objects in the model so that they dont get written to the ideal loads objects during forward translation.'
                },
                {'dir':'/measures/openstudio_results/', 'type': 3,
                'arguments': [{'argument': 'units', 'value': 'SI'}],
                'description': 'This measure creates high level tables and charts pulling both from model inputs and EnergyPlus results. It has building level information as well as detail on space types, thermal zones, HVAC systems, envelope characteristics, and economics. Click the heading above a chart to view a table of the chart data.',
                'modeler_description': 'For the most part consumption data comes from the tabular EnergyPlus results, however there are a few requests added for time series results. Space type and loop details come from the OpenStudio model. The code for this is modular, making it easy to use as a template for your own custom reports. The structure of the report uses bootstrap, and the graphs use dimple js. The new measure warning section will show warnings generated by upstream measures. It will not show forward translation warnings, EnergyPlus warnings, or warnings that might be reported by this measure.'
                },
            ]

        # endregion: PARAMETERS
        #===================================================================================================
        # region: FUNCTIONS
        #===================================================================================================
        def g3dverts2ospt3d(g3dverts, decimals = 6):
            pt3ds = []
            for v in g3dverts:
                xyz = v.point.xyz
                xyz = np.round(xyz, decimals=decimals)
                x = xyz[0]
                y = xyz[1]
                z = xyz[2]
                pt3d = openstudio.openstudioutilitiesgeometry.Point3d(x,y,z)
                pt3ds.append(pt3d)
            return pt3ds

        def save_osw_project(proj_dir: str, openstudio_model: osmod, measure_folder_list: list[dict]) -> str:
            # create all the necessary directory
            proj_path = pathlib.Path(proj_dir)
            proj_name = proj_path.stem
            wrkflow_dir = pathlib.PurePath(proj_dir, proj_name + '_wrkflw')
            dir_ls = ['files', 'measures', 'run']
            for dir in dir_ls:
                dir_in_wrkflw = pathlib.PurePath(wrkflow_dir, dir)
                pathlib.Path(dir_in_wrkflw).mkdir(parents=True, exist_ok=True)
            
            # create the osm file
            osm_filename = proj_name + '.osm'
            osm_path = pathlib.PurePath(proj_dir, osm_filename)
            openstudio_model.save(str(osm_path), True)

            # retrieve the osw file
            oswrkflw = openstudio_model.workflowJSON()
            oswrkflw.setSeedFile('../' + osm_filename)

            # create the result measure into the measures folder
            msteps = {0: [], 1: [], 2: [], 3: []}
            for measure_folder in measure_folder_list:
                measure_dir_orig = measure_folder['dir']
                foldername = pathlib.Path(measure_dir_orig).stem
                measure_dir_dest = str(pathlib.PurePath(wrkflow_dir, 'measures', foldername))
                copy_tree(measure_dir_orig, measure_dir_dest)
                # set measurestep
                mstep = openstudio.MeasureStep(measure_dir_dest)
                mstep.setName(foldername)
                mstep.setDescription(measure_folder['description'])
                mstep.setModelerDescription(measure_folder['modeler_description'])
                if 'arguments' in measure_folder.keys():
                    arguments = measure_folder['arguments']
                    for argument in arguments:
                        mstep.setArgument(argument['argument'], argument['value'])
                msteps[measure_folder['type']].append(mstep)
            
            for mt_val in msteps.keys():
                measure_type = openstudio.MeasureType(mt_val)
                measure_steps = msteps[mt_val]
                if len(measure_steps) != 0:
                    oswrkflw.setMeasureSteps(measure_type, measure_steps)
            
            wrkflw_path = str(pathlib.PurePath(wrkflow_dir, proj_name + '.osw'))
            oswrkflw.saveAs(wrkflw_path)
            with open(wrkflw_path) as wrkflw_f:
                data = json.load(wrkflw_f)
                steps = data['steps']
                for step in steps:
                    dirname = step['measure_dir_name']
                    foldername = pathlib.Path(dirname).stem
                    step['measure_dir_name'] = foldername

            out_file = open(wrkflw_path, "w") 
            json.dump(data, out_file)
            out_file.close()
            return wrkflw_path

        def save2idf(idf_path: str, openstudio_model: osmod):
            ft = openstudio.energyplus.ForwardTranslator()
            idf = ft.translateModel(openstudio_model)
            idf.save(idf_path, True)

        def get_ifc_facegeom(ifc_object: ifcopenshell.entity_instance) -> tuple[np.ndarray, np.ndarray]:
            """
            get the face geometry of the ifc entty, only works with ifc entity with geometry
            
            Parameters
            ----------
            ifc_object : ifcopenshell.entity_instance.entity_instance
                ifcopenshell entity.
            
            Returns
            -------
            result : tuple[np.ndarray, np.ndarray]
                tuple[np.ndarray[number_of_verts, 3], np.ndarray[number_of_faces, 3]] 
            """
            settings = ifcopenshell.geom.settings()
            shape = ifcopenshell.geom.create_shape(settings, ifc_object)
            verts = shape.geometry.verts # X Y Z of vertices in flattened list e.g. [v1x, v1y, v1z, v2x, v2y, v2z, ...]
            verts3d = np.reshape(verts, (int(len(verts)/3), 3))
            verts3d = np.round(verts3d, decimals = 2)
            face_idx = shape.geometry.faces
            face_idx3d = np.reshape(face_idx, (int(len(face_idx)/3), 3))
            return verts3d, face_idx3d

        def ifcopenshell_entity_geom2g3d(ifc_object: ifcopenshell.entity_instance) -> list[geomie3d.topobj.Face]:
            """
            Retrive the triangulated faces from the ifc_object. Merge the triangulated face from the ifcopenshell geometry into a single geomie3d face. 

            Parameters
            ----------
            ifc_object: ifcopenshell.entity_instance.entity_instance
                the ifc_object to retrieve geometry from.
            
            Returns
            -------
            faces : list[geomie3d.topobj.Face]
                list of geomie3d faces.
            """
            verts3d, face_idx3d = get_ifc_facegeom(ifc_object)
            g3d_verts = geomie3d.create.vertex_list(verts3d)
            face_pts = np.take(g3d_verts, face_idx3d, axis=0)
            flist = []
            for fp in face_pts:
                f = geomie3d.create.polygon_face_frm_verts(fp)
                flist.append(f)
            
            grp_faces = geomie3d.calculate.grp_faces_on_nrml(flist)
            mfs = []
            for grp_f in grp_faces[0]:
                outline = geomie3d.calculate.find_faces_outline(grp_f)[0]
                # geomie3d.viz.viz([{'topo_list': outline, 'colour': 'blue'}])
                n_loose_edges = 3
                loop_cnt = 0
                while n_loose_edges >= 3:
                    path_dict = geomie3d.calculate.a_connected_path_from_edges(outline)
                    outline = path_dict['connected']
                    # geomie3d.viz.viz([{'topo_list': outline, 'colour': 'blue'}])
                    bwire = geomie3d.create.wire_frm_edges(outline)
                    mf = geomie3d.create.polygon_face_frm_wires(bwire)
                    mfs.append(mf)
                    outline = path_dict['loose']
                    if loop_cnt == 0:
                        n_loose_edges = len(path_dict['loose'])
                    else:
                        if n_loose_edges - len(path_dict['loose']) == 0:
                            n_loose_edges = 0
                        else:        
                            n_loose_edges = len(path_dict['loose'])
                    loop_cnt+=1
            return mfs

        def create_rays_frm_verts(vertices: list[geomie3d.topobj.Vertex], dir_xyz: list[float]) -> list[geomie3d.utility.Ray]:
            rays = []
            for v in vertices:
                ray = geomie3d.create.ray(v.point.xyz, dir_xyz)
                rays.append(ray)
            return rays

        def extract_intx_frm_hit_rays(hit_rays: list[geomie3d.utility.Ray]) -> list[geomie3d.topobj.Vertex]:
            vs = []
            for r in hit_rays:
                att = r.attributes['rays_faces_intersection']
                intx = att['intersection'][0]
                v = geomie3d.create.vertex(intx)
                vs.append(v)
            return vs

        def setup_ppl_schedule(ruleset: osmod.ScheduleRuleset, act_ruleset:osmod.ScheduleRuleset, name: str = None) -> osmod.People:
            # occupancy definition
            ppl_def = osmod.PeopleDefinition(m)
            ppl = osmod.People(ppl_def)
            ppl.setNumberofPeopleSchedule(ruleset)
            ppl.setActivityLevelSchedule(act_ruleset)
            if name != None:
                ppl_def.setName(name + 'definition')
                ppl.setName(name)
            return ppl

        def setup_light_schedule(ruleset: osmod.ScheduleRuleset, name: str = None) -> osmod.Lights:
            # light definition
            light_def = osmod.LightsDefinition(m)
            light = osmod.Lights(light_def)
            if name != None:
                light_def.setName(name + '_definition')
                light.setName(name)
            light.setSchedule(ruleset)
            return light

        def setup_elec_equip_schedule(ruleset: osmod.ScheduleRuleset, name: str = None) -> osmod.ElectricEquipment:
            # light definition
            elec_def = osmod.ElectricEquipmentDefinition(m)
            elec_equip = osmod.ElectricEquipment(elec_def)
            if name != None:
                elec_def.setName(name + '_definition')
                elec_equip.setName(name)
            elec_equip.setSchedule(ruleset)
            return elec_equip

        def execute_workflow(wrkflow_path:str):
            print('executing workflow ...')
            result = subprocess.run(['openstudio', 'run', '-w', wrkflow_path], capture_output=True, text=True)
            print(result.stdout)

        #endregion: FUNCTIONS
        #===================================================================================================
        # region: MAIN
        #===================================================================================================
        # region: read the ifc file and extract all the necessary information for conversion to osm
        model = ifcopenshell.open(ifc_path)

        spaces = model.by_type('IfcSpace')
        envelope = []
        for space in spaces:
            space_info = space.get_info()
            space_name = space_info['LongName']
            mfs = ifcopenshell_entity_geom2g3d(space)
            for cnt, mf in enumerate(mfs):
                geomie3d.modify.update_topo_att(mf, {'space': space_name})
                geomie3d.modify.update_topo_att(mf, {'name': space_name + '_envelope_' + str(cnt)})
            envelope.extend(mfs)

        #geomie3d.viz.viz([{'topo_list': envelope, 'colour': 'blue', 'attribute': 'space'}])

        ifc_windows = model.by_type('IfcWindow')
        windows = []
        for win in ifc_windows:
            # find the center point of the window
            verts3d, face_idx3d = get_ifc_facegeom(win)
            g3d_verts = geomie3d.create.vertex_list(verts3d)
            bbox = geomie3d.calculate.bbox_frm_xyzs(verts3d)
            center_xyz = geomie3d.calculate.bbox_centre(bbox)
            center_vert = geomie3d.create.vertex(center_xyz)
            # using the center point, project rays out and find the closest envelope surfaces
            rays = geomie3d.create.rays_d4pi_frm_verts([center_vert], ndirs = 12, vert_id = False)
            ray_res = geomie3d.calculate.rays_faces_intersection(rays, envelope)
            hit_rays = ray_res[0]
            dists = []
            hit_faces = []
            for r in hit_rays:
                orig = r.origin
                att = r.attributes['rays_faces_intersection']
                hit_face = att['hit_face'][0]
                intx = att['intersection'][0]
                dist = geomie3d.calculate.dist_btw_xyzs(orig, intx)
                hit_faces.append(hit_face)
                dists.append(dist)
            
            min_id = np.argmin(dists)
            min_face = hit_faces[min_id]

            # project the bbox onto the closest surface base on the reverse surface normal
            n = geomie3d.get.face_normal(min_face)
            n = geomie3d.calculate.reverse_vectorxyz(n)
            n = np.round(n, decimals=3)
            box = geomie3d.create.boxes_frm_bboxes([bbox])[0]
            box_faces = geomie3d.get.faces_frm_solid(box)
            fuse1 = []
            for box_face in box_faces:
                verts = geomie3d.get.vertices_frm_face(box_face)
                bface_rays = create_rays_frm_verts(verts, n)
                ray_res2 = geomie3d.calculate.rays_faces_intersection(bface_rays, [min_face])
                hit_rays2 = ray_res2[0]
                intx_v = extract_intx_frm_hit_rays(hit_rays2)
                fused_intx = geomie3d.modify.fuse_vertices(intx_v, decimals=4)
                if len(fused_intx) > 3:
                    fuse1.extend(fused_intx)
            if len(fuse1) != 0:
                fuse2 = geomie3d.modify.fuse_vertices(fuse1, decimals=4)
                fuse_face = geomie3d.create.polygon_face_frm_verts(fuse2)
                if 'children' in min_face.attributes.keys():
                    min_face.attributes['children'].append(fuse_face)
                else:
                    min_face.attributes['children'] = [fuse_face]
                windows.append(fuse_face)

        # win_nrml_edges = geomie3d.create.pline_edges_frm_face_normals(windows)
        # env_nrml_edges = geomie3d.create.pline_edges_frm_face_normals(envelope)
        # geomie3d.viz.viz([{'topo_list': windows, 'colour': 'blue'},
        #                   {'topo_list': envelope, 'colour': 'red'},
        #                   {'topo_list': win_nrml_edges, 'colour': 'green'},
        #                   {'topo_list': env_nrml_edges, 'colour': 'white'}])
                
        # endregion
        #------------------------------------------------------------------------------------------------------
        # region: convert the geometry to openstudio
        #------------------------------------------------------------------------------------------------------
        m = osmod.Model()
        osbldgstry = osmod.BuildingStory(m)
        osbldgstry.setName('Mezzanine Level')

        epwfile = openstudio.openstudioutilitiesfiletypes.EpwFile(epw_path)
        oswrkflw = openstudio.WorkflowJSON()
        m.setWorkflowJSON(oswrkflw)
        oswf = m.getWeatherFile()
        oswf.setWeatherFile(m, epwfile)

        osm_sitegrd = osmod.SiteGroundTemperatureBuildingSurface(m)
        for i in range(12):
            temp = osm_sitegrd.setTemperatureByMonth(i+1, 25.0)

        # create wall materials and construction
        metal_clad_mat = osmod.MasslessOpaqueMaterial(m, "Rough", 0.36)
        metal_clad_mat.setThermalAbsorptance(0.9)
        metal_clad_mat.setSolarAbsorptance (0.7)
        metal_clad_mat.setVisibleAbsorptance (0.7)

        cement_board_mat = osmod.StandardOpaqueMaterial(m, "Smooth", 0.009, 
                                                        0.58, 1900, 1400)
        wall_const = osmod.Construction(m)
        wall_const.setName('wall_construction')
        wall_const.setLayers([metal_clad_mat, cement_board_mat])

        # create window material and construction
        lowe_glz_mat = osmod.SimpleGlazing(m, 1.4, 0.62)
        lowe_glz_mat.setVisibleTransmittance(0.78)
        win_const = osmod.Construction(m)
        win_const.setName('win_construction')
        win_const.setLayers([lowe_glz_mat])

        space_ls = []
        for env_srf in envelope:
            srf_att = env_srf.attributes
            osspace_name = srf_att['space']
            if osspace_name not in space_ls:
                oszone = osmod.ThermalZone(m)
                # oszone.setUseIdealAirLoads(True)
                osspace = osmod.Space(m)
                osspace.setName(osspace_name)
                osspace.setBuildingStory(osbldgstry)
                osspace.setThermalZone(oszone)
                space_ls.append(osspace_name)

            nrml = geomie3d.get.face_normal(env_srf)
            vs = geomie3d.get.vertices_frm_face(env_srf)
            pt3ds = g3dverts2ospt3d(vs, decimals = 1)
            ossrf = osmod.Surface(pt3ds, m)
            ossrf.setSpace(osspace)
            ossrf.setConstruction(wall_const)

            if 'children' in srf_att.keys():
                children = env_srf.attributes['children']
                parent_nrml = geomie3d.get.face_normal(env_srf)
                parent_nrml = np.round(parent_nrml, decimals=3)
                for child_srf in children:
                    child_nrml = geomie3d.get.face_normal(child_srf)
                    child_nrml = np.round(child_nrml, decimals=3)
                    child_vs = geomie3d.get.vertices_frm_face(child_srf)
                    child_pt3ds = g3dverts2ospt3d(child_vs, decimals=1)
                    # print(child_nrml, parent_nrml)
                    if not (child_nrml == parent_nrml).all():
                        child_pt3ds.reverse()
                    # for pt in child_pt3ds:
                    #     print(pt.x(), pt.y(), pt.z())
                    child_ossrf = osmod.SubSurface(child_pt3ds, m)
                    child_ossrf.setSurface(ossrf)
                    child_ossrf.setConstruction(win_const)
                    # print(child_ossrf)

        # setup time
        time9 = openstudio.openstudioutilitiestime.Time(0,9,0,0)
        time17 = openstudio.openstudioutilitiestime.Time(0,17,0,0)
        time24 = openstudio.openstudioutilitiestime.Time(0,24,0,0)

        # region:setup schedule type limits
        sch_type_lim_frac = osmod.ScheduleTypeLimits(m)
        sch_type_lim_frac.setName('fractional')
        sch_type_lim_frac.setLowerLimitValue(0.0)
        sch_type_lim_frac.setUpperLimitValue(1.0)
        sch_type_lim_frac.setNumericType('Continuous')

        sch_type_lim_temp = osmod.ScheduleTypeLimits(m)
        sch_type_lim_temp.setName('temperature')
        sch_type_lim_temp.setLowerLimitValue(-60)
        sch_type_lim_temp.setUpperLimitValue(200)
        sch_type_lim_temp.setNumericType('Continuous')
        sch_type_lim_temp.setUnitType('Temperature')

        sch_type_lim_act = osmod.ScheduleTypeLimits(m)
        sch_type_lim_act.setName('activity')
        sch_type_lim_act.setLowerLimitValue(75)
        # sch_type_lim_act.setUpperLimitValue(500)
        sch_type_lim_act.setNumericType('Continuous')
        sch_type_lim_act.setUnitType('ActivityLevel')
        # endregion:setup schedule type limits
        # region: setup occ schedule
        sch_day_occ = osmod.ScheduleDay(m)
        sch_day_occ.setName('weekday occupancy')
        sch_day_occ.setScheduleTypeLimits(sch_type_lim_frac)
        sch_day_occ.addValue(time9, 0.0)
        sch_day_occ.addValue(time17, 1.0)

        sch_ruleset_occ = osmod.ScheduleRuleset(m)
        sch_ruleset_occ.setName('occupancy schedule')
        sch_ruleset_occ.setScheduleTypeLimits(sch_type_lim_frac)

        sch_rule_occ = osmod.ScheduleRule(sch_ruleset_occ, sch_day_occ)
        sch_rule_occ.setName('occupancy weekdays')
        sch_rule_occ.setApplyWeekdays(True)
        # endregion: setup occ schedule
        # region: setup activity schedule
        sch_day_act = osmod.ScheduleDay(m)
        sch_day_act.setName('weekday activity')
        sch_day_act.setScheduleTypeLimits(sch_type_lim_act)
        sch_day_act.addValue(time24, 90)

        sch_ruleset_act = osmod.ScheduleRuleset(m)
        sch_ruleset_act.setName('activity schedule')
        sch_ruleset_act.setScheduleTypeLimits(sch_type_lim_act)

        sch_rule_act = osmod.ScheduleRule(sch_ruleset_act, sch_day_act)
        sch_rule_act.setName('activity weekdays')
        sch_rule_act.setApplyWeekdays(True)
        # endregion: setup activity schedule
        # region: setup thermostat cooling setpoint
        sch_day_cool_tstat = osmod.ScheduleDay(m)
        sch_day_cool_tstat.setName('thermostat cooling weekday schedule')
        sch_day_cool_tstat.setScheduleTypeLimits(sch_type_lim_temp)
        sch_day_cool_tstat.addValue(time9, 60.0)
        sch_day_cool_tstat.addValue(time17, 25.0)
        sch_day_cool_tstat.addValue(time24, 60.0)

        sch_day_cool_tstat2 = osmod.ScheduleDay(m)
        sch_day_cool_tstat2.setName('thermostat cooling weekends schedule')
        sch_day_cool_tstat2.setScheduleTypeLimits(sch_type_lim_temp)
        sch_day_cool_tstat2.addValue(time24, 60.0)

        sch_ruleset_cool_tstat = osmod.ScheduleRuleset(m)
        sch_ruleset_cool_tstat.setName('thermostat cooling ruleset')
        sch_ruleset_cool_tstat.setScheduleTypeLimits(sch_type_lim_temp)

        sch_rule_cool_tstat = osmod.ScheduleRule(sch_ruleset_cool_tstat, sch_day_cool_tstat)
        sch_rule_cool_tstat.setName('thermostat cooling weekday rule')
        sch_rule_cool_tstat.setApplyWeekdays(True)

        sch_rule_cool_tstat = osmod.ScheduleRule(sch_ruleset_cool_tstat, sch_day_cool_tstat2)
        sch_rule_cool_tstat.setName('thermostat cooling weekend rule')
        sch_rule_cool_tstat.setApplyWeekends(True)

        # endregion: setup thermostat cooling setpoint
        # region: setup thermostat heating setpoint
        sch_day_hot_tstat = osmod.ScheduleDay(m)
        sch_day_hot_tstat.setName('thermostat heating weekday schedule')
        sch_day_hot_tstat.setScheduleTypeLimits(sch_type_lim_temp)
        sch_day_hot_tstat.addValue(time9, -10.0)
        sch_day_hot_tstat.addValue(time17, -10.0)
        sch_day_hot_tstat.addValue(time24, -10.0)

        sch_ruleset_hot_tstat = osmod.ScheduleRuleset(m)
        sch_ruleset_hot_tstat.setName('thermostat heating ruleset')
        sch_ruleset_hot_tstat.setScheduleTypeLimits(sch_type_lim_temp)

        sch_rule_hot_tstat = osmod.ScheduleRule(sch_ruleset_hot_tstat, sch_day_hot_tstat)
        sch_rule_hot_tstat.setName('thermostat heating weekday rule')
        sch_rule_hot_tstat.setApplyAllDays(True)
        # endregion: setup thermostat heating setpoint

        tstat = osmod.ThermostatSetpointDualSetpoint(m)
        tstat.setCoolingSetpointTemperatureSchedule(sch_ruleset_cool_tstat)
        tstat.setHeatingSetpointTemperatureSchedule(sch_ruleset_hot_tstat)

        # set the internal loads of the space
        # setup the lighting schedule
        light = setup_light_schedule(sch_ruleset_occ)
        # setup electric equipment schedule
        elec_equip = setup_elec_equip_schedule(sch_ruleset_occ)

        spaces = osmod.getSpaces(m)
        # occ_numbers = [34, 71]
        occ_numbers = [25, 50]
        light_watts_m2 = 5
        elec_watts_m2 = 10
        for cnt, space in enumerate(spaces):
            space_name = space.nameString()
            space.autocalculateFloorArea()
            outdoor_air = osmod.DesignSpecificationOutdoorAir(m)
            outdoor_air.setOutdoorAirFlowperPerson(0.006) #m3/s
            space.setDesignSpecificationOutdoorAir(outdoor_air)
            # setup people schedule
            ppl = setup_ppl_schedule(sch_ruleset_occ, sch_ruleset_act, name = space_name + '_people')
            space.setNumberOfPeople(occ_numbers[cnt], ppl)
            space.setLightingPowerPerFloorArea(light_watts_m2, light)
            space.setElectricEquipmentPowerPerFloorArea(elec_watts_m2, elec_equip)
            # setup the thermostat schedule
            thermalzone = space.thermalZone()
            if thermalzone.empty() == False:
                thermalzone_real = thermalzone.get()
                thermalzone_real.setThermostatSetpointDualSetpoint(tstat)

        wrkflw_path = save_osw_project(res_path, m, measure_folder_list)
        execute_workflow(wrkflw_path)
        # save2idf(idf_path, m)
        # endregion
        # endregion: MAIN
    ```

## Writing Python Measure
1. Create a new measure with the openstudio core
    ```
    openstudio measure new --class-name HVACResults --type EnergyPlusMeasure --language Python --name "My energyplus measure" --description "this is my measure" --modeler-description "this does complicated stuff" --taxonomy-tag "Reporting.QAQC" ./measures/hvac_results
    ```
    - you can look at the help with this command
        ```
        openstudio measure --help
        ```
2. You can update the measure.xml file with this command
    ```
    openstudio measure -u ./measures/hvac_results
    ```

## Openstudio Code Snippets
- Removing objects from workspace
    ```
    meter_objs = workspace.getObjectsByType('Output:Meter')
    print('found these objs' + str(meter_objs))
    workspace.removeObject(meter_objs[0].handle())
    ```

## Understanding the energyplus output variables
The output variables is the lowest level result you can output from an energyplus simulation. These variables can be aggregated into meters where a meter is made up of a list of variables. On the highest level, you have the tables (report), where it is an aggregation of various variables and meters to form a complete performance view of the building.

- Energyplus documentation on output details: https://energyplus.net/assets/nrel_custom/pdfs/pdfs_v23.2.0/OutputDetailsAndExamples.pdf
- Energyplus input output reference: https://energyplus.net/assets/nrel_custom/pdfs/pdfs_v23.2.0/InputOutputReference.pdf
- Energyplus Output Files
    - **eplusout.eso**: Standard Output File (contains results from both Output:Variable and Output:Meter objects).
    - **eplusout.rdd**: Variable names that are applicable for reporting in the current simulation.
    - **eplusout.mtr**: Similar to .eso but only has Output:Meter outputs.
    - **eplusout.mtd**: Meter details report – what variables are on what meters and vice versa. This shows the meters on which the Zone: Lights Electric Energy appear as well as the contents of the Electricity:Facility meter
    - **eplusout.mdd**: Meter names that are applicable for reporting in the current simulation.
    - **eplusout.sql**: Mirrors the data in the .eso and .mtr files but is in SQLite format (for viewing with SQLite tools).

1. To output a variable, one needs to specify that output variable in the idf file for the simulation. Refer to the eplusout.rdd for the variable name.
    ```
    Output:Variable,
        ,                                       !- Key Value
        Zone Air Temperature,                   !- Variable Name
        hourly;                                 !- Reporting Frequency
    ```
    - the key value can be either the 'zone name' or a 'surface name' so you are reporting on a specific reference. 
2. To output a meter, one needs to specify that output meter in the idf file for the simulation. Refer to the eplusout.mtd for the meter name.
    ```
    Output:Meter,
        Electricity:Facility,                   !- Key Name
        Timestep;                               !- Reporting Frequency
    ```
    - A meter is the sum of all the variable under the meter. For example the meter 'EnergyTransfer:HVAC', the meter is the sum of the two variable listed below.
        ```
         For Meter=EnergyTransfer:HVAC [J], ResourceType=EnergyTransfer, Group=HVAC, contents are:
            THERMAL ZONE 1PTAC NO HEAT:Heating Coil Heating Energy
            THERMAL ZONE 1PTAC 1SPD DX AC CLG COIL:Cooling Coil Total Cooling Energy
        ```
    - In the .mtd file you can also see the variable 'THERMAL ZONE 1PTAC NO HEAT:Heating Coil Heating Energy' is in these meters.
        ```
        Meters for 7,THERMAL ZONE 1PTAC NO HEAT:Heating Coil Heating Energy [J]
            OnMeter=EnergyTransfer:Facility [J]
            OnMeter=EnergyTransfer:HVAC [J]
            OnMeter=HeatingCoils:EnergyTransfer [J]
        ```
3. To output a table, one needs to specify the output table in the idf file for the simulation. Refer to the eplusout.rdd for variable name and eplusout.mtd for the meter name. For a more detail description of the output tables and report refer to (https://energyplus.net/assets/nrel_custom/pdfs/pdfs_v23.2.0/InputOutputReference.pdf) Chapter 7: Standard Output Reports, Pg 2846. There are various ways to output tables in energyplus.
    - Output:Table:TimeBins
    - Output:Table:Monthly
    - Output:Table:Annual
    - Output:Table:ReportPeriod
    - Output:Table:SummaryReports - There are a number of predefine reports. If you specify 'AllSummary', all the report will be generated.
    - Output:Table:TableStyle - specify the different format of the output report 'Comma', 'Tab', 'Fixed', 'HTML', 'XML', 'CommaAndHTML', 'TabAndHTML', 'XMLAndHTML' and 'All'. 
    - An example of Output:Table:Monthly
        ```
        Output:Table:Monthly ,
            Building Monthly Cooling Load Report,       ! Name
            3,                                          ! Digits After Decimal
            Zone Air System Sensible Cooling Energy,    ! Variable or Meter 1 Name
            SumOrAverage,                               ! Aggregation Type for Variable or Meter 1
            Zone Air System Sensible Cooling Energy,    ! Variable or Meter 2 Name
            Maximum,                                    ! Aggregation Type for Variable or Meter 2
            Site Outdoor Air Drybulb Temperature,       ! Variable or Meter 3 Name
            ValueWhenMaxMin;                            ! Aggregation Type for Variable or Meter 3
        ```
### Python Openstudio Script to add Output Item to Idf files 
- output variable
    ```
    idfObject = openstudio.IdfObject(openstudio.IddObjectType('Output:Variable'))
    idfObject.setString(1, 'Zone Air Temperature')
    idfObject.setString(2, 'hourly')
    wsObject_ = workspace.addObject(idfObject)
    ```
- output meter
    ```
    idfObject = openstudio.IdfObject(openstudio.IddObjectType('Output:Meter'))
    idfObject.setString(0, 'EnergyTransfer:Facility')
    idfObject.setString(1, 'Timestep')
    wsObject_ = workspace.addObject(idfObject)
    ```
- output table monthly
    ```
    idfObject = openstudio.IdfObject(openstudio.IddObjectType('Output:Table:Monthly'))
    idfObject.setString(0, 'Building Monthly Cooling Load Report')
    idfObject.setString(1, '3')
    idfObject.setString(2, 'Zone Air System Sensible Cooling Energy')
    idfObject.setString(3, 'SumOrAverage')
    idfObject.setString(4, 'Zone Air System Sensible Cooling Energy')
    idfObject.setString(5, 'Maximum')
    wsObject_ = workspace.addObject(idfObject)
    ```
### Read the eplusout.sql
I will use the Ladybug tools library to read the sql generated from the energyplus result. Install the ladybug tool with pip.
```
pip install ladybug-core
```
```
from ladybug.sql import SQLiteResult
from pathlib import Path

eplusout_sql_path = Path(__file__).parent.joinpath('ifc2osm_eg_result', 'ifc2osm_eg_result_wrkflw', 'run', 'eplusout.sql')
sql_obj = SQLiteResult(eplusout_sql_path.__str__())
avail_output_names = sql_obj.available_outputs
run_indxs = sql_obj.run_period_indices
print(avail_output_names)
print(run_indxs)
data = sql_obj.data_collections_by_output_name_run_period('Zone Air Temperature', 3)
print(data[1].header.analysis_period)
print(data[1].header.metadata)

# data_collect = sql_obj.data_collections_by_output_name('Zone Air Relative Humidity')
# print(data_collect)
# zone_air_year = data_collect[2]
# dts = zone_air_year.datetimes
# print(dts[10].isoformat())
# for a_zair in zone_air_year:
#     print(a_zair)
```

## Resources
- API reference document (https://s3.amazonaws.com/openstudio-sdk-documentation/index.html)
- Openstudio-standards: the ruby library that is very useful for providing all the function uesful for energy modeling
    - structure of the big library - https://github.com/NREL/openstudio-standards/blob/master/docs/RepositoryStructure.md
    - 
- Resources
    - openstudio resources (https://github.com/NREL/OpenStudio-resources)
    - openstudio wiki (https://github.com/NREL/OpenStudio/wiki)
    - openstudio-standards ruby library API (https://www.rubydoc.info/gems/openstudio-standards/)
    - Radiant slab measure (https://bcl.nrel.gov/api/download?uids=8091a0c3-7760-4da6-adf4-133d55872816)
        - model_add_low_temp_radiant (https://www.rubydoc.info/gems/openstudio-standards/Standard#model_add_low_temp_radiant-instance_method)
        - model_add_doas (https://www.rubydoc.info/gems/openstudio-standards/Standard#model_add_doas-instance_method)
    - Adding Packaged Terminal Air-Conditioning
        - https://www.rubydoc.info/gems/openstudio-standards/Standard#model_add_ptac-instance_method
    - Sizing_run: https://github.com/NREL/openstudio-standards/blob/master/lib/openstudio-standards/utilities/simulation.rb#L173-L181
    - Parsing stat file: https://github.com/NREL/openstudio-standards/blob/master/lib/openstudio-standards/weather/stat_file.rb
    - Natural ventilation measure (https://bcl.nrel.gov/api/download?uids=c1290ecc-ef5b-4c64-8df5-367cc1129841)

- example script
    - create material (https://unmethours.com/question/97305/create-a-new-material-with-python-bindings/)
    - run energyplus + radiance simulation (https://unmethours.com/question/84025/co-simulation-with-e-and-radiance-in-openstudio/)
    - python example
        - https://github.com/jmarrec/OpenStudio_to_EnergyPlusAPI/blob/main/OpenStudio_to_EnergyPlusAPI.ipynb
        - https://snyk.io/advisor/python/openstudio/functions/openstudio.model.getSpaces
        - https://github.com/GFlechas/Gabes_OpenStudio_shared_resources/blob/main/load_model_add_ruleset/load_model_add_ruleset_notebook.ipynb
