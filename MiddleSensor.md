# Senior-Design
import sys
import smbus
import time
import numpy as np
import RPi.GPIO as GPIO
import pygame

in1 = 24
in2 = 25
in3 = 8
in4 = 7

 
step_count = 512
direction = True # True for clockwise, False for counter-clockwise
 
# defining stepper motor sequence (found in documentation http://www.4tronix.co.uk/arduino/Stepper-Motors.php)
step_sequence = [[1,0,0,1],
                 [1,0,0,0],
                 [1,1,0,0],
                 [0,1,0,0],
                 [0,1,1,0],
                 [0,0,1,0],
                 [0,0,1,1],
                 [0,0,0,1]]
 
# setting up
GPIO.setmode( GPIO.BCM )
GPIO.setup( in1, GPIO.OUT )
GPIO.setup( in2, GPIO.OUT )
GPIO.setup( in3, GPIO.OUT )
GPIO.setup( in4, GPIO.OUT )
 
# initializing
GPIO.output( in1, GPIO.LOW )
GPIO.output( in2, GPIO.LOW )
GPIO.output( in3, GPIO.LOW )
GPIO.output( in4, GPIO.LOW )
 
 
motor_pins = [in1,in2,in3,in4]
motor_step_counter = 0 ;
 
 
def cleanup():
    GPIO.output( in1, GPIO.LOW )
    GPIO.output( in2, GPIO.LOW )
    GPIO.output( in3, GPIO.LOW )
    GPIO.output( in4, GPIO.LOW )
    GPIO.cleanup()


bus = smbus.SMBus(1)
LIDAR_ADR=0x62

def measure():
   bus.write_byte_data(LIDAR_ADR,0,4)

   bus.write_byte(LIDAR_ADR, 1)
   while bus.read_byte(LIDAR_ADR) & 1 != 0:
       pass
   bus.write_byte(LIDAR_ADR, 0xf)
   d=bus.read_byte(LIDAR_ADR)<<8
   bus.write_byte(LIDAR_ADR, 0x10)
   d|=bus.read_byte(LIDAR_ADR)
   
   return d


while True:
    try:
        if direction==True:
            direction = False
        else:
            direction=True
        i = 0
        for i in range(step_count):
            for pin in range(0, len(motor_pins)):
                GPIO.output( motor_pins[pin], step_sequence[motor_step_counter][pin] )
            if direction==True:
                motor_step_counter = (motor_step_counter + 1) % 8
                t0=time.time()
                d=measure()
                t1=time.time()
                print(d)

				
                if (195<d<205 & 251< i <261):
                    pygame.mixer.init()
                    pygame.mixer.music.load("straight-2.wav")
                    pygame.mixer.music.play()
                if (195<d<205 & i < 251):
                    pygame.mixer.init()
                    pygame.mixer.music.load("left-2.wav")
                    pygame.mixer.music.play()
                if (195<d<205 & i > 261):
                    pygame.mixer.init()
                    pygame.mixer.music.load("right-2.wav")
                    pygame.mixer.music.play()
					
				if (495<d<505 & 251< i <261):
                    pygame.mixer.init()
                    pygame.mixer.music.load("straight-5.wav")
                    pygame.mixer.music.play()
                if (495<d<505 & i < 251):
                    pygame.mixer.init()
                    pygame.mixer.music.load("left-5.wav")
                    pygame.mixer.music.play()
                if (495<d<505 & i > 261):
                    pygame.mixer.init()
                    pygame.mixer.music.load("right-5.wav")
                    pygame.mixer.music.play()

            elif direction==False:
                motor_step_counter = (motor_step_counter - 1) % 8
                t0=time.time()
                d=measure()
                t1=time.time()
                print(d)
				
                if (195<d<205 & 251< i <261):
                    pygame.mixer.init()
                    pygame.mixer.music.load("straight-2.wav")
                    pygame.mixer.music.play()
                if (195<d<205 & i < 251):
                    pygame.mixer.init()
                    pygame.mixer.music.load("right-2.wav")
                    pygame.mixer.music.play()
                if (195<d<205 & i > 261):
                    pygame.mixer.init()
                    pygame.mixer.music.load("left-2.wav")
                    pygame.mixer.music.play()
					
				if (495<d<505 & 251< i <261):
                    pygame.mixer.init()
                    pygame.mixer.music.load("straight-5.wav")
                    pygame.mixer.music.play()
                if (495<d<505 & i < 251):
                    pygame.mixer.init()
                    pygame.mixer.music.load("right-5.wav")
                    pygame.mixer.music.play()
                if (495<d<505 & i > 261):
                    pygame.mixer.init()
                    pygame.mixer.music.load("left-5.wav")
                    pygame.mixer.music.play()
                   
            else: # defensive programming
                cleanup()
                exit( 1 )

       

    except KeyboardInterrupt:
        cleanup()
        exit( 1 )
