## <a name="load-balancer-differences"></a>Diferencias del equilibrador de carga

Existen diferentes opciones para distribuir el tráfico de red con Microsoft Azure. Estas opciones funcionan de forma diferente de otros, con una característica diferente establecer y escenarios diferentes. Cada pueden utilizarse por separado o combinarlas.

- **Equilibrador de carga de Azure** funciona en la capa de transporte (capa 4 en la pila de referencia de red OSI). Proporciona distribución de nivel de red del tráfico en varias instancias de una aplicación que se ejecuta en el mismo centro de datos de Azure.

- **Puerta de enlace de aplicación** funciona en el nivel de aplicación (nivel 7 en la pila de referencia de red OSI). Actúa como un servicio de proxy inverso terminar la conexión de cliente y reenviar solicitudes de extremos de back-end.

- **Administrador de tráfico** funciona en el nivel DNS.  Usa las respuestas de DNS para dirigir el tráfico de usuario final a los extremos de distribución global. A continuación, se conectan los clientes a esos extremos directamente.

La siguiente tabla resume las características de cada servicio:

| Servicio | Equilibrador de carga de Azure | Puerta de enlace de aplicación | Administrador de tráfico |
|---|---|---|---|
|Tecnología| Nivel de transporte (capa 4) | Nivel de la aplicación (nivel 7) | Nivel DNS |
| Protocolos de aplicación admitidos | Cualquier | HTTP y HTTPS |  Cualquiera (un extremo HTTP es necesario para la supervisión de extremo) |
| Extremos | Instancias de función Azure máquinas virtuales y servicios en la nube | Cualquier dirección IP interna de Azure o internet pública dirección IP | Máquinas virtuales de Azure, servicios de nube, aplicaciones Web de Azure y extremos externos |
| Soporte técnico de Vnet | Puede utilizar ambos opuestas e internos (Vnet) para aplicaciones de Internet | Puede utilizar ambos opuestas e internos (Vnet) para aplicaciones de Internet |    Solo es compatible con las aplicaciones a través de Internet |
Extremo de supervisión | Compatible a través de pruebas | Compatible a través de pruebas | Compatible a través de HTTP/HTTPS GET | 

El tráfico de Azure equilibrador de carga y la red de la ruta de puerta de enlace de aplicación a los extremos pero tienen diferentes escenarios de uso para que el tráfico a controlar. La siguiente tabla le ayuda a comprender la diferencia entre los equilibradores de dos carga:

| Tipo | Equilibrador de carga de Azure | Puerta de enlace de aplicación |
|---|---|---|
| Protocolos | TCP/UDP | HTTP / HTTPS |
| Reserva de IP | Compatibles | No compatible | 
| Modo de equilibrio de carga | 5-tuple(source IP, source port, destination IP, destination port, protocol type) | Turnos<br>Enrutamiento en función de la dirección URL | 
| Modo de equilibrio de carga (IP de origen o las sesiones) |  tupla de 2 (dirección IP de origen y destino IP), 3-tupla (dirección IP de origen, dirección IP de destino y puerto). Puede escalar hacia arriba o hacia abajo en función del número de máquinas virtuales | Afinidad basada en cookies<br>Enrutamiento en función de la dirección URL |
| Comprobaciones de estado | Predeterminado: intervalo de sondeo - 15 segundos. Sacar giro: 2 errores continuos. Es compatible con los sondeos definidas por el usuario | Intervalo de sondeo inactivo 30 segundos. Extrae después de un error de sondeo solo en modo inactivo o 5 errores consecutivos tráfico real. Es compatible con los sondeos definidas por el usuario | 
| Descarga de SSL | No compatible | Compatibles | 
  