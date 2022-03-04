# Kickstart10L
Kickstart10L is an implementation in MSX2 BASIC of Amiga bootstrap image.

Reference:
https://retrocomputing.stackexchange.com/questions/13897/why-was-the-kickstart-1-x-insert-floppy-graphic-so-bad


![screenshot](kickstart.gif)

Sources are stored at: https://github.com/robertocapuano/Kickstart10L

# Installation
Game was developed in MSX-BASIC v3 on a MSX2+. Emulator is available at https://webmsx.org/
1. connect to https://webmsx.org
2. click on the first floppy icon below the blue screen
3. Select "Add Disk Images"
4. Choose "kickstart.dsk"
5. type in:
```
load "a:kickstart.bas"
run
```

WebMSX Launch URL:

- https://webmsx.org/?DISK=https://github.com/robertocapuano/Kickstart10L/raw/main/kickstart.dsk&BASIC_RUN=kickstart.bas


# Source Description

```
1 data FF,01,23,0B,3A,0B,3A,21,71,21,71,0B,7D,0B,88,16,88,5E,7F,5E,7F,38,40,38,3E,36,35,36,34,38,2D,38,2D,41,23,48,23,0B,FE,02,25,45,FF,01,21,48,21,0A,7E,0A,8A,16,8A,5F,56,5F,56,64,52,6C,4E,71,4A,74,44,7D,3C,81,3C,8C,0A,8C
2 data 0A,6D,09,6D,09,51,0D,4B,14,45,15,41,19,3A,1E,37,21,36,21,36,1E,38,1A,3A,16,41,15,45,0E,4B,0A,51,0A,6C,0B,6D,0B,8B,28,8B,28,76,30,76,34,72,34,5F,32,5C,32,52,41,45,41,39,3E,37,3B,37,3E,3A,3E,41,3D,42,36,42,33,3F,2A,46
3 data 1E,4C,12,55,12,54,1E,4B,1A,4A,17,47,1A,49,1E,4A,21,48,FF,01,32,3D,34,36,3C,37,3D,3A,3D,41,36,41,32,3D,FF,01,33,5C,33,52,42,45,42,39,7D,39,7D,5E,34,5E,33,5A,FF,01,3C,0B,6F,0B,6F,20,3C,20,3C,0B,FF,01,60,0E,6B,0E,6B,1C
4 data 60,1C,60,0E,FE,03,3E,1F,FF,01,62,0F,69,0F,69,1B,62,1B,62,0F,FE,02,63,1A,FF,01,2F,39,32,39,32,3B,2F,3F,2F,39,FF,01,29,8B,29,77,30,77,35,72,35,69,39,6B,41,6B,41,6D,45,72,49,72,49,74,43,7D,3B,80,3B,8B,29,8B,FF,01,35,5F
5 data 35,64,3A,61,35,5F,FF,01,39,62,35,64,35,5F,4A,5F,40,69,3F,69,41,67,3C,62,39,62,FF,01,4E,5F,55,5F,55,64,51,6C,4E,70,49,71,46,71,43,6D,43,6A,4E,5F,FF,01,44,6A,44,6D,46,70,48,70,4C,6F,4D,6C,49,69,44,6A,FF,01,36,68,3E,6A
6 data 40,67,3C,63,39,63,36,65,36,68,FF,01,7E,0B,89,16,89,5E,FE,01,22,0B,FE,01,3B,0B,FE,01,61,0F,FE,01,6A,1B,FE,01,70,0F,FE,01,7E,5E,FE,01,4B,60,FE,01,2E,39,FF,FF
```

```
7 color 1,15,15:SCREEN 5,3,0: cls :KEYOFF:DEFINTA-Z: :open"grp:"as#1:M=0:I=0:C(0)=4:C(1)=1:C(2)=4:c(3)=14: SX=50:SY=25
```

```
8 READ R$:R=VAL("&H"+R$):I=I+1:READ T$:T=VAL("&H"+T$):if R=255 and T=255 then M=255:goto100: else if  R>=254 then C=C(T): M=1+-5*(R=254):goto8
```

```
10 if M=1 then X=R: Y=T:M=2:else if M=2then LINE (X+SX,Y+SY)-(R+SX,T+SY) :M=3: else if M=3 then  LINE-(R+SX,T+SY):else paint(R+SX,T+SY),C,1:M=0
```

```
100 if M<>255 then 8: else : pset(103,108): print#1,"Kickstart": goto 100
```

```
- Decomment line to load Kun BASIC.
```
10 screen1,0,0:COLOR2,1,1:KEYOFF:CLS:DEFINTA-Z:B=base(5):R=RND(-TIME):R=4: BB=B+23*32:CB=BASE(6):
```
- screen1,0,0: Set text mode 32x24
- COLOR2,1,1: black color
- KEYOFF: hide function keys
- CLS: clear screen
- DEFINTA-Z: use integer variables
- B=base(5): base of framebuffer
- R=RND(-TIME): init random number generator
- R=4: BB=B+23*32:CB=BASE(6): constants used for base addresses

```
20  for i=0 to 4: READ R$:VPOKE  CB+8+I,VAL("&H"+R$) : next I
```
- load tile sets

```
100 rem _TURBOON(BB,R)
```
- Decomment line to enable turbo mode

```
105  S=32*24-1:for I=0 to S: vpoke BB+31-I, 64 : next I 
```
- scroll screen

```
110 for I=0 to 31: LL=R* (RND(1)* SIN(I/31 * 3.14)): vpoke BB+I,(8+LL)*8+LL : NEXT I
```
- generate random heat values and store them the last screen row

```
120 for J=0TO20:for I=0 to 31:A=BB-j*32+I:v= vpeek(a)mod8:d=RND(1)*3: AB=A - D +1 - 32: vpoke AB, (8+V)*8+V:NEXT I,J
```
- compute heat advection from bottom to top screen

```
150 K$=INKEY$:K=(K$<>""): R=-K*(4-R) + (1+K)*R:goto110:
```
- check key press, R is switched to stop/start generation of new heat values

```
160 _TURBOOFF
```
- Decomment line to end of turbo section
```
310 DATA 11, 88, 99,  ba, 98,   64, 73, 82, 91, 100

```
- tile map