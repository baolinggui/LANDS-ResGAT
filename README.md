# LANDS-ResGAT

**LANDS-ResGAT** is an open-source object-based landslide mapping framework designed for high-resolution remote sensing imagery in complex mountain terrain. The framework combines **superpixel segmentation**, **multi-source object feature extraction**, **terrain-aware graph construction**, and a **hybrid residual graph neural network** integrating **GraphSAGE** and **GATv2Conv** for landslide detection.

This repository provides code for preprocessing, graph generation, model training, inference, and visualization.

## Why LANDS-ResGAT?

Compared with conventional pixel-based landslide mapping approaches, LANDS-ResGAT is designed to better preserve the **shape, boundary coherence, and spatial context** of landslide objects in rugged terrain.

### Main advantages
- **Object-based instead of pixel-only prediction**  
  The workflow first segments imagery into meaningful objects and then performs graph-based learning, reducing salt-and-pepper noise and fragmented outputs.

- **Terrain-aware graph representation**  
  Spatial relationships between neighbouring objects are explicitly modelled using centroid distance and edge attributes such as aspect difference and spectral contrast.

- **Hybrid residual GNN architecture**  
  The model combines **GraphSAGE** for neighbourhood aggregation and **GATv2Conv** for adaptive attention-based message passing, with residual connections for stable deep graph learning.

- **Practical efficiency**  
  The framework is designed to achieve strong mapping accuracy while keeping runtime and GPU memory demand lower than dense pixel-based methods.

## Repository structure
## Recommended citation format
## Gui, B., Bhardwaj, A., Sam, L., et al. Year. Scalable landslide detection in complex Himalayan topography via a novel object-based residual graph attention network. Journal Name, Volume(Issue), Pages. DOI

```text
LANDS-ResGAT/
│
├── README.md                # Project description and usage guide
├── code/                    # Main scripts / notebooks
├── code.txt                 # Example implementation / workflow
└── data/                    # (user-prepared) input imagery, DEM, labels

Note: the exact folder structure may be updated after code cleanup and full public release.

Requirements

Recommended environment:

Python 3.10+
PyTorch
PyTorch Geometric
NumPy
SciPy
scikit-image
scikit-learn
rasterio
matplotlib
tqdm

Install core dependencies:

pip install numpy scipy matplotlib scikit-image scikit-learn rasterio tqdm
pip install torch
pip install torch_geometric
Input data

The framework currently expects three core inputs:

Multispectral remote sensing imagery
Example: PlanetScope 4-band imagery
Format: GeoTIFF
Spatial resolution should be consistent across bands
DEM
Example: ALOS PALSAR DEM
Format: GeoTIFF
Landslide label mask
Rasterized binary mask for training / validation
Format: GeoTIFF
Example file naming
Raw2.tif      # multispectral image
dem2.tif      # DEM
shape2.tif    # landslide label mask
Data preparation workflow

The preprocessing pipeline includes:

Read the multispectral image as the spatial reference
Reproject DEM and label mask to match the image grid
Normalize DEM values
Visualize RGB image, DEM, and label mask
Perform object segmentation using Felzenszwalb
Extract object-level spectral, texture, geometric, and DEM-derived features
Select informative features using variance filtering + random forest importance
Build an object graph with radius-based neighbour connectivity
Generate node labels from the raster landslide mask
Convert the graph into a PyTorch Geometric Data object
Segmentation settings

Default object segmentation is based on:

scale = 25
sigma = 0.5
min_size = 10

These values can be modified depending on image texture, object size, and terrain complexity.

Graph construction

Objects are connected using centroid-based radius neighbours.

Typical settings:

radius = 30

Edge attributes currently include:

normalized centroid distance
slope aspect difference
red-band spectral contrast
Model architecture

LANDS-ResGAT uses:

an initial GraphSAGE projection layer
stacked HybridResNet blocks
each block includes:
one SAGEConv
one GATv2Conv
residual connection
batch normalization
ReLU activation

The framework is designed for binary classification:

landslide
non-landslide
Quick start
1. Prepare your data

Place your imagery, DEM, and label mask in a working folder.

2. Update file paths

Edit the input paths in the script:

base = "/path/to/your/project"
raw_path = os.path.join(base, "Raw2.tif")
dem_path = os.path.join(base, "dem2.tif")
label_path = os.path.join(base, "shape2.tif")
3. Run preprocessing

The script will:

align all rasters
normalize DEM
generate superpixels
extract object features
build the object graph
4. Train the model

Training uses PyTorch Geometric with:

Adam optimizer
weighted loss for class imbalance
train / validation split

Example:

model = HybridResNet18GNN(
    in_channels=graph_data.x.shape[1],
    num_classes=2,
    hidden_channels=32
)

optimizer = torch.optim.Adam(model.parameters(), lr=0.001, weight_decay=1e-4)
model = train(model, graph_data, optimizer, criterion, epochs=200)
5. Run inference

After training, the script predicts object labels and maps them back to the image space.

6. Visualize outputs

The framework provides visualization for:

ground-truth landslide mask
predicted landslide map
segmented boundaries
feature maps
Outputs

Typical outputs include:

segmented object map
selected object features
graph data structure
trained model
predicted landslide label map
visualization figures
How to adapt the tool to your own study area

To use LANDS-ResGAT in another region:

Prepare co-registered imagery, DEM, and labels
Adjust segmentation parameters if object size differs
Recompute object features and graph edges
Retrain the model on your own data
Evaluate using F1-score, IoU, and visual inspection
Notes and current limitations
The current implementation is designed mainly for single-scene / single-time landslide mapping
Labels should be carefully prepared and quality-checked
Segmentation parameters may need adjustment for different sensors or land-cover conditions
Large-area deployment may require graph partitioning or memory optimization
Citation

If you use this code in your research, please cite the associated paper once published.



