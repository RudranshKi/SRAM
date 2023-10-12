# Design and Development of 16-byte SRAM Circuit using 180nm CMOS Technology

SRAM (Static Random Access Memory) is a type of RAM which stores data indefinitely as long as there is power unlike DRAM (Dynamic RAM) which loses data gradually even if they are powered. They store 1 bit of data and is faster than DRAM as they don't need to be recharged before read operation to get the correct output and hence used as cache memory in processors. But while maintaining speed SRAM have to compromise with area.

![BlockDiagram(1)](https://github.com/RudranshKi/SRAM/assets/110120694/83de02e1-7abe-420a-bdc5-cfb3cacf5f82)
|:--:| 
| *SRAM Block diagram* |

## Table of contents        

1.  [Introduction](https://github.com/RudranshKi/SRAM/blob/main/README.md#about)
2.  [SRAM Circuit](https://github.com/RudranshKi/SRAM/blob/main/README.md#Cells-Used)
    1. [6T SRAM cell](https://github.com/RudranshKi/SRAM#6t-sram-cell)
        - [Operations](https://github.com/RudranshKi/SRAM#operation)
           1. [Write](https://github.com/RudranshKi/SRAM#write)
           2. [Read](https://github.com/RudranshKi/SRAM#read)
           3. [Hold](https://github.com/RudranshKi/SRAM#hold)
        - [6T-SRAM Design](https://github.com/RudranshKi/SRAM#Transistor-sizing)
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

Here we are trying to write 0V , so the node voltage is 0.3 and the inverter opposite to it has already swapped the values from 0 to 1.8V.

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


### <ins>Pre Charge Circuit</ins>

![Precharge](https://github.com/RudranshKi/SRAM/assets/110120694/b2791dd2-160d-482f-bd65-570977d68ba4)
|:--:| 
| *Pre charge circuit* |

when the **PC (Pre charge)** signal is high (1.8V), the PMOSes turn on, BL and BLB's caps charge to 1.8V or VDD. The sizes of PMOS transistors decide how fast BL and BLB can charge up. It is essential to maintain good performance to area ratio while sizing it.

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

![Sense Amplifier](https://github.com/RudranshKi/SRAM/assets/110120694/82eb7710-86d3-4c27-9763-1824ddca2bf2)
|:--:| 
| *Sense Amplifier* |

The main goal of Sense Amplifier is to sense the output during the transition and amplify the result saving the transition time and increasing the speed of operation. 

Here, the goal of first current mirror circuit (m5 and m0) is to mirror the current from the current source , so the circuit try to draw the same amount of current from the VDD source as that of current source connected. And the goal of second current mirror circuit (m3 and m4) is to mirror the current in the both nodes for BL and BLB (since they will try to mirror the current source's output and that current is going to mirrored again by this current mirror so effectively the current will get halved).


When BL and BLB is is pre charged to 1.8V the node voltage at ***Vsense*** stabilizes at a certain node value after a certain time, in our case around 0.9V. (refer to [Sense Amplifier sizing](https://github.com/RudranshKi/SRAM#differential-amplifier-sense-amp-tranistor-sizing) for details on this)

So, once the node value of BL or BLB changes the node voltage ***Vsense*** is going to reflect it immediately as the current mirror circuit above is going to try and mirror the same current in both nodes. If BL goes lower than BLB, the node voltage ***Vsense*** will go lower than 0.9V and in vice versa it will go higher than 0.9V.

We then use two inverters back to back to amplify that difference. 


For e.g- we are reading BL - 0V, BLB - 1.8V, so when we are reading those, BL (pre charged to 1.8V) is going to go down to 0V so the channel current will decrease. Consequently the current flow in transistor (m3) will also decrease. Since Path 1 current flow decreases , the current flow from Path 2 has to increase since they are in parallel and according to **KCL**, current through incoming nodes should be equal to outgoing node. But transistor (m4) with it's gate shorted with transistor (m3) 's gate node acts as current mirror , it will try to mirror the same current as of Path 1. So consequently , transistor (m2) has to discharge the Inv 1's input node to satisfy the current need which in turn will decrease the ***Vsense*** node voltage (Inv 1's parasitic cap which would have been charged to 0.9V will discharge through transistor (m2)). This will be immediately reflected upon the first inverter which would have been sized such that it's trip point is same 0.9V (i.e - When the input is 0.9V the output will also be 0.9V). Since the gain the first inverter is high, a slight amount of change in input is going to immediately send the output to one of the stable 1.8V or 0V (in this case 1.8V).



#### <ins>Sense amplifier Design</ins>

For sizing the current mirror we are first setting a transistor in saturation region by short circuting the gate node with source node so it always stays in the saturation region (saturation region because it has maximum current flow through it)(refer to fig (Sizing setup(only m6))). Then after setting a  **Gm / ID** ratio (between 10 - 4 with 4 being the best region), we set up a similar transistor in conjunction to it with same width and length so that same current flows through it since both of them have same resistance and same gate voltage.

(*We are using Gm/ID to maintain a good saturation region*)

![Current mirror(1)](https://github.com/RudranshKi/SRAM/assets/110120694/8678d862-4cc2-4aac-9bc0-5dc3c463c838)
|:--:| 
| *Sizing setup* |

Since ***Vnode*** is drawing the same current from the given current source we use another current mirror circuit to mirror the same current (we size them using the above procedure again but for PMOS) and connect BL an BLB transistors to it. The NMOSes (m1 and m2) connecting the PMOS (m3 and m4) current mirror circuit to NMOS (m5 and m0) current mirror circuit is then connected to BL and BLB through their gates. We size them to maintain a specific ***Vsense*** node voltage which will act as a middle trip point for output (Above that trip point - 1.8V, below that trip point - 0v).



Inverter 1 is sized so that it's trip point is around the same value as that of ***Vsense***. So once the value falls below or above that trip point , it will immediately amplify the output to a stable voltage level (either 1.8V or 0V). Inverter 2 is used to amplify ***Vsense*** node voltage further and complement it to get the original value.

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


## <ins>Specifications</ins> 

Supply Voltage : 1.8V

Maximum operation frequency : 42 MHz

Memory : 16 byte


## <ins>Design</ins>

#### SRAM
![16 byte SRAM](https://github.com/RudranshKi/SRAM/assets/110120694/ae33b882-690b-4494-ae0e-6dcd1ddd0309)
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

![Read_delay_0to1](https://github.com/RudranshKi/SRAM/assets/110120694/c2706e74-d35d-42a2-bc29-b3e17082e012)
|:--:| 
| *Read (0->1) Delay* |

**Read (0 -> 1) Delay** - Delay between the rising edge of the output ( from 0V to 1.8V ) and *Ctrl* signal's falling edge. (note : *Lower the better*)

![Write delay](https://github.com/RudranshKi/SRAM/assets/110120694/7229d809-d66f-4f43-be5e-f257cf660012)
|:--:| 
| *Write Delay* |

**Write Delay** - Delay between the write signal's rising edge and falling or rising edge of the node voltage in SRAM cell. (note : *Lower the better*)

![Precharge delay](https://github.com/RudranshKi/SRAM/assets/110120694/d0b0f239-4fb0-4e2e-af2d-0d122b0f331a)
|:--:| 
| *Pre Charge Delay* |

**Pre Charge Delay** - Time it takes for BL and BLB caps to charge upto 5% of VDD once pre charge signal is low (0V in our case). (note : *Lower the better*)



## <ins>Operating Frequency Measurement</ins>

We tested SRAM memory array for different process and temperatures and below is the result.

![Process_corner_tp_11n](https://github.com/RudranshKi/SRAM/assets/110120694/cce5c8f1-6a87-466e-b2c9-06789c5444c7)
|:--:| 
| *Process Corner test analysis for PC period 22 ns* |


Note : Here , we can see for Read Delay (1 -> 0) , for WP and WS in 85&deg;C , we are getting positive delay (i.e - The output settles at 0V after ***Ctrl*** signal)


For finding out the maximum operating frequency for the SRAM:

1. **PreCharge signal ON time**

          Take the maximum delay from all the process corner (for both 1 -> 0 and 0 -> 1) and increase the Precharge ON time by that (amount+20%)

2. **PreCharge signal OFF time**

          Take maximum delay of Precharge delay from all the process corner and increase the PreCharge OFF time by that (amount+20%)


For finding out the maximum frequency in which we can write in the SRAM:

- **Write Signal ON time**

          Take maximum delay of Write delay from all the process corner and set the Write signal ON time by that (amount+20%)
  



In our case the final Precharge signal ON time is around 12 nm, so when we set the PC period to 24 nm , the Read delay falls well within margins (i.e - all of them are in negative so in every process corner the values settle before ***Ctrl*** turns off). And pre charge delay is also within in the PC OFF time.

![Process_corner_tp_12n](https://github.com/RudranshKi/SRAM/assets/110120694/04774545-30f1-4779-b71b-c24a28c879a4)
|:--:| 
| *Process Corner test analysis for PC period 24 ns* |

## <ins>Result</ins>

<ins>**SRAM Read/Write Operation output in nominal conditions**</ins>

![image](https://github.com/RudranshKi/SRAM/assets/110120694/980f3d51-9a6e-4792-add6-c0e01409dce8)
|:--:| 
| *SRAM RD/WR* |

% ## <ins>Conclusion</ins>
