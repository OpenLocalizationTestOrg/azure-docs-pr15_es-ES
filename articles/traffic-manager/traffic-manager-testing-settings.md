<properties
    pageTitle="Probar la configuración de administrador de tráfico | Microsoft Azure"
    description="En este artículo le ayudará a probar la configuración del Administrador de tráfico"
    services="traffic-manager"
    documentationCenter=""
    authors="sdwheeler"
    manager="carmonm"
    editor=""
/>
<tags
    ms.service="traffic-manager"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="10/11/2016"
    ms.author="sewhee"
/>

# <a name="test-your-traffic-manager-settings"></a>Pruebe su configuración de administrador de tráfico

Para probar la configuración del Administrador de tráfico, debe tener varios clientes, en varias ubicaciones, desde donde puede ejecutar las pruebas. A continuación, visualice los extremos en el perfil de administrador de tráfico hacia abajo de uno en uno.

* Establezca el valor de TTL de DNS bajo para que los cambios se propagarán rápidamente (por ejemplo, 30 segundos).
* Conocer las direcciones IP de los servicios de nube de Azure y los sitios Web en el perfil que está probando.
* Use herramientas que le permiten resolver un nombre DNS a una dirección IP y mostrar esa dirección.

Revisa para ver que resolverán los nombres DNS a direcciones IP de los extremos del perfil. Deberían resolver los nombres de manera coherente con el método de enrutamiento de tráfico definido en el perfil de administrador de tráfico. Puede usar las herramientas como **nslookup** o **profundizar** para resolver los nombres DNS.

Los ejemplos siguientes ayudan a probar el perfil de administrador de tráfico.

### <a name="check-traffic-manager-profile-using-nslookup-and-ipconfig-in-windows"></a>Compruebe el perfil de administrador de tráfico con nslookup y ipconfig en Windows

1. Abra un comando o el símbolo de Windows PowerShell como administrador.
2. Tipo de `ipconfig /flushdns` para vaciar la caché del solucionador DNS.
3. Tipo de `nslookup <your Traffic Manager domain name>`. Por ejemplo, el comando siguiente comprueba el nombre de dominio con el prefijo *myapp.contoso*

        nslookup myapp.contoso.trafficmanager.net

    Un resultado típico muestra la siguiente información:

    * El nombre DNS y la dirección IP del servidor DNS que se tiene acceso para resolver este nombre de dominio de administrador de tráfico.
    * El nombre de dominio de administrador de tráfico que escribió en la línea de comandos después de "nslookup" y la dirección IP a la que se resuelve el dominio del Administrador de tráfico. La segunda dirección IP es la importante para comprobar. Debe coincidir con una dirección IP (VIP) virtual pública para uno de los servicios de nube o los sitios Web en el perfil de administrador de tráfico que está probando.

## <a name="how-to-test-the-failover-traffic-routing-method"></a>Cómo probar el método de enrutamiento de tráfico de migración tras error

1. Deje todos los extremos hacia arriba.
2. Con un solo cliente, solicitar la resolución DNS para el nombre de dominio de la empresa con nslookup o una utilidad similar.
3. Asegúrese de que la dirección IP resuelta coincide con el extremo principal.
4. Traer hacia abajo el extremo principal o quitar el archivo de seguimiento para que el Administrador de tráfico considera que la aplicación está hacia abajo.
5. Espere a que el DNS Time-to-Live (TTL) del perfil de administrador de tráfico más otros dos minutos. Por ejemplo, si el TTL DNS es 300 segundos (5 minutos), debe esperar siete minutos.
6. Vacíe su DNS cliente caché y solicitud de resolución DNS mediante nslookup. En Windows, puede vaciar la caché DNS con el comando ipconfig /flushdns.
7. Asegúrese de que la dirección IP resuelta coincide con el extremo secundario.
8. Repita el proceso, interrumpir cada extremo por separado. Compruebe que el sistema DNS devuelve la dirección IP del extremo siguiente en la lista. Cuando todos los extremos hacia abajo, debe obtener la dirección IP del extremo principal nuevo.

## <a name="how-to-test-the-weighted-traffic-routing-method"></a>Cómo probar el método de enrutamiento de tráfico ponderada

1. Deje todos los extremos hacia arriba.
2. Con un solo cliente, solicitar la resolución DNS para el nombre de dominio de la empresa con nslookup o una utilidad similar.
3. Asegúrese de que la dirección IP resuelta coincide con uno de sus extremos.
4. Vaciar la caché del cliente DNS y repita los pasos 2 y 3 para cada extremo. Ahora debe aparecer direcciones IP diferentes devueltos para cada uno de sus extremos.

## <a name="how-to-test-the-performance-traffic-routing-method"></a>Cómo probar el método de enrutamiento de tráfico de rendimiento

Para probar eficazmente un método de enrutamiento de tráfico de rendimiento, debe tener los clientes que se encuentran en distintas partes del mundo. Puede crear a los clientes en diferentes regiones Azure que se pueden usar para probar sus servicios. Si tiene una red global, puede iniciar sesión en clientes de otras partes del mundo remotamente y ejecutar las pruebas desde allí.

Como alternativa, hay libre basada en web de búsqueda DNS y profundizar los servicios disponibles. Algunas de estas herramientas le permiten comprobar la resolución de nombres DNS desde diversas ubicaciones del mundo. Realizar una búsqueda de "Búsqueda DNS" para obtener ejemplos. Servicios de terceros como Gomez o denominado pueden utilizarse para confirmar que los perfiles distribuye el tráfico según lo esperado.

## <a name="next-steps"></a>Pasos siguientes

* [Acerca de los métodos de enrutamiento de tráfico de tráfico administrador](traffic-manager-routing-methods.md)
* [Consideraciones de rendimiento del Administrador de tráfico](traffic-manager-performance-considerations.md)
* [Estado de degradado de solución de problemas de tráfico de administrador](traffic-manager-troubleshooting-degraded.md)




