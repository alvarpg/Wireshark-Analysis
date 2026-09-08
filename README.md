# Wireshark-Analysis

## Objetivo

El análisis de tráfico de red es una de las habilidades fundamentales dentro de la ciberseguridad. Permite comprender cómo se comunican los dispositivos, identificar comportamientos anómalos, detectar posibles incidentes de seguridad y realizar tareas de diagnóstico.

El objetivo de este laboratorio es utilizar Wireshark para capturar y analizar diferentes tipos de tráfico de red generados durante actividades habituales, como consultas DNS, navegación web, pruebas de conectividad mediante ICMP y conexiones SSH.

Durante las pruebas se analizaron diferentes protocolos con el objetivo de comprender su funcionamiento y diferenciar entre comunicaciones cifradas y no cifradas.

---

## Arquitectura

El laboratorio se realizó desde un equipo cliente conectado a una red local.

La estructura básica utilizada fue:

                 RED LOCAL
                     |
                PC CLIENTE
                     |
                Wireshark
                     |
        +------------+------------+
        |            |            |
       DNS          HTTP        HTTPS
        |            |            |
     Servidor      Servidor     Servidor
        |
     192.168.1.1


También se realizaron pruebas de comunicación con otros dispositivos de la red local mediante los protocolos ARP e ICMP, además de una prueba de conexión SSH ## hacia un servidor Ubuntu.

## Herramienta

Durante el laboratorio se utilizaron las siguientes herramientas:

**Wireshark**: captura y análisis del tráfico de red.
**Windows CMD**: ejecución de comandos para generar tráfico y limpiar cachés.
**Ubuntu Server**: utilizado como servidor para la prueba SSH.
**TCP/IP**: conjunto de protocolos utilizado en las comunicaciones analizadas.
**Navegador web**: utilizado para generar tráfico HTTP y HTTPS.
**YouTube**: utilizado para generar tráfico durante el análisis de conexiones TCP.

## Análisis DNS

Primero desde la terminal del PC hacemos el comando: ipconfig /flushdns, para limpiar el cache DNS del ordenador. Después para la prueba hacer un ping a una pagina web, www.wikipedia.com
Después en Wireshark usamos el filtro dns

Vemos que Wireshark ha captado dos paquetes, la pregunta y la respuesta del ping. 
En el primer paquete estamos preguntando que IP corresponde a nombre de www.wikipedia.com. Y en el segundo paquete nos está respondiendo la IP 192.168.1.1, mi servidor DNS, con la IP de la pagina web: 185.15.58.226






## Análisis HTTP
HTTP es un protocolo de comunicación utilizado para la transferencia de páginas web el cual no cifra la información transmitida.
Para la prueba he usado la página web http://testasp.vulnweb.com/Search.asp?tfSearch=que%20tal

Una página con numerosas vulnerabilidades, utilizada para hacer este tipo de pruebas.
En ella he creado mi usuario con las credenciales
alvaro
contraseña1234

Tras iniciar Wireshark, se accede a la página web utilizando las credenciales correspondientes.

Como la comunicación no se encuentra cifrada y la información se almacena en texto plano, muchoslos datos quedan registrados y pueden ser visualizados fácilmente por cualquier usuario con acceso a la captura de tráfico.

Aplicando el filtro HTTP en Wireshark es posible visualizar todos los paquetes enviados y recibidos entre el cliente y el servidor web. Para examinar el contenido completo de la comunicación, se debe hacer clic derecho sobre cualquier paquete y seleccionar la opción Seguir > HTTP Stream.

Dentro del flujo HTTP se puede observar una gran cantidad de información relacionada con la sesión, incluyendo las credenciales del usuario transmitidas durante el proceso de autenticación.





## Análisis HTTPS
HTTPS utiliza TLS para cifrar las comunicaciones. Aunque el tráfico puede capturarse, su contenido permanece protegido.
El filtro para ver los paquetes https en Wireshark es 	tcp.port == 443

