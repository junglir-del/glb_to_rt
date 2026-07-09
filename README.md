# Example of loading a .glb 3D map into Sionna RT for mobile network (digital-twin) simulations 

1. Download any map based on latitude and longitude from https://download.geofabrik.de/ for the modeling of buildings and other objects, and from https://opentopography.org/ for DEM. Then, combine them to generate the .glb 3D map with buildings and terrain.

2. Convert the generated .glb 3D map into the format that Sionna RT can recognize, i.e., .xml + .ply.

3. Load the map (.xml and .ply) into Sionna RT, place the Tx and Rx and do configurations, after that, start the simulations.


# MapMatching

1. Download the xxx.osm.pbf map first from https://download.geofabrik.de/. Since the files are large, you can put them in a separate folder named "Maps".
2. Apply for a free API key from https://opentopography.org/.

# How to use the code to generate a 3D map (with terrain)

python3 osm_pbf_DEM_precise_soil_fill.py ../Maps/chongqing-260703.osm.pbf output_soil_fill.glb --bbox 29.5410 106.5238 29.5865 106.5948 --material-mode infer --download-dem --dem-type COP30 --terrain-grid 200 --opentopo-key xxxxx

(The output files are: "output_soil_fill.glb", the corresponding JSON file, as well as the downloaded .tif file that matches the latitude and longitude.)

1. Map selection:

--bbox 32.0000 118.7000 32.0900 118.8600, which means minimum latitude, minimum longitude, maximum latitude, and maximum longitude.

FYI, for convenience (the more buildings in the map, the longer the running time):

    "New York Manhattan": (40.70 -74.02 40.72 -73.99),
    "London Camden": (51.52 -0.19 51.56 -0.11),
    "London Canary Wharf": (51.49 -0.02 51.51 0.02),
    "Edinburgh": (55.9429 -3.1752 55.9609 -3.2074),
    "Shanghai Pudong": (31.20 121.45 31.27 121.55),
    "Nanjing Jiangning": (31.63 118.42 32.10 119.05),
    "Nanjing Xinjiekou": (32.0000 118.7000 32.0900 118.8600),
    "Nanjing Baijiahu": (31.9100 118.7750 31.9700 118.8500),
    "Nanjing Zijinshan": (118.825 32.032 118.8655 32.0605),
    "Chongqing": (29.5410 106.5238 29.5865 106.5948)

2. Parameters:

--format glb, or --format gltf

--download-dem: according to the latitude and longitude specified by "--bbox", download the corresponding DEM GeoTIFF from OpenTopography.

--dem path.tif: use the downloaded DEM on your local computer.

--terrain-grid 160: split the "--bbox" map into a 160 × 160 grid. Therefore,

--terrain-grid 60:    for fast testing
--terrain-grid 160:   good detail but slower
--terrain-grid 200+:  "you know it already"

--opentopo-key xxxxx: you need to apply for a free API key and put it here.

3. Materials for the buildings and other objects

--material-mode infer:

If OSM does not contain the materials, it will be marked as unknown in the JSON, or a material will be inferred based on the building information, such as the building name.

Therefore:

--material-mode actual: only use the real material in OSM; if no such material exists, it will be marked as unknown.
--material-mode infer: if there is no material, infer it based on the building name (building=*), e.g., residential -> concrete.

(If you generate .gltf files, you can find an "extras" section in the GLTF file and also in the JSON file.

        "inferred_material": "brick",
        "material_inference_source": "default_brick",
        "dielectric_constant": 4.44,
        "conductivity_s_per_m": 0.018...)

4. Black background color: there are no geometry fillings, or the area is filled with soil directly. This is recorded in _extras.json, e.g.,

"remaining_ground_generated": false,
"unmodeled_background_material": "soil",
"unmodeled_background_note": "No ground mesh is generated in DEM mode unless ..."

5. You can check more details at in the code itself.


# How to convert .glb to .xml + .ply

1. Run the Jupyter Notebook server to support the rest of the workflow.
2. Run "load_glb_to_RT.ipynb". It will generate a folder named "generated_model" containing the .xml and .ply files for Sionna RT to load the model.
   (You may need to open the notebook and modify the configuration, such as the input file path and filename.)
3. Run the example notebook "RT_on_real_map.ipynb".
   (Likewise, you may need to open the notebook and modify the configuration, such as the input file path and filename.)