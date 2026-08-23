# Sudoku Solver from a Photo

Upload a photo of a sudoku puzzle and get back the same photo with the solution filled in.

## Pipeline

1. **Grid detection** (`vision/grid_detect.py`) - OpenCV adaptive threshold +
   contour detection finds the puzzle's outer border in the photo, then a
   perspective transform warps it into a flat, axis-aligned 450x450 square.
2. **Cell extraction** - the warped grid is split into 81 cells (28x28 each).
3. **Digit recognition** (`vision/recognize_digits.py`, `models/`) - a small
   CNN classifies each cell as blank or 1-9. Trained on 14,000 synthetic
   printed-digit images rendered from 66 system fonts with rotation/blur/
   noise augmentation (`data/generate_digits.py`), since real puzzles use
   printed fonts, not handwriting like MNIST.
4. **Centering** (`vision/center_digit.py`) - before classification, each
   cell's ink is isolated via connected components (filtering out grid-line
   residue and noise) and re-pasted at a consistent scale/position. This
   exact function is also used when generating training data, so the model
   never sees a scale/position distribution at inference that it didn't
   train on.
5. **Solving** (`solver/solve.py`) - backtracking with an MRV (minimum
   remaining values) heuristic: always branch on the emptiest cell first.
6. **Overlay** (`vision/overlay.py`) - the solved digits are drawn onto a
   transparent layer in the warped grid's coordinate space, then warped
   *back* through the inverse perspective matrix and composited onto the
   original photo. Only the cells that were originally blank get painted.


**Note**: This is currently a repository with no code in it. 
