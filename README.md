# CAPL
convert CAN message of Benz E200L to J1939 Format
## example
![1 step](./Image/1.png)<br />
![2 step](./Image/2.png)<br />
![3 step](./Image/3.png)<br />
![4 step](./Image/4.png)<br />
```C
/*@!Encoding:1252*/
/*
convert CAN foramt message (Benz E200L) to CAN extended foramt message (J1939)
*/
includes
{
  
}

variables
{
  message CAN2.*m;
}

void attribut_msg(message*m,int chn, int dlc, dword ID)
{
  m.can = chn; // assign channel 
  m.dlc = dlc; // assign dlc
  m.id = mkExtId(ID); // assign message ID
}

on message CAN1.* // Called when a message received on CAN1
{
  if(this.DIR==RX)        // if it is a received frame
  {
   if(this.CAN==1 & this.dlc!=0) // block message from channel 2 and filter message based on dlc
   {
    /*************************************************
    Convert Tachospeed singal of Benz E200L to J1939
    **************************************************/
    if(this.ID==0x98) 
    {
      attribut_msg(m,2,this.dlc, 0xCFE6C17);
      m.byte(6) = ((0x0F & this.byte(4)) << 4)|((0xF0 & this.byte(3)) >> 4); // ((00001111 & this.byte(4)) shift to left 4 bits)|((11110000 & this.byte(3)) shift to right 4 bits)
      m.byte(7) = (0xF0 & this.byte(4)) >> 4;                                // (11110000 & this.byte(4)) shift to right 4 bits
      output(m);      // send it to the other channel
    }
    /********************End*************************/
    
    
   }
  }
}

```
