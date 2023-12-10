Group report 

# Project title

Describe the task in your own words, and/or link to the task description. 

## Layout of the algorithm, functions to be created

Name the most important functions and mention their functionality, i.e., input and output.

      def myroutine(var1,var2):
          # does the following ...
          return ...

## How to run the code

# -*- coding: utf-8 -*-
import sys
import numpy as np
import math
import matplotlib.pyplot as plt

#function that reads the file and puts it into a numpy array so that its easier to handle
def read_grid(filename):
    grid = np.genfromtxt(filename, delimiter=" ", filling_values=-1)
    for x in grid:
        if np.any(x) > 1 or np.any(x) == -1:
            return "Only binary files allowed"
    return grid

def visualize_grid(grid):
    one = 0
    for x in range(len(grid)):
        for y in range(len(grid)): 
            if grid[x,y] == 1:
               one += 1
    return one

#next function searches all the circles and finds the centers and the radii, my idea is to go through the file until you find a 1, 
#then first you go up and down and count the number of 1 there are in that column, then you go one to the right and do the same. 
#if the number of ones is bigger then you continue in that direction, else in the other, until you reach the highest column. 
# the central value of that column will be the center of the circle and the lenght of it the diameter
def analyze_grid_simple(grid, output_file):
    centerx = []
    centery = []
    radius = []
    for x in range(len(grid)-1):
        for y in range(len(grid[0])-1):
            n = 0
            h = 0
            if grid[int(x)][int(y)] == 1:
                original_x = x
                original_y = y
                while grid[int(x)][int(y)] == 1:
                    y += 1
                    h += 1
                while grid[int(x)][math.ceil(y - h/2)] == 1:
                    x += 1
                    n += 1
                if n != 0 and h != 0:
                    centery.append(y-h/2)
                    centerx.append(x-n/2)
                    radius.append(n/2)
                    for s in range(int(centerx[-1])-math.ceil(n/2)-2, int(centerx[-1])+math.ceil(n/2)+2):
                        for t in range(int(centery[-1])-math.ceil(n/2)-2, int(centery[-1])+math.ceil(n/2)+2):
                            if 0 <= s < len(grid) and 0 <= t < len(grid[0]):
                                grid[s][t] = 0
                x = original_x
                y = original_y + 1

    return np.savetxt(output_file, np.column_stack((centerx, centery, radius)), fmt='%.' + str(1) + 'f', comments="")


#here im going to analize the advanced files, the code is similar to the simple, however i have to add an algorithm that controls if the circle is 
#touching the vorder of the image and if it is then for those circles he checks line per line which is the one with biggest number of ones
#this code also works for all the simple files.
def analyze_grid_advanced(grid, output_file):
    centerx = []
    centery = []
    radius = []
    #going through the whole grid searching ones
    for x in range(len(grid)-1):
        for y in range(len(grid[0])-1):
            n = 0
            h = 0
            if grid[int(x)][int(y)] == 1:
                original_x = x
                original_y = y
                # as soon as i found a one i want to find the y coordinate of the center
                while grid[int(x)][int(y)] == 1 and int(y)<(len(grid[0])-1):
                    y += 1
                    h += 1
                    # same for the x coordinate
                while grid[int(x)][math.ceil(original_y + h/2)] == 1 and int(x)<(len(grid)-1):
                    x += 1
                    n += 1
                    #the next part is the stuff i added for the advanced part
                if n != 0 and h != 0:
                    temp_y = int(original_y + h/2)
                    temp_x = int(x-n/2)
                    list = []
                    #here i check if the x coordinate i chose is really the most centered one by comparing it to its neighbours
                    for element in range(temp_x-1,temp_x+1):
                        z = 0
                        while grid[element, temp_y] == 1 and 0 < element <(len(grid)-1) and int(temp_y)<(len(grid[0])-1):
                            z += 1
                            temp_y += 1
                        list.append(z)
                        temp_y = temp_y -z
                        # if it actualy was the most centered one i just add its coordinates to the result file 
                    if np.max(list) == list[1]:
                        centerx.append(temp_x)
                        centery.append(int(original_y + h/2))
                        radius.append(n/2)
                        # if the one to its left was more centered (had a longer radius) then i continue in that direction until i find the most centered one
                    elif np.max(list) == list[0]:
                        c = 0
                        a = list[0]
                        b = list[1]
                        while a>b:
                            temp_x -= 1
                            b = a
                            t = 0
                            c += 1   
                            while grid[temp_x, temp_y] == 1 and int(temp_y)<(len(grid[0])-1):
                                temp_y += 1
                                t += 1
                        centerx.append(temp_x)
                        centery.append(int(original_y + h/2))
                        radius.append(n/2 + c)
                        #same stuff but for the case that the right one was more centered
                    elif np.max(list) == list[2]:
                        c = 0
                        a = list[2]
                        b = list[1]
                        while a>b:
                            temp_x += 1
                            b = a
                            t = 0
                            c += 1
                            while grid[temp_x, temp_y] == 1 and int(temp_y)<(len(grid[0])-1):
                                temp_y += 1
                                t += 1
                        centerx.append(temp_x)
                        centery.append(int(original_y + h/2))
                        radius.append(n/2 + c)
                        #in thid final part i turn to zero all the values of the circle so that the algoritm wont analyze the same circle twice
                    for s in range(int(centerx[-1])-int(radius[-1])-3, int(centerx[-1])+int(radius[-1])+3):
                        for t in range(int(centery[-1])-int(radius[-1])-3, int(centery[-1])+int(radius[-1])+3):
                            if 0 <= s < len(grid) and 0 <= t < len(grid[0]):
                                grid[s][t] = 0
                x = original_x
                y = original_y
