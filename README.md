General information about the CTL course available at https://ctl.polyphys.mat.ethz.ch/

# :wave: PROJECT circles

The goal is to identify amounts, radii, and center positions of filled, eventually overlapping, or incompletely shown circles in a two-dimensional image reliably and quickly. This project operates on a regular grid of binary pixel values (pixel value 1 stands for a filled pixel, pixel value 0 stands for an empty pixel). A valid circle has a radius of at least 2 pixels, its center resides within the image, and it is not fully contained in a larger circle. Both the positions and radii of circles must not be integer-valued, but returned with a precision of a single digit (pixel units). A pixel is defined to reside within the circle if its center does so. In the 'simple' version we assume that the circles are 
non-overlapping, and shown completely. In the 'advanced' version we assume that circles do not overlap, but might partially reside outside the image. In the 'expert' version circles may overlap, but are shown completely.

Test images are available in the respective directories in the present github repository, three of them shown here:

| grids-simple | grids-advanced | grids-expert | 
| ------| ---------|--------|
| <img src="https://ctl.polyphys.mat.ethz.ch/CTL-I-PUBLIC/circles/A0.png" width="100%"> | <img src="https://ctl.polyphys.mat.ethz.ch/CTL-I-PUBLIC/circles/B1.png" width="100%"> | <img src="https://ctl.polyphys.mat.ethz.ch/CTL-I-PUBLIC/circles/C1.png" width="100%"> |

Your task is to create a python code named circles.py that analyzes any of the test images, identifies the amount, size, and center positions of circles. It then writes this information to a file and visualizes both the original image and the recognized image for comparison. 

In its final version this code should be able to take a file name as argument (on the command line) instead of hard-coding the filename inside your circles.py. This final version can then be called from the vscode-Terminal (or a linux shell) via  
 
      python3 circles.py grids-simple/A1.txt

## def read_grid(filename)

This function 
1. reads a binary rows $\times$ columns grid of 0/1 values from a file named filename (see [here](https://github.com/mkmat/ETH-Computational-Thinking-Labs/blob/main/README.md#readwritefile) how to read such file, files containing binary grids are available in the folders grids-simple, grids-advanced, grids-expert)
2. determines if the grid contains only zeros and ones, if so, returns the grid. If not, returns+exits with -1

## def visualize_grid(grid)

This function
1. Visualizes the grid (e.g., using matplotlib)
2. Counts the number of 1-pixels and returns this value

Example: 

     def visualize_grid(grid)
     ...
     return OnePixels
     
## def analyze_grid_simple(grid)

This function can assume that circles are completely contained in the grid and that circles are non-overlapping and separated by at least
one 0-pixel. Such grids are available in the folder grids-simple.

1. determine the pixel positions of the filled circle centers, and the circle radii in pixel units
2. create a file identified-circles.txt that contains one line per recognized circle. Each such line carries three
blank-separated numbers: horizontal position (column number) of the circle center,
vertical position (row number) of the circle center, circle radius (pixel units). 

Example for a file identified-circles.txt that reports two recognized circles of radii 7.3 and 11.9

       121 63 7.3
       87 140 12.5

## def read_identified()

1. read the circle centers and their radii contained in identified-circles.txt
2. create a binary grid based on these circle centers+radii
3. return the grid

## def verify_code(filename)

This function makes use of your existing functions and allows you to test your algorithm visually and quantitatively, by comparing the values OnePixels and OnePixelsAnalyzed. The basic structure looks like this: 

    def verify_code(filename):
        grid = read_grid(filename)
        OnePixels = visualize_grid(grid)
        analyze_grid_simple(grid)
        grid = read_identified()
        OnePixelsAnalyzed = visualize_grid(grid)

## def analyze_grid_advanced(grid)

This function can assume that circles do not overlap. All circle centers can be assumed
to reside within the image. Otherwise it is identical with Task 3. Such grids are available in the folder grids-advanced.

## def analyze_grid_expert(grid)

This function can assume that circles fully reside within the grid, but this time circles are allowed to overlap.
Otherwise it is identical with Task 3. Such grids are available in the folder grids-expert.

