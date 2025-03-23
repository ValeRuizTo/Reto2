# **Sistema IoT para Detección de Incendios en los Cerros Orientales de Bogotá**

## Miembros: Samuel Rodriguez, Valentina Ruiz Torres y Darek Aljuri Martínez

## 1. Introducción
### 1.1 Resumen General
El presente documento describe el desarrollo de un sistema IoT para la detección temprana de incendios en los cerros orientales de Bogotá. Se detallan las etapas de diseño, desarrollo, implementación y validación de la solución, asegurando que el prototipo sea funcional y eficiente en la identificación de riesgos de incendio mediante sensores de temperatura, gas y llama. La solución utiliza un microcontrolador ESP-32 junto con una pantalla LCD, un buzzer y un led rgb para proporcionar alertas locales, asi como un “Tablero de control” en un servidor web embebido proporcionado por el microcontrolador conectado a la WLAN ofrecida por la alcaldía, donde tambien se podra visualizar el valor actual y un histórico reciente de las variables físicas de interés, así como recibir notificaciones de cualquier eventualidad y desactivar alarmas físicas.

### 1.2 Motivación
Los cerros orientales de Bogotá son una zona ecológica de gran importancia, pero altamente susceptible a incendios forestales debido a la sequía y actividad humana. La detección temprana de incendios es clave para minimizar daños ambientales y proteger comunidades cercanas. Para ello, se requiere un sistema de monitoreo en tiempo real que permita detectar cambios bruscos en temperatura, presencia de humo y emisión de gases característicos de la combustión. Además, es fundamental contar con una plataforma que no solo permita visualizar el historial de datos y activar alarmas físicas de manera remota, sino que también genere alertas automáticas en caso de riesgo inminente de incendio, facilitando una respuesta rápida y efectiva.



### 1.3 Justificación
El desarrollo de este sistema surge de la necesidad de implementar una solución de bajo costo y fácil instalación para la detección temprana de incendios forestales en zonas vulnerables. Utilizando sensores especializados, el sistema detecta la presencia de gases y anomalías en la temperatura, proporcionando información en tiempo real para la toma de decisiones. Además, cuenta con alarmas in situ y envía los datos recopilados a una página web embebida, facilitando el monitoreo y la respuesta rápida ante posibles incendios.

### 1.4 Estructura de la Documentación

Este documento se divide en las siguientes secciones:

1. **Solución propuesta**: Restricciones, arquitectura, desarrollo teórico y estándares aplicados.
2. **Configuración experimental y resultados**: Validación del sistema en condiciones controladas.
3. **Autoevaluación del protocolo de pruebas**: Verificación de confiabilidad y precisión.
4. **Conclusiones y trabajo futuro**: Desafíos enfrentados y mejoras futuras.
5. **Anexos**: Código fuente, esquemáticos y documentación adicional.

## 2. Solución Propuesta
### 2.1 Restricciones de Diseño

***Técnicas***
- Uso de un ESP32 como microcontrolador central. Este dispositivo, además de ser de código abierto, incorpora un microcontrolador reprogramable con conectividad Wi-Fi y Bluetooth, así como múltiples pines de entrada y salida analógicos y digitales. Esto permite la conexión eficiente con sensores y actuadores. "El ESP32 destaca por su alto rendimiento gracias a su procesador de doble núcleo. Puede llegar hasta 240 MHz, manejando tareas complejas y procesamiento en tiempo real. Esto es clave para proyectos IoT avanzados que necesitan manejar varios procesos a la vez" [1]
- Sensor de temperatura DS18B20, "permite medir la temperatura a través de un termistor NTC, un comparador LM393, y un potenciometro"[2], esto le permite al sensor adecuar una señal de trabajo operable digital.
- Sensor de gas (MQ-2), "Este sensor es adecuado para detectar GLP, I-butano, propano, metano, alcohol, hidrógeno y humo. Tiene alta sensibilidad y respuesta rápida"[3], ademas de esto cuenta con un potenciometrp para ajustar la sensibilidad.
- Sensor de Llama (KY-026). "Consta de un LED receptor de infrarrojos de 5 mm, un comparador diferencial dual LM393, un potenciómetro de recorte de 3296 W, 6 resistencias, y 4 pines de cabezal macho"[4] Es decir que detecta la luz infrarroja emitida por el fuego, y debido a su potenciometro se puede ajustar su sensibilidad.
- Pantalla LCD, con un modulo I2C para visualización de datos en tiempo real. Modulo utilizado debido a que facilita la conexión de cables y uso de pines del arduino, haciendo uso de solo dos (SDA y SCL), asi mismo se trabajo con la libreria LiquidCrystal_I2C.h, facilitando el codigo para hacer uso del LCD.
- Buzzer para alertas sonoras en caso de detección de incendio.

 
***Económicas***
- Implementación con componentes de bajo costo y accesibles.
- Uso de software de código abierto compatible con el ESP32 para minimizar costos de desarrollo implementado con el IDE arduino
  
