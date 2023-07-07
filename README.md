# HEDG-BOT - 2023
Battlebot realizado en otoño de 2023 para el curso "Battlebots: Tu primera máquina de pelea" de la Universidad de Chile. Trabajo realizado colaborativamente entre estudiantes de la facultad de Cs. Fisicas y Matematicas y estudiantes de diseño de la Facultad de Arquitectura y Urbanismo.

### Historia del Battlebot

Hedgebot (HEDG-BOT), nació a partir de la observación/analisis de los erizos y su mecanimo de defensa, las puas. Mezclando este pequeño animal, con su representación más famosa, sonic; llegamos a nuestro battlebot, el cual con un brillante color azul y pernos en su carcasa, busca asimilarse a nuestros objetos de inspiración, contando con un arma giratoria, un rodillo con tornillos, como reinterpretación del ataque famoso de sonic, donde se hace bolita y gira a gran velocidad. 

![H](/multimedia/FotoHedgebot.jpeg)

## Integrantes
- Giovanni Magnani - FCFM
- Camila Rojas - FCFM
- Silvana Olivares - FAU

  ![N](/multimedia/Nosotros.jpeg)

## Descripción del proyecto
  
### Estrategia utilizada
  
#### Ofensiva
Nuestra estrategia ofensiva consta de un rodillo metalico, perforado por pernos con tuercas el cual gracias a un motor DC (reciclado de un battlebot anterior) y un mecanimos de poleas y correas, gira a gran velocidad.

#### Defensiva
Nuestra estrategia defensiva comprende una carcasa metalica de gran espesor (metal reciclado) moldeado alrededor de nuestro robot, protegiendo sus ruedas y partes electronicas. La carcasa contaba con pernos con tuercas representando los pinchos del erizo.

### Diagrama funcional
![D](/diagrama/DiagramaRobot.jpg)

