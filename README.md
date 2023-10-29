# Design and Development of 16-byte SRAM Circuit using 180 nm CMOS Technology

SRAM (Static Random Access Memory) is a type of RAM which stores data indefinitely as long as there is power unlike DRAM (Dynamic RAM) which loses data gradually even if they are powered. They store 1 bit of data and is faster than DRAM as they don't need to be recharged before read operation to get the correct output and hence used as cache memory in processors. But while maintaining speed SRAM have to compromise with area.

![sram_block drawio](https://github.com/RudranshKi/SRAM/assets/110120694/a74bc785-c224-4686-a5f7-d7e493ad0010)
|:--:| 
| *SRAM Block diagram* |

## Table of contents        

1.  [Introduction](https://github.com/RudranshKi/SRAM/blob/main/README.md#about)
2.  [SRAM Circuit](https://github.com/RudranshKi/SRAM/blob/main/README.md#SRAM-Circuit)
    1. [6T SRAM cell](https://github.com/RudranshKi/SRAM#6t-sram-cell)
        - [Operations](https://github.com/RudranshKi/SRAM#operation)
           1. [Write](https://github.com/RudranshKi/SRAM#write)
           2. [Read](https://github.com/RudranshKi/SRAM#read)
           3. [Hold](https://github.com/RudranshKi/SRAM#hold)
        - [6T-SRAM Design](https://github.com/RudranshKi/SRAM#Transistor-sizing)
        - [Static Noise Margin of our 6-T SRAM](https://github.com/RudranshKi/SRAM#static-noise-margin-of-our-6-t-sram)  
    2. [Pre charge circuit](https://github.com/RudranshKi/SRAM#pre-charge-circuit)
    3. [Write driver](https://github.com/RudranshKi/SRAM#write-driver)
    4. [Row decoder](https://github.com/RudranshKi/SRAM/tree/main#row-decoder)
    5. [Sense Amplifier](https://github.com/RudranshKi/SRAM#sense-amplifier)
         - [Sense Amplifier Design](https://github.com/RudranshKi/SRAM#sense-amplifier-design)        
3.  [Specifications](https://github.com/RudranshKi/SRAM/blob/main/README.md#specifications)
4.  [Schematic Design](https://github.com/RudranshKi/SRAM/blob/main/README.md#design)
5.  [Testbench](https://github.com/RudranshKi/SRAM/blob/main/README.md#design-testbench)
6.  [Delays in SRAM](https://github.com/RudranshKi/SRAM#delays-in-sram)
7.  [Operating Frequency Measurement](https://github.com/RudranshKi/SRAM#operating-frequency-measurement)
8.  [Result](https://github.com/RudranshKi/SRAM/blob/main/README.md#result) 
<!-- 9.  [Conclusion](https://github.com/RudranshKi/SRAM/blob/main/README.md#conclusion) -->



## <ins>Introduction</ins>
16-byte SRAM array based on 6T design of SRAM. It reads and writes 8 bits at once and the column is selected by a decoder which will decode the input address. While writing data onto SRAM cell, we provide a external source to forcefully alter the data stored. The data is drived by a buffer chain to effeciently write the data onto SRAM. And for reading we let the pre charged Bit Line (BL) or Bit Line Bar (BLB) value to drain to the stored value. While reading the sense amplifier senses the changes in BL and BLB, and amplifies the difference so as to provide the output fast without waiting for one of them to completely drain.

It is generally used as cache memory for it's ability to retain data for theoritically infinite time as long as supply is on and can be used for low powered IOT devices where speed and power consumption is a priority.

## <ins>SRAM Circuit</ins>

### <ins>6T SRAM cell</ins>

![6T-SRAM](https://github.com/RudranshKi/SRAM/assets/110120694/cd3955fe-db7d-4e36-a7fc-aa811675de4d)
|:--:| 
| *Sense amp (6T)* |


#### <ins>Operation  </ins>

##### <ins>WRITE</ins>

Procedure for writing in 6T SRAM :

    1. When pre charge is on, BL and BLB are charged to 1.8V.
    2. The write signal latches the voltage sources to the BL and BLB.
    2. When the access transistors are turned on using Word Line, there is a connection between BL and the SRAM's inverter input and similarly for the BLB.
    3. The input sources for data we provide tries to forcefully alter the voltage in the SRAM inverter. (For that we need to find the appropriate sizing for the transistors involved in the data writing process.)
    


![Write path(2)](https://github.com/RudranshKi/SRAM/assets/110120694/f6b08e97-d7b7-4e97-850e-75970814fb40)
|:--:| 
| *WRITE PATH* |

Here we are trying to write 0V , so the node voltage is 0.3V and the inverter opposite to it has already swapped the values from 0 to 1.8V.

Here the transistor , m5 is in saturation and m3 in linear according to **(VSD = VSG - VT)** eqn.

![image](https://github.com/RudranshKi/SRAM/assets/110120694/d8718afc-2366-4b78-9f21-068d5cc97075)

![image](https://github.com/RudranshKi/SRAM/assets/110120694/7f39fa18-774a-418a-8398-7ce769a47d6e)

![image](https://github.com/RudranshKi/SRAM/assets/110120694/b17b9bf7-50f9-450c-a889-c6ce71778855)

Note : *Vt = 0.67V (found from model files of the transistors)*

##### <ins>READ</ins>

Procedure for reading in 6T SRAM :

    1. When pre charge is on, the BL and BLB are charged to 1.8V.
    2. When the access transistors are turned on using Word Line, there is a connection between BL and the inverter input and similarly for BLB.
    3. Then BL and BLB start draining through the m1 node (if you're reading 0V else it will stay at pre charged value since acess transistor will never turn on since Vgs < Vt).
    4. The output is then extracted from BL and BLB after the voltages settle down.
    

![Read path](https://github.com/RudranshKi/SRAM/assets/110120694/1874556d-4508-41e1-ae91-1c91b54084a2)
|:--:| 
| *READ PATH* |


Here we are trying to read 0V stored in the SRAM. For that we need to  find the operating mode of the transistors involved in reading it.

Here transistor m3 is in saturation and m1 is in linear mode according to **(VSD = VSG - VT)** eqn. Since both of them are connected in series, same current will flow through them.



To find the width for transistors in read operation :

![image](https://github.com/RudranshKi/SRAM/assets/110120694/df40acf1-1bf1-4c29-8f0e-fa8103d4bb09)

![image](https://github.com/RudranshKi/SRAM/assets/110120694/f31f0463-1ffa-462e-b5af-36467ad58579)

![image](https://github.com/RudranshKi/SRAM/assets/110120694/ddd826ea-1617-49f3-8710-7f31487aa6ef)

Note : *Vt = 0.67V (found from model files of the transistors)*

##### <ins>HOLD</ins>

When the **access transistors** (m3 and m5) are off the data is hold onto the inverters of SRAM as long sufficent supply voltage is present.

#### <ins>6T-SRAM Design</ins>

![image](https://github.com/RudranshKi/SRAM/assets/110120694/818d5fa0-e721-4c7e-8397-6683c371c1ce)


**Final transistor size,**
|  Transistor   |     Type      |     Width     |     Length    |
| ------------- | ------------- | ------------- | ------------- |
|      m1       |    NMOS  |    220 nm  |   180 nm  |
|      m2       |    NMOS  |    220 nm  |   180 nm  |
|      m3       |    NMOS  |    220 nm  |   180 nm  |
|      m4       |    NMOS  |    220 nm  |   180 nm  |
|      m5       |    PMOS  |    550 nm  |   180 nm  |
|      m6       |    PMOS  |    550 nm  |   180 nm  |

##### <ins>Static Noise Margin of our 6-T SRAM</ins>

The noise margin of the device shows the noise tolerance of the device so that the output doesn't deviate from the intended output for a specific range of inputs.


In our case in inverters the noise margin for the output is generally given as :

![image](https://github.com/RudranshKi/SRAM/assets/110120694/722617bd-14eb-47c1-9edb-f2a1c5a7f594)

Since, we are using back to back inverters for our SRAM, we will use the ***Butterfly technique*** to find the noise margin of both the inverters. In ***Butterfly technique*** we essentially plot noise margin of the both the inverters and place one in potrait and other in landscape so that they look like the wings of a butterfly. After that we try to fit the biggest square which won't step out of the boundries of each of the wing and from both the squares we take the one with the smallest area which will show the worst case noise margin of the SRAM. 

![SNM](https://github.com/RudranshKi/SRAM/assets/110120694/4c08b78a-e7e8-464b-875d-40fd0f1b4450)
|:--:| 
| *Static Noise Margin for 6-T SRAM* |

Note - *It is essential to maintain a good noise margin for the SRAM so it can operate in enviornment with a lot of noise. We can increase the SNM of a inverter by increasing it's width so that resistance decreases and current flow increases which makes the transition sharp*


**More accurate SNM**


![snm](https://github.com/RudranshKi/SRAM/assets/110120694/5f6de1df-1cc1-4aca-8646-d1a6246abde3)
|:--:| 
| *Static Noise Margin calculated for 6-T SRAM* |

Note : *So 0.64v Noise margin for our design*

<details>
<summary>Algorithm for finding SNM</summary>
    
1. Import the CSV file of the inverters' DC sweep values.    
2. Separate the values for Q vs QB X-axis, Q vs QB Y-axis, QB vs Q X-axis, QB vs Q Y-axis.
3. Declare a length for the line segments.
4. For both the lobes, we initially mark the points with -1 slope.
5. Then plot line segments from the points in both 90 degrees and 0 degrees so that the line segments from the two opposite points converge with each other to form a rectangle.
6. Then check the distance between the point on slope points and the intersection points , if the magnitude is equal or almost equal to each other. If not we move to step 7.
7. We then check the same for the next point of the any of the two graph (suppose in first lobe, either QB vs Q  or Q vs QB) (note : we change the points only between half of the points on the graph so as to decrease the simulation time of the program).
8. Once we find the difference between the magnitude of all the Q vs QB to intersection and QB vs Q to intersection , we sort them in ascending order.
9. After both lobes are done, compare and plot the square for the lobe with lowest magnitude for X-axis 
</details>
<details>
<summary>PYTHON code to measure SNM</summary>
    
            import csv
            import numpy as np
            import matplotlib.pyplot as plt
            from math import *
            import math
            from sympy import Point, Line
            # Load data from the CSV file
            q_qbX, q_qbY, qb_qX, qb_qY = np.loadtxt("SRAM_SNM.csv", delimiter=",", skiprows=1, unpack=True)
            line_length = 1    # Length of the line to draw from the points
            # To find point of intersection of the line segments 
            
            def find_intersection(line1, line2):
                line1 = Line(Point(line1[0]), Point(line1[1]))
                line2 = Line(Point(line2[0]), Point(line2[1]))
            
                intersection = line1.intersection(line2)
            
                if intersection:
                    return (intersection[0].x, intersection[0].y)
                else:
                    return None
            
            #########################################################################################################################
            
            # To calculate noise margin from 2nd lobe
            
            line1= []
            line3=[]
            line2 = []
            line4 = []
            line_angle1 = 270
            line_angle2=180
            
            # Calculate and store only points with -1 slope
            slope_threshold = 0.25    # How close we want the slope to be to -1 slope
            slope_pairs = [] 
            def negSlope2(pointX_1,pointX_2,pointY_1,pointY_2) :
                if (pointX_2 > 0.734) and (pointY_2 < 0.752) :
                    derivativeX = pointX_1-pointX_2
                    derivativeY = pointY_1-pointY_2
                    slope =derivativeX/derivativeY
                    if abs(slope+1) <= slope_threshold :
                        slope_pairs.append([pointX_2,pointY_2])
                       
            for i in range(len(q_qbX)-1) :
                negSlope2(q_qbX[i],q_qbX[i+1],q_qbY[i],q_qbY[i+1])
                
            
            # Calculate the ending point for q_qb 
            x_end1 = slope_pairs[0][0] + line_length * (cos(radians(line_angle1)))
            y_end1 = slope_pairs[0][1] + line_length * (sin(radians(line_angle1)))
            
            # Storing the plot points for vertical line for 2nd lobe
            line3.append(slope_pairs[0])
            line3.append([x_end1,y_end1])
            
            # Calculate the ending point for q_qb 
            x_end2 = slope_pairs[0][0] + line_length * (cos(radians(line_angle2)))
            y_end2 = slope_pairs[0][1] + line_length * (sin(radians(line_angle2)))
            
            # Storing the plot points for horizontal line for q_qb
            line1.append(slope_pairs[0])
            line1.append([x_end2,y_end2])
            
            # Changing angle to plot next pair of lines
            line_angle1= 90
            line_angle2=0
            
            for i in range(len(qb_qX)-1) :
                negSlope2(qb_qX[i],qb_qX[i+1],qb_qY[i],qb_qY[i+1])
                
            m=30          # to calculate the points from the second lobe only (used to limit the calculated parameters)
            pairs = []    # To store the points and difference x and y axis
            
            # Iterate through all the points till we find the closest to square 
            for i in range(int(m)) :
                slope_pairs[1] = [qb_qX[20+i],qb_qY[20+i]]
               
                # Calculate and plot end point for first line
                x_end = slope_pairs[1][0] + line_length * (cos(radians(line_angle1)))
                y_end = slope_pairs[1][1] + line_length * (sin(radians(line_angle1)))
            
                line2.append(slope_pairs[1])
                line2.append([x_end,y_end])
            
                x_end = slope_pairs[1][0] + line_length * (cos(radians(line_angle2)))
                y_end = slope_pairs[1][1] + line_length * (sin(radians(line_angle2)))
            
                line4.append(slope_pairs[1])
                line4.append([x_end,y_end])
            
                intersection1 = find_intersection(line1,line2)
                intersection1 = Point(*intersection1)
                distance1 = intersection1.distance(slope_pairs[0])
                distance1 = distance1.evalf() 
            
            
                intersection2 = find_intersection(line3,line4)
                intersection2 = Point(*intersection2)
                distance2 = intersection2.distance(slope_pairs[0])
                distance2 = distance2.evalf() 
            
                pairs.append([line2,line4,abs(distance1-distance2),distance2])
                
                line2 = []
                line4 = []
            
            # Ascending Sorting according to least difference between horizontal and vertical side of the square
            pairs.sort(key=lambda x: x[2])
            
            ######################################################################################################################
            
            # To calculate noise margin from 1st lobe
            
            # Calculate and store only points with -1 slope
            slope_threshold = 0.2    # How close we want the slope to be to -1 slope
            slope_pairs = [] 
            
            def negSlope1(pointX_1,pointX_2,pointY_1,pointY_2) :
                if (pointX_2 < 0.734) and (pointY_2 > 0.752) :
                    derivativeX = pointX_1-pointX_2
                    derivativeY = pointY_1-pointY_2
                    slope =derivativeX/derivativeY
                    if abs(slope+1) <= slope_threshold :
                        slope_pairs.append([pointX_2,pointY_2])
            ########################################################################################
            line1 = []   # Emptying the previous stored line segment points
            line3 = []   # Emptying the previous stored line segment points
            line2 = []   # Emptying the previous stored line segment points
            line4 = []   # Emptying the previous stored line segment points
            #line_length = 1
            line_angle1 = 270
            line_angle2=180
            
            for i in range(len(qb_qX)-1) :
                negSlope1(qb_qX[i],qb_qX[i+1],qb_qY[i],qb_qY[i+1])
            
            
            # Calculate the ending point for qb_qX 
            x_end3 = slope_pairs[0][0] + line_length * (cos(radians(line_angle1)))
            y_end3 = slope_pairs[0][1] + line_length * (sin(radians(line_angle1)))
            
            # Storing the plot points for vertical line for 1st lobe
            line3.append(slope_pairs[0])
            line3.append([x_end3,y_end3])
            
            
            
            # Calculate the ending point for qb_qX 
            x_end4 = slope_pairs[0][0] + line_length * (cos(radians(line_angle2)))
            y_end4 = slope_pairs[0][1] + line_length * (sin(radians(line_angle2)))
            
            # Storing the plot points for horizontal line for 1st lobe
            line1.append(slope_pairs[0])
            line1.append([x_end4,y_end4])
            
            # Overwriting the angles for next set of line segments
            line_angle1= 90
            line_angle2=0
            
            n = 0  # For iterating through points (Used to find the number of points in the 1st lobe)
            for i in range(len(qb_qX)-1) :
                negSlope1(q_qbX[i],q_qbX[i+1],q_qbY[i],q_qbY[i+1])
                n = n+1
                
            pairs1 = []  # To store the points and difference x and y axis
            # Iterate through all the points till we find the closest to square 
            for i in range(int(n/2)) :
                slope_pairs[1] = [q_qbX[n-i],q_qbY[n-i]]
               
                # Calculate and plot end point for first line
                x_end = slope_pairs[1][0] + line_length * (cos(radians(line_angle1)))
                y_end = slope_pairs[1][1] + line_length * (sin(radians(line_angle1)))
            
                line2.append(slope_pairs[1])
                line2.append([x_end,y_end])
            
                x_end = slope_pairs[1][0] + line_length * (cos(radians(line_angle2)))
                y_end = slope_pairs[1][1] + line_length * (sin(radians(line_angle2)))
            
                line4.append(slope_pairs[1])
                line4.append([x_end,y_end])
            
                intersection1 = find_intersection(line1,line2)
                intersection1 = Point(*intersection1)
                distance1 = intersection1.distance(slope_pairs[0])
                distance1 = distance1.evalf() 
            
            
                intersection2 = find_intersection(line3,line4)
                intersection2 = Point(*intersection2)
                distance2 = intersection2.distance(slope_pairs[0])
                distance2 = distance2.evalf() 
            
                pairs1.append([line2,line4,abs(distance1-distance2),distance2])
                
                if (i==int(n/2)) :
                    break
                line2 = []
                line4 = []
            
            # Sorting according to least difference between horizontal and vertical side of the square
            pairs1.sort(key=lambda x: x[2])
            
            print(pairs1[0])
            # Calculating the distance between the horizontal lines of the squares
            #distance_1lobe = math.dist([pairs1[0][1][0][0],pairs1[0][1][0][1]],[pairs1[0][1][1][0],pairs1[0][1][1][1]])
            #distance_lobe = math.dist([pairs[0][1][0][0],pairs[0][1][0][1]],[pairs[0][1][1][0],pairs[0][1][1][1]])
            #distance_lobe = math.dist(pairs[0][1][0][0],pairs[0][1][0][1])
            
            print(pairs1[0][3])
            print(pairs[0][3])
            
            distance_1lobe = pairs1[0][3]
            distance_lobe = pairs[0][3]
            
            # Comparing and plotting the square with the lowest side length
            if (distance_lobe < distance_1lobe) :
                # For plotting points 1st half
                x_axis_ver=pairs1[0][0][0][0]
                y_axis_ver=pairs1[0][0][0][1]
                x_axis_ver_end=pairs1[0][0][1][0]
                y_axis_ver_end=pairs1[0][0][1][1]
            
                x_axis_hor=pairs1[0][1][0][0]
                y_axis_hor=pairs1[0][1][0][1]
                x_axis_hor_end=pairs1[0][1][1][0]
                y_axis_hor_end=pairs1[0][1][1][1]
            
                # PLotting Square    
                plt.plot([x_axis_ver,x_axis_ver_end], [y_axis_ver,y_axis_ver_end], marker="o")
                plt.plot([x_axis_hor,x_axis_hor_end], [y_axis_hor,y_axis_hor_end], marker="o")
                plt.plot([slope_pairs[0][0], x_end4], [slope_pairs[0][1], y_end4], marker="o")    
                plt.plot([slope_pairs[0][0], x_end3], [slope_pairs[0][1], y_end3], marker="o")
                label_x = (x_axis_hor + x_axis_hor_end) / 4
                label_y = (y_axis_hor + y_axis_hor_end) / 2
                distance_lobe = "{:.2f}".format(distance_lobe)
                # Add the distance label above the line segment
                plt.text(label_x, label_y, f'dx={distance_lobe}', ha='center', va='bottom')
            else :
                x_axis_ver=pairs[0][0][0][0]
                y_axis_ver=pairs[0][0][0][1]
                x_axis_ver_end=pairs[0][0][1][0]
                y_axis_ver_end=pairs[0][0][1][1]
            
                x_axis_hor=pairs[0][1][0][0]
                y_axis_hor=pairs[0][1][0][1]
                x_axis_hor_end=pairs[0][1][1][0]
                y_axis_hor_end=pairs[0][1][1][1]
            
                # PLotting Square    
                plt.plot([x_axis_ver,x_axis_ver_end], [y_axis_ver,y_axis_ver_end], marker="o")
                plt.plot([x_axis_hor,x_axis_hor_end], [y_axis_hor,y_axis_hor_end], marker="o")
                plt.plot([slope_pairs[0][0], x_end4], [slope_pairs[0][1], y_end4], marker="o")    
                plt.plot([slope_pairs[0][0], x_end3], [slope_pairs[0][1], y_end3], marker="o")
                label_x = (x_axis_hor + x_axis_hor_end) / 4
                label_y = (y_axis_hor + y_axis_hor_end) / 2
                distance_1lobe = "{:.2f}".format(distance_1lobe)
                # Add the distance label above the line segment
                plt.text(label_x, label_y, f'dx={distance_1lobe}', ha='center', va='bottom')
                
                
                
            # Plotting the butterfly output for DC sweep output for both q vs qb and qb vs q
            
            #plt.scatter(q_qbX, q_qbY, marker="o")
            #plt.scatter(qb_qX, qb_qY, marker="o")
            plt.plot(q_qbX, q_qbY, qb_qX, qb_qY)
            plt.xlabel("in V ------------------------->")
            plt.gca().set_aspect('equal')   # Sets the graph's aspect ratio to 1:1
            plt.title("Butterfly graph for finding Static Noise Margin for 6-T SRAM")
            plt.show()
</details>


### <ins>Pre Charge Circuit</ins>

![Precharge](https://github.com/RudranshKi/SRAM/assets/110120694/b2791dd2-160d-482f-bd65-570977d68ba4)
|:--:| 
| *Pre charge circuit* |

when the **PC (Pre charge)** signal is high (1.8V), the PMOSes turn on, BL and BLB charges to 1.8V or VDD. The sizes of PMOS transistors decide how fast BL and BLB can charge up. It is essential to maintain good performance to area ratio while sizing it.

**Equalizer** equalizes both the BL and BLB caps' voltage to same voltage (during physical implementation the BL and BLB there will be leakage current which will drop the voltage of BL and BLB).

In our case : 

|  Transistor   |     Type      |     Width     |     Length    |
| ------------- | ------------- | ------------- | ------------- |
|      m1       |    PMOS  |    1 &micro;m  |   180 nm  |
|      m2       |    PMOS  |    1 &micro;m  |   180 nm  |
|      m3       |    PMOS  |    220 nm  |   180 nm  |

### <ins>Write Driver</ins>

![Write driver](https://github.com/RudranshKi/SRAM/assets/110120694/9dcd9f7b-f113-4900-b639-bec40614e88c)
|:--:| 
| *Write driver using transmission gate* |

When **WR (Write)** signal is high (1.8V) , the TG (Transmission Gate) acts as short circuit and conducts current allowing the user to write the data to BL or BLB on command.

TG is used instead of simple NMOS or PMOS as switch because if we use only NMOS then the node voltage BL or BLB will only be able to write **(VGS - VT)** which is 1.13V instead of full 1.8V if the data is 1.8V and if we use PMOS then it will be only be abe to write **(VT)** if we write 0V to a node which has 1.8V stored in it. But using TG we can overcome that since both the mosfets will be turned on so for writing 1.8V the PMOS will be more suitable while writing 0V NMOS will be more suitable.

Since, SRAM array has 16 SRAMs which will have some parasitic capacitance. To drive a huge capacitance we need short rise and fall time to decrease power loss and increase speed. For that we need to drive it efficently we will need increase the strength of the inverter connected to it. Since increasing the strength of the inverter we will need to increase the size of NMOS and PMOS in it but that will also consequently increase parasitic cap. So we have to create a ***Buffer chain*** with a stage ratio of 4:1. 


In ***Buffer chain*** we essentially gradually reduce the size of transistors needed to drive a capacitance by successively driving the prior cap by smaller cap. And this ratio is usually 4:1 which gives a good proportion for area to performance.


In our case the parasitic cap isn't huge so going from Inv 2X to Inv 1x for buffer chain is enough.

### <ins>Row decoder</ins>

To address 16 byte memory in our case , we need a 4x16 decoder. Which will take 4 bit address as input and then provide output signal to turn on a specific access transistors in the SRAM coloumn array.
The ***Ctrl*** signal acts as a enable signal for the row decoder so once ***Ctrl*** signal is low , it switches the output of decoder to 0V but when it is on , it allows the normal operation of the decoder.


![Row decoder](https://github.com/RudranshKi/SRAM/assets/110120694/02dcecdf-c576-459d-adcf-de4c7454270d)
|:--:| 
| *4x16 Row Decoder* |


### <ins>Sense Amplifier</ins>
<!---
![currentMirror](https://github.com/RudranshKi/SRAM/assets/110120694/7e8da5c7-4a1b-4a4b-8b7e-911817951e3c)
|:--:| 
| *Current mirror* |
--->

![senseamp](https://github.com/RudranshKi/SRAM/assets/110120694/5f455770-a2de-453d-8ca9-91b7d485c484)
|:--:| 
| *Sense Amplifier* |

The main goal of Sense Amplifier is to sense the output during the transition and amplify the result saving the transition time and increasing the speed of operation. 

Here, the goal of first current mirror circuit (m5 and m0) is to mirror the current from the current source , so the circuit try to draw the same amount of current from the VDD source as that of current source connected. And the goal of second current mirror circuit (m3 and m4) is to mirror the current in the both nodes for BL and BLB (since they will try to mirror the current source's output and that current is going to mirrored again by this current mirror so effectively the current will get halved).


When BL and BLB is is pre charged to 1.8V the node voltage at ***Vsense*** stabilizes at a certain node value after a certain time, in our case around 0.9V. (refer to [Sense Amplifier Design](https://github.com/RudranshKi/SRAM#sense-amplifier-design) for details on this)

So, once the node value of BL or BLB changes the node voltage ***Vsense*** is going to reflect it immediately as the current mirror circuit above is going to try and mirror the same current in both nodes. If BL goes lower than BLB, the node voltage ***Vsense*** will go lower than 0.9V and in vice versa for the node voltage.

We then use two inverters back to back to amplify that difference. 


For e.g- we are reading BL - 0V, BLB - 1.8V, so when we are reading those, BL (pre charged to 1.8V) is going to go down to 0V so the channel current will decrease. Consequently the current flow in transistor (m3) will also decrease. Since Path 1 current flow decreases , the current flow from Path 2 has to increase since they are in parallel and according to **KCL**, current through incoming nodes should be equal to outgoing node. But transistors (m4 and m3) acts as current mirror , it will try to mirror the same current as of Path 1. So consequently , transistor (m2) has to discharge the Inv 1's input node to satisfy the current need which in turn will decrease the ***Vsense*** node voltage (Inv 1's parasitic cap which would have been charged to 0.9V will discharge through transistor (m2)). This will be immediately reflected upon the first inverter which would have been sized such that it's trip point is same 0.9V (i.e - When the input is 0.9V the output will also be 0.9V). Since the gain the first inverter is high, a slight amount of change in input is going to immediately send the output to one of the stable state (i.e- 1.8V or 0V (in this case 1.8V)).



#### <ins>Sense amplifier Design</ins>

For sizing the current mirror we are first setting a transistor in saturation region by short circuting the gate node with source node so it always stays in the saturation region (saturation region because it has maximum current flow through it)(refer to fig (Sizing setup(only m5))). Then after setting a  **Gm / ID** ratio (between 10 - 4 with 4 being the best region), we set up a similar transistor in conjunction to it with same width and length so that same current flows through it since both of them have same resistance and same gate voltage.

(*We are using Gm/ID to maintain a good saturation region*)

![Current mirror](https://github.com/RudranshKi/SRAM/assets/110120694/54d48703-a760-40d2-889a-083d211fe864)

|:--:| 
| *Sizing setup* |

Since ***Vnode*** is drawing the same current from the given current source we use another current mirror circuit to mirror the same current (we size them using the above procedure again but for PMOS) and connect BL an BLB transistors to it. The NMOSes (m1 and m2) connecting the PMOS (m3 and m4) current mirror circuit to NMOS (m5 and m0) current mirror circuit is then connected to BL and BLB through their gates. We size them to maintain a specific ***Vsense*** node voltage which will act as a middle trip point for output (Above that trip point - 1.8V, below that trip point - 0V).


[Gain of current mirror circuit.](https://github.com/RudranshKi/SRAM/assets/110120694/6b6121f2-419a-404a-b8c1-93fa8e254bfb)

Note : *Having a good gain for current mirror circuit is essential for faster operation since , if one of the nodes (BL or BLB) drops or increases by a very small amount (let's say for 200 mv) the node voltage ***Vsense*** will increase or decrease according to the gain amount (so if you have 30dB gain then just 200mv drop will be amplified by drop of 200mV x 30 amount)*.*To increase the gain you need to burn more current and increase the sizing of the current mirror circuit. To increase the gain of differential amplifier (current mirror circuit here) we increase the W/L ratio such that the transistors go towards lower GM/ID ratio making the transistors more deep into saturation region.*


**Inverter 1** is sized so that it's trip point is around the same value as that of ***Vsense***. So once the value falls below or above that trip point , it will immediately amplify the output to a stable voltage level (either 1.8V or 0V). Inverter 2 is used to amplify ***Vsense*** node voltage further and complement it to get the original value.



**Differential Amplifier transistor sizing**

**--------------------------------------------------------------**

|  Transistor   |     Type      |     Width     |     Length    |
| ------------- | ------------- | ------------- | ------------- |
|      m5       |    NMOS  |    350 nm  |    2 &micro;m  |
|      m0       |    NMOS  |    350 nm  |    2 &micro;m  |
|      m1       |    NMOS  |    300 nm  |   1.5 &micro;m  |
|      m2       |    NMOS  |    300 nm  |   1.5 &micro;m  |
|      m3       |    PMOS  |    600 nm  |   1.7 &micro;m  |
|      m4       |    PMOS  |    600 nm  |   1.7 &micro;m  |



**Inverter transistor sizing**

**------------------------------------------------------------**

|  Inverter   |     Type      |     Width     |     Length    |
| ------------- | ------------- | ------------- | ------------- |
|      Inv 1       |    PMOS  |    2.045 &micro;m  |    180 nm  |
|      Inv 1       |    NMOS  |    500 nm  |    180 nm  |
|      Inv 2       |    PMOS  |    6 &micro;m  |    180 nm  |
|      Inv 2       |    NMOS  |    500 nm  |    180 nm  |


<ins>**Differential amplifier (sense amplifier)  working output**</ins>
![sense_output](https://github.com/RudranshKi/SRAM/assets/110120694/5c14caf6-b8f2-4633-9c42-deba45e8f490)





## <ins>Specifications</ins> 

Supply Voltage : 1.8V

Total power consumption : ~270 &micro;w to 1.154 mw

Maximum operation frequency : 52 MHz

Memory : 16 byte

Operable temperture : -40&deg;C to 85&deg;C


Note : *To calculate the average power consumption we average the current draw from the supply voltages and multiply it with the supply voltage* 

## <ins>Design</ins>

#### SRAM

![16 byte SRAM](https://github.com/RudranshKi/SRAM/assets/110120694/fae3615e-da03-445c-82b2-c7b4f9d814f8)
|:--:| 
| *16 byte SRAM* |

Inside the blocks :

#### Pre charge circuit
![8 bit Pre Charge Circuit](https://github.com/RudranshKi/SRAM/assets/110120694/233d80fe-fe6e-4efb-8341-e024fb755695)
|:--:| 
| *8 bit Pre Charge Circuit* |


#### Row decoder
![Row Decoder](https://github.com/RudranshKi/SRAM/assets/110120694/9523594c-0703-4855-a086-54a1b1ffcfbd)
|:--:| 
| *Row Decoder* |

#### Transistor gate chain
![TG Chain](https://github.com/RudranshKi/SRAM/assets/110120694/f2e60df5-dd9a-4b7f-94e6-681a4fa01a18)
|:--:| 
| *8 bit write data (TG chain)* |

#### Write Driver
![Screenshot from 2023-10-10 10-52-38](https://github.com/RudranshKi/SRAM/assets/110120694/a2c0cd55-1cb1-4d1e-96fa-41b36ed81fa6)
|:--:| 
| *8 bit Write Driver* |

#### Sense amplifier
![Senseamp_8bit](https://github.com/RudranshKi/SRAM/assets/110120694/31d1e046-bba0-454d-b636-50f7e4da67ee)
|:--:| 
| *8 bit Sense amplifier* |


## <ins>Testbench</ins>

![Testbench](https://github.com/RudranshKi/SRAM/assets/110120694/2014c61b-b766-463b-b216-d391da3261b5)
|:--:| 
| *16byte SRAM testbench* |

![ADE_L setup](https://github.com/RudranshKi/SRAM/assets/110120694/fd3d11d2-10ce-4c11-9c0e-5fc23abdc37c)
|:--:| 
| *Test settings* |




## <ins>Delays in SRAM</ins>

![Read_delay_1to0](https://github.com/RudranshKi/SRAM/assets/110120694/a88c925f-cab3-4882-b0b7-9f82602f043c)
|:--:| 
| *Read (1->0) Delay* |

**Read (1 -> 0) Delay** - Delay between the falling edge of the output ( from 1.8V to 0V ) and *Ctrl* signal's falling edge. (note : *Lower the better*)
<br><br><br>
![Read_delay_0to1](https://github.com/RudranshKi/SRAM/assets/110120694/c2706e74-d35d-42a2-bc29-b3e17082e012)
|:--:| 
| *Read (0->1) Delay* |

**Read (0 -> 1) Delay** - Delay between the rising edge of the output ( from 0V to 1.8V ) and *Ctrl* signal's falling edge. (note : *Lower the better*)
<br><br><br>
![Write delay](https://github.com/RudranshKi/SRAM/assets/110120694/7229d809-d66f-4f43-be5e-f257cf660012)
|:--:| 
| *Write Delay (1->0)* |

**Write Delay (1->0)** - Delay between the write signal's rising edge and falling edge of the node voltage in SRAM cell. (note : *Lower the better*)
<br><br><br>

![Write_delay_0to1](https://github.com/RudranshKi/SRAM/assets/110120694/390f2774-1f4c-4548-a817-1d921d88853a)
|:--:| 
| *Write Delay (0->1)* |

**Write Delay (0->1)** - Delay between the write signal's rising edge and rising edge of the node voltage in SRAM cell. (note : *Lower the better*)
<br><br><br>
![Precharge delay](https://github.com/RudranshKi/SRAM/assets/110120694/d0b0f239-4fb0-4e2e-af2d-0d122b0f331a)
|:--:| 
| *Pre Charge Delay* |

**Pre Charge Delay** - Time it takes for BL and BLB caps to charge upto 5% of VDD once pre charge signal is low (0V in our case). (note : *Lower the better*)


## <ins>Operating Frequency Measurement</ins>

We tested SRAM memory array for different process and temperatures and below is the result.

<!--![Process Corner 14n before optimizations](https://github.com/RudranshKi/SRAM/assets/110120694/bd6a1ff2-3626-419a-8c78-8c4e253b237e) -->

![table](https://github.com/RudranshKi/SRAM/assets/110120694/6010969c-5424-4627-813f-c8755957a4e4)
|:--:| 
| *Process Corner test analysis for PC period 14ns PC ON time, 3ns PC OFF time* |


Note : Here , we can see for Read Delay (1 -> 0) , for WP and WS in 85&deg;C , we are getting positive delay (i.e - The output settles at 0V after ***Ctrl*** signal)


For finding out the maximum operating frequency for the SRAM:

1. **PreCharge signal ON time**

          Take the maximum delay from all the process corner (for both 1 -> 0 and 0 -> 1) and increase the Precharge ON time by that (amount+20%)

2. **PreCharge signal OFF time**

          Take maximum delay of Precharge delay from all the process corner and increase the PreCharge OFF time by that (amount+20%)


For finding out the maximum frequency in which we can write in the SRAM:

- **Write Signal ON time**

          Take maximum delay of Write delay from all the process corner and set the Write signal ON time by that (amount+20%)
  



In our case the final Precharge signal ON time is around 12 nm, so when we set the PC period to 18 ns , the Read delay falls well within margins (i.e - all of them are in negative so in every process corner the values settle before ***Ctrl*** turns off). And pre charge delay is also within in the PC OFF time.


<!-- ![Process corner 17n after optimizations](https://github.com/RudranshKi/SRAM/assets/110120694/1fbf564e-bb4c-4a1e-bd7a-359f72a9e355) -->
![table](https://github.com/RudranshKi/SRAM/assets/110120694/e5c09e0b-e0c5-4353-b3e4-f18805165a0d)
|:--:| 
| *Process Corner test analysis for PC period 17ns PC ON time, 1ns PC OFF time* |

**So, for a safer side we will use PC period 19 ns.**

![table](https://github.com/RudranshKi/SRAM/assets/110120694/2094eb5e-3a20-4b60-9598-6fddc4b6a306)
|:--:| 
| *Process Corner test analysis for PC period 18ns PC ON time, 1ns PC OFF time* |

<br><br>
<details>
<summary>Python code to tabulate process coner data</summary>
    
                import csv
                import numpy as np
                from tabulate import tabulate
                import matplotlib.pyplot as plt
                filename = "14_3n.csv"
                
                corner = [[] for _ in range(7)]
                pcorner = [[] for _ in range(7)]
                rows_skip = [0,1,2,3,4,5,7,9,10,11,12,13,14,15,16]
                
                corner[0], corner[1], corner[2], corner[3], corner[4], corner[5],corner[6] = np.loadtxt(filename, delimiter=",", usecols=(2,8, 9, 10, 11, 12, 13), skiprows=13, unpack=True, dtype=float)
                minimum,maximum = np.loadtxt(filename, delimiter=",", usecols=(6,7), skiprows=13, unpack=True, dtype=float)
                pcorner[0], pcorner[1], pcorner[2], pcorner[3], pcorner[4], pcorner[5],pcorner[6] = np.loadtxt(filename, delimiter=",", usecols=(2,8, 9, 10, 11, 12, 13), max_rows=9, unpack=True, dtype=str)
                
                for i in range(len(pcorner)) :
                    pcorner[i][0] = pcorner[i][5]
                    pcorner[i][1] = pcorner[i][8]
                
                for i in range(len(pcorner)):
                    pcorner[i] = pcorner[i][:2]
                
                def convertNanoSecond(seconds):
                    return seconds * 1e9
                
                for i in range(7):
                    corner[i] = [convertNanoSecond(value) for value in corner[i]]
                    
                
                for i in range(7):
                    minimum[i] = convertNanoSecond(minimum[i])
                for i in range(7):
                    maximum[i] = convertNanoSecond(maximum[i])
                
                
                read1to0  = [corner[0][0], corner[1][0], corner[2][0], corner[3][0], corner[4][0], corner[5][0],corner[6][0],minimum[0],maximum[0]]
                read0to1  = [corner[0][1], corner[1][1], corner[2][1], corner[3][1], corner[4][1], corner[5][1],corner[6][1],minimum[1],maximum[1]]
                precharge = [corner[0][2], corner[1][2], corner[2][2], corner[3][2], corner[4][2], corner[5][2],corner[6][2],minimum[2],maximum[2]]
                write1to0 = [corner[0][3], corner[1][3], corner[2][3], corner[3][3], corner[4][3], corner[5][3],corner[6][3],minimum[3],maximum[3]]
                write0to1 = [corner[0][4], corner[1][4], corner[2][4], corner[3][4], corner[4][4], corner[5][4],corner[6][4],minimum[4],maximum[4]]
                
                
                col_names = ["Delay Output"] + ["mos: " + pcorner[i][0] + ", \ntemp :"+pcorner[i][1]+" °C" for i in range(7)] + ["Minimum"] + ["Maximum"]
                
                data = [
                    ["read 0 from 1"]  + [f"{value:.3f} nS" for value in read1to0],
                    ["read 1 from 0"]  + [f"{value:.3f} nS" for value in read0to1],
                    ["Precharge"]      + [f"{value:.3f} nS" for value in precharge],
                    ["Write 0 from 1"] + [f"{value:.3f} nS" for value in write1to0],
                    ["Write 1 from 0"] + [f"{value:.3f} nS" for value in write0to1],
                    ]
                
                
                print(tabulate(data, headers=col_names, tablefmt="fancy_grid", showindex="always"))
                
                table = tabulate(data, headers=col_names, tablefmt="fancy_grid", showindex="always")
                
                plt.figure(figsize=(10, 7))
                plt.axis('off')  # Turn off axis
                
                # Render the table as text and add it to the plot
                plt.text(0.1, 0.1, table, fontsize=12, family='monospace')
                
                # Save the plot as an image (e.g., PNG)
                plt.savefig("table.png", bbox_inches='tight', pad_inches=0.1, dpi=300)







</details>

## <ins>Result</ins>

<ins>**SRAM Read/Write Operation output in nominal conditions**</ins>

![SRAM_Output](https://github.com/RudranshKi/SRAM/assets/110120694/1240962b-1b31-439f-ab4a-772c1ec6f5a5)
|:--:| 
| *SRAM RD/WR* |

<!-- ## <ins>Conclusion</ins> -->
