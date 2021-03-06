
*******************************
openMVG_main_GlobalSfM
*******************************

[GlobalACSfM]_ is based on the paper "Global Fusion of Relative Motions for Robust, Accurate and Scalable Structure from Motion."  published at ICCV 2013.

Multi-view structure from motion (SfM) estimates the position and orientation of pictures in a common 3D coordinate frame. When views are treated incrementally, this external calibration can be subject to drift, contrary to global methods that distribute residual errors evenly. Here the method propose a new global calibration approach based on the fusion of relative motions between image pairs. 

Algorithm of the Global Structure from Motion

.. code-block:: c++

  Require: internal camera calibration (possibly from EXIF data)
  Require: pairwise geometry consistent point correspondences
  Ensure: 3D point cloud
  Ensure: camera poses

  compute relative pairwise rotations
  detect and remove false relative pairwise rotations
    - using composition error of triplet of relative rotations
  compute the global rotation
    - using a dense least square and approximated rotations
  compute relative translations
    - using triplet of views for stability and colinear motion support
  compute the global translation
    - integration of the relative translation directions using a l-∞ method. 
  final structure and motion
    - link tracks validated per triplets and compute global structure by triangulation,
    - refine estimated parameter in a 2 step Bundle Adjustment
      - refine structure and translations
      - refine structure and camera parameters (rotations, translations).

Information and usage
========================

The chain is designed to run on a sfm_data.json file and some pre-computed matches.
The chain will only consider images with known approximate focal length. Image with invalid intrinsic id will be ignored.

  .. code-block:: c++
  
    $ openMVG_main_GlobalSfM -i Dataset/matches/sfm_data.json -m Dataset/matches/ -o Dataset/out_Global_Reconstruction/

Arguments description:

**Required parameters:**

  - **[-i|--input_file]**

    - a SfM_Data file

  - **[-m|--matchdir]**

    - path were geometric matches were stored
  
  - **[-o|--outdir]**

    - path where the output data will be stored

**Optional parameters:**

  - **[-r|--rotationAveraging]**

    - 2: (default) L2 rotation averaging _[Martinec]
    - 1: L1 rotation averaging _[Chatterjee]

  - **[-t|--translationAveraging]**

    - 1: (default) L1 translation averaging _[GlobalACSfM]
    - 2: L2 translation averaging _[Kyle2014]

  - **[-c|--camera_model]**

    - The camera model type that will be used for views with unknown intrinsic:

      - 1: Pinhole
      - 2: Pinhole radial 1
      - 3: Pinhole radial 3 (default)

  - **[-f|--refineIntrinsics]**

    - 0: intrinsic parameters are kept as constant
    - 1: refine intrinsic parameters (default)

*_[GlobalACSfM]* default settings are "-r 2 -t 1".


**Tips**

  As a dense image network is required to perform global SfM it is required to detect more **Regions points** per image to ensure a high probability of matching.

Please use:
 - "-p HIGH" option on openMVG_main_ComputeFeatures
 - "-r .8" on openMVG_main_ComputeMatches.






