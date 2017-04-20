<properties
   pageTitle="Cómo configurar las alertas de seguridad | Microsoft Azure"
   description="Obtenga información sobre cómo configurar las alertas de seguridad de la extensión de administración de identidades con privilegios de Azure."
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
   ms.date="09/02/2016"
   ms.author="kgremban"/>

# <a name="how-to-configure-security-alerts-in-azure-ad-privileged-identity-management"></a>Cómo configurar las alertas de seguridad en la administración de identidades con privilegios de Azure AD

## <a name="security-alerts"></a>Alertas de seguridad
Administración de identidades con privilegios Azure (PIM) genera alertas cuando hay actividad sospechosa o no segura en su entorno. Cuando se activa una alerta, se muestra en el panel PIM. Seleccione la alerta para ver un informe que muestra los usuarios o los roles que activó el aviso.

![Alertas de seguridad de panel PIM - captura de pantalla][1]



| Alertas | Desencadenador | Recomendación |
| ----- | ------- | -------------- |
| **Se asignan roles fuera PIM** | Un administrador permanentemente se ha asignado a un rol, fuera de la interfaz PIM. | Revise la nueva asignación de roles. Desde otros servicios sólo pueden asignar a administradores permanentes, cámbielo a una asignación válida si es necesario. |
| **Con demasiada frecuencia se activan los roles** | Hay demasiados Reactivaciones de la misma función en el tiempo que se permiten en la configuración. | Póngase en contacto con el usuario para ver por qué activó la función tantas veces. ¿El plazo es demasiado pequeña para ellos realizar sus tareas, o bien, quizá no está usando secuencias de comandos para activar automáticamente un rol. |
| **Funciones no requieren autenticación multifactor para la activación** | Hay funciones sin AMF habilitadas en la configuración. | Se requiere AMF para las funciones más amplios privilegios, pero se recomienda habilitar AMF para la activación de todas las funciones. |
| **Los administradores no están usando sus funciones con privilegios** | Hay administradores válidos que todavía no lo ha activado recientemente sus funciones. | Iniciar una revisión de acceso para determinar los usuarios que he ya no necesiten acceso. |
| **Hay demasiados administradores globales** | Hay más globales administradores que recomienda. | Si tiene un gran número de los administradores globales, es probable que los usuarios reciben más permisos de los que necesitan. Mover usuarios a las funciones con menos privilegios o realice alguna de ellas elegible para la función en lugar de permanentemente asignados. |

## <a name="configure-security-alert-settings"></a>Configurar las opciones de alerta de seguridad

Puede personalizar algunas de las alertas de seguridad en PIM para trabajar con su entorno y los objetivos de seguridad. Siga estos pasos para alcanzar el módulo de configuración:

1. Inicie sesión en el [portal de Azure](https://portal.azure.com/) y seleccione el icono de **la administración de identidades con privilegios de Azure AD** desde el panel.
2. Seleccione **Administrar roles con privilegios** > **configuración** > **configuración de alertas**.

    ![Vaya a configuración de alertas de seguridad][2]

### <a name="roles-are-being-activated-too-frequently-alert"></a>Alerta de "Funciones se activan con demasiada frecuencia"

Esta alerta activa si un usuario activa la misma función privilegios varias veces dentro de un período especificado. Puede configurar el período de tiempo y el número de activaciones.

- **Intervalo de tiempo de renovación de activación**: especificar en días, horas, minutos y segundos, el período de tiempo que desea usar para realizar un seguimiento de renovación sospechoso.

- **Número de renovación de activación**: especifique el número de activaciones de 2 a 100, que considere pena alerta, dentro del período elegido. Para cambiar esta configuración moviendo el control deslizante o escribiendo un número en el cuadro de texto.


### <a name="there-are-too-many-global-administrators-alert"></a>Alerta de "Hay demasiados administradores globales"

PIM activa esta alerta si se cumplen dos diferentes criterios y, a continuación, puede configurar los dos. En primer lugar, debe alcanzar un determinado umbral de administradores globales. Segundo, un determinado porcentaje de la asignación de rol total debe ser los administradores globales. Si solo cumple una de estas medidas, no aparece la alerta.  

- **Número mínimo de los administradores globales**: especifique el número de administradores globales, de 2 a 100, tenga en cuenta un importe no seguro.

- **Porcentaje de los administradores globales**: especifique el porcentaje de los administradores de los administradores globales, de 0% a 100%, que es seguro en su entorno.

### <a name="administrators-arent-using-their-privileged-roles-alert"></a>"Los administradores no están usando sus funciones con privilegios" alerta

Esta alerta activa si un usuario deja un cierto tiempo sin activar una función.

- **Número de días**: especifique el número de días, de 0 a 100, que un usuario puede ir sin activar una función.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Pasos siguientes
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]


<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-how-to-configure-security-alerts/PIM_security_dash.png
[2]: ./media/active-directory-privileged-identity-management-how-to-configure-security-alerts/PIM_security_settings.png
