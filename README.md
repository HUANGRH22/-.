#include "my_include.h"


char dis0[32];
char dis1[128];

u8 bzFlag  = 0;
u8 soundFlag  = 0;
u8 doorFlag  = 0
u8 bjFlag  = 0;
u8 sfFlag  = 

u16 soundDelay =0

void OnGetUartMessage(const _uart_msg_obj *uartMsgRec)
{
    char *strPtr;
    if((strPtr=strstr(uartMsgRec->payload,"RTC"))!=NULL)
    {
        My_RTC_SetStr(strPtr+3);
    }
    if((strPtr=strstr(uartMsgRec->payload,"MD"))!=NULL)
    {
        setNum=ParseInteger(strPtr+2,2);  
    }
}   

void scanKeyAnddealKey(void);

int main(void)
{
    USARTx_Init(USART1,9600);
    USARTx_Init(USART2,9600);
    
    My_KEY_Init();
    My_LED_Init();
    
     while(1)
    {
        scanKeyAnddealKey();
        if(myReadFlag_tick == true )
        {
            myReadFlag_tick = false;
                        
            if(soundFlag == 1){soundDelay = 100 ;} 
            else
            {
               if(soundDelay>0) soundDelay--;
            }                
            
            if(bjFlag == 0)
            {                    
                 if(sfFlag == 1) 
                {
                    if(soundDelay> 0 || doorFlag == 1 || bzFlag == 1) 
                    {
                        buzzer =1 ;
                    }    
                    else{buzzer =0;}
                }
                else
                {
                    buzzer =0 ;
                }                   
            }
            else
            {buzzer = 1;}


        }

        if(mySendFlag_tick == true )
        {
            mySendFlag_tick = false;
           sprintf((char*)dis1,"N01:%.2fN\r\n",(float)100000/1000*9.8);              
            sprintf(dis1,"%sTemp:%4.1f'C  ",dis1,12.5);           
            USARTSendString(USART1 , dis1);
            
        }
        My_UartMessage_Process();
        
    }
}


void scanKeyAnddealKey(void)
{
    My_KeyScan();
    
    if(KeyIsPress(key))
    {      
        if(bjFlag == 0)
        {
                bjFlag = 1;
        }
        else {    bjFlag = 0;}            
    }

    if(KeyIsPressed(doorCheck)) 
    {      
        doorFlag = 0;        
    }
    else
    {
        doorFlag = 1;
    }

    if(KeyIsPressed(sound))
    {      
        soundFlag = 1;
    }
    else
    {
        soundFlag = 0;        
    }

    if(KeyIsPressed(bizhang))
    {      
        bzFlag = 1;
    }
    else
    {
        bzFlag = 0;        
    }        

    if(KeyIsPressed(sw)) 
    {      
        sfFlag = 0;
    }
    else
    {
        sfFlag = 1;
    }            
}
