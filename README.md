# 6T SRAM based on 180 nm technology

SRAM (Static Random Access Memory) is a type of RAM which stores data indefinitely as long as there is power unlike DRAM which loses data gradually even if they are powered. They store 1 bit of data and is faster than DRAM and hence used as cache memory in processors. 

![SRAM block diagram](https://github.com/RudranshKi/SRAM/assets/110120694/7dc3362b-c70c-4604-b0a6-7eccbf39492b)
|:--:| 
| *SRAM Block diagram* |

## Table of contents        

1.  [About](https://github.com/RudranshKi/SRAM/blob/main/README.md#about)
2.  [Cells used](https://github.com/RudranshKi/SRAM/blob/main/README.md#Cells-Used)
    1. [6T SRAM cell](https://github.com/RudranshKi/SRAM#6t-sram-cell)
        - [Operations](https://github.com/RudranshKi/SRAM#operation)
           1. [Write](https://github.com/RudranshKi/SRAM#write)
           2. [Read](https://github.com/RudranshKi/SRAM#read)
           3. [Hold](https://github.com/RudranshKi/SRAM#hold)
           4. [Transistor sizing](https://github.com/RudranshKi/SRAM#Transistor-sizing)
    2. [Pre charge circuit](https://github.com/RudranshKi/SRAM#pre-charge-circuit)
    3. [Write driver](https://github.com/RudranshKi/SRAM#write-driver)
    4. [Row decoder](https://github.com/RudranshKi/SRAM/tree/main#row-decoder)
    5. [Sense Amplifier](https://github.com/RudranshKi/SRAM#sense-amplifier)
3.  [Specifications](https://github.com/RudranshKi/SRAM/blob/main/README.md#specifications)
4.  [Design](https://github.com/RudranshKi/SRAM/blob/main/README.md#design)
5.  [Design testbench](https://github.com/RudranshKi/SRAM/blob/main/README.md#design-testbench)
6.  [Result analysis](https://github.com/RudranshKi/SRAM/blob/main/README.md#result-analysis) 
7.  [Conclusion](https://github.com/RudranshKi/SRAM/blob/main/README.md#conclusion)



## About    

The design is based on 180nm MOS technology and 6T SRAM design.

## Cells Used

### 6T SRAM cell

![SRAM 6T](https://github.com/RudranshKi/SRAM/assets/110120694/b99eba4a-1d20-49a1-926a-c6cf94a31b16)
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


![Write path(1)](https://github.com/RudranshKi/SRAM/assets/110120694/d9ca614f-ad84-4c31-abba-bcc33cb56e0d)
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
    

![read_path](https://github.com/RudranshKi/SRAM/assets/110120694/8093b07b-0e1d-41c2-a90e-96a548188ebb)
|:--:| 
| *READ PATH* |


Here we are trying to read 0V stored in the SRAM. For that we need to  find the operating mode of the transistors involved in reading it.

Here transistor m3 is in saturation and m1 is in linear mode. Since both of them are connected in series, same current will flow through them.



To find the width for transistors in read operation :

![image](https://github.com/RudranshKi/SRAM/assets/110120694/df40acf1-1bf1-4c29-8f0e-fa8103d4bb09)

![image](https://github.com/RudranshKi/SRAM/assets/110120694/f31f0463-1ffa-462e-b5af-36467ad58579)

![image](https://github.com/RudranshKi/SRAM/assets/110120694/ddd826ea-1617-49f3-8710-7f31487aa6ef)


#### HOLD

When the access transistors are off the data is hold onto the inverters of SRAM

#### Transistor sizing

![image](https://github.com/RudranshKi/SRAM/assets/110120694/0aa21064-c1f5-4c9c-852f-35a7b8975233)


### Pre Charge Circuit

![Precharge](https://github.com/RudranshKi/SRAM/assets/110120694/c6428b27-2a1f-46dc-bcf9-e6151838a6ae)
|:--:| 
| *Pre charge circuit* |

when the PC (pre charge) signal is high (1.8V), the PMOSes turn on , BL and BLB's caps charge to 1.8V or VDD.

Equalizer equalizes both the BL and BLB caps' voltage to same voltage.

### Write Driver

![WRITE_driver](https://github.com/RudranshKi/SRAM/assets/110120694/98c8b3e4-1c47-4b5b-99e0-87076ca4770a)
|:--:| 
| *Write driver using transmission gate* |

When WR signal is high (1.8V) , the TG acts as short circuit and conducts current allowing the user to write the data to BL or BLB on command.

TG is used instead of simple NMOS or PMOS because if we use only NMOS then the node voltage BL or BLB will only be able to write (VGS- VT) which is 1.13V instead of full 1.8V if the data is 1.8V and if we use PMOS then it will be only be abe to write (VSG - VT) which is 0.67 or VT if we write 0V to a node which has 1.8V stored in it. But using TG we can overcome that since both the mosfets will be turned on so for writing 1.8V the PMOS will be more suitable while writing 0V NMOS will be more suitable.

### Row decoder

To address 16 byte memory in our case , we need a 4x16 decoder.

![sample 2x4 decoder](https://github.com/RudranshKi/SRAM/assets/110120694/ece61c03-fc1b-491e-9bcf-2a244ef0916c)
|:--:| 
| *Sample 2x4 decoder* |

**Note : we aren't using 2x4 decoder in our case since we are going to address 16 bytes so 16 coloumns of memory registers, so we will be using a [4x16 deocder](https://github.com/RudranshKi/SRAM/blob/main/README.md#row-decoder-1)**

### Sense amplifier

![currentMirror](https://github.com/RudranshKi/SRAM/assets/110120694/7e8da5c7-4a1b-4a4b-8b7e-911817951e3c)
|:--:| 
| *Current mirror* |

![Differential amplifier(1)](https://github.com/RudranshKi/SRAM/assets/110120694/56327d17-23a3-4339-b994-70ad850145dd)
|:--:| 
| *Sense Amplifier* |

The main goal of first current mirror circuit (marked with green) is to mirror the current from the current source , so the circuit try to draw the same amount of current from the VDD source as that of current source connected. And the goal of second current mirror circuit (marked with blue) is to mirror the current in the both nodes for BL and BLB (since they will try to mirror the current source's output and that current is going to mirrored again by this current mirror so effectively the current will get halved).


When BL and BLB is is pre charged to 1.8V the node voltage at Vsense stabilizes at a certain node value after a certain time, in our case around 0.9V. (refer to current mirror sizing for details on this)

So, once the node value of BL or BLB changes the node voltage Vsense is going to reflect it immediately as the current mirror circuit above is going to try and mirror the same current in both nodes.

We then use two inverters back to back to amplify that difference , so for e.g- we are reading BL - 0V, BLB - 1.8V, so when we are reading those, BL (pre charged to 1.8V) is going to go down to 0V but the current mirror circuit is going to sense that voltage dip in BL node and will try maintain the same current in the other node by decreasing the Vsense node voltage down from 0.9V , and this will be immediately reflected upon the first inverter which would have been sized such that it's trigger point is same 0.9V (i.e - When the input is 0.9V the output will also be 0.9V). Since the gain the first inverter is high, a slight amount of change in input is going to immediately send the output to one of the stable 1.8V or 0V (in this case 1.8V).

Then immediately the next inverter which also have a high gain will send the output to reflect the actual output (in our case 0V) immediately.


#### Current Mirror Sizing

For sizing the current mirror we are first setting a transistor in saturation region by short circuting the gate node with source node so it always stays in the saturation region (saturation region because it has maximum current flow through it)(refer to fig ([transistor sizing setup]())). Then after setting a  **Gm / ID** ratio, we set up a similar transistor in conjunction to it with same width and length so that same current flows through it since both of them have same resistance and same gate voltage.

(*We are using Gm/ID to maintain a good saturation region*)


Since ***Vnode*** is dawing the same current from the given current source we use another current mirror circuit to mirror the same current (we size them using the above procedure again but for pmos) and connect BL an BLB transistors to it. The NMOSes connecting the PMOS current mirror circuit to NMOS current mirror circuit is then connected to BL and BLB through their gates. We size them to maintain a specific ***Vsense*** node voltage which will act as a middle trip point for output (Above that output - 1.8V, below that output - 0v).



Inverter 1 is sized so that it's trip point is around the same value as that of ***Vsense***. 


## Specifications 

Supply Voltage : 1.8V

Maximum operation frequency : 65 MHz

Memory : 16 byte

Current supply : 8 uA



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

#### Write Driver
![Write_driver_8bit](https://github.com/RudranshKi/SRAM/assets/110120694/f2e60df5-dd9a-4b7f-94e6-681a4fa01a18)
|:--:| 
| *8 bit write driver* |

#### Sense amplifier
![Senseamp_8bit](https://github.com/RudranshKi/SRAM/assets/110120694/31d1e046-bba0-454d-b636-50f7e4da67ee)
|:--:| 
| *8 bit Sense amplifier* |


## Design testbench

![Testbench](https://github.com/RudranshKi/SRAM/assets/110120694/2014c61b-b766-463b-b216-d391da3261b5)
|:--:| 
| *16byte SRAM testbench* |

![ADE_L_setup](https://github.com/RudranshKi/SRAM/assets/110120694/28315818-ae21-4090-b03b-511e78e9ca01)
|:--:| 
| *Test settings* |


## Result analysis

![Process corner](https://github.com/RudranshKi/SRAM/assets/110120694/73fc6827-fdd4-4e44-8dff-16a58ecff644)
|:--:| 
| *Process Corner test analysis* |

![SRAM_working](https://github.com/RudranshKi/SRAM/assets/110120694/7b432393-22c6-406b-ade3-d529450b03a3)
|:--:| 
| *SRAM RD/WR* |

## Conclusion