***Espacio y Escalabilidad***
- Diseño compacto para facilitar su instalación en zonas estratégicas.
- Posibilidad de expansión mediante comunicación con otros dispositivos IoT gracias a la conectividad Wi-Fi y Bluetooth del ESP32
- Adaptabilidad para futuras mejoras con nuevos sensores o algoritmos de detección.

  
***Temporales***
- Desarrollo del prototipo en un plazo limitado, asegurando funcionalidad básica.
- Posibilidad de mejoras futuras en algoritmos y hardware para mayor precisión.



### 2.2 Arquitectura Propuesta

***Arquitectura IoT del Sistema***

La arquitectura IoT permite la transmisión de información digitalizada a través de la red, llevando los datos capturados por los sensores hacia un centro de procesamiento local, donde son analizados y almacenados. Posteriormente, mediante actuadores, se pueden emitir comandos para que los dispositivos conectados ejecuten acciones específicas, como la activación o desactivación de un mecanismo.

El sistema de detección de incendios está basado en una estructura distribuida compuesta por sensores, procesamiento local y comunicación de datos para la notificación de alertas. Se organiza en tres capas principales:

1. Capa de Percepción (Sensores y Adquisición de Datos)
Es la capa encargada de capturar la información del entorno mediante sensores físicos. Los dispositivos utilizados incluyen:

- Sensor de temperatura (): Mide la temperatura del aire en la zona monitoreada.
- Sensor de gas (): Detecta concentraciones de gases como CO y CO₂, indicativos de combustión.
- Sensor de llama (): Detecta la presencia de llamas en el área monitoreada.
Los sensores están conectados a un ESP32, que procesa la información en tiempo real.

2. Capa de Procesamiento y Control
El ESP32 actúa como la unidad central de procesamiento (CPU), encargada de:
- Leer y analizar los datos recibidos de los sensores.
- Determinar si las condiciones indican un posible incendio.
- Activar mecanismos de alerta local (buzzer, LED RGB y pantalla LCD).
- Registrar datos históricos para su posterior consulta.

3. Capa de Comunicación y Notificación
En la versión actual del prototipo, las alertas se generan localmente mediante:

- Servidor Web Integrado: El ESP32 ejecuta un servidor web accesible a través de WiFi, donde el usuario puede consultar el estado de los sensores en tiempo real y controlar los dispositivos (actuadores)
- Pantalla LCD (I2C 16x2): Muestra valores en tiempo real y advertencias sobre posibles incendios.
- LED RGB: Señalización visual de estados normales y de alerta.
- Buzzer: Alarma sonora para advertir de situaciones críticas.
- Registro de datos: Se almacenan lecturas recientes para su análisis y consulta remota.

Esta arquitectura permite la toma de decisiones en tiempo real, proporcionando una plataforma eficiente para la detección temprana de incendios y la prevención de desastres.
![.](imagenesWiki/arqui1.jpg)




### 2.3 Desarrollo Teórico Modular

#### **Principios de Diseño del Sistema**
- **Fiabilidad:** Uso de sensores calibrados para evitar falsas alarmas.
- **Bajo Consumo Energético:** Optimización del código para minimizar el consumo de energía.
- **Interfaz Intuitiva:** Uso de una pantalla LCD y alertas sonoras para notificaciones claras.
- **Escalabilidad:** Posibilidad de agregar conectividad remota en versiones futuras.


## **Definición de umbrales de seguridad**

Para garantizar una detección confiable de incendios, se establecieron los siguientes umbrales en el sistema:

- **Temperatura máxima aceptable:** `TEMP_MAX = 30°C`
- **Nivel máximo de gas permitido:** `GAS_MAX = 700`
- **Cambio brusco de temperatura:** Se define como un incremento mayor a `5°C` en comparación con la lectura anterior.

Estos valores permiten detectar condiciones anómalas en el ambiente y activar una alerta en caso de peligro.

### **Justificación del umbral de temperatura**

Según el planteamiento inicial del reto, la zona de interés presenta temperaturas medias anuales entre **8.4°C y 13°C**.  
Sin embargo, se decidió establecer un umbral superior de **30°C** para la activación de alertas por las siguientes razones:

1. **Prevención de falsas alarmas:**  
   - En condiciones normales, un umbral demasiado bajo podría generar **alertas innecesarias**, activándose por aumentos naturales de temperatura.
   - Se buscó un equilibrio entre sensibilidad y precisión en la detección de incendios.

