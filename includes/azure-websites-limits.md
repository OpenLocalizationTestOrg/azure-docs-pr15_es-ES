Recursos|Liberar|Compartido (vista preliminar)|Básico|Estándar|Premium (vista preliminar)</th>
---|---|---|---|---|---
[Web, móvil o aplicaciones de la API](https://azure.microsoft.com/services/app-service/) por [plan de aplicación de servicio](../articles/app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)<sup>1</sup>|10|100|Ilimitado<sup>2</sup>|Ilimitado<sup>2</sup>|Ilimitado<sup>2</sup>
[Aplicaciones de lógica](https://azure.microsoft.com/services/app-service/logic/) por [plan de servicios de aplicación](../articles/app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)</a><sup>1</sup>|10|10|10|20 por núcleo|20 por núcleo
[Plan de servicios de aplicación](../articles/app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)|1 por región|10 por grupo de recursos|100 por grupo de recursos|100 por grupo de recursos|100 por grupo de recursos
Calcular el tipo de instancia|Compartido|Compartido|Dedicada<sup>3</sup>|Dedicada<sup>3</sup>|Dedicada<sup>3</sup></p>
[Escalado](../articles/app-service-web/web-sites-scale.md) (instancias max)|1 compartido|1 compartido|3 dedicado<sup>3</sup>|10 dedicado<sup>3</sup>|20 dedicado (50 en ASE)<sup>3,4</sup>
Almacenamiento<sup>5</sup>|1 GB de memoria<sup>5</sup>|1 GB de memoria<sup>5</sup>|<sup>5</sup> de 10 GB|50 GB<sup>5</sup>|500 GB<sup>4,5</sup></p>
CPU hora (nombre corto)<sup>6</sup>|3 minutos|3 minutos|Ilimitado, pago a estándar [tasas](https://azure.microsoft.com/pricing/details/app-service/)</a>|Ilimitado, pago en tasas estándar|Ilimitado, pago en tasas estándar
CPU hora (días)<sup>6</sup>|60 minutos|240 minutos|Ilimitado, pago a estándar [tasas](https://azure.microsoft.com/pricing/details/app-service/)</a>|Ilimitado, pago en tasas estándar|Ilimitado, pago en tasas estándar
Memoria (1 hora)|1024 MB por plan de servicios de aplicación|1024 MB por aplicación|N/A.|N/A.|N/A.
Ancho de banda|165 MB|Ilimitado, aplicar [tasas de transferencia de datos](https://azure.microsoft.com/pricing/details/data-transfers/)|Ilimitado, aplicarán tasas de transferencia de datos|Ilimitado, aplicarán tasas de transferencia de datos|Ilimitado, aplicarán tasas de transferencia de datos
Arquitectura de la aplicación|versión de 32 bits|versión de 32 bits|32 bits o 64 bits|32 bits o 64 bits|32 bits o 64 bits
Web Sockets por instancia<sup>7</sup>|5|35|350|Ilimitado|Ilimitado
Simultáneas [conexiones depurador](../articles/app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md) por aplicación|1|1|1|5|5
[subdominio azurewebsites.NET con S/FTP y SSL](../articles/app-service-web/web-sites-configure-ssl-certificate.md)|X|X|X|X|X
Soporte técnico de [dominio personalizado](../articles/app-service-web/web-sites-custom-domain-name.md)||X|X|X|X
[Compatibilidad con SSL](../articles/app-service-web/web-sites-configure-ssl-certificate.md) de dominio personalizado|||Ilimitado|Ilimitado, 5 SSL SNI y conexiones de SSL IP 1 incluido|Ilimitado, 5 SSL SNI y conexiones de 1 IP SSL incluido
Equilibrador de carga integrado||X|X|X|X
[Siempre activado](../articles/app-service-web/web-sites-configure.md)|||X|X|X
[Copias de seguridad programadas](../articles/app-service-web/web-sites-backup.md)||||Una vez al día|Una vez cada 5 minutos<sup>8</sup>
[Autoescala](../articles/app-service-web/web-sites-scale.md)|||X|X|X
[WebJobs](../articles/app-service-web/web-sites-create-web-jobs.md) <sup>9</sup>|X|X|X|X|X
Soporte de [Programador de Azure](https://azure.microsoft.com/services/scheduler/)||X|X|X|X
[Extremo de supervisión](../articles/app-service-web/web-sites-monitor.md)|||X|X|X
[Ensayo ranuras (vista preliminar)](../articles/app-service-web/web-sites-staged-publishing.md)||||5|20
Dominios personalizados por aplicación</a>||500|500|500|500
SLA||<p>|99,9%|99,95%<sup>10</sup>|99,95%<sup>10</sup>

<sup>1</sup> Aplicaciones y las cuotas de almacenamiento están por plan de servicios de aplicación a menos que se indique lo contrario.  
<sup>2</sup> El número real de aplicaciones que puede hospedar en estos equipos depende de la actividad de las aplicaciones, el tamaño de las instancias de equipo y el uso de recursos correspondiente.  
<sup>3</sup> Instancias dedicadas pueden ser de diferentes tamaños. Para obtener más detalles, consulte [Precios de servicio de aplicación](https://azure.microsoft.com/pricing/details/data-transfers/pricing/details/app-service/) .  
<sup>4</sup> Nivel de Premium permite hasta 50 calcula instancias (sujeto a disponibilidad) y 500 GB de espacio en disco cuando se usa en entornos de aplicación de servicio y 20 calcular instancias y 250 GB de almacenamiento de lo contrario.  
<sup>5</sup> El límite de almacenamiento es el tamaño total del contenido en todas las aplicaciones en el mismo plan de servicio de aplicación. Más opciones de almacenamiento están disponibles en el [Entorno de aplicación de servicio](../articles/app-service-web/app-service-web-configure-an-app-service-environment.md#storage)  
<sup>6</sup> Estos recursos están limitados por los recursos físicos en las instancias dedicados (el tamaño de la instancia y el número de instancias).  
<sup>7</sup> Si escala una aplicación en el nivel básico a dos instancias, tiene 350 conexiones simultáneas para cada una de las dos instancias.  
<sup>8</sup> Nivel de Premium permite intervalos de copia de seguridad hacia abajo hasta cada 5 minutos mediante entornos de aplicación de servicio y 50 horas por día en caso contrario.  
<sup>9</sup> Ejecutar ejecutables personalizados y secuencias de comandos a petición, en una programación o continuamente como una tarea en segundo plano dentro de la instancia de servicio de aplicación. Siempre se requiere para la ejecución de WebJobs continua. Se requiere para WebJobs programada Azure programador libre o estándar. No hay ningún límite predefinido en el número de WebJobs que se pueden ejecutar en una instancia de servicio de aplicaciones, pero hay límites prácticos que dependen de lo que el código de la aplicación está intentando hacer.   
<sup>10</sup> SLA de 99,95% proporcionado para las implementaciones que usan varias instancias con el administrador del tráfico de Azure configurado para la migración tras error.  
