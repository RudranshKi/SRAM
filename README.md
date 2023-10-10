# Design and Development of 16-byte SRAM Circuit using 180nm CMOS Technology

SRAM (Static Random Access Memory) is a type of RAM which stores data indefinitely as long as there is power unlike DRAM which loses data gradually even if they are powered. They store 1 bit of data and is faster than DRAM and hence used as cache memory in processors. 

![SRAM block diagram(1)](https://github.com/RudranshKi/SRAM/assets/110120694/934f64a3-1412-482b-a371-fa4ef5b33ba5)
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
3.  [Specifications](https://github.com/RudranshKi/SRAM/blob/main/README.md#specifications)
4.  [Schematic Design](https://github.com/RudranshKi/SRAM/blob/main/README.md#design)
5.  [Testbench](https://github.com/RudranshKi/SRAM/blob/main/README.md#design-testbench)
6.  [Result analysis](https://github.com/RudranshKi/SRAM/blob/main/README.md#result-analysis) 
7.  [Conclusion](https://github.com/RudranshKi/SRAM/blob/main/README.md#conclusion)



## Introduction    

The design is based on 180nm MOS technology and 6T SRAM design.

## SRAM Circuit

### 6T SRAM cell


![6T sram(2)](https://github.com/RudranshKi/SRAM/assets/110120694/89d462e3-e65b-43b0-9cc5-0f13c9c6443c)
|:--:| 
| *Sense amp (6T)* |

1. 1 bit memory device
2. 2 inverters connected in feedback
3. As long as supply voltage is on, they retain the memory
4. To write we need to forcefully manipulate the node voltages of the inverter

#### Operation  

##### WRITE

Procedure for writing in 6T SRAM :

    1. When pre charge is on , the parasitic capacitors connected to BL and BLB are charged to 1.8V.
    2. The write signal latches the voltage sources to the BL and BLB.
    2. When the access transistors are turned on using Word Line , there is a connection between BL and the inverter input.
    3. The input sources for data we provide tried to forcefully alter the voltage in the SRAM inverter.
    4. For that we need to find the appropriate sizing for the transistors involved in the data writing process.


![Write path(2)](https://github.com/RudranshKi/SRAM/assets/110120694/f6b08e97-d7b7-4e97-850e-75970814fb40)
|:--:| 
| *WRITE PATH* |

Here we are trying to write 0V , so the node voltage is 0.3 so the inverter opposite to it has already swapped the values from 0 to 1.8V.

Here the transistor , m5 is in saturation and m3 in linear according to (VSD = VSG - VT) eqn

![image](https://github.com/RudranshKi/SRAM/assets/110120694/d8718afc-2366-4b78-9f21-068d5cc97075)

![image](https://github.com/RudranshKi/SRAM/assets/110120694/7f39fa18-774a-418a-8398-7ce769a47d6e)

![image](https://github.com/RudranshKi/SRAM/assets/110120694/b17b9bf7-50f9-450c-a889-c6ce71778855)


##### READ

Procedure for reading in 6T SRAM :

    1. When pre charge is on , the BL and BLB's caps are charged to 1.8V.
    2. When the access transistors are turned on using Word Line , there is a connection between BL and the inverter input.
    3. The parasitic capacitors connected to the BL and BLB start draining according to the node voltage of the SRAM's inverter nodes.
    4. The output is then extracted from BL and BLB.
    

![Read path](https://github.com/RudranshKi/SRAM/assets/110120694/1874556d-4508-41e1-ae91-1c91b54084a2)
|:--:| 
| *READ PATH* |


Here we are trying to read 0V stored in the SRAM. For that we need to  find the operating mode of the transistors involved in reading it.

Here transistor m3 is in saturation and m1 is in linear mode. Since both of them are connected in series, same current will flow through them.



To find the width for transistors in read operation :

![image](https://github.com/RudranshKi/SRAM/assets/110120694/df40acf1-1bf1-4c29-8f0e-fa8103d4bb09)

![image](https://github.com/RudranshKi/SRAM/assets/110120694/f31f0463-1ffa-462e-b5af-36467ad58579)

![image](https://github.com/RudranshKi/SRAM/assets/110120694/ddd826ea-1617-49f3-8710-7f31487aa6ef)


#### HOLD

When the access transistors are off the data is hold onto the inverters of SRAM as long sufficent supply voltage is present.

#### 6T-SRAM Design

![image](https://github.com/RudranshKi/SRAM/assets/110120694/818d5fa0-e721-4c7e-8397-6683c371c1ce)


### Pre Charge Circuit

![Precharge](https://github.com/RudranshKi/SRAM/assets/110120694/2aaccf0c-48a4-4f14-9a13-1747e99df3b6)
|:--:| 
| *Pre charge circuit* |

when the PC (pre charge) signal is high (1.8V), the PMOSes turn on , BL and BLB's caps charge to 1.8V or VDD. The sizes of PMOS transistors decide how fast BL and BLB can charge up. It is essential to maintain good performance to area ratio while sizing it.

Equalizer equalizes both the BL and BLB caps' voltage to same voltage.

### Write Driver

![Write driver](https://github.com/RudranshKi/SRAM/assets/110120694/9dcd9f7b-f113-4900-b639-bec40614e88c)
|:--:| 
| *Write driver using transmission gate* |

When WR signal is high (1.8V) , the TG acts as short circuit and conducts current allowing the user to write the data to BL or BLB on command.

TG is used instead of simple NMOS or PMOS because if we use only NMOS then the node voltage BL or BLB will only be able to write (VGS- VT) which is 1.13V instead of full 1.8V if the data is 1.8V and if we use PMOS then it will be only be abe to write (VSG - VT) which is 0.67 or VT if we write 0V to a node which has 1.8V stored in it. But using TG we can overcome that since both the mosfets will be turned on so for writing 1.8V the PMOS will be more suitable while writing 0V NMOS will be more suitable.

To drive the BL and BLB's parasitic capacitance, we need to chain drive it like domino effect. Where to drive higher cap we need a relatively smaller , and for driving that smaller cap we need even more relatively smaller cap. This generally follows roughly 2:1 ratio where a capacitor can drive another cap which is almost 2 times the size of it.

### Row decoder

To address 16 byte memory in our case , we need a 4x16 decoder. Which will take 4 bit address as input and then provide output signal to turn on access transistors in the SRAM coloumn array.

![Row decoder](https://github.com/RudranshKi/SRAM/assets/110120694/02dcecdf-c576-459d-adcf-de4c7454270d)
|:--:| 
| *4x16 Row Decoder* |



### Sense amplifier
<!---
![currentMirror](https://github.com/RudranshKi/SRAM/assets/110120694/7e8da5c7-4a1b-4a4b-8b7e-911817951e3c)
|:--:| 
| *Current mirror* |
--->

![Sense Amplifier](https://github.com/RudranshKi/SRAM/assets/110120694/82eb7710-86d3-4c27-9763-1824ddca2bf2)
|:--:| 
| *Sense Amplifier* |

The main goal of first current mirror circuit (m5 and m0) is to mirror the current from the current source , so the circuit try to draw the same amount of current from the VDD source as that of current source connected. And the goal of second current mirror circuit (m3 and m4) is to mirror the current in the both nodes for BL and BLB (since they will try to mirror the current source's output and that current is going to mirrored again by this current mirror so effectively the current will get halved).


When BL and BLB is is pre charged to 1.8V the node voltage at ***Vsense*** stabilizes at a certain node value after a certain time, in our case around 0.9V. (refer to [Sense amplifier sizing](https://github.com/RudranshKi/SRAM#differential-amplifier-sense-amp-tranistor-sizing) for details on this)

So, once the node value of BL or BLB changes the node voltage ***Vsense*** is going to reflect it immediately as the current mirror circuit above is going to try and mirror the same current in both nodes.

We then use two inverters back to back to amplify that difference , so for e.g- we are reading BL - 0V, BLB - 1.8V, so when we are reading those, BL (pre charged to 1.8V) is going to go down to 0V but the current mirror circuit is going to sense that voltage dip in BL node and will try maintain the same current in the other node by decreasing the ***Vsense*** node voltage down from 0.9V , and this will be immediately reflected upon the first inverter which would have been sized such that it's trip point is same 0.9V (i.e - When the input is 0.9V the output will also be 0.9V). Since the gain the first inverter is high, a slight amount of change in input is going to immediately send the output to one of the stable 1.8V or 0V (in this case 1.8V).

Then immediately the next inverter which also have a high gain will send the output to reflect the actual output (in our case 0V) immediately.


#### Sense amplifier Design

For sizing the current mirror we are first setting a transistor in saturation region by short circuting the gate node with source node so it always stays in the saturation region (saturation region because it has maximum current flow through it)(refer to fig (Sizing setup(only m6))). Then after setting a  **Gm / ID** ratio (between 10 - 4 with 4 being the best region), we set up a similar transistor in conjunction to it with same width and length so that same current flows through it since both of them have same resistance and same gate voltage.

(*We are using Gm/ID to maintain a good saturation region*)

![Current mirror(1)](https://github.com/RudranshKi/SRAM/assets/110120694/8678d862-4cc2-4aac-9bc0-5dc3c463c838)
|:--:| 
| *Sizing setup* |

Since ***Vnode*** is dawing the same current from the given current source we use another current mirror circuit to mirror the same current (we size them using the above procedure again but for PMOS) and connect BL an BLB transistors to it. The NMOSes (m1 and m2) connecting the PMOS (m3 and m4) current mirror circuit to NMOS (m5 and m0) current mirror circuit is then connected to BL and BLB through their gates. We size them to maintain a specific ***Vsense*** node voltage which will act as a middle trip point for output (Above that trip point - 1.8V, below that trip point - 0v).



Inverter 1 is sized so that it's trip point is around the same value as that of ***Vsense***. So once the value falls below or above that trip point , it will immediately amplify the output to a stable voltage level (either 1.8V or 0V). Inverter 2 is used to amplify ***Vsense*** node voltage further and complement it to get the original value.


## Specifications 

Supply Voltage : 1.8V

Maximum operation frequency : 65 MHz

Memory : 16 byte


## Design

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
| *8 bit write driver* |

#### Sense amplifier
![Senseamp_8bit](https://github.com/RudranshKi/SRAM/assets/110120694/31d1e046-bba0-454d-b636-50f7e4da67ee)
|:--:| 
| *8 bit Sense amplifier* |


## Testbench

![Testbench](https://github.com/RudranshKi/SRAM/assets/110120694/2014c61b-b766-463b-b216-d391da3261b5)
|:--:| 
| *16byte SRAM testbench* |

![ADE_L_setup](https://github.com/RudranshKi/SRAM/assets/110120694/28315818-ae21-4090-b03b-511e78e9ca01)
|:--:| 
| *Test settings* |


## Result analysis
We tested SRAM memory array for different process and temperatures and below is the result.
![Process corner](https://github.com/RudranshKi/SRAM/assets/110120694/73fc6827-fdd4-4e44-8dff-16a58ecff644)
|:--:| 
| *Process Corner test analysis* |



**Read Delay** - Delay between the output and *Ctrl* signal's falling edge. (note : *Higher the better*)

**Write Delay** - Delay between the write signal's rising edge and falling or rising edge of the node voltage in SRAM cell. (note : *Lower the better*)

**Pre Charge Delay** - Time it takes for BL and BLB caps to charge upto 5% of VDD once pre charge signal is low (0V in our case). (note : *Lower the better*)

*NOTE: Nominal conditions : temperature - 27&deg;C , mos.scs - tm*


![SRAM_working](https://github.com/RudranshKi/SRAM/assets/110120694/7b432393-22c6-406b-ade3-d529450b03a3)
|:--:| 
| *SRAM RD/WR* |

## Conclusion
