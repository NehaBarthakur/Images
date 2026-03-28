# Comparative study on resizing methods for smartphone-captured intraoral images

This project implements a **non-uniform quad patching and stitching** pipeline for ROI-focused image processing. It divides an image into quads (patches), applies distance-based scaling, stitches them with varying sizes, cleans up the result through bounding box detection, cropping, inpainting, and finally reconstructs a high-quality output image.

The pipeline is particularly useful for medical imaging, satellite imagery, or any domain where the Region of Interest (ROI) needs special emphasis while maintaining contextual background.

## Files Overview

| File       | Purpose |
|-----------|--------|
| `1.py`    | Main preprocessing script: Draws layered quads around ROI, extracts & saves individual quads, computes distance-based scaling factors for each quad. |
| `2.py`    | Alternative quad drawing & stitching version. Loads saved quads and stitches them into a single image using scaling factors. |
| `3.py`    | Post-stitching: Draws convex hull bounding box around the stitched content and saves the result (`W2.png`). |
| `4.py`    | Bounding box + Inpainting: Cleans black areas inside the hull using Telea inpainting (`W3.png`). |
| `5.py`    | Edge-based cropping: Detects edges, finds the best 1:1 square bounding box, crops the image (`W4.png` → `W5.png`). |
| `6.py`    | Improved inpainting version: Inpaints black areas and fills outside regions with green (`W6.png`). |
| `7.py`    | Background filling: Replaces green areas with red temporarily, then fills red regions using pixels from the original image (`W7.png`). |
| `8.py`    | Final inpainting: Removes remaining blue artifacts and produces the clean final output (`S-181-01.png`). |

## Pipeline Flow

```text
1.py  →  Saves quads + scaling factors
   ↓
2.py  →  Stitches quads with non-uniform scaling → W1.png
   ↓
3.py  →  Adds convex hull bounding box → W2.png
   ↓
4.py  →  Inpaints black areas inside hull → W3.png
   ↓
5.py  →  Edge detection + Square cropping → W5.png
   ↓
6.py  →  Further inpainting + green background → W6.png
   ↓
7.py  →  Fills background using original image pixels → W7.png
   ↓
8.py  →  Final cleanup (blue inpainting) → S-181-01.png (Final Output)
```

## Requirements

```bash
pip install numpy opencv-python matplotlib
```

## How to Run

### Step-by-step Execution:

1. **Prepare your data**
   - Place your input image in:  
     `Sri Lanka_Dataset/images/S-181-01.jpg`
   - Place corresponding mask in:  
     `Sri Lanka_Dataset/masks/S-181-01.png`

2. **Run the pipeline in order:**

```bash
python 1.py          # Generate and save quads + scaling
python 2.py          # Stitch quads into W1.png
python 3.py          # Add bounding hull → W2.png
python 4.py          # Inpaint → W3.png
python 5.py          # Crop to square → W5.png
python 6.py          # Inpaint + green fill → W6.png
python 7.py          # Fill background from original → W7.png
python 8.py          # Final cleanup → S-181-01.png
```

**Note**: Make sure all hardcoded paths in the scripts match your folder structure.

## Folder Structure (Expected)

```
Proposed Method/
├── patch/                  # Folder where individual quads are saved (from 1.py)
├── final images/
│   ├── W1.png
│   ├── W2.png
│   ├── W3.png
│   ├── W4.png
│   ├── W5.png
│   ├── W6.png
│   ├── W7.png
│   └── S-181-01.png        # Final output
├── 1.py, 2.py, ..., 8.py
```

## Key Features

- **Non-uniform scaling**: Quads closer to ROI are larger; distant quads are smaller.
- **Layered coloring**: Visualizes distance from ROI using different colors.
- **Content-aware stitching**: Preserves ROI detail while compressing background.
- **Multiple cleanup stages**: Bounding hull, edge-based cropping, and iterative inpainting.
- **Background restoration**: Smartly reuses pixels from the original image.

## Customization

You can modify:
- `quad_size` (currently 20) in `1.py` and `2.py`
- `max_scaling_factor` / `min_scaling_factor` in scaling functions
- Inpaint radius in `4.py`, `6.py`, and `8.py`

## Future Improvements

- Make paths configurable via command-line arguments or config file
- Add automated full pipeline runner (`run_pipeline.py`)
- Support batch processing of multiple images
- Add visualization of scaling factor map
