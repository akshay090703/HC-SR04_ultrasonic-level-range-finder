# HC-SR04 Ultrasonic Level Range Finder - Proteus Project

## Overview

This project demonstrates the implementation of an HC-SR04 Ultrasonic Level Range Finder using assembly language. The HC-SR04 is an ultrasonic sensor that uses sonar to determine distance to an object. This project provides a simulation of the HC-SR04 sensor in Proteus.

## Table of Contents

- [Introduction](#introduction)
- [Features](#features)
- [Hardware Requirements](#hardware-requirements)
- [Software Requirements](#software-requirements)
- [Getting Started](#getting-started)
- [Simulation](#simulation)
- [Assembly Code](#assembly-code)
- [Resources](#resources)
- [License](#license)

## Introduction

The HC-SR04 Ultrasonic Level Range Finder is a popular sensor for measuring distance without direct contact. It emits ultrasonic waves and measures the time taken for the waves to bounce back, providing accurate distance information.

## Features

- Simulated HC-SR04 sensor in Proteus.
- Assembly language code for distance measurement.
- Distance displayed in the simulation environment.

## Hardware Requirements

- Proteus simulation software.
- HC-SR04 Ultrasonic Level Range Finder module.
- Appropriate microcontroller (e.g., Arduino) for interfacing with the sensor.

## Software Requirements

- Proteus simulation software.
- Appropriate assembler or compiler for the microcontroller.

## Getting Started

1. Clone or download the project repository.
2. Open the Proteus project file.
3. Load the assembly code onto the microcontroller.
4. Run the simulation.

## Simulation

The Proteus simulation provides a virtual environment for testing the HC-SR04 sensor and the assembly language code. The simulation includes a graphical representation of the HC-SR04 module and the distance measured.

## Assembly Code

The assembly code for this project is located in the main directory. It includes comments to explain each section of the code. You can modify the code to suit your specific requirements.

````assembly
$NOMOD51
$INCLUDE (8051.MCU)


trig  EQU  P3.1
echo  EQU  P3.0
enable equ p2.2
rs equ p2.0
rw equ p2.1
LCD_dat equ p1
 ORG  0000

setb echo
clr trig
mov tmod, #02h
mov th0, #202
 acall LCD_init
 acall delay_2s
acall LCD_clear
loop1:
        ACALL get_level
        ACALL CONVERT
        acall cursr_home
		ACALL display
        SJMP Loop1


LCD_init: mov dptr, #syntax
		clr rs
		clr rw
loop:	clr a
		movc a, @a+dptr
		jz LCD_logo
		setb enable
		mov LCD_dat, a
		clr enable
		acall delay1ms
		inc dptr
		sjmp loop

syntax: db 38h,0fh,01h,10h,00h

LCD_logo: mov dptr, #syntax1
		setb rs
		clr rw
loop4:	clr a
		movc a, @a+dptr
		jz new_command
		setb enable
		mov LCD_dat, a
		clr enable
		acall delay1ms
		inc dptr
		sjmp loop4

syntax1: db 'Group',0

new_command: mov dptr, #syntax2
		clr rs
		clr rw
loop5:	clr a
		movc a, @a+dptr
		jz LCD_logo_2
		setb enable
		mov LCD_dat, a
		clr enable
		acall delay1ms
		inc dptr
		sjmp loop5

syntax2: db 0c0h,14h,14h,14h,00h


LCD_logo_2: mov dptr, #syntax3
		setb rs
		clr rw
loop6:	clr a
		movc a, @a+dptr
		jz return
		setb enable
		mov LCD_dat, a
		clr enable
		acall delay1ms
		inc dptr
		sjmp loop6

syntax3: db "2",0

return:ret

cursr_home:
clr rs
setb enable
mov LCD_dat,#80h
clr enable
acall delay10ms
setb enable
mov LCD_dat,#0Ch
clr enable
ret
LCD_clear:
clr rs
setb enable
mov LCD_dat,#01h
clr enable
acall delay10ms
ret

get_level:
			clr a
			setb trig
			acall delay_10us
			clr trig
wait5:		jnb echo, wait5
			setb tr0
wait6:		jnb tf0, wait6
			inc A
			clr tf0
			jz return

			jb echo, wait6
			clr tr0
			ret


delay_10us:
			mov r7, #18
stay:		djnz r7, stay
			ret





CONVERT:
        MOV     B,#10
        DIV     AB
        MOV     41,B
        MOV     B,#10
        DIV     AB
        MOV  42,B
		MOV  43,A
		CALL LOKUP
		MOV  43,A
		MOV  A,42
		CALL LOKUP
		MOV  42,A
		MOV  A,41
		CALL LOKUP
		MOV  41,A
		RET


LOKUP:
        CJNE A,#00H,ONE
        MOV     A,#'0'
        RET
ONE:    CJNE A,#01H,TWO
        MOV     A,#'1'
        RET
TWO:    CJNE A,#02H,THREE
        MOV     A,#'2'
        RET
THREE:  CJNE A,#03H,FOUR
        MOV    A,#'3'
        RET
FOUR:   CJNE A,#04H,FIVE
        MOV     A,#'4'
        RET
FIVE:   CJNE A,#05H,SIX
        MOV     A,#'5'
        RET
SIX:    CJNE A,#06H,SEVEN
        MOV     A,#'6'
        RET
SEVEN:  CJNE A,#07H,EIGHT
        MOV     A,#'7'
        RET
EIGHT:  CJNE A,#08H,NINE
        MOV     A,#'8'
        RET
NINE:   CJNE A,#09H,TEN
        MOV     A,#'9'
        RET
TEN:
        RET



display:
        clr rw
		setb rs
		acall delay1ms

		SETB    enable
    	MOV     LCD_dat,#' '
        clr enable
		acall delay1ms

		SETB    enable
    	MOV     LCD_dat,43
        clr enable
		acall delay1ms

		SETB    enable
     	MOV     LCD_dat,42
        clr enable
		acall delay1ms

		SETB    enable
    	MOV     LCD_dat,41
        clr enable
		acall delay1ms

		SETB    enable
        MOV     LCD_dat,#'c'
        clr enable
		acall delay1ms

		SETB    enable
        MOV     LCD_dat,#'m'
        clr enable
		acall delay1ms

        RET




delay10ms: 	MOV   R3,#1
		    MOV   R2,#1
      		MOV   R1,#19
TT1:  		DJNZ  R1,TT1
      		DJNZ  R2,TT1
      		DJNZ  R3,TT1
      		RET
delay1ms: 	MOV   R2,#04
      		MOV   R1,#18
TT2:  		DJNZ  R1,TT2
      		DJNZ  R2,TT2
      		RET
delay_2s:MOV   R3,#50
		    MOV   R2,#10
      		MOV   R1,#250
TT3:  		DJNZ  R1,TT1
      		DJNZ  R2,TT1
      		DJNZ  R3,TT3
      		RET

      END```

## Resources

- [HC-SR04 Datasheet](link-to-datasheet)
- [Proteus Simulation Software](link-to-proteus)

## License

This project is licensed under the [MIT License](LICENSE).

---

````
