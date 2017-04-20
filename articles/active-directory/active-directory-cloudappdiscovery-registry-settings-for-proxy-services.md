<properties 
    pageTitle="Configuración de registro de detección de la aplicación de servicios de Proxy de nube | Microsoft Azure" 
    description="El objetivo de este tema es para proporcionarle los pasos que debe llevar a cabo para configurar el puerto necesario en los equipos que ejecutan al agente de descubrimiento de aplicación de nube." 
    services="active-directory" 
    documentationCenter="" 
    authors="markusvi" 
    manager="femila"/>

<tags 
    ms.service="active-directory" 
    ms.workload="identity" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/10/2016" 
    ms.author="markusvi"/>

# <a name="cloud-app-discovery-registry-settings-for-proxy-services"></a>Configuración del registro de detección de la aplicación de nube para los servicios de Proxy

De forma predeterminada, el agente de descubrimiento de aplicación de nube está configurado para utilizar sólo los puertos 80 o 443. Si planea instalar detección de aplicación de nube en un entorno con un servidor proxy que está usando un puerto personalizado (ni 80 ni 443), debe configurar los agentes para utilizar este puerto. La configuración se basa en una clave del registro.


El objetivo de este tema es para proporcionarle los pasos que debe llevar a cabo para configurar el puerto necesario en los equipos que ejecutan al agente de descubrimiento de aplicación de nube.



**Para modificar el puerto usado por el equipo que ejecuta al agente de descubrimiento de aplicación de nube, realice los pasos siguientes:**


1. Iniciar el editor del registro. <br> ![Ejecutar](./media/active-directory-cloudappdiscovery-registry-settings-for-proxy-services/proxy01.png)

2. Vaya a o crear la clave del registro siguiente: <br> **HKLM_LOCAL_MACHINE\Software\Microsoft\Cloud aplicación Discovery\Endpoint** 

3. Crear un nuevo valor de **cadena múltiple** llamado **puertos**. ![Nuevo](./media/active-directory-cloudappdiscovery-registry-settings-for-proxy-services/proxy02.png)

4. Para abrir el cuadro de diálogo **Modificar cadenas múltiples** , haga doble clic en el valor de puertos.


5. En el cuadro de texto de datos de valor, escriba los valores siguientes y agregar todos los puertos personalizados que se usan en su organización: <br><br>
**80** <br>
**8080** <br>
**8118** <br>
**8888** <br>
**81** <br>
**12080** <br>
**6999** <br>
**30606** <br>
**31595** <br>
**4080** <br>
**443** <br>
**1110** <br><br>
![Editar cadenas múltiples](./media/active-directory-cloudappdiscovery-registry-settings-for-proxy-services/proxy03.png)

6. Haga clic en **Aceptar** para cerrar el cuadro de diálogo **Modificar cadenas múltiples** .



**Recursos adicionales**


* [¿Cómo puedo descubrir aplicaciones no sancionada nube que se utilizan dentro de mi organización](active-directory-cloudappdiscovery-whatis.md) 


