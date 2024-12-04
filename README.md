# Practica 11 Final

## Integrantes

- Dante Mejía Silva
- Atzin Morales Alejandre

## Introducción

En esta práctica se diseñó e implementó un sistema de control para un brazo robótico mediante la integración de un microcontrolador ESP32, un sensor ultrasónico y un software especializado para el manejo del robot. La finalidad del sistema fue permitir que el brazo respondiera de manera precisa y automática a los cambios detectados en su entorno, basándose en datos obtenidos en tiempo real.

El ESP32 actuó como el controlador principal, procesando las mediciones proporcionadas por el sensor ultrasónico para detectar variaciones en la distancia de los objetos cercanos. Estas mediciones se tradujeron en comandos específicos que se enviaron al brazo robótico a través de comunicación serial. El sistema fue diseñado para garantizar que el brazo realizara movimientos hacia posiciones predefinidas según los datos recibidos, optimizando así su capacidad de respuesta.

## Instrucciones

En primer lugar se desarrolló el código en Arduino:
```
#include "BluetoothSerial.h"
BluetoothSerial ESP_BT;

const int TRIG_PIN = 12; // Pin para el TRIG del sensor
const int ECHO_PIN = 14; // Pin para el ECHO del sensor

int incoming = 1000;
int incomingAnt = 1000;

void setup() {
  // put your setup code here, to run once:
  Serial2.begin(9600, SERIAL_8N1, 16, 17);
  ESP_BT.begin("ESP_Robot");
  Serial.begin(9600);

  pinMode(TRIG_PIN, OUTPUT);
  pinMode(ECHO_PIN, INPUT);
  
}

void loop() {
  // put your main code here, to run repeatedly:
    
  // Envía un pulso ultrasónico
  digitalWrite(TRIG_PIN, LOW);
  delayMicroseconds(2);
  digitalWrite(TRIG_PIN, HIGH);
  delayMicroseconds(10);
  digitalWrite(TRIG_PIN, LOW);

  // Mide el tiempo que tarda el pulso en volver
  long duration = pulseIn(ECHO_PIN, HIGH);

  // Calcula la distancia en centímetros
  float distance = (duration * 0.034) / 2;  
    
  if ((distance >= 10) && (distance <= 20)){
    incoming = 49;
  }
  else if ((distance >= 30) && (distance <= 40)){
    incoming = 50;
  }
  else if((distance >= 50) && (distance <= 60)){
    incoming = 51;
  }
  else {
    incoming = 1000;
  }
  

  if (incomingAnt != incoming){
    Serial2.write(incoming);
    //Serial.println(incoming);
     incomingAnt = incoming;
  }
  
  //incomingAnt = incoming;
 
  delay(500);
}
```

El código en Arduino para el ESP32 utiliza un sensor ultrasónico para medir distancias y enviar comandos a un brazo robótico mediante comunicación serial. En la configuración inicial, se habilitan los pines del sensor (`TRIG` y `ECHO`) y las comunicaciones serial y Bluetooth para el envío de datos y la depuración. En el bucle principal, el sensor ultrasónico mide distancias calculando el tiempo que tarda un pulso en reflejarse; estas distancias se comparan con rangos predefinidos para asignar valores específicos (`49`, `50`, `51`) que corresponden a posiciones del brazo robótico. Solo si hay un cambio en el comando asignado, este se envía por el puerto serial, evitando transmisiones redundantes. El sistema funciona de manera continua y eficiente, pausando brevemente entre lecturas para procesar los datos en tiempo real y garantizar que el brazo responda únicamente a cambios relevantes en las mediciones del entorno.

Luego, se procedió a realizar el código en el software del Epson:
```
Integer dato
Function main
Motor On
Home
Power High
SpeedS 1000
AccelS 10000, 10000
Speed 50
Accel 50, 50
Weight 0.1
Tool 1
Xqt serial
Do
		
	Select 1
		Case dato = 49
			Move N1
		Case dato = 50
			Move N2
		Case dato = 51
			Move N3
	Send
Loop
Fend

Function serial
	Integer numChars
	OpenCom #1
	Wait 1
	Do
		Do While numChars = 0
			numChars = ChkCom(1)
		Loop
		ReadBin #1, dato
		Print dato
	Loop
	CloseCom #1
	
Fend
```

El código del brazo robótico Epson está diseñado para recibir y procesar comandos seriales enviados por el ESP32, lo que permite mover el brazo a posiciones específicas. En la función principal (`main`), se configuran parámetros iniciales como velocidad, aceleración y herramienta activa, y se habilita la recepción de datos seriales a través de la función `serial`. Esta última se encarga de abrir el puerto de comunicación, esperar datos entrantes, leer el valor recibido y almacenarlo en una variable (`dato`). En el flujo principal, un bucle evalúa continuamente el valor de `dato` y, mediante una estructura `select-case`, ejecuta el movimiento correspondiente hacia posiciones predefinidas (`N1`, `N2`, `N3`) según el comando recibido.

![Imagen de WhatsApp 2024-12-02 a las 16 04 47_3c259024](https://github.com/user-attachments/assets/499c1a39-e746-4f98-b141-8a0f3cf31cce)
![Imagen de WhatsApp 2024-12-02 a las 16 04 49_2e7bf57b](https://github.com/user-attachments/assets/069eaa99-346c-465f-83a3-f4b6345394f0)

## Conclusiones

***Atzin Morales Alejandre:*** 
El desarrollo de este sistema de control destacó por su enfoque en la interacción dinámica entre un microcontrolador y un brazo robótico, mostrando cómo sensores simples pueden potenciar aplicaciones complejas. Al traducir datos ambientales en acciones concretas, se creó un vínculo eficiente entre la medición del entorno y la ejecución precisa de movimientos. Esta práctica evidencia la versatilidad de la robótica en la automatización de procesos, demostrando que con recursos accesibles y una programación bien estructurada, es posible implementar soluciones innovadoras para aplicaciones industriales, educativas o experimentales.

***Dante Mejía Silva:*** 
En esta práctica, se logró una integración eficiente entre un ESP32 y un brazo robótico Epson, utilizando un sensor ultrasónico como entrada principal para generar movimientos automatizados y precisos. El ESP32 procesó datos en tiempo real, interpretó distancias en comandos específicos y los envió al brazo mediante comunicación serial, mientras que el robot ejecutó estos comandos utilizando configuraciones optimizadas para velocidad y precisión. Este sistema demuestra la capacidad de combinar hardware y software de manera efectiva, creando una solución adaptable y escalable para tareas de automatización y robótica. La experiencia adquirida refuerza la importancia de la coordinación entre sistemas heterogéneos y la optimización del flujo de datos para lograr un control robusto y dinámico.

## Referencias Bibliográficas 

[1] 	EpsonCompany, «Especialistas en automatización industrial». 2024, https://www.epson.es/es_ES/robots