2. **Consideración de escenarios extremos:**  
   - En ciertas circunstancias (como exposición solar directa, equipos en funcionamiento o ventilación deficiente), la temperatura en el entorno puede superar los valores promedio de la región.
   - Un umbral más alto permite descartar estas fluctuaciones y centrarse en **situaciones realmente peligrosas**.

3. **Adaptación a condiciones reales de incendio:**  
  - Según el documento "Las fases de un incendio", durante la fase de incremento de un incendio, la temperatura aumenta rápidamente, situándose entre los 300°C y los 700°C. [5] Este incremento significativo de temperatura en las etapas iniciales de un incendio respalda la decisión de establecer un umbral de 30°C en nuestro sistema de detección. Aunque las temperaturas medias anuales en la zona de interés varían entre 8.4°C y 13°C, un aumento repentino que supere los 30°C podría indicar una situación anómala que requiera atención inmediata. Por lo tanto, este umbral permite detectar de manera efectiva un incipiente foco de incendio sin comprometer la confiabilidad del sistema.​   

4. **Resultados observados en la fase de pruebas de laboratorio:**  
   - Durante las pruebas en el laboratorio, se registraron temperaturas promedio entre **25°C y 27°C**.  
   - Esto evidenció que, aunque las temperaturas externas sean más bajas, el ambiente de prueba puede ser significativamente más cálido.  
   - Debido a esto, **se estableció 30°C como un valor adecuado para definir una temperatura "alta"**, asegurando que las alertas solo se activen en condiciones realmente anómalas.



## **Condiciones de activación de alerta**
El sistema genera una alerta cuando ocurre cualquiera de las siguientes condiciones:

1. **Sobrecalentamiento y gas elevado:** La temperatura supera los `30°C` y el nivel de gas es mayor a `700`.
2. **Presencia de llama:** Se detecta una llama a través del sensor (`PIN_LLAMAS`).
3. **Cambio brusco de temperatura:** Se registra un incremento superior a `5°C` en comparación con la última medición.

Cuando se activa una de estas condiciones, el sistema toma medidas inmediatas para alertar al usuario.



## **Consideraciones del entorno de laboratorio**
Las condiciones ambientales en el laboratorio difieren del entorno final donde se implementará el sistema. Por este motivo:

- Se ajustaron los umbrales para adaptarse al entorno de prueba y minimizar **falsos positivos**.
- Se añadió un umbral para detectar cambios bruscos de temperatura (`>5°C`), mejorando la precisión en la identificación de incendios.

Estos ajustes garantizan que el sistema responda correctamente sin generar alertas innecesarias.



## **Funcionamiento de la detección de cambios bruscos de temperatura**
El sistema monitorea la temperatura de forma continua para identificar cambios abruptos:

1. **Comparación de lecturas:** Se compara la temperatura actual (`valorTemp`) con la última registrada (`tempAnt`).
2. **Detección de incremento abrupto:** Si la diferencia supera `5°C`, se activa el indicador `saltoTemp = true`.
3. **Registro y notificación:**
   - El evento se almacena en el sistema de logs.
   - La información se envía a la interfaz web para que el usuario la visualice en tiempo real.


## **Intervalo de detección de cambios de temperatura**
- La temperatura se actualiza cada `500 ms` (`refresco = 500`), permitiendo una detección casi en **tiempo real**.
- Esto garantiza que cualquier cambio repentino se registre de manera inmediata y se tomen acciones rápidas.

## **Respuesta del sistema ante detección de incendio**
Si se activa una alerta por alguna de las condiciones previamente definidas, el sistema ejecuta las siguientes acciones:

**Indicadores visuales y auditivos:**
- Se enciende el **LED RGB en rojo** para señalar peligro.
- Se activa el **zumbador (buzzer)** con una frecuencia de `1000 Hz`.
- La pantalla **LCD muestra el mensaje "FUEGO!"** para alertar al usuario.

**Monitoreo en tiempo real:**
- La alerta y los datos del sensor se envían a la **interfaz web embebida**.
- El usuario puede visualizar en línea el estado del sistema y tomar decisiones inmediatas.


#### **2.4 Manejo de tareas concurrentes**


El código utiliza FreeRTOS para ejecutar tareas concurrentes en el ESP32. Se crea una tarea separada para la lectura de sensores (leerSensores()), la cual se ejecuta en un bucle infinito con un vTaskDelay(pdMS_TO_TICKS(200)) para no sobrecargar el procesador. Mientras tanto, el servidor web sigue operando en el loop principal (loop()), permitiendo que los usuarios interactúen sin interrupciones.









...

...
...
.

.
.
 