Aunque es posible visualizar direcciones IP de origen y destino, no es posible visualizar contenido sensible, como usuarios o contraseñas.

## ARP
ARP se utiliza para asociar direcciones IP con direcciones MAC dentro de una red local.
Cuando un equipo necesita comunicarse con otro dispositivo de la misma red, primero debe conocer su dirección MAC.
Primero limpiamos el caché arp desde la cmd con el siguiente comando: 	arp -d
Se utilizó el filtro:	arp


Se observó tráfico ARP del tipo:	Who has 192.168.1.129 Tell 192.168.1.132
y la correspondiente respuesta:	192.168.1.1 is at de:c8:07:e1:9b:c0
Esto demuestra cómo los equipos descubren la dirección física del dispositivo con el que desean comunicarse.

## ICMP (Ping)
ICMP (Internet Control Message Protocol) se utiliza para comprobar la conectividad entre dispositivos.
Se hico el siguiente ping:	ping 192.168.1.129
Filtro utilizado:		icmp

Se puede ver el primer paquete, el ping que lanza mi PC. Y los demás los reply del ordenador confirmando que existe conexión entre ambos equipos

## Análisis TCP Handshake
TCP establece una conexión mediante un proceso conocido como Three-Way Handshake.
El proceso consta de tres pasos:
Cliente → Servidor
SYN
Servidor → Cliente
SYN ACK
Cliente → Servidor
ACK
Filtro utilizado:		tcp.flags.syn == 1

Para la prueba abrí un video de Youtube, por lo que se captura el principio de la conexión con el servidor de Youtube. Se identificó claramente la secuencia, que indica el establecimiento correcto de una conexión TCP:	SYN -> SYN, ACK -> ACK

## Análisis SSH
Durante la práctica se realizó una conexión SSH a un servidor Ubuntu. Pero no se consiguió el registro de los paquetes con Wireshark. 
Filtro utilizado:		tcp.port == 22


## Problemas encontrados

Durante el laboratorio se encontraron principalmente los siguientes problemas:

  ### Captura del tráfico SSH
  
  No se consiguió registrar correctamente los paquetes correspondientes a la conexión SSH mediante el filtro:
  
  ```text
  tcp.port == 22
  ```
  
  Esto impidió realizar un análisis completo de esta comunicación.
  
  ### Gran cantidad de tráfico
  
  Durante algunas capturas se generó una cantidad elevada de paquetes, por lo que fue necesario utilizar filtros de Wireshark para localizar únicamente el protocolo que se quería analizar.
  
  ### Diferencia entre HTTP y HTTPS
  
  Durante las pruebas fue necesario interpretar correctamente las diferencias entre tráfico HTTP y HTTPS. En HTTP fue posible observar información transmitida sin cifrado, mientras que en HTTPS el contenido estaba protegido mediante TLS.


## Conclusiones

El laboratorio permitió adquirir una visión práctica del funcionamiento de diferentes protocolos de red mediante la captura y análisis de paquetes con Wireshark.

Se comprobó el funcionamiento de protocolos como **DNS, ARP, ICMP y TCP**, observando directamente cómo se produce la comunicación entre los dispositivos.

Uno de los resultados más relevantes fue la comparación entre **HTTP y HTTPS**. El análisis permitió comprobar cómo la ausencia de cifrado en HTTP puede exponer información transmitida, mientras que HTTPS utiliza TLS para proteger el contenido de las comunicaciones.

También se pudo analizar el proceso **TCP Three-Way Handshake**, identificando los paquetes SYN, SYN-ACK y ACK utilizados para establecer una conexión.

Finalmente, aunque no se consiguió capturar correctamente la comunicación SSH, el problema sirvió para identificar la necesidad de revisar la interfaz de captura, la generación del tráfico y los filtros utilizados.

En conjunto, la práctica permitió reforzar los conocimientos de redes y comprender la utilidad de Wireshark como herramienta de análisis y diagnóstico dentro del ámbito de la ciberseguridad.
