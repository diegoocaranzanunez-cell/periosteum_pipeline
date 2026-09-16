# Periosteal Thickness Quantification Pipeline

Code accompanying the manuscript "Semiautomated Image Processing Pipeline for Murine Periosteal Thickness Quantification" 

The pipeline takes a histological image of a murine bone section and separates the stain channels with color deconvolution. It then thresholds the hematoxylin channel, keeps the largest contour as the periosteum, skeletonizes that region and prunes the skeleton, and reads the distance transform along the skeleton to get thickness in microns.

## Running the pipeline

The code was written in Google Colab and the notebook is set up to run there.

1. Open `periosteum_pipeline.ipynb` in Colab. In Colab go to File > Open notebook, choose the GitHub tab, and paste the URL of this repository.
2. Put your images in a Google Drive folder called `Periosteum Quantification Images` directly inside My Drive. Name them with a shared prefix followed by a number starting at 1, for example `IM1.jpg`, `IM2.jpg`, `IM3.jpg`.
3. Run the cells in order. In the last cell, change `processBatch("IM", 3)` so the first argument is your prefix and the second is the number of images.

Each image prints its average width and shows a five-panel figure (original image, binary mask, largest contour mask, skeleton, distance transform) so you can check what was measured.

When the batch finishes you are asked for an image number to redo. Use this when the largest contour was something other than the periosteum. That image is run again keeping the largest outer contour that is smaller than the one picked the first time. Enter `No` when you are done.

## Outputs

`processBatch` returns the average width across all images in microns, weighted by the number of skeleton pixels measured in each image.

It also writes these files to the Colab working directory. Colab deletes them when the session ends, so download them from the Files panel on the left before closing.

- `skeleton_distances.csv`: one per image, with the distance transform value in pixels at every skeleton pixel that was kept. Each value is a half-width, so the pipeline doubles it before converting to microns.
- `results.csv`: every skeleton distance from every run in the batch pooled into one column.

## Parameters you may need to change

These values were set for our images and imaging setup. They are all in the Parameters cell (section 4 of the notebook), with the value we used as the default. Check them before using the pipeline on other images and rerun that cell after changing anything.

- `stain_matrix`: stain vectors for color deconvolution
- `mask_threshold`: intensity threshold on the stain channel for the binary mask (155)
- `hole_size_percent`: holes inside the periosteum smaller than this percentage of its contour area are filled in (1)
- `dse_pruning_threshold`: skeleton pruning threshold in pixels (2500)
- `min_half_thickness_px`: skeleton points with a distance transform value at or below this are left out of the measurement (4.0 pixels, which is half the thickness, so about 2.0 microns of thickness at our scale)
- `pixels_per_um`: scale factor for your microscope and magnification (3.966 pixels per micron)

## Requirements

The first cell installs `histomicstk`, `imagecodecs-numcodecs`, and `sknw`. The rest (`numpy`, `scipy`, `scikit-image`, `opencv-python`, `pandas`, `matplotlib`, `networkx`) come preinstalled in Colab.

Versions used for the manuscript: [FILL IN]


## License

MIT License. See `LICENSE`.

## Acknowledgments

The skeleton pruning functions are adapted from [DSE-skeleton-pruning](https://github.com/originlake/DSE-skeleton-pruning) (MIT License, Copyright (c) 2020 s.zhong). Color deconvolution uses [HistomicsTK](https://github.com/DigitalSlideArchive/HistomicsTK), and the skeleton graph is built with [sknw](https://github.com/Image-Py/sknw).
