# UltraSonic
This is a driver for the HC-SR04 Ultrasonic Sensor in STM32L476. 

The timer that was used was the Timer 2 to generate 1 microsecond delay.
TrigPulse() function is also create to create the initial trigger for the Ultrasonic Sensor to start the Ultrasonic burst for sensing. 

Given below is the while loop which is to be written in the main.c
while (1) 
 {  
 
    Trig_Pulse();
    
    uint32_t timeout = TIM2->CNT;
    
    while((GPIOB->IDR & (1 << Echo_Pin)) == 0)
    {
        if((TIM2->CNT - timeout) > 500000) //50 ms Timeout 
          break;
    }
    
    uint32_t star = TIM2->CNT; //Start counting
    while((GPIOB->IDR & (1 << Echo_Pin)) != 0);
    
    uint32_t end = TIM2->CNT; //End counting
    uint32_t time = (end >= star) ? (end - star) : (0xFFFF - star + end); //Total Time from start to end
    
    unsigned long distance = time/58; 
    
    char distance_string[10];
    sprintf(distance_string, "%lu cm", distance);  //Convert unsigned long to char array for printing.
    
    star = TIM2->CNT;
    while((TIM2->CNT - star) < 500000); // Delay till the next cycle
 } 
