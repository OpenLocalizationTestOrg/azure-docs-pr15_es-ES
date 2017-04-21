<properties 
    pageTitle="Análisis de secuencia: Girar las credenciales de registro para entradas y salidas | Microsoft Azure" 
    description="Obtenga información sobre cómo actualizar las credenciales para el análisis de secuencia entradas y salidas."
    keywords="credenciales de inicio de sesión"
    services="stream-analytics" 
    documentationCenter="" 
    authors="jeffstokes72" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="stream-analytics" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="data-services" 
    ms.date="09/26/2016" 
    ms.author="jeffstok"/>

#<a name="rotate-login-credentials-for-inputs-and-outputs-in-stream-analytics-jobs"></a>Girar las credenciales de inicio de sesión para entradas y los resultados de los trabajos de análisis de secuencia

##<a name="abstract"></a>Resumen
Análisis de secuencia de Azure hoy no permite reemplazar las credenciales en una entrada y salida mientras se está ejecutando el trabajo.

Mientras el análisis de secuencia de Azure admiten reanudar un trabajo desde el último resultado, queremos compartir todo el proceso para minimizar el retraso entre la detención e inicial de la tarea y girar las credenciales de inicio de sesión.

##<a name="part-1---prepare-the-new-set-of-credentials"></a>Parte 1: preparar el nuevo conjunto de credenciales:
Este elemento es aplicable a las siguientes entradas/salidas:

* Almacenamiento de blobs
* Evento Hubs
* Base de datos SQL
* Almacenamiento de tablas

Para otras entradas/salidas, continúe con parte 2.

###<a name="blob-storagetable-storage"></a>Almacenamiento de blobs de almacenamiento o tabla
1.  Vaya a la extensión de almacenamiento en el portal de administración de Azure:  
![Gráfico1][graphic1]
2.  Busque el almacenamiento de su trabajo y vaya a ella:  
![Gráfico2][graphic2]
3.  Haga clic en el comando administrar las teclas de acceso:  
![graphic3][graphic3]
4.  Entre la clave de acceso principal y secundario tecla de acceso, **Elija el no utilizado su trabajo**.
5.  Visita regenerar:  
![graphic4][graphic4]
6.  Copie la clave recién generada:  
![graphic5][graphic5]
7.  Vaya a la parte 2.

###<a name="event-hubs"></a>Hubs de evento
1.  Vaya a la extensión de Bus de servicio en el portal de administración de Azure:  
![graphic6][graphic6]
2.  Busque el Namespace de Bus de servicio utilizando su trabajo y vaya a ella:  
![graphic7][graphic7]
3.  Si su trabajo utiliza una directiva de acceso compartido en el Namespace de Bus de servicio, salte al paso 6  
4.  Vaya a la pestaña evento Hubs:  
![graphic8][graphic8]
5.  Busque el Hub de evento utilizando su trabajo y vaya en él:  
![graphic9][graphic9]
6.  Vaya a la ficha configurar:  
![graphic10][graphic10]
7.  En la lista desplegable Nombre de la directiva, busque la directiva de acceso compartido utilizada por su trabajo:  
![graphic11][graphic11]
8.  Entre la clave principal y la clave secundaria, **Elija el no utilizado su trabajo**.  
9.  Visita regenerar:  
![graphic12][graphic12]
10. Copie la clave recién generada:  
![graphic13][graphic13]
11. Vaya a la parte 2.  

###<a name="sql-database"></a>Base de datos SQL

>[AZURE.NOTE] Nota: debe conectar con el servicio de base de datos de SQL. Vamos a mostrar cómo hacer esto mediante la experiencia de administración en el portal de administración de Azure, pero puede usar algunas herramientas de cliente, como SQL Server Management Studio.

