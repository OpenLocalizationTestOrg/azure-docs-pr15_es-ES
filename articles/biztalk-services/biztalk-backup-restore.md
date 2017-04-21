<properties 
    pageTitle="Crear y restaurar una copia de seguridad en servicios de BizTalk | Microsoft Azure" 
    description="Servicios de BizTalk incluye copia de seguridad y restauración. Obtenga información sobre cómo crear y restaurar una copia de seguridad y determinar qué obtiene copia. MABS, WABS" 
    services="biztalk-services" 
    documentationCenter="" 
    authors="MandiOhlinger" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="biztalk-services" 
    ms.workload="integration" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/15/2016" 
    ms.author="mandia"/>


# <a name="biztalk-services-backup-and-restore"></a>Servicios de BizTalk: Copia de seguridad y restauración

Servicios de BizTalk Azure incluye capacidades de copia de seguridad y restauración. Este tema describe cómo hacer copia de seguridad y restaurar los servicios de BizTalk con el portal de clásico de Azure.

También puede hacer los servicios de BizTalk con la [API de REST de servicios de BizTalk](http://go.microsoft.com/fwlink/p/?LinkID=325584). 

> [AZURE.NOTE] Conexiones de híbrido no son copia, independientemente de la edición. Debe volver a crear las conexiones híbrido.

## <a name="before-you-begin"></a>Antes de comenzar

- Copia de seguridad y restauración no esté disponible para todas las ediciones. Consulte [servicios BizTalk: gráfico de ediciones](biztalk-editions-feature-chart.md).

- Con el portal de clásico Azure, puede crear una copia de seguridad a petición o crear una copia de seguridad programada. 

- Contenido de copia de seguridad puede restaurarse a la misma BizTalk Service o a un nuevo BizTalk Service. Para restaurar el BizTalk Service con el mismo nombre, debe eliminar la BizTalk Service existente y el nombre debe estar disponible. Después de eliminar un BizTalk Service, puede llevar más tiempo del deseado para que el mismo nombre esté disponible. Si no puede esperar a que el mismo nombre esté disponible, a continuación, restaure una BizTalk Service nuevo.

- Servicios de BizTalk se pueden restaurar a la misma edición o una edición superior. Restaurar los servicios de BizTalk en una edición anterior, de cuando se realizó la copia de seguridad, no es compatible.

    Por ejemplo, se puede restaurar una copia de seguridad con la edición básica para la edición Premium. No se puede restaurar una copia de seguridad con la edición Premium a Standard Edition.

- Se realizan copias de seguridad de los números de Control de EDI para mantener la continuidad de los números de control. Si los mensajes se procesan después de la última copia de seguridad, restaurar el contenido de la copia de seguridad puede provocar números de control duplicados.

- Si un lote tiene mensajes activos, procesar el lote de **antes de** ejecutar una copia de seguridad. Al crear una copia de seguridad (según sea necesario o programada), nunca se almacenan mensajes en lotes. 

    **Si no se realiza una copia de seguridad con activos mensajes en un lote, estos mensajes no se realizan copias de seguridad y, por tanto, se pierden.**

- Opcional: En el Portal de servicios de BizTalk, detenga las operaciones de administración.


## <a name="create-a-backup"></a>Crear una copia de seguridad

Una copia de seguridad puede realizarse en cualquier momento y completamente está controlado por el usuario. Esta sección enumeran los pasos para crear copias de seguridad con el portal clásico Azure, incluidos:

[En la copia de seguridad de petición](#backupnow)

[Programar una copia de seguridad](#backupschedule)

#### <a name="backupnow"></a>En la copia de seguridad de petición
1. En el portal de clásico Azure, seleccione **Servicios de BizTalk**y, a continuación, seleccione el BizTalk Service que desea hacer copia de seguridad.
2. En la pestaña **panel** , seleccione **hacer copia de seguridad** en la parte inferior de la página.
3. Escriba un nombre de copia de seguridad. Por ejemplo, escriba *myBizTalkService*BU*fecha*.
4. Elija una cuenta de almacenamiento de blobs de Windows y seleccione la marca de verificación para iniciar la copia de seguridad.

Una vez completada la copia de seguridad, se crea un contenedor con el nombre de copia de seguridad de la cuenta de almacenamiento. Este contenedor contiene la configuración de copia de seguridad de BizTalk Service.

#### <a name="backupschedule"></a>Programar una copia de seguridad

1. En el portal de clásico Azure, seleccione los **Servicios de BizTalk**, seleccione el nombre de BizTalk Service que desea programar la copia de seguridad y, a continuación, seleccione la ficha **Configurar** .
2. Establecer el **estado de copia de seguridad** en **automático**. 
3. Seleccione la **Cuenta de almacenamiento** para almacenar la copia de seguridad, especifique la **frecuencia** para crear las copias de seguridad y cuánto tiempo para mantener las copias de seguridad (**Días de retención**):

    ![][AutomaticBU]

    **Notas**   
    - En **Días de retención**, el período de retención debe ser mayor que la frecuencia de copia de seguridad.
    - Seleccione **mantener siempre al menos una copia de seguridad**, incluso si está más allá del período de retención.
    

4. Seleccione **Guardar**.


Cuando se ejecuta un trabajo programado de copia de seguridad, crea un contenedor (para almacenar los datos de copia de seguridad) en la cuenta de almacenamiento especificada. El nombre del contenedor se denomina *servicio BizTalk nombre-fecha y hora*. 

Si el panel de BizTalk Service muestra un estado de **error** :

![Último estado de copia de seguridad programada][BackupStatus] 

El vínculo abre los registros de operación de administración de servicios para ayudar a solucionar problemas. Consulte [servicios BizTalk: solucionar problemas de uso de los registros de operación](http://go.microsoft.com/fwlink/p/?LinkId=391211).

## <a name="restore"></a>Restaurar

Puede restaurar las copias de seguridad desde el portal de clásico Azure o de la [API de REST de servicio de BizTalk restaurar](http://go.microsoft.com/fwlink/p/?LinkID=325582). Esta sección enumeran los pasos para restaurar con el portal clásico.

#### <a name="before-restoring-a-backup"></a>Antes de restaurar una copia de seguridad

- Nuevo seguimiento, archivado y supervisión stores se pueden especificar al restaurar un BizTalk Service.

- Los mismos datos en tiempo de ejecución de EDI se restauran. La copia de seguridad de tiempo de ejecución de EDI almacena los números de control. Los números de control restaurados están en secuencia desde el momento de la copia de seguridad. Si los mensajes se procesan después de la última copia de seguridad, restaurar el contenido de la copia de seguridad puede provocar números de control duplicados.

#### <a name="restore-a-backup"></a>Restaurar una copia de seguridad

1. En el portal de clásico Azure, seleccione **nuevo** > **Servicios de aplicación** > **Servicio BizTalk** > **Restaurar**:

    ![Restaurar una copia de seguridad][Restore]

2. En **Dirección URL de la copia de seguridad**, seleccione el icono de carpeta y expanda la cuenta de almacenamiento de Azure que almacena la copia de seguridad de configuración de BizTalk Service. Expanda el contenedor y en el panel derecho, seleccione el archivo .txt copia correspondiente. 
<br/><br/>
Seleccione **Abrir**.

3. En la página **Servicio BizTalk restaurar** , escriba un **Nombre de servicio de BizTalk** y compruebe la **Dirección URL del dominio**, **Edición**y **región** para el BizTalk Service restaurado. **Crear una nueva instancia de base de datos SQL** para la base de datos de seguimiento:

    ![][RestoreBizTalkService]

    Haga clic en la flecha siguiente.

4.  Compruebe el nombre de la base de datos SQL, especifique el servidor físico donde desea crear la base de datos SQL y un nombre de usuario y contraseña para el servidor.


    Si desea configurar la edición de la base de datos SQL, tamaño y otras propiedades, seleccione **Configurar configuración avanzada de base de datos**. 

    Haga clic en la flecha siguiente.

5. Crear una nueva cuenta de almacenamiento, o especifique una cuenta existente de almacenamiento para BizTalk Service.

7. Seleccione la marca de verificación para iniciar la restauración.

Una vez completada la restauración correctamente, aparece una nueva BizTalk Service en un estado de suspensión en la página de servicios de BizTalk en el portal de clásico de Azure.



### <a name="postrestore"></a>Después de restaurar una copia de seguridad

BizTalk Service siempre se restaura en un estado **suspendido** . En este estado, puede realizar los cambios de configuración antes de que el nuevo entorno es funcional, incluidos:

- Si ha creado aplicaciones BizTalk Service mediante el SDK de servicios de BizTalk de Azure, necesite actualizar las credenciales de Control de acceso (ACS) en las aplicaciones para trabajar con el entorno restaurado.

- Restaurar un BizTalk Service para replicar un entorno existente de BizTalk Service. En este caso, si hay agreements configurados en el portal de servicios de BizTalk original que usan una carpeta FTP de origen, debe actualizar los contratos en el entorno recién restaurado para utilizar una carpeta de otro origen FTP. En caso contrario, puede haber dos agreements diferentes intentando extraer el mismo mensaje.

- Si ha restaurado para tener varios entornos de BizTalk Service, asegúrese de que destinar el entorno correcto en aplicaciones de Visual Studio, cmdlets de PowerShell, las API de REST o cotización API de OM de administración asociado.

- Es una buena práctica para configurar copias de seguridad automatizadas en el entorno de BizTalk Service recién restaurado.

Para iniciar BizTalk Service en el portal de clásico Azure, seleccione el BizTalk Service restaurada y seleccione **Reanudar** en la barra de tareas. 



## <a name="what-gets-backed-up"></a>¿Qué Obtiene copia de seguridad

Cuando se crea una copia de seguridad, se copian de los elementos siguientes:

<table border="1"> 
<tr bgcolor="FAF9F9">
<th> </th>
<TH>Copia de seguridad de elementos</TH> 
</tr> 
<tr>
<td colspan="2">
 <strong>Portal de servicios de BizTalk Azure</strong></td>
</tr> 
<tr>
<td>Configuración y en tiempo de ejecución</td> 
<td>
<ul>
<li>Detalles de Partner y el perfil</li>
<li>Contratos de Partner</li>
<li>Ensamblados personalizados implementados</li>
<li>Puentes implementado</li>
<li>Certificados</li>
<li>Transformaciones implementadas</li>
<li>Canalizaciones</li>
<li>Plantillas creado y guardado en el Portal de servicios de BizTalk</li>
<li>X12 ST01 y GS01 asignaciones</li>
<li>Números de control (EDI)</li>
<li>Valores de AS2 mensaje Micrófono</li>
</ul>
</td>
</tr> 
 
<tr>
<td colspan="2">
 <strong>Servicio de BizTalk Azure</strong></td>
</tr> 
<tr>
<td>Certificado SSL</td> 
<td>
<ul>
<li>Datos del certificado SSL</li>
<li>Contraseña de certificado SSL</li>
</ul>
</td>
</tr> 
<tr>
<td>Configuración del servicio de BizTalk</td> 
<td>
<ul>
<li>Número de unidades de escala</li>
<li>Edition</li>
<li>Versión del producto</li>
<li>Centro de datos o región</li>
<li>Clave y el espacio de nombres de acceso de Control de servicio (ACS)</li>
<li>Cadena de conexión de base de datos de seguimiento</li>
<li>Cadena de conexión de cuenta de almacenamiento de archivado</li>
<li>Cadena de conexión de cuenta de almacenamiento de supervisión</li>
</ul>
</td>
</tr> 
<tr>
<td colspan="2">
 <strong>Elementos adicionales</strong></td>
</tr> 
<tr>
<td>Base de datos de seguimiento</td> 
<td>Cuando se crea BizTalk Service, se especifican los detalles de la base de datos de seguimiento, incluyendo el servidor de base de datos de SQL Azure y el nombre de la base de datos de seguimiento. La base de datos de seguimiento no se copia automáticamente.
<br/><br/>
<strong>Importante</strong><br/>
Si se elimina la base de datos de seguimiento y recuperación las necesidades de la base de datos, debe existir una copia de seguridad anterior. Si no existe una copia de seguridad, la base de datos de seguimiento y sus datos no pueden recuperarse. En esta situación, cree una nueva base de datos de seguimiento con el mismo nombre de base de datos. Se recomienda geo replicación.</td>
</tr> 
</table>

## <a name="next"></a>Siguiente

Para crear los servicios de BizTalk de Azure en el portal de clásico Azure, vaya a [servicios BizTalk: portal clásico de aprovisionamiento Azure usando](http://go.microsoft.com/fwlink/p/?LinkID=302280). Para empezar a crear aplicaciones, vaya a [Azure BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=235197).

## <a name="see-also"></a>Vea también
- [Servicio de copia de seguridad de BizTalk](http://go.microsoft.com/fwlink/p/?LinkID=325584)
- [Restaurar el servicio de BizTalk de copia de seguridad](http://go.microsoft.com/fwlink/p/?LinkID=325582)
- [Servicios de BizTalk: Developer, Basic, estándar y Premium ediciones gráfico](http://go.microsoft.com/fwlink/p/?LinkID=302279)
- [Servicios de BizTalk: Portal clásico de Azure usando el aprovisionamiento](http://go.microsoft.com/fwlink/p/?LinkID=302280)
- [Servicios de BizTalk: Gráfico de estado de aprovisionamiento](http://go.microsoft.com/fwlink/p/?LinkID=329870)
- [Servicios de BizTalk: Fichas de paneles, el Monitor y la escala](http://go.microsoft.com/fwlink/p/?LinkID=302281)
- [Servicios de BizTalk: límite](http://go.microsoft.com/fwlink/p/?LinkID=302282)
- [Servicios de BizTalk: Nombre del emisor y la clave del emisor](http://go.microsoft.com/fwlink/p/?LinkID=303941)
- [¿Cómo puedo comenzar a utilizar el SDK de servicios de BizTalk de Azure](http://go.microsoft.com/fwlink/p/?LinkID=302335)

[BackupStatus]: ./media/biztalk-backup-restore/status-last-backup.png
[Restore]: ./media/biztalk-backup-restore/restore-ui.png
[AutomaticBU]: ./media/biztalk-backup-restore/AutomaticBU.png
[RestoreBizTalkService]: ./media/biztalk-backup-restore/RestoreBizTalkServiceWindow.png
 
