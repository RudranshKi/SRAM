
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
![6T SRAM](https://github.com/RudranshKi/SRAM/assets/110120694/3c2ee50d-2515-4fea-b71e-34c5978dce62)
|:--:| 
| *Sense amp (6T)* |

1. 1 bit memory device
2. 2 inverters connected in feedback
3. As long as supply voltage is on, they retain the memory
4. To write we need to forcefully manipulate the node voltages of the inverter

   

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
