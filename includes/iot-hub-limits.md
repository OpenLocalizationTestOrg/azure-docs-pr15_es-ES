La siguiente tabla enumeran los límites de los niveles de servicio diferente (S1, S2, S3, F1). Para obtener información sobre el coste de cada *unidad* en cada nivel, consulte [IoT Hub de precios](https://azure.microsoft.com/pricing/details/iot-hub/).

| Recursos | S1 estándar | S2 estándar | S3 estándar | F1 libre |
| -------- | ----------- | ----------- | ----------- | ------- |
| Mensajes por día | 400.000 | 6,000,000   | 300,000,000 | 8.000   |
| Capacidad máxima | 200    | 200         | 200         | 1       |

> [AZURE.NOTE] Si piensa usar más de 200 unidades con un concentrador de nivel S1 o S2 o S3, póngase en contacto con el soporte técnico de Microsoft.

La siguiente tabla enumera los límites que se aplican a recursos de concentrador IoT:

| Recursos | Límite |
| -------- | ----- |
| Máximo IoT hubs por Azure suscripción de pago | 10 |
| Máximos hubs IoT gratuitas por suscripción de Azure | 1 |
| Número máximo de identidades de dispositivo<br/>  devuelto en una sola llamada | 1000 |
| Concentrador IoT mensaje máximo de retención para los mensajes de la nube de dispositivo | 7 días |
| Tamaño máximo de mensaje de nube de dispositivo | 256 KB |
| Tamaño máximo del lote de nube de dispositivo | 256 KB |
| Número máximo de mensajes en el lote de nube de dispositivo | 500 |
| Tamaño máximo de mensaje de dispositivo de nube | 64 KB |
| TTL máximo para los mensajes de dispositivo de nube | 2 días |
| Recuento de entrega máximo de nube al dispositivo <br/> mensajes | 100 |
| Número máximo de entrega para mensajes de comentarios <br/> en respuesta a un mensaje de dispositivo de nube | 100 |
| TTL máximo para los mensajes de comentarios en <br/> respuesta a un mensaje de dispositivo de nube | 2 días |

> [AZURE.NOTE] Si necesita más de 10 hubs IoT pagadas con una suscripción de Azure, póngase en contacto con el soporte técnico de Microsoft.

El servicio de concentrador IoT limita las solicitudes cuando se superan las cuotas siguientes:

| Acelerador | Valor por concentrador |
| -------- | ------------- |
| Operaciones de registro de identidad <br/> (crear, recuperar, lista, actualizar, eliminar) <br/> importar o exportar individual o masiva | 5000/min/unidad (para S3) <br/> 100/min/unidad (para S1 y S2). |
| Conexiones de dispositivo | sec/6000/unidad (para S3), 120/sec/unidad (S2), s/12/unidad (para S1). <br/>Mínimo de 100/seg. |
| Envía el dispositivo a la nube | sec/6000/unidad (para S3), 120/sec/unidad (S2), s/12/unidad (para S1). <br/>Mínimo de 100/seg. |
| Dispositivo de nube envía | 5000/min/unidad (para S3), 100/min /: unidad (para S1 y S2). |
| Recibe el dispositivo de nube | 50000/min/unidad (para S3), 1000/min /: unidad (para S1 y S2). |
| Operaciones de carga de archivos | carga de archivos de 5000 min/notificaciones/unidad (para S3), carga de archivo 100 notificaciones/min/unidad (para S1 y S2). <br/> URI de SA 10000 puede ser por para una cuenta de Azure almacenamiento a la vez.<br/> 10 SA URI o dispositivo puede ser por a la vez. |