1.  Vaya a la extensión de bases de datos SQL en el portal de administración de Azure:  
![graphic14][graphic14]
2.  Busque la base de datos de SQL que utiliza el vínculo de tarea y **haga clic en el servidor** en la misma línea:  
![graphic15][graphic15]
3.  Haga clic en el comando administrar:  
![graphic16][graphic16]
4.  Tipo de base de datos de patrón:  
![graphic17][graphic17]
5.  Escriba su nombre de usuario, contraseña y haga clic en el registro en:  
![graphic18][graphic18]
6.  Haga clic en nueva consulta:  
![graphic19][graphic19]
7.  Escriba la siguiente consulta reemplazando < login_name > con su nombre de usuario y reemplazar <enterStrongPasswordHere> con la nueva contraseña:  
`CREATE LOGIN <login_name> WITH PASSWORD = '<enterStrongPasswordHere>'`
8.  Haga clic en ejecutar:  
![graphic20][graphic20]
9.  Volver al paso 2 y esta vez haga clic en la base de datos:  
![graphic21][graphic21]
10. Haga clic en el comando administrar:  
![graphic22][graphic22]
11. Escriba su nombre de usuario, contraseña y haga clic en Inicio de sesión:  
![graphic23][graphic23]
12. Haga clic en nueva consulta:  
![graphic24][graphic24]
13. Escriba la siguiente consulta reemplazando < nombreDeUsuario > con un nombre que desea identificar este inicio de sesión en el contexto de esta base de datos (puede proporcionar el mismo valor que proporcionó para < login_name >, por ejemplo) y reemplazar < login_name > con el nuevo nombre de usuario:  
`CREATE USER <user_name> FROM LOGIN <login_name>`
14. Haga clic en ejecutar:  
![graphic25][graphic25]
15. Ahora debe proporcionar el nuevo usuario con el mismo roles y privilegios que el usuario original.
16. Vaya a la parte 2.

##<a name="part-2-stopping-the-stream-analytics-job"></a>Parte 2: Detener el trabajo de análisis de secuencia
1.  Vaya a la extensión de análisis de secuencia en el portal de administración de Azure:  
![graphic26][graphic26]
2.  Busque su trabajo y vaya a ella:  
![graphic27][graphic27]
3.  Vaya a la pestaña entradas o salidas en función de si desea girar las credenciales de entrada o de un resultado.  
![graphic28][graphic28]
4.  Haga clic en el comando Detener y confirmar que ha dejado el trabajo:  
![graphic29][graphic29] esperar a detener el trabajo.
5.  Busque la entrada y salida que desea girar credenciales en y vaya a ella:  
![graphic30][graphic30]
6.  Vaya a la parte 3.

##<a name="part-3-editing-the-credentials-on-the-stream-analytics-job"></a>Parte 3: Modificar las credenciales en el trabajo de análisis de secuencia

###<a name="blob-storagetable-storage"></a>Almacenamiento de blobs de almacenamiento o tabla
1.  Busque el campo de clave de cuenta de almacenamiento y pegue la clave generada recientemente:  
![graphic31][graphic31]
2.  Haga clic en el comando Guardar y confirme guardar los cambios:  
![graphic32][graphic32]
3.  Una prueba de la conexión se iniciará automáticamente cuando guarda los cambios, es decir Asegúrese de que ha pasado correctamente.
4.  Vaya a la parte 4.

###<a name="event-hubs"></a>Hubs de evento
1.  Busque el campo de clave de directiva de concentrador de evento y pegue la clave generada recientemente:  
![graphic33][graphic33]
2.  Haga clic en el comando Guardar y confirme guardar los cambios:  
![graphic34][graphic34]
3.  Una prueba de la conexión se iniciará automáticamente cuando guarda los cambios, asegúrese de que ha pasado correctamente.
4.  Vaya a la parte 4.

###<a name="power-bi"></a>Power BI
1.  Haga clic en la autorización de renovar:  
* ![graphic35][graphic35]
* Obtendrá la confirmación siguiente:  
* ![graphic36][graphic36]
2.  Haga clic en el comando Guardar y confirme guardar los cambios:  
![graphic37][graphic37]
3.  Una prueba de la conexión se iniciará automáticamente cuando guarda los cambios, asegúrese de que ha pasado correctamente.
4.  Vaya a la parte 4.

###<a name="sql-database"></a>Base de datos SQL
1.  Buscar los campos nombre de usuario y contraseña y pegue el conjunto de credenciales recién creado:  
![graphic38][graphic38]
2.  Haga clic en el comando Guardar y confirme guardar los cambios:  
![graphic39][graphic39]
3.  Una prueba de la conexión se iniciará automáticamente cuando guarda los cambios, asegúrese de que ha pasado correctamente.  
4.  Vaya a la parte 4.

