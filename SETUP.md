# Environment Setup
## Environment Setup — Riverside County Economic Development & Commercial Opportunity Zone Analysis

This notebook requires a dedicated conda environment to avoid dependency conflicts,
particularly between `libpysal`, `esda`, `mgwr`, `geopandas`, and `osmnx`.
All geospatial C-library packages (`rasterio`, `geopandas`, `pyproj`, `fiona`) must be
installed via `conda-forge` — pip installs of these packages frequently fail due to
missing GDAL/GEOS/PROJ binaries.

> **Note:** `mgwr` (Geographically Weighted Regression) has no conda package and must
> be installed via pip. The `environment.yml` in Option B handles this automatically.

---

## Option A — Create Environment Manually

**Step 1: Create and activate the environment**
```bash
conda create -n riverside_econ python=3.11 -y
conda activate riverside_econ
```

**Step 2: Install packages in order**
```bash
# Core geospatial stack — must come from conda-forge for C-library bindings
conda install -c conda-forge geopandas pandas numpy matplotlib seaborn -y

# Geospatial utilities
conda install -c conda-forge pyproj shapely fiona rtree contextily -y

# OpenStreetMap POI extraction
conda install -c conda-forge osmnx -y

# Spatial statistics (Moran's I, LISA, weights matrix)
conda install -c conda-forge libpysal esda splot -y

# Machine learning (K-Means, DBSCAN)
conda install -c conda-forge scikit-learn -y

# Interactive mapping
conda install -c conda-forge folium branca -y

# Utilities
conda install -c conda-forge requests tqdm python-dotenv -y

# Jupyter
conda install -c conda-forge jupyterlab ipykernel ipywidgets -y

# GWR — pip only, no conda package available
pip install mgwr
```

**Step 3: Register the kernel**
```bash
python -m ipykernel install --user --name riverside_econ --display-name "Riverside Econ P10 (Python 3.11)"
```

---

## Option B — Create from environment.yml (Recommended)

Save the block below as `environment.yml` in your project folder, then run:
```bash
conda env create -f environment.yml
conda activate riverside_econ
python -m ipykernel install --user --name riverside_econ --display-name "Riverside Econ P10 (Python 3.11)"
```

**environment.yml**
```yaml
name: riverside_econ

channels:
  - conda-forge
  - defaults

dependencies:
  # Python
  - python=3.11

  # Core scientific stack
  - numpy
  - pandas
  - scipy
  - matplotlib
  - seaborn

  # Geospatial core
  - geopandas
  - pyproj
  - shapely
  - fiona
  - rtree
  - contextily

  # OpenStreetMap / POI extraction
  - osmnx

  # Spatial statistics (Moran's I, LISA, weights matrix)
  - libpysal
  - esda
  - splot

  # Machine learning (K-Means, DBSCAN)
  - scikit-learn

  # Interactive mapping
  - folium
  - branca

  # GWR — pip only, no conda package available
  - pip
  - pip:
      - mgwr

  # Notebook / IDE
  - jupyterlab
  - ipykernel
  - ipywidgets

  # Utilities
  - requests
  - tqdm
  - python-dotenv
```

---

## IDE Configuration

| IDE | Steps |
|-----|-------|
| **VS Code** | `Ctrl+Shift+P` → *Python: Select Interpreter* → select `riverside_econ` |
| **JupyterLab** | Run `jupyter lab` with env active → select kernel from top-right dropdown |
| **PyCharm** | *Settings → Project → Python Interpreter → Add → Conda Environment → Existing → `riverside_econ`* |

---

## Verify Installation

Run the following cell in your notebook to confirm all packages loaded correctly before proceeding:

```python
import geopandas as gpd
import osmnx as ox
import folium
import esda
import libpysal
from mgwr.gwr import GWR
from mgwr.sel_bw import Sel_BW
from sklearn.cluster import KMeans, DBSCAN
import pandas as pd
import numpy as np

print("All libraries imported successfully!")
print(f"  GeoPandas : {gpd.__version__}")
print(f"  OSMnx     : {ox.__version__}")
print(f"  Folium    : {folium.__version__}")
print(f"  esda      : {esda.__version__}")
print(f"  libpysal  : {libpysal.__version__}")
print(f"  pandas    : {pd.__version__}")
print(f"  numpy     : {np.__version__}")
```

**Expected output:**
```
All libraries imported successfully!
  GeoPandas : 0.14.x
  OSMnx     : 1.9.x
  Folium    : 0.15.x
  esda      : 2.5.x
  libpysal  : 4.9.x
  pandas    : 2.x.x
  numpy     : 1.x.x
```

---

## Troubleshooting

**`mgwr` fails to install**  
Ensure `numpy` and `scipy` are installed first, then retry:
```bash
pip install numpy scipy
pip install mgwr
```
If it still fails, restart the terminal and re-activate the environment before retrying.

**`ImportError` for `geopandas` or `fiona`**  
These packages have C-library dependencies that pip cannot resolve reliably.
Reinstall from conda-forge:
```bash
conda install -c conda-forge geopandas fiona -y
```

**`osmnx` raises `InsufficientResponseError` during POI extraction**  
The Overpass API (which OSMnx queries) has rate limits. Wait 60 seconds and retry.
For large city queries, add `ox.settings.timeout = 180` before your extraction loop.

**Kernel not appearing in JupyterLab**  
Re-run the ipykernel registration command with the environment active:
```bash
conda activate riverside_econ
python -m ipykernel install --user --name riverside_econ --display-name "Riverside Econ P10 (Python 3.11)"
```
Then restart JupyterLab.