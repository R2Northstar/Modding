MDL Modding
===========

MDL Overview
------------

Model (MDL) is the extension for Source's proprietary model format. It defines the structure of the model along with animation, bounding box, hit box, materials, mesh and LOD information. Unlike other Source games in Titanfall 2 files like the .phy, .vtx, .vvd etc. are not separate files and are instead included in the .mdl file.

`VALVe developer docs "Model" <https://developer.valvesoftware.com/wiki/.mdl>`__


Editing an existing Model
-------------------------

While creating an entire Model from scratch is possible it would be an extremely long and tedious task. Instead its recommended to use an existing Model as your base.


Prequisites
-----------
•	`Blender <https://www.blender.org/download/>`__ (min. 2.92)
•	`Blender Source Tools <https://developer.valvesoftware.com/wiki/Blender_Source_Tools>`__  (refer to Download page for install instructions)
•	`Crowbar <https://steamcommunity.com/groups/CrowbarTool>`__

        - One of the following two games on Steam:

        - `Portal 2 <https://store.steampowered.com/app/620/Portal_2/>`__ (and its Authoring Tools `Portal 2 SDK <https://developer.valvesoftware.com/wiki/Authoring_Tools/SDK_(Portal_2)>`__)  

        - `SourceFilmMaker <https://store.steampowered.com/app/1840/Source_Filmmaker/>`__ (FREE)
              
•	`MDLSHIT <https://github.com/headassbtw/mdlshit>`__ 
•	`HARMONY VPK <https://github.com/harmonytf/HarmonyVPKTool>`__

.. note::
    This Guide is tailored towards users with atleast some experience with Blender. If you are new to Blender I recommend you to first familiarize yourself with the basics of Blender. There are many tutorials on the Internet that can help you with that.
    Especially the following topics are important for modifying or creating game assets:

    •	Modeling
    •	UV Mapping 
    •	Texturing 
    •	Rigging 

    These things work in conjunction with each other and are all important for creating a game ready model. An understanding of these topics is very helpful for proper usage of Blender.


Workflow
--------
The workflow for editing a model is as follows:

•	Extract the model using `HARMONY VPK <#harmony-vpk>`__
•	Decompile the model using `CROWBAR <#crowbar>`__
•	Importing the model in `BLENDER <#blender>`__
•	Editing the model to our liking
•	Assigning a material to parts of our model
•	Exporting the model from `BLENDER <#blender>`__
•	Compiling using `CROWBAR <#crowbar>`__
•	Combing the MDL file with other output files using `MDLSHIT <#mdlshit>`__


Blender
-------

Blender will be used to edit the model. It is a free and open-source 3D computer graphics software toolset with many features. It is widely used for animation, modeling, scene creation, and game development.

The Source Tools add-on for Blender allows us to import and export Source Engine assets. It is a community-driven project that aims to provide users with a convenient way to import and export Source Engine assets from and to Blender, for a wide range of applications, including animation, modeling, scene creation, and game development.

We will use both in conjunction to edit the model.


Crowbar
-------

Crowbar is a tool written by ZeqMacaw. It is used to decompile and compile Source Engine assets, such as models, textures, and sounds.

We will use it to decompile and compile the model.


MDLShit
-------

MDLShit is a tool written by headassbtw. It is used to combine the MDL file with the other output files from Crowbar.

We will use it to combine the MDL file with the other output files from Crowbar.


Harmony VPK
-----------

Harmony VPK is a tool written by headassbtw. It is used to extract the model from the game files.

We will use it to extract the model from the game files.


Workflow in Detail
------------------

In this section we will go through the workflow in detail by using the Flatline as an example.
We will add a cube to the side of the Flatline and assign a custom material to it.


Step 1: Extracting
------------------

- Open HARMONY VPK.
- On the top right click on the ``Open VPK`` button.
- Navigate to the location of your Titanfall 2 installions ``/vpk/`` folder.
- Open the ``.vpk`` file you want to extract (most multiplayer weapons are in ``client_mp_common.bsp.pak000_000.vpk`` since you most likely want one of those).
- You will now see a list of all files in the ``.vpk`` file on the left side looking something like this:
 
.. code-block:: text

    RootDir
    ├── materials
    ├── resource
    ├── cfg
    ├── scripts
    ├── models
    ├── maps
    └── depot

