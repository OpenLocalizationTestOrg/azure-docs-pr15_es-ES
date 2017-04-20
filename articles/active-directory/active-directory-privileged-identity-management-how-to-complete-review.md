<properties
   pageTitle="Cómo finalizar una revisión de access | Microsoft Azure"
   description="Después de iniciar una revisión de access en la administración de identidades con privilegios de Azure AD, obtenga información sobre cómo completarla y ver los resultados"
   services="active-directory"
   documentationCenter=""
   authors="kgremban"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="06/30/2016"
   ms.author="kgremban"/>

# <a name="how-to-complete-an-access-review-in-azure-ad-privileged-identity-management"></a>Cómo finalizar una revisión de access en la administración de identidades con privilegios de Azure AD


Los administradores de la función privilegios pueden revisar con privilegios de acceso una vez una [revisión de seguridad se ha iniciado](active-directory-privileged-identity-management-how-to-start-security-review.md). Administración de identidades de Azure AD privilegios (PIM) automáticamente le enviará un correo electrónico preguntar a los usuarios para revisar su acceso. Si un usuario no recibido un correo electrónico, puede enviarles las instrucciones en [cómo llevar a cabo una revisión de seguridad](active-directory-privileged-identity-management-how-to-perform-security-review.md).

Después de que el periodo de revisión de seguridad, o han terminado de su propia revisar todos los usuarios, siga los pasos de este artículo para administrar la revisión y ver los resultados.

## <a name="manage-security-reviews"></a>Administrar las revisiones de seguridad

1. Vaya al [portal de Azure](https://portal.azure.com/) y seleccione la aplicación de **la administración de identidades con privilegios de Azure AD** en su escritorio.
2. Seleccione la sección **Access revisa** del panel.
3. Seleccione la revisión de access que desea administrar.

En el módulo de detalle de la revisión de access hay un número opciones de administración de revisión.

![Botones de revisión de acceso PIM - captura de pantalla][1]

### <a name="remind"></a>Recuerde

Si una revisión de access está configurada para que los usuarios revisión propiamente dichas, el botón **Avisar** envía una notificación. 

### <a name="stop"></a>Detener

Todas las revisiones de access tienen una fecha de finalización, pero puede usar el botón **Detener** a fin de temprano. Si aún no ha revisado todos los usuarios en este momento, no podrán a después de detener la revisión. No puede reiniciar una revisión después de que se ha detenido.

### <a name="apply"></a>Aplicar

Después de completa una revisión de access, ya sea porque se ha alcanzado la fecha de finalización o había dejado de forma manual, el botón **Aplicar** implementa el resultado de la revisión. Si se ha denegado el acceso de un usuario en la revisión, este es el paso que elimine su asignación de roles.  

### <a name="export"></a>Exportar

Si desea aplicar los resultados de la revisión de seguridad manualmente, puede exportar la revisión. El botón **Exportar** comenzará a descargar un archivo CSV. Puede administrar los resultados en Excel u otros programas que abren archivos CSV.

### <a name="delete"></a>Eliminar

Si no está interesado en la revisión de cualquier otra, elimínelo. El botón **Eliminar** quita la revisión de la aplicación de PIM.

> [AZURE.IMPORTANT] No se recibe una advertencia antes de la eliminación, así que asegúrese de que desea eliminar esa revisión.


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Pasos siguientes
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]


<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-how-to-complete-review/PIM_review_buttons.png