##<a name="part-4-starting-your-job-from-last-stopped-time"></a>Parte 4: Iniciar el trabajo desde la última vez que ha dejado de
1.  Sale de la entrada y salida:  
![graphic40][graphic40]
2.  Haga clic en el comando de inicio:  
![graphic41][graphic41]
3.  Elija la última vez detenido y haga clic en Aceptar:  
 ![graphic42][graphic42]
4.  Vaya a la parte 5.  

##<a name="part-5-removing-the-old-set-of-credentials"></a>Parte 5: Quitar el antiguo conjunto de credenciales
Este elemento es aplicable a las siguientes entradas/salidas:
* Almacenamiento de blobs
* Evento Hubs
* Base de datos SQL
* Almacenamiento de tablas

###<a name="blob-storagetable-storage"></a>Almacenamiento de blobs de almacenamiento o tabla
Repita parte 1 de la tecla de acceso que se ha usado anteriormente por su trabajo para renovar la clave de acceso sin usar ahora.

###<a name="event-hubs"></a>Hubs de evento
Repita parte 1 para la clave que se usó previamente por su trabajo para renovar la clave ahora sin usar.

###<a name="sql-database"></a>Base de datos SQL
1.  Volver a la ventana de consulta de parte 1 paso 7 y escriba la siguiente consulta, reemplazando < previous_login_name > con el nombre de usuario que se ha usado anteriormente por su trabajo:  
`DROP LOGIN <previous_login_name>`  
2.  Haga clic en ejecutar:  
    ![graphic43][graphic43]  

Debe obtener la confirmación siguiente: 

    Command(s) completed successfully.

