
# 6T SRAM based on 180 nm technology

SRAM (Static Random Access Memory) is a type of RAM which stores data indefinitely as long as there is power unlike DRAM which loses data gradually even if they are powered. They store 1 bit of data and is faster than DRAM and hence used as cache meomry in processors. 


## Table of contents        

1.  [About](https://github.com/RudranshKi/SRAM/blob/main/README.md#about)
2.  [Specifications](https://github.com/RudranshKi/SRAM/blob/main/README.md#specifications)
3.  [Design](https://github.com/RudranshKi/SRAM/blob/main/README.md#design)
4.  [Design testbench](https://github.com/RudranshKi/SRAM/blob/main/README.md#design-testbench)
5.  [Result analysis](https://github.com/RudranshKi/SRAM/blob/main/README.md#result-analysis) 
6.  [Conclusion](https://github.com/RudranshKi/SRAM/blob/main/README.md#conclusion)



## About    

The design is based on 180nm MOS technology and 6T SRAM design.

## 6T SRAM cell
![6T SRAM](https://github.com/RudranshKi/SRAM/assets/110120694/9f6e0d71-e67f-4fbc-b913-a254151d34c1)
|:--:| 
| *Sense amp (6T)* |

1. 1 bit memory device
2. 2 inverters connected in feedback
3. As long as supply voltage is on, they retain the memory
4. To write we need to forcefully manipulate the node voltages of the inverter

### Operation  

#### WRITE

Procedure for writing in 6T SRAM :

    1. When precharge is on , the parasitic capacitors connected to BL and BLB's caps are charged to 1.8V.
    2. The write signal latches the voltage sources to the BL and BLB.
    2. When the access transistors are turned on using Word Line , there is a connection between BL and the inverter input.
    3. The input sources for data we provide tried to forcefully alter the voltage in the SRAM inverter.
    4. For that we need to find the appropriate sizing for the transistors involved in the data writing process.

![Write_path](https://github.com/RudranshKi/SRAM/assets/110120694/ef87047e-10e0-4080-b72d-4055f16712a4)
|:--:| 
| *WRITE PATH* |

#### READ


Procedure for reading in 6T SRAM :

    1. When precharge is on , the BL and BLB's caps are charged to 1.8V.
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


## Specifications 

Supply Voltage : 1.8V

Maximum operation frequency : 50 MHz

Memory : 16 byte

Current supply : 8 uA




## Design


![16 byte SRAM](https://github.com/RudranshKi/SRAM/assets/110120694/ae33b882-690b-4494-ae0e-6dcd1ddd0309)
|:--:| 
| *16 byte SRAM* |

![8 bit Pre Charge Circuit](https://github.com/RudranshKi/SRAM/assets/110120694/233d80fe-fe6e-4efb-8341-e024fb755695)
|:--:| 
| *8 bit Pre Charge Circuit* |

![Row Decoder](https://github.com/RudranshKi/SRAM/assets/110120694/9523594c-0703-4855-a086-54a1b1ffcfbd)
|:--:| 
| *Row Decoder* |

## Design testbench
## Result analysis
## Conclusion
