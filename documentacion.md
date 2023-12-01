A continuacion, procederemos a mostrar el codigo utilizado para nuestro proyecto actual:

.DSEG
   .EQU OPR1=0X00    ; Definir la constante OPR1 con valor 0x00
   .EQU OPR2=0X01    ; Definir la constante OPR2 con valor 0x01
   .ORG 0X100        ; Establecer la dirección de inicio de datos en 0x100
   RESUL: .BYTE 1    ; Reservar 1 byte para la variable RESUL

.CSEG

   LDI R16, OPR1      ; Cargar el valor de la constante OPR1 en R16
   LDI R17, OPR2      ; Cargar el valor de la constante OPR2 en R17
   STS 0X100, R16     ; Almacenar el valor en R16 en la dirección de memoria 0x100
   STS 0X101, R17     ; Almacenar el valor en R17 en la dirección de memoria 0x101

   ; Inicializar el pulsador (simulado como variable BTN)
          ; R25 almacenará el estado del pulsador (0 = no presionado, 1 = presionado)

		  ; Define directions for port pins
	;ldi r16,(1<<PB7)|(1<<PB6)|(1<<PB1)|(1<<PB0)
	;ldi r17,(1<<DDB3)|(1<<DDB2)|(1<<DDB1)|(1<<DDB0)
	;out PORTB,r16
	;out DDRB,r17

   LDI R18, 0X00
   LDI R19, 0XFF
   OUT DDRB, R18
   OUT DDRD, R19
   
   OUT PORTD, R16

   ; Bucle principal
   MainLoop:
      ; Leer el estado del pulsador (simulado)
	   
      IN R24, PINB
	  SBRS R24, 0     
	  RJMP MainLoop

      ; Si hay un cambio en el estado del pulsador, ejecutar la rutina de suma
      CALL SumRoutine

	  OUT PORTD, R16

	  MOV R25, R24

	  DelayLoop:
		 IN R24, PINB 
		 CP R24, R25 
		 BREQ DelayLoop ; Continuar el bucle hasta que el contador llegue a cero

      RJMP MainLoop     ; Volver al bucle principal

   ; Rutina de suma
   SumRoutine:
      LDS R16, 0X100     ; Cargar el valor de la dirección de memoria 0x100 en R16
      LDS R17, 0X101     ; Cargar el valor de la dirección de memoria 0x101 en R17
      ADD R16, R17       ; Sumar los valores en R16 y R17 y almacenar el resultado en R16

      CPI R16, 10        ; Comparar el resultado con 10
      BREQ ResetResult   ; Si es igual a 10, reiniciar el resultado a 0
      
      STS 0X100, R16     ; Almacenar el resultado de la suma en la dirección de memoria 0x100
	  
      RET               ; Retornar de la rutina de suma

   ResetResult:
      LDI R16, 0        ; Reiniciar el resultado a 0
      STS 0X100, R16     ; Almacenar el resultado reiniciado en la dirección de memoria 0x100
	  
      RET               ; Retornar de la rutina de suma