## <a name="get-help"></a>Obtener ayuda
Para obtener más ayuda, pruebe nuestro [foro de análisis de secuencia de Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Pasos siguientes

- [Introducción a análisis de secuencia de Azure](stream-analytics-introduction.md)
- [Empezar a usar el análisis de secuencia de Azure](stream-analytics-get-started.md)
- [Escala trabajos de análisis de secuencia de Azure](stream-analytics-scale-jobs.md)
- [Referencia del lenguaje de consulta de análisis de secuencia de Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referencia de la API de REST de administración de secuencia de Azure análisis](https://msdn.microsoft.com/library/azure/dn835031.aspx)


[graphic1]: ./media/stream-analytics-login-credentials-inputs-outputs/1-stream-analytics-login-credentials-inputs-outputs.png
[graphic2]: ./media/stream-analytics-login-credentials-inputs-outputs/2-stream-analytics-login-credentials-inputs-outputs.png
[graphic3]: ./media/stream-analytics-login-credentials-inputs-outputs/3-stream-analytics-login-credentials-inputs-outputs.png
[graphic4]: ./media/stream-analytics-login-credentials-inputs-outputs/4-stream-analytics-login-credentials-inputs-outputs.png
[graphic5]: ./media/stream-analytics-login-credentials-inputs-outputs/5-stream-analytics-login-credentials-inputs-outputs.png
[graphic6]: ./media/stream-analytics-login-credentials-inputs-outputs/6-stream-analytics-login-credentials-inputs-outputs.png
[graphic7]: ./media/stream-analytics-login-credentials-inputs-outputs/7-stream-analytics-login-credentials-inputs-outputs.png
[graphic8]: ./media/stream-analytics-login-credentials-inputs-outputs/8-stream-analytics-login-credentials-inputs-outputs.png
[graphic9]: ./media/stream-analytics-login-credentials-inputs-outputs/9-stream-analytics-login-credentials-inputs-outputs.png
[graphic10]: ./media/stream-analytics-login-credentials-inputs-outputs/10-stream-analytics-login-credentials-inputs-outputs.png
[graphic11]: ./media/stream-analytics-login-credentials-inputs-outputs/11-stream-analytics-login-credentials-inputs-outputs.png
[graphic12]: ./media/stream-analytics-login-credentials-inputs-outputs/12-stream-analytics-login-credentials-inputs-outputs.png
[graphic13]: ./media/stream-analytics-login-credentials-inputs-outputs/13-stream-analytics-login-credentials-inputs-outputs.png
[graphic14]: ./media/stream-analytics-login-credentials-inputs-outputs/14-stream-analytics-login-credentials-inputs-outputs.png
[graphic15]: ./media/stream-analytics-login-credentials-inputs-outputs/15-stream-analytics-login-credentials-inputs-outputs.png
[graphic16]: ./media/stream-analytics-login-credentials-inputs-outputs/16-stream-analytics-login-credentials-inputs-outputs.png
[graphic17]: ./media/stream-analytics-login-credentials-inputs-outputs/17-stream-analytics-login-credentials-inputs-outputs.png
[graphic18]: ./media/stream-analytics-login-credentials-inputs-outputs/18-stream-analytics-login-credentials-inputs-outputs.png
[graphic19]: ./media/stream-analytics-login-credentials-inputs-outputs/19-stream-analytics-login-credentials-inputs-outputs.png
[graphic20]: ./media/stream-analytics-login-credentials-inputs-outputs/20-stream-analytics-login-credentials-inputs-outputs.png
[graphic21]: ./media/stream-analytics-login-credentials-inputs-outputs/21-stream-analytics-login-credentials-inputs-outputs.png
[graphic22]: ./media/stream-analytics-login-credentials-inputs-outputs/22-stream-analytics-login-credentials-inputs-outputs.png
[graphic23]: ./media/stream-analytics-login-credentials-inputs-outputs/23-stream-analytics-login-credentials-inputs-outputs.png
[graphic24]: ./media/stream-analytics-login-credentials-inputs-outputs/24-stream-analytics-login-credentials-inputs-outputs.png
[graphic25]: ./media/stream-analytics-login-credentials-inputs-outputs/25-stream-analytics-login-credentials-inputs-outputs.png
[graphic26]: ./media/stream-analytics-login-credentials-inputs-outputs/26-stream-analytics-login-credentials-inputs-outputs.png
[graphic27]: ./media/stream-analytics-login-credentials-inputs-outputs/27-stream-analytics-login-credentials-inputs-outputs.png
[graphic28]: ./media/stream-analytics-login-credentials-inputs-outputs/28-stream-analytics-login-credentials-inputs-outputs.png
[graphic29]: ./media/stream-analytics-login-credentials-inputs-outputs/29-stream-analytics-login-credentials-inputs-outputs.png
[graphic30]: ./media/stream-analytics-login-credentials-inputs-outputs/30-stream-analytics-login-credentials-inputs-outputs.png
[graphic31]: ./media/stream-analytics-login-credentials-inputs-outputs/31-stream-analytics-login-credentials-inputs-outputs.png
[graphic32]: ./media/stream-analytics-login-credentials-inputs-outputs/32-stream-analytics-login-credentials-inputs-outputs.png
[graphic33]: ./media/stream-analytics-login-credentials-inputs-outputs/33-stream-analytics-login-credentials-inputs-outputs.png
[graphic34]: ./media/stream-analytics-login-credentials-inputs-outputs/34-stream-analytics-login-credentials-inputs-outputs.png
[graphic35]: ./media/stream-analytics-login-credentials-inputs-outputs/35-stream-analytics-login-credentials-inputs-outputs.png
[graphic36]: ./media/stream-analytics-login-credentials-inputs-outputs/36-stream-analytics-login-credentials-inputs-outputs.png
[graphic37]: ./media/stream-analytics-login-credentials-inputs-outputs/37-stream-analytics-login-credentials-inputs-outputs.png
[graphic38]: ./media/stream-analytics-login-credentials-inputs-outputs/38-stream-analytics-login-credentials-inputs-outputs.png
[graphic39]: ./media/stream-analytics-login-credentials-inputs-outputs/39-stream-analytics-login-credentials-inputs-outputs.png
[graphic40]: ./media/stream-analytics-login-credentials-inputs-outputs/40-stream-analytics-login-credentials-inputs-outputs.png
[graphic41]: ./media/stream-analytics-login-credentials-inputs-outputs/41-stream-analytics-login-credentials-inputs-outputs.png
[graphic42]: ./media/stream-analytics-login-credentials-inputs-outputs/42-stream-analytics-login-credentials-inputs-outputs.png
[graphic43]: ./media/stream-analytics-login-credentials-inputs-outputs/43-stream-analytics-login-credentials-inputs-outputs.png
 
