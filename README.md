# Space Exploration

This repository is a collection of mini-projects and tutorials related to space images and geo-spatial data. It is a sandbox where I'll try tools and techniques; as such, it will grow organically.

Each mini-project has a dedicated folder with a `README.md`. Datasets should be contained in `data`, however, image data is usually not commited; instead, I provide the necessary download links.

## Contents

- [`space_image_classification`](./space_image_classification/): CNN which classifies space images from  [Satellite Image Classification](https://www.kaggle.com/datasets/mahmoudreda55/satellite-image-classification); RBG images are used and the classification is per image.
- [`geospatial_data_guide`](./geospatial_data_guide/): a guide on how to visualize and work with geospatial data, based on two courses from Datacamp:
  - [Visualizing Geospatial Data in Python](https://app.datacamp.com/learn/courses/visualizing-geospatial-data-in-python)
  - [Working with Geospatial Data in Python](https://app.datacamp.com/learn/courses/working-with-geospatial-data-in-python)
- [`satellite_image_analysis`](./satellite_image_analysis): classification, clustering and dimensionality reduction examples with satellite images; images with several bands (channels) are processed and the ML techniques are applied pixel-wise. The examples come originally from [syamkakarla98/Satellite_Imagery_Analysis](https://github.com/syamkakarla98/Satellite_Imagery_Analysis).

## Requirements

The notebooks can be opened with Google Colab (if link provided in each notebook) or in a dedicated environment. A brief recipe to set one up using [conda](https://docs.conda.io/en/latest/) is the following:

```bash
# Create and activate e
conda create --name ds pip python=3.7
conda activate ds

# Install pip dependencies
pip install -r requirements.txt
```

## Authorship

Mikel Sagardia, 2023.  
No guarantees.

If you find this repository useful and use it, please, cite the original source. This work is protected by the  GPL-3.0 license; see [`LICENSE.md`](LICENSE.md) for more information.