.
.
.
.
.



#### **2.5 Diagramas UML**
1. **Diagrama de Caso de Uso**: Describe la interacción entre los usuarios y el sistema
![.](imagenesWiki/uso.jpg)
![.](imagenesWiki/uso1.jpg)
   
2. **Diagrama de Clases**: Representación de la estructura del software.

   ![.](imagenesWiki/Diagrama.png)

3. **Diagrama de Secuencia**: Flujo de datos y eventos en el sistema.

   ![.](imagenesWiki/Diagrama2.png)

TIDAVIA NO
HILOS


#### **Esquemático de Hardware**

- Conexión de sensores al ESP32.
- Uso de resistencias pull-up para el DS18B20.
- Configuración del MQ-2 para detección de gases.

#### **Estándares de Diseño Aplicados**
- IEEE 802.11 para conectividad WiFi.
- Protocolo HTTP para comunicación web embebida.
- Normativas de seguridad eléctrica para conexiones de sensores.






















---

## **3. Configuración Experimental y Resultados**

### **Metodología de Pruebas**
1. **Prueba de Sensores**: Validación de datos de temperatura y detección de humo/gases en distintos entornos.
2. **Prueba del Servidor Web**: Accesibilidad desde diferentes dispositivos.
3. **Prueba de Alertas**: Activación de alarmas ante detección de condiciones anómalas.
4. **Simulación de Incendio**: Evaluación del tiempo de respuesta del sistema.

### **Resultados**
- Detección de aumentos bruscos de temperatura en tiempo real.
- Interfaz web funcional con actualización en vivo de datos.
- Alertas activadas exitosamente en condiciones críticas.

---

## **4. Autoevaluación del Protocolo de Pruebas**
- **Precisión**: Comparación de mediciones con instrumentos calibrados.
- **Fiabilidad**: Pruebas repetidas en diferentes condiciones ambientales.
- **Escalabilidad**: Capacidad de añadir más sensores sin comprometer rendimiento.

---

## **5. Explicación del Código**

### **Estructura del Código**
- **Bibliotecas utilizadas**: WiFi, WebServer, DallasTemperature, LiquidCrystal_I2C.
- **Variables y configuraciones**: Definición de pines, umbrales de detección.
- **Función de lectura de sensores**: Toma de datos y almacenamiento en buffer.
- **Servidor web embebido**: Página HTML y API REST para monitoreo remoto.
- **Manejo de alertas**: Activación de zumbador y LED RGB según condiciones detectadas.

---

## **6. Conclusiones y Trabajo Futuro**

### **Retos Presentados**
- Variabilidad en la lectura de sensores debido a interferencias ambientales.
- Optimización del servidor web para minimizar latencia.
- Consumo energético en aplicaciones de largo plazo.

### **Trabajo Futuro**
- Integración con sistemas de geolocalización para alertas más precisas.
- Uso de inteligencia artificial para predicción de incendios.
- Implementación de protocolos de comunicación alternativos como LoRa.

---

## **7. Seguridad y Fiabilidad**
- **Protección contra errores**: Validación de datos anómalos.
- **Redundancia de sensores**: Múltiples sensores para mejorar confiabilidad.
- **Seguridad de conexión**: Uso de autenticación en la red WiFi.

---

## **7. Anexos**
- **Código fuente documentado**
- **Esquemáticos y diagramas**
- **Material complementario**





## **8. Referencias**
 1:  E. R. Moraguez, "Ventajas y Desventajas ESP32 en IoT y Desarrollo," LovTechnology, 9 meses atrás. [En línea]. Disponible en: https://lovtechnology.com/ventajas-y-desventajas-esp32-en-iot-y-desarrollo/ (Accedido: 22-mar-2025)​
  2: Robotlandia, "Módulo KY-028 Sensor de Temperatura Digital", https://robotlandia.es/temperatura-y-humedad/681-modulo-ky-028-sensor-de-temperatura-digital.html (accedido: 16 de febrero de 2025).

 3: Julpin, "Módulo Sensor Analógico de Gas MQ-2 para Arduino", https://www.julpin.com.co/inicio/modulos-sensores/492-modulo-sensor-analogico-de-gas-mq-2-para-arduino.html (accedido: 16 de febrero de 2025).

 4: D Bots, "KY-026 Módulo Sensor de Llama", https://3dbots.co/producto/ky-026-modulo-sensor-de-llama/ (accedido: 16 de febrero de 2025).

 5: "Las fases de un incendio," Crónica Seguridad, 9 de julio de 2024. [En línea]. Disponible en: https://cronicaseguridad.com/2024/07/09/las-fases-de-un-incendio/. (Accedido: 22 de marzo de 2025)


