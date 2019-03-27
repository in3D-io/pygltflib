# pygltflib

Python library for reading, writing and handling GLTF files. Python3.6+

## Requirements
* Python 3.6+
* dataclasses
* dataclasses-json
* pytest (optional)

It requires python 3.6 and above because it uses dataclasses and all attributes are type hinted. And f-strings, plenty of f-strings.

## About
This is an unofficial library that tracks the [official file format](https://github.com/KhronosGroup/glTF/blob/master/specification/2.0/README.md) for GLTF2. 

The library was initially built to load and save simple meshes but support for the entire spec, including materials 
and animations is pretty good. Supports both json (.gltf) and binary (.glb) file formats, although .glb support 
is missing some features at the moment. 

Check the table below for an idea of which sample models validate.

Questions? Contributions? Bug reports? Open an issue on the [gitlab page for the project](https://gitlab.com/dodgyville/pygltflib).
We are very interested in hearing your use cases for `pygltflib` to help drive the roadmap.

###### Roadmap:
* Add helper functions for creating meshes
* Full support for binary GLTF (.glb) files
* Reject file overwrites unless overwrite flag set
* Give options on storing binary buffers (embedded vs external)


###### Contributors:
* Luke Miller
* Sebastian Höffner
* Arthur van Hoff


###### Changelog:
* 1.6
    * provide better support for binary (.glb) files (bug fixes)
    * promote `load_json`, `load_binary`, `save_json` and `save_binary` from internal methods 
* 1.5
    * align embedded data correctly
    * add `def glb2gltf` and `def gltf2glb` util functions to `pygltflib.utils` for easy file conversion
* 1.4 
    * add basic support for saving to binary GLTF (.glb) files
    * move undocumented non-core methods to `pygltfib.utils`
* 1.3 
    * add basic support for reading binary GLTF (.glb) files
* 1.2 
    * provide better json support
    * remove numpy requirement
    * suppress infer warning
    * add basic default methods
* 1.0 
    * initial release


## Install

### PIP

###### Method 1
`pip install pygltflib` 

###### Method 2
`python -m pip install pygltflib`

###### Method 3
`py -3 -m pip install pygltflib`


### Source

`git clone https://gitlab.com/dodgyville/pygltflib`

## Usage
Note: These examples use the official [sample models](https://github.com/KhronosGroup/glTF-Sample-Models) provided by Khronos at:

https://github.com/KhronosGroup/glTF-Sample-Models

### Creating
```python3
>>> from pygltflib import GLTF2, Scene
>>> gltf = GLTF2()
>>> gltf.scene # no scene set by default
>>> len(gltf.scenes)
0

>>> scene = Scene()
>>> gltf.scenes.append(scene)
>>> len(gltf.scenes)
1

```

### Loading and saving

`pygltflib` can load json-based .GLTF files and binary .GLB files, based on the file extension. 

#### GLTF files

```python3
>>> from pygltflib import GLTF2
>>> filename = "glTF-Sample-Models/2.0/AnimatedCube/glTF/AnimatedCube.gltf"
>>> gltf = GLTF2().load(filename)
>>> gltf.scene
0

>>> gltf.scenes
[Scene(name='', nodes=[0])]

>>> gltf.nodes[0]
Node(mesh=0, skin=None, rotation=[0.0, -1.0, 0.0, 0.0], translation=[], scale=[], children=[], matrix=[], camera=None, name='AnimatedCube')
>>> gltf.nodes[0].name
'AnimatedCube'

>>> gltf.meshes[0].primitives[0].attributes
Attributes(NORMAL=4, POSITION=None, TANGENT=5, TEXCOORD_0=6)

>>> filename2 = "test.gltf"
>>> gltf.save(filename2)

```

#### GLB files 

```python3
>>> from pygltflib import GLTF2
>>> glb_filename = "glTF-Sample-Models/2.0/Box/glTF-Binary/Box.glb"
>>> glb = GLTF2().load(glb_filename)
>>> glb.scene
0

>>> glb.scenes
[Scene(name='', nodes=[0])]

>>> glb.nodes[0]
Node(mesh=None, skin=None, rotation=[], translation=[], scale=[], children=[1], matrix=[1.0, 0.0, 0.0, 0.0, 0.0, 0.0, -1.0, 0.0, 0.0, 1.0, 0.0, 0.0, 0.0, 0.0, 0.0, 1.0], camera=None, name=None)

>>> glb.meshes[0].primitives[0].attributes
Attributes(POSITION=2, NORMAL=1, TANGENT=None, TEXCOORD_0=None, TEXCOORD_1=None, COLOR_0=None, JOINTS_0=None, WEIGHTS_0=None)

>>> glb.save("test.glb")


```

#### Converting files

##### First method
```python3
>>> from pygltflib import GLTF2

>>> # convert glb to gltf
>>> glb = GLTF2().load("glTF-Sample-Models/2.0/Box/glTF-Binary/Box.glb")
>>> glb.save("test.gltf")

>>> # convert gltf to glb
>>> gltf = GLTF2().load("glTF-Sample-Models/2.0/Box/glTF/Box.gltf")
>>> gltf.save("test.glb")

```

##### Second method using utils
```python3
>>> from pygltflib.utils import glb2gltf, gltf2glb
>>> # convert glb to gltf
>>> glb2gltf("glTF-Sample-Models/2.0/Box/glTF-Binary/Box.glb")

>>> # convert gltf to glb
>>> gltf2glb("glTF-Sample-Models/2.0/Box/glTF/Box.gltf", "test.glb", override=True)


```

## Running the tests

### Status of gltf-validator
Using sample models loaded and then saved using this library, here are validator reports (blank is untested). 


#### Validator Status
| Model | gltf to gltf | gltf to glb | 
| ------| ------- | ------- |
| 2CylinderEngine | passes | fail |
| AlphaBlendModeTest | passes | fail | 
| AnimatedCube | passes | passes |
| AnimatedMorphCube | passes |  passes |
| AnimatedMorphSphere | passes |  passes |
| AnimatedTriangle | passes |  fail |
| Avocado | passes |  passes 
| BarramundiFish | passes |
| BoomBox | passes |
| BoomBoxWithAxes | passes |
| Box | passes | fail
| BoxAnimated | passes |
| BoxInterleaved | passes |
| BoxTextured | passes |
| BoxTexturedNonPowerOfTwo | passes |
| BoxVertexColors | passes |
| BrainStem | passes |
| Buggy | passes |
| Cameras | passes | passes
| CesiumMan | passes |
| CesiumMilkTruck | passes |
| Corset | passes |
| Cube | passes | passes 
| DamagedHelmet | passes | passes
| Duck | passes | fail | 
| FlightHelmet | passes |
| GearboxAssy | passes |
| Lantern | passes |
| MetalRoughSpheres | passes |
| Monster | passes |
| MultiUVTest | passes |
| NormalTangentMirrorTest | passes |
| NormalTangentTest | passes |
| OrientationTest | passes |
| ReciprocatingSaw | passes |
| RiggedFigure | passes |  fail |
| RiggedSimple | passes |  fail |
| SciFiHelmet | passes |  passes |
| SimpleMeshes | passes |
| SimpleMorph | passes |
| SimpleSparseAccessor | passes |
| SpecGlossVsMetalRough | passes |
| Sponza | passes |
| Suzanne | passes |
| TextureCoordinateTest | passes |
| TextureSettingsTest | passes | fail |
| TextureTransformTest | passes |
| Triangle | passes |
| TriangleWithoutIndices | passes |
| TwoSidedPlane | passes |
| VC | passes |
| VertexColorTest | passes |
| WaterBottle | passes | passes | 


### unittests
`pytest tests.py`
    
