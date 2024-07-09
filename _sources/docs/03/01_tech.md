# Open-Source Technologies and Data for Design Process

## Resources
- Open Toolchain Foundation: https://opentoolchain-foundation.org/
    - open source ecosystems for better engineering
- https://osarch.org/
    - Creating a built environment with free software, increased transparency, and a more ethical approach
- ASHRAE Standards and Guideline (https://www.ashrae.org/technical-resources/standards-and-guidelines/read-only-versions-of-ashrae-standards)

## Datasets
- US commercial building stocks (https://comstock.nrel.gov/)

## Open Data Models
### Geographic Information System
- CityGML (https://www.ogc.org/standard/CityGML/)
- LandInfra (https://www.ogc.org/standard/infragml/)
- Shapefile (https://www.esri.com/content/dam/esrisites/sitecore-archive/Files/Pdfs/library/whitepapers/pdfs/shapefile.pdf)
- Digital Elevation Model (DEM) (https://www.usgs.gov/faqs/what-digital-elevation-model-dem)
- LAS (https://www.ogc.org/standard/LAS/)
- Building footprint of the whole world (https://github.com/microsoft/GlobalMLBuildingFootprints)

### Building Information Modeling
- Industry Foundation Class (IFC) 
    - Introduction: https://technical.buildingsmart.org/standards/ifc/
    - Formats: https://technical.buildingsmart.org/standards/ifc/ifc-formats/
    - Specificaitons: https://technical.buildingsmart.org/standards/ifc/ifc-schema-specifications/
        - IFC 4X3_ADD2: https://standards.buildingsmart.org/IFC/RELEASE/IFC4_3/
            - https://standards.buildingsmart.org/IFC/RELEASE/IFC4_3/HTML/ifcsharedbldgelements/content.html
            - to read the specifications, the attributes click the expand toggle at the bottom of the table. The numbered attributes are what define the entity.
- Constructon to Operations Building Information Exhcnage (COBie): https://www.nibs.org/nbims/cobie/v3
- BIM Collaboration Format (BCF): https://technical.buildingsmart.org/standards/bcf/
- Information Delivery Specification (IDS)
    - https://technical.buildingsmart.org/projects/information-delivery-specification-ids/
    - https://github.com/buildingSMART/IDS
- Standard 224-2023 -- Standard for the Application of Building Information Modeling (ANSI Approved) (https://ashrae.iwrapper.com/ASHRAE_PREVIEW_ONLY_STANDARDS/STD_224_2023)

### Building Operations
- Brick Schema (https://brickschema.org/)
- Guideline 36-2021 -- High-Performance Sequences of Operation for HVAC Systems (https://ashrae.iwrapper.com/ASHRAE_PREVIEW_ONLY_STANDARDS/GL_36_2021) 
- Standard 135-2020, BACnetTM A Data Communication Protocol for Building Automation and Control Networks (https://ashrae.iwrapper.com/ASHRAE_PREVIEW_ONLY_STANDARDS/STD_135_2020)
- CDL (https://obc.lbl.gov/specification/cdl.html#overview-of-cdl-and-terminology)
- Project Haystack (https://project-haystack.org/)
- ASHRAE 223P
    - https://open223.info/
    - https://github.com/open223
- BOPTest simulation based controls https://github.com/ibpsa/project1-boptest?tab=readme-ov-file

### Building Performance Simulation
- Openstudio Model (https://openstudio.net/) 
- Standard 90.1-2022 (S-I), Energy Standard for Buildings Except Low-Rise Residential Buildings (https://www.ashrae.org/technical-resources/standards-and-guidelines/read-only-versions-of-ashrae-standards)
- Standard 90.2-2018, Energy Efficient Design of Low-Rise Residential Buildings (https://ashrae.iwrapper.com/ASHRAE_PREVIEW_ONLY_STANDARDS/STD_90.2_2018)
- Standard 189.1-2020, Standard for the Design of High-Performance Green Buildings (https://www.ashrae.org/technical-resources/standards-and-guidelines/read-only-versions-of-ashrae-standards)
- Standard 183-2007 (RA 2020) -- Peak Cooling and Heating Load Calculations in Buildings Except Low-Rise Residential Buildings (ACCA Co-sponsored) (https://ashrae.iwrapper.com/ASHRAE_PREVIEW_ONLY_STANDARDS/STD_183_2007_RA_2020)

## 3D Modeling
- Blender 3D (https://www.blender.org/) - Visualization
- FreeCAD (https://www.freecadweb.org/) - 3D Drafting
    - Native IFC workbench just like BlenderBIM: https://github.com/yorikvanhavre/FreeCAD-NativeIFC
    - Geodata workbench for importing geospatial data: https://github.com/microelly2/geodata
- JupyterCAD (https://github.com/jupytercad/jupytercad) - Collaborative CAD environment. Works with FreeCAD.
- Wikiifc (https://wikiifc.com/) - web-based ifc tool for viewing and limited editing of IFC files

## 2D Drawings
- LibreCAD (https://librecad.org/) - Drafting
- GIMP (https://www.gimp.org/) - Image editor
- Inkscape (https://inkscape.org/) - Illustration

## Geographic Information System (GIS)
- QGIS (https://qgis.org)

## Building Performance Simulation
- EnergyPlus (https://energyplus.net/)
- Radiance (https://www.radiance-online.org/)
- Modelica Building Library (https://simulationresearch.lbl.gov/modelica/)
    - Spawn of Energyplus - coupling of energyplus with modelica building library
- ladybug tools (https://www.ladybug.tools/index.html#header-slide-show)
- Blender3d Vi-suite: https://blogs.brighton.ac.uk/visuite/
- Python HVAC - https://pythoncvc.net/?author=1&lang=en

## Scripting
- Python (https://www.python.org/) - Batteries included programming language for automating the boring stuffs
  - Geomie3d (https://github.com/chenkianwee/geomie3d) - geometry kernel in development
  - py3dtileslib (https://github.com/chenkianwee/py3dtileslib) - generating 3dtiles for web visualization
  - IFCopenshell (http://ifcopenshell.org/) - Read and write IFC files
  - Scikit-learn (https://scikit-learn.org/stable/index.html) - machine learning
  - networkx (https://networkx.org/) - network modeling
- Programming for Designers: https://www.coursera.org/specializations/programming-for-designers

## Web Vizualization
- CesiumJS (https://cesium.com/platform/cesiumjs/)
- Itowns (https://github.com/iTowns/itowns)
- Vts Geospatial (https://vts-geospatial.org/)
- Kepler.gl (https://kepler.gl/)
- Pyodide (https://pyodide.org/en/stable/) - run python in the browser
- Pyscript (https://pyscript.net/) - framework for python in the browser
- JupyterHub (https://jupyter.org/hub) - ide for coding in the browser
- xeokit-bim-viewer: https://github.com/xeokit/xeokit-bim-viewer

## Data Management
- 3DCityDB (https://www.3dcitydb.org/3dcitydb/)
- FROST-Server (https://github.com/FraunhoferIOSB/FROST-Server)
- Timescale (https://www.timescale.com/)
- BIMServer (https://github.com/opensourceBIM/BIMserver)
- speckle (https://speckle.systems/)
- COMPAS (https://compas.dev/)
- Bhom (https://bhom.xyz/)
- Yun2Infinity (https://github.com/chenkianwee/yun2infinity)

## Communication
- Element.io (https://element.io/)

## Collaboration
- https://www.goodfirms.co/collaboration-software/blog/best-free-open-source-collaboration-software

## Reality Capture
### Photogrammetry
- Article about photogrammetry software (https://all3dp.com/2/best-free-photogrammetry-software/)
- Conclusion
    - It is not useful for capturing room. Rooms have bland walls, which make feature detection difficult (https://github.com/alicevision/Meshroom/issues/2144).
    - Need a 3d lidar scanner for scanning room.
        - the use of Ipad or iphone could be a viable solution (https://canvas.io/#pricing)
        - most low-cost off-the-shelf 3d scanners are developed for scanning benchscale products
            - https://us.revopoint3d.com/?&msclkid=a4160ffc1b5214fb34d26b2e7cd3ce76&utm_source=bing&utm_medium=cpc&utm_campaign=%5BUS%5D3D%20Scanner(p)_en&utm_term=3d%20scanner&utm_content=3D%20scanner(e)_en
            - https://store.creality.com/products/cr-scan-ferret-3d-scanner?spm=..404.header_1.1
        - Overview of 3dscanners on the market (https://www.3dsourced.com/rankings/best-3d-scanner/)
        - roomscale scanners quickly become very expensive >USD 5000 
            - https://matterport.com/pro3?utm_source=bing&utm_medium=ppc&utm_campaign=EN_NORAM_US_Shopping&utm_content=&utm_term=&device=c&nw=s&msclkid=938aa638b49917a52adac2eadd95949e
        - DIY solution 
            - https://courses.engr.illinois.edu/ece445/getfile.asp?id=19286
            - https://www.seeedstudio.com/catalogsearch/result/?q=lidar
            
- Software that I have tried:
    - Meshroom (https://alicevision.org/#meshroom), the most matured and well-supported open-source photogrammetry software. However, it does not have a MacOS version.
        - https://meshroom-manual.readthedocs.io/en/bibtex1/test/test.html
    - visualSFM (http://ccwu.me/vsfm/index.html) - not allowed for commercial use.
        - http://ccwu.me/vsfm/doc.html#usage
    - OpenCV Python (https://github.com/opencv/opencv-python) both of the tutorials below are not working anymore.
        - https://github.com/adnappp/Sfm-python
        - https://github.com/muneebaadil/how-to-sfm
- Software that require you to compile
    - https://github.com/openMVG/openMVG
    - https://demuc.de/colmap/
    - https://github.com/micmacIGN/micmac
- Related software
    - https://github.com/isl-org/Open3D – uses rgbd images (need realsense camera) for reconstruction.