- Navigate to the ``models`` folder (this is where all models in this file are located).
- Navigate to the folder of the model you want to extract (for example ``weapons/vinson`` which is the internal name for the Flatline).
- Select the ``.mdl`` file you want to extract (for example ``ptpov_vinson.mdl``).
 
  .. note::

    Weapons are mostly split into two models, one for the first person view(``ptpov_`` and the world model ``w_``.

    ``ptpov`` is used when you are in first person.
    
    ``w_`` is used when the weapon is viewed in the world (for example when its dropped on the ground).
    
- Click on the ``Unpack`` button on the top right.
- You are now prompted to select a folder to extract the model to, select a folder of your choice and click on ``Select Folder``.
- Your model is now extracted and you can close Harmony VPK.


Step 2: Decompiling the model
-----------------------------

.. note::
    In order to use Crowbar the way we will we need to setup a few things first.
    
    This step only needs to be done once


Setup Crowbar (one time only)
~~~~~~~~~~~~~

- Uppon first launch select the ``Set Up Games`` tab on the top left.
- Select either ``Portal 2`` or ``Source Filmmaker`` in the dropdown menu on the top.
- Make sure your Lirary Path is set to the location of the Steam library you have the game installed in on the bottom.
- The ``Game Setup`` section should now be filled with the correct paths.

- Select the ``Decompile`` tab on the top.
- In this tab make sure the following settings are set:
 
  - ``MDL input``: ``File``
  - ``Output to``: ``Subfolder (of MDL input)`` (change the text in the box to the right of that to ``decompiled``)
  Check the following boxes:

  - ``QC file``
  - ``Each $texturegroup skin-familiy on single line``
  - ``Include $definebones lines (typical for view models)``
  - ``Use MixedCase for keywords``
  - ``Reference mesh SMD file``
  - ``LOD mesh SMD files``
  - ``Physics mesh SMD file``
  - ``Vertex animation VTA file (flexes)``
  - ``Procedural bones VRD file``
  - ``Bone animation SMD files``
  - ``Place in "anims" subfolder``
  - ``Prefix mesh file names with model name``

- Select the ``Compile`` tab on the top.
- In this tab make sure the following settings are set:
  
  - ``QC input``: ``File`` 
  - ``Output to``: ``Subfolder (of QC input)`` (change the text in the box to the right of that to ``compiled``)
  Check the following boxes:

  - ``No P4``
  - ``Verbose``
- This concludes the setup for crowbar these settings will be saved and you will not need to do this again.
  

Decompiling the model
~~~~~~~~~~~~~~~~~~~~~

- Click on the ``Browse`` button on the top right.
- Navigate to the folder you extracted the model to in the previous step.
- Select the ``.mdl`` file you want to decompile (for example ``ptpov_vinson.mdl``).
- Press the ``Decompile`` button.
- Crowbar now decompiles the model and outputs the files to the ``decompiled`` folder in the same folder as the ``.mdl`` file.
  

Step 3: Importing to Blender
----------------------------

- Open Blender.
- In the top left corner select ``File`` -> ``Import`` -> ``Source Engine``.
- Navigate to the folder you extracted the model to in the previous step and select the ``.qc`` file (for example ``ptpov_vinson.qc``) and uncheck the ``Import Animations`` box and check the ``Create Collections`` box.


Step 4: Editing the model
-------------------------

.. note::
    This step is entirely up to you and depends on what you want to do with the model.
    In this example we will add a cube to the side of the Flatline and assign a custom material to it.

Before editing let me explain how the model is structured in Blender.
By selecting a qc file in the import menu we told Blender to import all SMD files referenced in that qc file.
This means that the model is split into multiple collections based on the SMD files referenced in the qc file.
For example the ``ptpov_vinson.qc`` file references the ``ptpov_vinson_v_vinson.smd`` file which contains the model for the Flatline.

- Select the ``ptpov_vinson_v_vinson.smd`` mesh in the outliner.
- Enter ``EDIT Mode``.
- In ``EDIT Mode`` add a cube to the side of the Flatline.
- Exit ``EDIT Mode``.
- This cube should now be part of the ``ptpov_vinson_v_vinson.smd`` mesh.
- Make sure that you now weight paint the cube to the correct bones.
- 
  .. note::
    On Weapons the most safe bone to weight paint to is ``def_c_base`` since it is the root bone of the weapon. This means that the cube will always move with the weapon.
    If you want the cube to move with a specific part of the weapon you can also weight paint it to the bone that moves that part of the weapon.
    Again, remember to somewhat learn how rigging works in Blender to properly understand this process.

- Now that we have our cube we want to assign a material to it.


Step 5: Assigning Materials
---------------------------

- Enter ``EDIT Mode``.
- Select the cube.
- In the ``Material Properties`` tab on the right click on the ``New`` button.
- Set the name of the material to its path in the game files, to learn more about materials and how to make them see `RPak Modding </guides/tools/rpakmodding.html>`__ or `VTF Modding </guides/tools/vtfmodding.html>`__. (for example ``models\weapons_r2\coolmaterial\cool_material``)
- Exit ``EDIT Mode``.
- Your cube should now have the material assigned to it ingame after compiling.

    .. note:: 
    To clearify: the material of a mesh or individual faces in the game will be associated using the name of the assigned material in Blender.


Step 6: Exporting from Blender
------------------------------

- In the ``Source Engine Export`` Menu in the ``Scene Properties`` select an ``Export Path`` usually the same folder as the original qc file.
- Set the ``Export Format`` to ``SMD``.
- Press the ``Export`` button and select ``Scene Export`` (this will export all meshes in the scene to SMD files, you can also individually export meshes by selecting them in the outliner and then pressing the ``Export`` button and selecting the mesh in the Export Menu).
- Your SMD files are now exported and you can close Blender.


Step 7: Compiling the model
-----------------------------------------------------

- Open Crowbar.
- Select the ``Compile`` tab on the top.
- Click on the ``Browse`` button on the top right.
- Select the ``.qc`` file you want to compile (for example ``ptpov_vinson.qc``).
- Press the ``Compile`` button.
- Crowbar now compiles the model and outputs the files to the ``compiled`` folder in the same folder as the ``.qc`` file, inside the ``compiled`` folder you will find the full folder path of the model (for example ``models\weapons\vinson\``).
  
    .. note::
        Usually the error is self explainatory and you can fix it by yourself. 
        By default Crowbar will not output a compiled file if any errors occur during the compilation process.

Step 8: Combining model files
-----------------------------

- Open MDLShit.
- In a file explorer navigate to the compiled folder of your model (for example ``compiled\models\weapons\vinson\``).
- In this folder you will find the ``.mdl`` file and multiple other files, in our case there will be 3 files ``.mdl``, ``.vvd`` and ``.vtx``) all with the same name.
- In MDLShit drag these into their respective boxes.
- Make sure they are checked and the boxes you dont have files for are unchecked.
- Press the ``Check`` button.
- Press the ``Convert`` button.
- MDLShit will now combine the files into a single ``_conv.mdl`` file, this is our final exported and working model you can now close MDLShit and use that model in a mod.