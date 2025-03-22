# **Sistema IoT para Detección de Incendios en los Cerros Orientales de Bogotá**

## **1. Resumen General**

### **Motivación y Justificación**
Los cerros orientales de Bogotá son una zona ecológica de gran importancia, pero altamente susceptible a incendios forestales debido a la sequía y actividad humana. La detección temprana de incendios es clave para minimizar daños ambientales y proteger comunidades cercanas. Para ello, se requiere un sistema de monitoreo en tiempo real que permita detectar cambios bruscos en temperatura, presencia de humo y emisión de gases característicos de la combustión.

### **Estructura de la Documentación**
1. **Solución propuesta**: Restricciones, arquitectura, desarrollo teórico y estándares aplicados.
2. **Configuración experimental y resultados**: Validación del sistema en condiciones controladas.
3. **Autoevaluación del protocolo de pruebas**: Verificación de confiabilidad y precisión.
4. **Conclusiones y trabajo futuro**: Desafíos enfrentados y mejoras futuras.
5. **Anexos**: Código fuente, esquemáticos y documentación adicional.

---

## **2. Solución Propuesta**

### **Restricciones de Diseño**
- **Técnicas**: Uso de ESP32 como sistema embebido, sensores DS18B20 (temperatura), MQ-2 (humo y gases) y módulo WiFi integrado.
- **Económicas**: Uso de hardware asequible y componentes de bajo consumo energético.
- **Regulatorias**: Cumplimiento de normativas de emisiones y transmisión de datos.
- **Espacio y Escalabilidad**: Diseño compacto y modular con posibilidad de expansión.
- **Temporales**: Recolección de datos en intervalos de 1 segundo para garantizar una respuesta rápida ante incendios.

### **Arquitectura Propuesta**

#### **Diagrama de Bloques (Hardware y Software)**
- **Sensores**: DS18B20 (temperatura), MQ-2 (humo/gases)
- **Microcontrolador**: ESP32
- **Comunicación**: Servidor Web embebido en ESP32
- **Interfaz**: Tablero de control accesible vía navegador
- **Alertas**: Notificaciones visuales y sonoras in situ

### **Desarrollo Teórico Modular**

#### **Diagramas UML**
1. **Diagrama de Caso de Uso**: Describe la interacción entre los usuarios y el sistema.
2. **Diagrama de Clases**: Representación de la estructura del software.
3. **Diagrama de Secuencia**: Flujo de datos y eventos en el sistema.

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

## **8. Anexos**
- **Código fuente documentado**
- **Esquemáticos y diagramas**
- **Material complementario**

---

## **9. Video Demostrativo**
Se incluirá un video de máximo 5 minutos explicando la validación del prototipo funcional.


