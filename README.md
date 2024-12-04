# Colmap Mejorado con Cambios del Paper Kataria

Esta es una versión mejorada de **Colmap** basada en los cambios descritos en el paper *"Improving Structure from Motion with Reliable Resectioning"* de Kataria et al. La versión base de Colmap utilizada es la **v11** que al momento de realizar este trabajo y de subir el codigo es la mas actual, con mejoras que incluyen la optimización de la reconstrucción mediante reducción de puntos redundantes y la eliminación de estructuras ambiguas.

A continuación, se describen los pasos para instalar y ejecutar esta versión mejorada en **Google Colab**.

---

## Requisitos
Antes de comenzar, asegúrate de contar con:
- Una cuenta de **Google** para acceder a **Colab**.
- Acceso a Google Colab (puedes usar la versión gratuita o Pro para obtener más recursos).
- Archivos de imágenes para la reconstrucción 3D.

---

## Pasos de Instalación en Google Colab

### 1. Clonar el Repositorio
Ejecuta el siguiente comando para clonar el repositorio con la versión modificada de Colmap:
```bash
!git https://github.com/anibalvale666/colmap_v311_kataria_improve.git



# Instalacion de dependencias
!sudo apt-get install \
    git \
    cmake \
    ninja-build \
    build-essential \
    libboost-program-options-dev \
    libboost-filesystem-dev \
    libboost-graph-dev \
    libboost-system-dev \
    libboost-test-dev \
    libeigen3-dev \
    libflann-dev \
    libfreeimage-dev \
    libmetis-dev \
    libgoogle-glog-dev \
    libgflags-dev \
    libgtest-dev \
    libgmock-dev \
    libsqlite3-dev \
    libglew-dev \
    qtbase5-dev \
    libqt5opengl5-dev \
    libcgal-dev \
    libceres-dev \
    libjsoncpp-dev -y

# Compilar colmap
%cd /content/colmap
!mkdir build
%cd build
!cmake .. -GNinja
!ninja
!sudo ninja install


# Paso 1: Extracción de características
!colmap feature_extractor --database_path {database_path} --image_path {input_path} --output_path {output_path}

# Paso 2: Matching de características
!colmap exhaustive_matcher --database_path {database_path}

# Paso 3: Reconstrucción (integrando cambios del paper)
!colmap mapper --database_path {database_path} --image_path {input_path} --output_path {output_path}/sparse



# Paso 4 OPCIONAL: Reconstruccion de Mallas
!colmap image_undistorter \
    --image_path {input_path} \
    --input_path {output_path}/sparse \
    --output_path {output_path}/dense \
    --output_type COLMAP

!colmap patch_match_stereo \
    --workspace_path {output_path}/dense \
    --workspace_format COLMAP \
    --PatchMatchStereo.geom_consistency true

!colmap stereo_fusion \
    --workspace_path {output_path}/dense \
    --workspace_format COLMAP \
    --input_type geometric \
    --output_path {output_path}/dense/fused.ply

```

ACERCA DE COLMAP

COLMAP is a general-purpose Structure-from-Motion (SfM) and Multi-View Stereo
(MVS) pipeline with a graphical and command-line interface. It offers a wide
range of features for reconstruction of ordered and unordered image collections.
The software is licensed under the new BSD license. If you use this project for
your research, please cite:

    @inproceedings{schoenberger2016sfm,
        author={Sch\"{o}nberger, Johannes Lutz and Frahm, Jan-Michael},
        title={Structure-from-Motion Revisited},
        booktitle={Conference on Computer Vision and Pattern Recognition (CVPR)},
        year={2016},
    }

    @inproceedings{schoenberger2016mvs,
        author={Sch\"{o}nberger, Johannes Lutz and Zheng, Enliang and Pollefeys, Marc and Frahm, Jan-Michael},
        title={Pixelwise View Selection for Unstructured Multi-View Stereo},
        booktitle={European Conference on Computer Vision (ECCV)},
        year={2016},
    }

    @INPROCEEDINGS{9320437,
      author={Kataria, Rajbir and DeGol, Joseph and Hoiem, Derek},
      booktitle={2020 International Conference on 3D Vision (3DV)}, 
      title={Improving Structure from Motion with Reliable Resectioning}, 
      year={2020},
      volume={},
      number={},
      pages={41-50},
      keywords={Image reconstruction;Feature extraction;Tracking;Structure from motion;Pose estimation;Visualization;Robustness;Structure from motion;SfM;Geometry},
      doi={10.1109/3DV50981.2020.00014}
    }

License
-------

The COLMAP library is licensed under the new BSD license. Note that this text
refers only to the license for COLMAP itself, independent of its thirdparty
dependencies, which are separately licensed. Building COLMAP with these
dependencies may affect the resulting COLMAP license.

    Copyright (c) 2023, ETH Zurich and UNC Chapel Hill.
    All rights reserved.

    Redistribution and use in source and binary forms, with or without
    modification, are permitted provided that the following conditions are met:

        * Redistributions of source code must retain the above copyright
          notice, this list of conditions and the following disclaimer.

        * Redistributions in binary form must reproduce the above copyright
          notice, this list of conditions and the following disclaimer in the
          documentation and/or other materials provided with the distribution.

        * Neither the name of ETH Zurich and UNC Chapel Hill nor the names of
          its contributors may be used to endorse or promote products derived
          from this software without specific prior written permission.

    THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS "AS IS"
    AND ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE
    IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE
    ARE DISCLAIMED. IN NO EVENT SHALL THE COPYRIGHT HOLDERS OR CONTRIBUTORS BE
    LIABLE FOR ANY DIRECT, INDIRECT, INCIDENTAL, SPECIAL, EXEMPLARY, OR
    CONSEQUENTIAL DAMAGES (INCLUDING, BUT NOT LIMITED TO, PROCUREMENT OF
    SUBSTITUTE GOODS OR SERVICES; LOSS OF USE, DATA, OR PROFITS; OR BUSINESS
    INTERRUPTION) HOWEVER CAUSED AND ON ANY THEORY OF LIABILITY, WHETHER IN
    CONTRACT, STRICT LIABILITY, OR TORT (INCLUDING NEGLIGENCE OR OTHERWISE)
    ARISING IN ANY WAY OUT OF THE USE OF THIS SOFTWARE, EVEN IF ADVISED OF THE
    POSSIBILITY OF SUCH DAMAGE.