#the algoritm gives me as a result a textfile that has the coordinates of the centers and the radii of all the circles 
    return np.savetxt(output_file, np.column_stack((centerx, centery, radius)), fmt='%.' + str(1) + 'f', comments=""), list

#here i read the file containing the centers and radii of the circles
def read_identified(filename,filename1):
    #just reading
    grid = read_grid(filename)
    gridid = np.zeros((len(grid),len(grid[0])))
    identified = np.genfromtxt(filename1, delimiter=" ")
    x_0 = identified[:,0]
    y_0 = identified[:,1]
    r = identified[:,2]
    #here i draw the circles that my algoritm found on a new "blank" matrix 
    for i in range(len(r)):
        gridid[round(x_0[i])][round(y_0[i])] = 1
        for x in range(0,round(r[i])):
            y = 0
            while (y**2+x**2) <= (r[i]**2+4): # this is the mathematical way to draw a circle 
                if int(round(y_0[i])+y)<(len(grid[0])-1) and int(round(x_0[i])+x)<(len(grid)-1):
                    gridid[round(x_0[i])+x][round(y_0[i])+y] = 1
                if int(round(y_0[i])-y)<(len(grid[0])-1) and int(round(x_0[i])+x)<(len(grid)-1):
                    gridid[round(x_0[i])+x][round(y_0[i])-y] = 1
                if int(round(y_0[i])+y)<(len(grid[0])-1) and int(round(x_0[i])-x)<(len(grid)-1):
                    gridid[round(x_0[i])-x][round(y_0[i])+y] = 1
                if int(round(y_0[i])-y)<(len(grid[0])-1) and int(round(x_0[i])-x)<(len(grid)-1):
                    gridid[round(x_0[i])-x][round(y_0[i])-y] = 1
                y += 1
    return gridid

#this part compares the ctual file of the circles with the one i created using the code
def verify_code(filename,filename1):
    grid = read_grid(filename)
    OnePixels = visualize_grid(grid)
    analyze_grid_advanced(grid, output_file ="identified-circles.txt")
    grid = read_identified(filename,filename1)
    OnePixelsAnalyzed = visualize_grid(grid)
    deviation = abs(OnePixels-OnePixelsAnalyzed)
    return deviation

#plotting the graphs
def show_balls(matrix, title=''):
    plt.figure(figsize=(5,5))
    matrix2 = np.matrix(matrix).astype(int)
    plt.imshow(matrix2, cmap='hot', interpolation='nearest')
    plt.title(title)
    plt.savefig('C:/Users/Noel/OneDrive/Desktop/eth/Bachelor 2/PP3/P1/vs code/CTL2/identified2.png')

# just defining some stuff and printing the results
filename = "B4.txt"
filename1 = "identified-circles.txt"

print(show_balls(read_grid(filename), "balls"))
print(show_balls(read_identified(filename,filename1), "identified balls"))
print(verify_code(filename,filename1))

Mention, how to run the code. Mention the parameters, in case your code has command line arguments. 

      python3 mycode.py
      python3 mycode,py arg1 arg2 

where arg1 and arg2 are ...

## Resulting files

Mention any resulting files and their format and meaning. 

## Successful tests

Summarize, which tests have been successfully passed to test your code. 

## Problems

Keep track of problems and yet unanswered questions so that all group members know where to focus next. 

## Results (speed tests etc.)

The beautiful part, where you report results, tables, files, or figures, after your code was able to run. 