### Diagrama de conecciones electricas
![WhatsApp Image 2023-06-27 at 5 40 07 PM](https://github.com/CamilaRojass/Hedgebot/assets/90356056/7f604866-7d07-4d1d-962f-6514eec8d5c5)

### Código de Arduino IDE
```java
/*
 NodeMCU Access Point - Servidor Web por Dani No www.esploradores.com
 Crea un servidor Web en modo Access Point que permite encender y apagar un LED conectado a la salida D4 (GPIO02) del módulo NodeMCU.
 Este código de ejemplo es de público dominio.
 */

#include <ESP8266WiFi.h>                  //Incluye la librería ESP8266WiFi

const char ssid[] = "armacran";    //Definimos la SSDI de nuestro servidor WiFi -nombre de red- 
const char password[] = "12345";       //Definimos la contraseña de nuestro servidor 
WiFiServer server(80);                    //Definimos el puerto de comunicaciones

int PinLED = 2;                           //Definimos el pin de salida - GPIO2 / D4
int estado = HIGH;                         //Definimos la variable que va a recoger el estado del LED
int data1 = 1000;
int data2 = 0;
int data3 = 0;






// -----------------------------SETUP-------------------------------------------------------------------

void setup() {

  Serial.begin(9600);                     //Iniciamos la comunicación serial con una velocidad de 9600 baudios        

  pinMode(PinLED, OUTPUT);                //Inicializamos el GPIO2 como salida
  digitalWrite(PinLED, HIGH);              //Dejamos inicialmente el GPIO2 apagado. Funciona con una lógica inversa. HIGH es apagado y LOW es encendido

  pinMode(12, OUTPUT);
  pinMode(14, OUTPUT);
  pinMode(15, OUTPUT);
  pinMode(5, OUTPUT);
  pinMode(4, OUTPUT);
  pinMode(13, OUTPUT);
  
  pinMode(D3, OUTPUT);
  //pinMode(n2, OUTPUT);
  //pinMode(n3, OUTPUT);

  digitalWrite(13, HIGH);
  digitalWrite(15, HIGH);
  digitalWrite(D3, LOW);

  server.begin();                         //inicializamos el servidor
  WiFi.mode(WIFI_AP);                     // Definimos el modo del WiFi como Access Point
  // WiFi.softAP(ssid, password);            //Red con clave, en el canal 1 y visible
  
  //WiFi.softAP(ssid, password,3,1);      //Otra opción: Red con clave, en el canal 3 y visible 
  WiFi.softAP(ssid);                    //Otra opción: Red abierta

  Serial.println();                       // Enviamos un salto de línea en el monitor serial

  Serial.print("Direccion IP Access Point - por defecto: ");      //Imprime la dirección IP en el monitor serial
  Serial.println(WiFi.softAPIP()); 
  Serial.print("Direccion MAC Access Point: ");                   //Imprime la dirección MAC en el monitor serial
  Serial.println(WiFi.softAPmacAddress()); 

  //IPAddress local_ip(192, 168, 1, 1);                           //Modifica la dirección IP 
  //IPAddress gateway(192, 168, 1, 1);   
  //IPAddress subnet(255, 255, 255, 0);
  //WiFi.softAPConfig(local_ip, gateway, subnet);
  //Serial.println();
  //Serial.print("Access Point - Nueva direccion IP: ");
  //Serial.println(WiFi.softAPIP());
}

//---------------------------------------------LOOP----------------------------------------------


void loop() 
{
 //Potencia de rodillo (dependen de data3)
   if (data3 == 0) {
    digitalWrite(D3, LOW);
  }
  if (data3 == 1) {
    digitalWrite(D3, LOW);
    delay(50);
    digitalWrite(D3, HIGH);
    delay(10);
  }
  if (data3 == 2) {
    digitalWrite(D3, LOW);
    delay(50);
    digitalWrite(D3, HIGH);
    delay(15);
  }
  
  // Comprueba si el cliente ha conectado
  WiFiClient client = server.available(); 
  if (!client) {  //si no hay un cliente disponible
    return;  // retornamos al inicio del loop
  }

  // Espera hasta que el cliente envía alguna petición
  Serial.println("nuevo cliente");
  while(!client.available()){   // Mientras no haya un cliente disponible
    delay(1); //Esperamos un milisegundo y volvemos a comprobar, nos quedamos dando vueltas en este loop While.
  }

  // Imprime el número de clientes conectados
  Serial.printf("Clientes conectados al Access Point: %dn", WiFi.softAPgetStationNum()); 

  // Lee la petición/solicitud (En este ejemplo la solicitud es todo lo que va despues de la IP -> 192.168.4.1/solicitud)
  String peticion = client.readStringUntil('r'); // Creamos una variable String (texto) llamada petición. 
  // Dentro de esta variable va todo lo que contiene la variable client hasta que se encuentra con una 'r'
  //El caracter retorno de carro determina el fin de la petición, y está denotado por \r.

  Serial.println(peticion); //imprimimos en el monitor serial
  client.flush(); //Mandamos 

  // Comprueba la petición
  if (peticion.indexOf('/on') != -1) {
    estado = LOW;
  }
  if (peticion.indexOf('/off') != -1){
     estado = HIGH;
  }

  //Enciende o apaga el LED en función de la petición
  if (estado == LOW) {
  digitalWrite(PinLED, LOW);
  }
  else {
    digitalWrite(PinLED, HIGH);
  }
  //motores
   if (peticion.indexOf('/up') != -1) {
    analogWrite(12, 0);
    analogWrite(14, data1);
    analogWrite(4, data1);
    analogWrite(5, 0);
  }
  if (peticion.indexOf('/b') != -1) {
    //quedó con esta letra porque por alguna razón, no funciona con "right"
    analogWrite(12, data1);
    analogWrite(14, 0);
    analogWrite(4, data1);
    analogWrite(5, 0);
  }
  if (peticion.indexOf('/d') != -1) {
    //quedó con esta letra porque escribiendo "down" no funciona
    analogWrite(12, data1);
    analogWrite(14, LOW);
    analogWrite(4, LOW);
    analogWrite(5, data1);
  }
  if (peticion.indexOf('/left') != -1) {
    analogWrite(12, LOW);
    analogWrite(14, data1);
    analogWrite(4, LOW);
    analogWrite(5, data1);
  }
  if (peticion.indexOf('/quieto') != -1) {
    digitalWrite(12, LOW);
    digitalWrite(14, LOW);
    digitalWrite(4, LOW);
    digitalWrite(5, LOW);
  }
  //potencia
   if (peticion.indexOf("/v1") != -1) {
    data1 = 250;
  }  
  if (peticion.indexOf('/v2') != -1) {
    data1 = 500;
  }  
   if (peticion.indexOf('/v3') != -1) {
    data1 = 750;
  }  
  if (peticion.indexOf('/v4') != -1) {
    data1 = 1000;
  }  
  if (peticion.indexOf('/v5') != -1) {
    data1 = 1028;
  }  
  //arma (rodillo con púas)
if (peticion.indexOf('/RON') != -1) {
  if (data2 == 0) {
    data3 = 1;
  }
  else {
    data3 = 2;
  }
}
  if (peticion.indexOf('/ROFF') != -1) {
    data3 = 0;
  }

  if (peticion.indexOf('/v11') != -1) {
    data2 = 0;
  }  
  if (peticion.indexOf('/v12') != -1) {
    data2 = 1;
  }  


  

  // Envía la página HTML de respuesta al cliente
  client.println("HTTP/1.1 200 OK");
  client.println("");                                     //No olvidar esta línea de separación
  client.println("<!DOCTYPE HTML>");
  client.println("<meta charset='UTF-8'>");
  client.println("<html>");

      //Imprime el estado del led
  client.print("<h1>El LED está ahora: ");                 
  if(estado == LOW) {
    client.print("ENCENDIDO</h1>");  
  } else {
    client.print("APAGADO</h1>");
  }

      //Se crean botones con estilos para modificar el estado del LED
  client.println("<button type='button' onClick=location.href='/LED=ON' style='margin:auto; background-color:green; color:#A9F5A9; padding:5px; border:2px solid black; width:200;'><h2> ENCENDER</h2> </button>");
  client.println("<button type='button' onClick=location.href='/LED=OFF' style='margin:auto; background-color:red; color:#F6D8CE; padding:5px; border:2px solid black; width:200;'><h2> APAGAR</h2> </button><br><br>");

  client.println("</html>"); 
  delay(1);
  Serial.println("Petición finalizada");          // Se finaliza la petición al cliente. Se inicaliza la espera de una nueva petición.

  //Desconexión de los clientes
  //WiFi.softAPdisconnect();
}
```
## Licencia
<a rel="license" href="http://creativecommons.org/licenses/by-nc/4.0/"><img alt="Licencia Creative Commons" style="border-width:0" src="https://i.creativecommons.org/l/by-nc/4.0/88x31.png" /></a><br />Esta obra está bajo una <a rel="license" href="http://creativecommons.org/licenses/by-nc/4.0/">Licencia Creative Commons Atribución-NoComercial 4.0 Internacional</a>.

### Otras dificultades enfrentadas
En general los errores que ocurrieron mas fecuentemente fueron piezas de impresión 3D que se rompieron 
![WhatsApp Image 2023-07-03 at 11 22 20 PM](https://github.com/CamilaRojass/Hedgebot/assets/90356056/1262cd88-9163-4f19-843e-f3a722763d7e)
![WhatsApp Image 2023-07-03 at 11 22 45 PM](https://github.com/CamilaRojass/Hedgebot/assets/90356056/aa7281c6-20ca-452e-aca6-c699aa6266af)

Además enfrentamos problemas en la pelea final porque dejaron de llegar las señales al motor del arma. 
