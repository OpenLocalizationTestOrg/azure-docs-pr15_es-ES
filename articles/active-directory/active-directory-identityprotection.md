<properties
    pageTitle="Protección de la identidad de Azure Active Directory | Microsoft Azure"
    description="Obtenga información sobre cómo protección de identidad de Azure AD le permite limitar la capacidad de un intruso para aprovechar un dispositivo o en peligro de identidad y proteger una identidad o un dispositivo que sospecha o conocido que pueden comprometido anteriormente."
    services="active-directory"
    keywords="protección de la identidad de Azure directorio activo, detección de aplicación de nube, administración de aplicaciones, seguridad, riesgo, nivel de riesgo, vulnerabilidad, directiva de seguridad"
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/26/2016"
    ms.author="markvi"/>

#<a name="azure-active-directory-identity-protection"></a>Protección de la identidad de Azure Active Directory 

Azure Active Directory identidad Protection es una característica de la edición de Azure AD Premium P2 que le ofrece una vista consolidada en eventos de riesgos y posibles vulnerabilidad que afectan a las identidades de su organización. Microsoft ha Proteger identidades en la nube para en un diez años y con protección de identidad de Azure AD, Microsoft está realizando estos mismos sistemas de protección disponibles para los clientes de empresa. Protección de identidad aprovecha las capacidades de detección de anomalías de existente Azure AD (disponibles a través de informes de actividad anómala de Azure AD) e introduce a nuevos tipos de evento de riesgo que pueden detectar anomalías en tiempo real.



##<a name="getting-started"></a>Introducción

La mayoría de los problemas de seguridad producirá cuando los atacantes obtienen acceso a un entorno por robo de identidad de un usuario. Los atacantes son cada vez más eficaces para sacar provecho de violaciones de terceros y el uso de ataques de suplantación de identidad sofisticados. Una vez que un intruso obtenga acceso a incluso una cuenta de usuario de privilegios mínimos, es relativamente sencillo para obtener acceso a los recursos de empresa importantes a través de desplazamiento lateral. Por lo tanto, es fundamental para proteger todas las identidades y, cuando se ve comprometida una identidad, proactiva impedir la identidad comprometida se abuso. 

Descubrir comprometidas identidades no es fácil una tarea. Afortunadamente, puede ayudar protección de identidad: protección de identidad usa algoritmos de aprendizaje de máquina adaptación y heurística para detectar anomalías y eventos que pueden indicar que se ha comprometido una identidad de riesgos.
 
Con estos datos, protección de identidad genera informes y alertas que le permite investigar estos eventos riesgos y tomar la acción de reducción o la corrección correspondiente.
 
Pero protección de identidad de Azure Active Directory es más de una herramienta de supervisión y generación de informes. En función de eventos de riesgo, protección de identidad calcula un nivel de riesgo de usuario para cada usuario, lo que le permite configurar directivas basadas en el riesgo para proteger automáticamente las identidades de su organización.  Estas directivas de riesgo, además de otros controles de acceso condicional proporcionados por Azure Active Directory y EMS, pueden bloquear automáticamente o se ofrecen acciones de corrección adaptación que incluyen el restablecimiento de contraseñas y exigir la autenticación multifactor.  

####<a name="explore-identity-protections-capabilities"></a>Explorar las capacidades de protección de identidad 

**Detectar cuentas con riesgos y eventos de riesgo:**  

- Detectar 6 tipos de evento de riesgo mediante reglas heurísticas y aprendizaje de equipo 

- Calcular los niveles de riesgo de usuario

- Proporcionar recomendaciones personalizadas para mejorar la posición de seguridad general resaltando vulnerabilidad



**Investigar eventos de riesgo:**

- Enviar notificaciones de eventos de riesgo

- Investigar eventos de riesgo con información relevante y contextual

- Proporcionar básicas flujos de trabajo para realizar un seguimiento de las investigaciones

- Proporcionar un fácil acceso a las acciones de corrección, como el restablecimiento de contraseña



**Directivas de acceso condicional basado en el riesgo:**

- Directiva de mitigar inicios de sesión arriesgados bloqueando inicios de sesión o que requieren los desafíos de autenticación multifactor.

- Directiva de cuentas de usuario arriesgado segura o bloque

- Directiva de requerir que los usuarios registrar con autenticación multifactor


## <a name="detection-and-risk"></a>Detección y los riesgos

### <a name="risk-events"></a>Eventos de riesgo

Eventos de riesgo son eventos que se han marcado como sospechoso por la protección de identidad e indican que una identidad haya sufrido. Para obtener una lista completa de los eventos de riesgo, vea [tipos de eventos de riesgo detectados por protección de identidad de Azure Active Directory](active-directory-identityprotection-risk-events-types.md). 


### <a name="risk-level"></a>Nivel de riesgo

El nivel de un evento de riesgo es una indicación (alta, Media o baja) de la gravedad del evento de riesgo. El nivel de riesgo ayuda a los usuarios de protección de identidad prioridades a las acciones que debe realizar para reducir el riesgo a su organización. La gravedad del evento de riesgo representa la intensidad de la señal como una predicción de compromiso de identidad, junto con la cantidad de ruido que presenta normalmente. 

- **Alto**: alta confianza y evento de riesgo gravedad alta. Estos eventos son buenos indicadores que se ha comprometido la identidad del usuario y las cuentas de usuario que se ven afectadas se deben corregir inmediatamente.

- **Medio**: gravedad alta, pero el evento de riesgo confianza inferior, o viceversa. Estos eventos son potencialmente peligrosos y, a continuación, deben corregir las cuentas de usuario que se ven afectadas.

- **Bajo**: baja confianza y evento de riesgo de gravedad baja. Este evento no requieran una acción inmediata, pero cuando se combina con otros eventos de riesgo, puede proporcionar una indicación segura que está comprometida la identidad. 


![Nivel de riesgo] (./media/active-directory-identityprotection/01.png "Nivel de riesgo")

 

Eventos de riesgo bien identificados en **tiempo real**, o en posterior al procesamiento después de que el evento de riesgo ya ha tenido colocar (sin conexión). Actualmente la mayoría de eventos de riesgo en protección de identidad se calculan sin conexión y mostrarse en protección de identidad dentro de 2 a 4 horas. Mientras se evalúa en tiempo real, los eventos de riesgo en tiempo real aparecerá en la consola de protección de identidad dentro de 5 a 10 minutos.

Varios clientes heredados no admiten actualmente prevención y detección de evento de riesgo en tiempo real. Como resultado, los inicios de sesión de estos clientes no detectados o impedidos en tiempo real.


## <a name="investigation"></a>Investigación
Normalmente, su viaje por la protección de identidad empieza con el panel de protección de la identidad. 

![Corrección] (./media/active-directory-identityprotection/1000.png "Corrección")

El panel le ofrece acceso a:
 
- Informes como **usuarios marcan para riesgo**, **eventos de riesgo** y **vulnerabilidad**
- Configuración como la configuración de **Directivas de seguridad**, **notificaciones** y **registro autenticación multifactor**
 

Normalmente es el punto de partida para investigación, que es el proceso de revisión de las actividades, los registros y otra información relevante relacionada con un evento de riesgo decidir si los pasos de corrección o mitigación son necesarios, y cómo se ha comprometido la identidad y comprender cómo se utiliza la identidad comprometida.

Puede vincular las actividades de investigación a las [notificaciones](active-directory-identityprotection-notifications.md) de que Azure Active Directory Protection envía por correo electrónico.

Las siguientes secciones proporcionan con más detalles y los pasos que están relacionados con una investigación.  



## <a name="what-is-a-user-risk-level"></a>¿Qué es un nivel de riesgo de usuario?

Un nivel de riesgo de usuario es una indicación (alta, Media o baja) de la probabilidad de que se ha comprometido la identidad del usuario. Se calcula basándose en los eventos de riesgo de usuario que están asociados a la identidad del usuario. 

El estado de un evento de riesgo está **activo** o **cerrado**. Solo los eventos de riesgo que están **activas** contribuyen a los cálculos de riesgos de usuario. 

El nivel de riesgo de usuario se calcula mediante las siguientes entradas:

- Eventos de riesgo activo que afectan a los usuarios
- Nivel de riesgo de estos eventos 
- Si se han adoptado las acciones de corrección 

![Riesgos de usuario] (./media/active-directory-identityprotection/1001.png "Riesgos de usuario")



Puede utilizar los niveles de riesgo de usuario para crear directivas de acceso condicional para impedir que los usuarios con riesgos inicio de sesión u obligue a cambiar su contraseña de forma segura. 


## <a name="closing-risk-events-manually"></a>Cierre manualmente los eventos de riesgo

En la mayoría de los casos, tendrá las acciones de corrección, como una contraseña segura restablecer para cerrar automáticamente los eventos de riesgo. Sin embargo, esto no siempre posible.  
Por ejemplo, este es el caso, cuando:

- Se ha eliminado un usuario con los eventos de riesgo activo
- Una investigación revela que ha sido realizar un evento de riesgo notificado por el usuario legítimo

Eventos de riesgo que están **activas** suponen el cálculo del riesgo de usuario, debe disminuir manualmente un nivel de riesgo, cierre manualmente los eventos de riesgo.  
Durante el curso de investigación, puede realizar cualquiera de estas acciones para cambiar el estado de un evento de riesgo:

![Acciones] (./media/active-directory-identityprotection/34.png "Acciones")

- **Resolver** - si después de investigar un evento de riesgo, que realizó una acción correspondiente corrección fuera de la protección de identidad y cree que se debe considerar el evento de riesgo cerrado, marque el evento como resuelto. Resolver eventos establecerá el estado del evento de riesgo a cerrado y el evento de riesgo ya no se contribuir a riesgo de usuario.

- **Marcar como falso positivo** : en algunos casos, puede investigar un evento de riesgo y descubrir que se ha marcado incorrectamente como una arriesgada. Puede ayudar a reducir el número de esas ocurrencias marcando el evento de riesgo como falso positivo. Esto le ayudará a la máquina aprendizaje algoritmos para mejorar la clasificación de eventos similares en el futuro. El estado de eventos de falso positivo a **cerrado** y ya no contribuyen a riesgos de usuario.

- **Ignorar** - si no se ha realizado una acción de corrección, pero desea que se quita de la lista activa el evento de riesgo, puede marcar un evento de riesgo ignorar y se cerrará el estado del evento. Eventos omitidos no contribuyen a riesgos de usuario. Esta opción solo debe usarse en circunstancias inusuales. 

- **Reactivar** - eventos de riesgo que se hayan cerrado manualmente (eligiendo **resolver**, **falso positivo**o **Ignorar**) se pueden reactivar, establecer el estado del evento a **activo**. Eventos de riesgo reactivar contribuyan a los cálculos de nivel de riesgo de usuario. No se puede reactivar eventos de riesgo cerrados mediante la corrección (como restablece una contraseña segura). 




**Para abrir el cuadro de diálogo de configuración**:

1. En el módulo de **protección de la identidad de Azure AD** , **investigar**, haga clic en **eventos de riesgo**.

    ![Restablecimiento de contraseña manual] (./media/active-directory-identityprotection/1002.png "Restablecimiento de contraseña manual")

2. En la lista **eventos de riesgo** , haga clic en un riesgo.

    ![Restablecimiento de contraseña manual] (./media/active-directory-identityprotection/1003.png "Restablecimiento de contraseña manual")

2. En el módulo de riesgo, haga clic en un usuario.

    ![Restablecimiento de contraseña manual] (./media/active-directory-identityprotection/1004.png "Restablecimiento de contraseña manual")



### <a name="closing-all-risk-events-for-a-user-manually"></a>Cierre todos los eventos de riesgo para un usuario manualmente

En lugar de manualmente cerrar eventos de riesgo para un usuario de forma individual, protección de identidad de Azure Active Directory también proporciona un método para cerrar todos los eventos de un usuario con un solo clic.


![Acciones] (./media/active-directory-identityprotection/2222.png "Acciones")

Al hacer clic en **Descartar todos los eventos**, todos los eventos están cerrados y el usuario afectado ya no está en riesgo.



## <a name="remediating-user-risk-events"></a>Eventos de riesgo adecuadas de usuario

Una corrección es una acción para proteger una identidad o un dispositivo que sospecha o conocido que pueden comprometido anteriormente. Acción de corrección restaura la identidad o el dispositivo a un estado seguro y resuelve anterior eventos de riesgos asociados con la identidad o el dispositivo.

Para corregir los eventos de riesgo de usuario, puede:

- Realizar una contraseña segura restablecer para solucionar manualmente los eventos de riesgo de usuario 

- Configurar una directiva de seguridad del riesgo de usuario para mitigar o corregir automáticamente los eventos de riesgo de usuario

- Crear una nueva imagen del dispositivo infectado  


### <a name="manual-secure-password-reset"></a>Restablecimiento de contraseña segura manual

Un restablecimiento de contraseña segura es una eficaz corrección para muchos de los eventos de riesgo y cuando realiza, automáticamente se cierra estos eventos de riesgo y vuelve a calcular el nivel de riesgo de usuario. Puede usar el panel de protección de identidad para iniciar una restablecimiento de contraseña para un usuario con riesgos. 

El cuadro de diálogo relacionado proporciona dos métodos diferentes para restablecer una contraseña:

**Restablecer la contraseña** - seleccione **solicitar al usuario restablecer la contraseña** para permitir que al usuario if automática de recuperar el usuario se ha registrado con autenticación multifactor. Durante del siguiente inicio de sesión de usuario, el usuario se requiera para resolver un desafío de autenticación multifactor correctamente y, a continuación, se obliga a cambiar la contraseña. Esta opción no está disponible si la cuenta de usuario no está registrado con autenticación multifactor.

**Contraseña temporal** : seleccione **generar una contraseña temporal** a inmediatamente invalidar la contraseña existente y crear una nueva contraseña temporal para el usuario. Enviar la nueva contraseña temporal a una dirección de correo electrónico alternativa para el usuario o el administrador del usuario. Dado que la contraseña es temporal, el usuario se le indicará que cambiar la contraseña al iniciar sesión.


![Directiva] (./media/active-directory-identityprotection/1005.png "Directiva")


**Para abrir el cuadro de diálogo de configuración**:

1. En el módulo de **identidad de Azure AD protección** , haga clic en **usuarios marcan para riesgos**.

    ![Restablecimiento de contraseña manual] (./media/active-directory-identityprotection/1006.png "Restablecimiento de contraseña manual")


2. En la lista de usuarios, seleccione un usuario con los eventos de riesgo al menos una.

    ![Restablecimiento de contraseña manual] (./media/active-directory-identityprotection/1007.png "Restablecimiento de contraseña manual")


2. En el módulo de usuario, haga clic en **Restablecer contraseña**.

    ![Restablecimiento de contraseña manual] (./media/active-directory-identityprotection/1008.png "Restablecimiento de contraseña manual")





## <a name="user-risk-security-policy"></a>Directiva de seguridad de usuario riesgo

Una directiva de seguridad de usuario riesgo es una directiva de acceso condicional que evalúa el nivel de riesgo a un usuario específico y aplica acciones de corrección y mitigación basadas en reglas y condiciones predefinidas.


![Directiva de ridk de usuario] (./media/active-directory-identityprotection/1009.png "Directiva de ridk de usuario")


Protección de identidades de AD Azure le ayuda a administrar la mitigación y la corrección de usuarios marcados para riesgo por lo que le permite:

- Configurar los usuarios y grupos que se aplica la directiva: 

    ![Directiva de ridk de usuario] (./media/active-directory-identityprotection/1010.png "Directiva de ridk de usuario")


- Establecer el usuario nivel umbral de riesgo (bajo, medio o alto) que activa la directiva: 

    ![Directiva de ridk de usuario] (./media/active-directory-identityprotection/1011.png "Directiva de ridk de usuario")


- Establecer los controles para que se ejecute cuando activa la directiva:

    ![Directiva de ridk de usuario] (./media/active-directory-identityprotection/1012.png "Directiva de ridk de usuario")


- Cambiar el estado de la directiva:

    ![Directiva de ridk de usuario] (./media/active-directory-identityprotection/403.png "Registro MFA")


- Revisar y evaluar el impacto de un cambio antes de activarlo:

    ![Directiva de ridk de usuario] (./media/active-directory-identityprotection/1013.png "Directiva de ridk de usuario")


Elegir un umbral **alto** reduce el número de veces que una directiva se activa y minimiza el impacto para los usuarios.
Sin embargo, excluya usuarios **bajo** y **medio** marcados para el riesgo de la directiva, que no se puede proteger identidades o dispositivos anteriormente sospechosos o conocidos que estar en peligro.

Al establecer la directiva

- Excluir los usuarios que puedan generar una gran cantidad de falsos positivos (programadores, analistas de seguridad)

- Excluir los usuarios en las configuraciones regionales que no sea práctico habilitar la directiva (por ejemplo, sin acceso al servicio de asistencia)

- Usar distribuir un umbral **alto** durante la directiva inicial, o si debe minimizar los desafíos de los usuarios finales ver.

- Si su organización requiere la mayor seguridad, use un umbral **bajo** . Seleccionar un umbral **bajo** presenta adicionales sesión desafíos de los usuarios, pero una mayor seguridad.

El valor predeterminado recomendado para la mayoría de las organizaciones es configurar una regla de umbral **medio** alcanzar un equilibrio entre facilidad de uso y seguridad.

Para obtener información general de la experiencia del usuario relacionados, vea:

- [Flujo de recuperación de cuenta en peligro](active-directory-identityprotection-flows.md#compromised-account-recovery).  

- [La cuenta en peligro bloqueada flujo](active-directory-identityprotection-flows.md#compromised-account-blocked).  


**Para abrir el cuadro de diálogo de configuración**:

1. En el módulo de **protección de la identidad de Azure AD** , en la sección **Configurar** , haga clic en **la directiva de riesgo de usuario**.

    ![Directiva de ridk de usuario] (./media/active-directory-identityprotection/1009.png "Directiva de ridk de usuario")






## <a name="mitigating-user-risk-events"></a>Mitigar eventos de riesgo de usuario
Los administradores pueden establecer una directiva de seguridad de usuario riesgo a bloquear a los usuarios al iniciar sesión en función del nivel de riesgo. 

Bloquear una sesión:
 
- Evita la generación de eventos de riesgo usuario nuevo para el usuario afectado

- Permite a los administradores a solucionar los eventos de riesgo que afectan a la identidad del usuario y restaurarlo a un estado seguro manualmente



## <a name="what-is-a-sign-in-risk-level"></a>¿Qué es un nivel de riesgo de inicio de sesión?

Un nivel de inicio de sesión de riesgo es una indicación (alta, Media o baja) de la probabilidad de que un determinado inicio de sesión de, otra persona está intentando autenticar con la identidad del usuario. El nivel de inicio de sesión de riesgo se evalúa en el momento de una sesión y considera eventos de riesgo e indicadores detectan en tiempo real para esa sesión específica. 

## <a name="mitigating-sign-in-risk-events"></a>Mitigar el riesgo de inicio de sesión de eventos 
Una mitigación es una acción para limitar la capacidad de un intruso para explotar una identidad comprometido o dispositivo sin restaurar la identidad o el dispositivo a un estado seguro. Una mitigación no resuelve anterior eventos de inicio de sesión de riesgo asociados con la identidad o dispositivo.

Puede usar acceso condicional en protección de identidad de Azure AD automáticamente mitigar riesgos de inicio de sesión de eventos. Mediante estas directivas, considere el nivel de riesgo del usuario o el inicio de sesión de bloquear arriesgados inicios de sesión o requieren que el usuario para realizar la autenticación multifactor. Estas acciones pueden impedir que un intruso aproveche una identidad robada para causar daños y pueden dar algún tiempo para proteger la identidad. 


## <a name="sign-in-risk-security-policy"></a>Directiva de seguridad de inicio de sesión de riesgo

Una directiva de inicio de sesión de riesgo es una directiva de acceso condicional que evalúa el riesgo a una sesión específica y se aplica a su eliminación basada en reglas y condiciones predefinidas.

![Inicio de sesión de la directiva de riesgo] (./media/active-directory-identityprotection/1014.png "Inicio de sesión de la directiva de riesgo")


Protección de identidades de AD Azure le ayuda a administrar la mitigación arriesgadas inicios de sesión, ya que permite:

- Configurar los usuarios y grupos que se aplica la directiva: 

    ![Inicio de sesión de la directiva de riesgo] (./media/active-directory-identityprotection/1015.png "Inicio de sesión de la directiva de riesgo")


- Establecer el inicio de sesión de nivel umbral de riesgo (bajo, medio o alto) que activa la directiva: 

    ![Inicio de sesión de la directiva de riesgo] (./media/active-directory-identityprotection/1016.png "Inicio de sesión de la directiva de riesgo")


- Establecer los controles para que se ejecute cuando activa la directiva:  

    ![Inicio de sesión de la directiva de riesgo] (./media/active-directory-identityprotection/1017.png "Inicio de sesión de la directiva de riesgo")
    

- Cambiar el estado de la directiva:

    ![Registro MFA] (./media/active-directory-identityprotection/403.png "Registro MFA")

- Revisar y evaluar el impacto de un cambio antes de activarlo: 

    ![Inicio de sesión de la directiva de riesgo] (./media/active-directory-identityprotection/1018.png "Inicio de sesión de la directiva de riesgo")


### <a name="what-you-need-to-know"></a>¿Qué necesita saber

Puede configurar una directiva de seguridad de inicio de sesión de riesgo para exigir la autenticación multifactor:

![Inicio de sesión de la directiva de riesgo] (./media/active-directory-identityprotection/1017.png "Inicio de sesión de la directiva de riesgo")

Sin embargo, por motivos de seguridad, esta configuración solo funciona para los usuarios que ya se han registrado con autenticación multifactor. Si se cumple la condición para requerir la autenticación multifactor para un usuario que aún no esté registrado con autenticación multifactor, se bloquea el usuario. 

Como práctica recomendada, si desea requerir autenticación multifactor para inicios de sesión con riesgos, debe:

1. Habilitar la [Directiva de registro de autenticación multifactor](#multi-factor-authentication-registration-policy) para los usuarios afectados.
2. Requiere que los usuarios afectados para iniciar sesión en una sesión no arriesgado para realizar un registro AMF

Completar estos pasos se asegura de que la autenticación multifactor se requiere un inicio de sesión de arriesgada. 


### <a name="best-practices"></a>Prácticas recomendadas

 
Elegir un umbral **alto** reduce el número de veces que una directiva se activa y minimiza el impacto para los usuarios.  
 
Sin embargo, excluye **bajo** y **medio** inicios de sesión marcados para el riesgo de la directiva, que no se puede bloquear un intruso pueda aprovechar una identidad comprometida. 

Al establecer la directiva 

- Excluir los usuarios que no / no tienen la autenticación multifactor

- Excluir los usuarios en las configuraciones regionales que no sea práctico habilitar la directiva (por ejemplo, sin acceso al servicio de asistencia)

- Excluir los usuarios que puedan generar una gran cantidad de falsos positivos (programadores, analistas de seguridad)

- Usar distribuir un umbral **alto** durante la directiva inicial, o si debe minimizar los desafíos de los usuarios finales ver.

- Si su organización requiere la mayor seguridad, use un umbral **bajo** . Seleccionar un umbral **bajo** presenta adicionales sesión desafíos de los usuarios, pero una mayor seguridad.

El valor predeterminado recomendado para la mayoría de las organizaciones es configurar una regla de umbral **medio** alcanzar un equilibrio entre facilidad de uso y seguridad.

 
La directiva de inicio de sesión de riesgo es:

- Aplicar a todo el tráfico de explorador y complementos de inicio de sesión utilizando autenticación moderna.
- No se aplica a las aplicaciones que utilizan los protocolos de seguridad anteriores deshabilitando el extremo de WS-Trust en el IDP federado, como ADFS.

La página de **Eventos de riesgo** en la consola de protección de identidad enumera todos los eventos:

- Esta directiva se aplica a
- Puede revisar la actividad y determinar si la acción correspondiente o no 

Para obtener información general de la experiencia del usuario relacionados, vea:

- [Recuperación de inicio de sesión con riesgos](active-directory-identityprotection-flows.md#risky-sign-in-recovery) 

- [Bloquear el inicio de sesión con riesgos](active-directory-identityprotection-flows.md#risky-sign-in-blocked)  

- [Registro de autenticación multifactor durante un inicio de sesión con riesgos](active-directory-identityprotection-flows.md#multi-factor-authentication-registration-during-a-risky-sign-in)  





**Para abrir el cuadro de diálogo de configuración**:

1. En el módulo de **protección de la identidad de Azure AD** , en la sección **Configurar** , haga clic en **Inicio de sesión de la directiva de riesgo**.

    ![Directiva de ridk de usuario] (./media/active-directory-identityprotection/1014.png "Directiva de ridk de usuario")





## <a name="multi-factor-authentication-registration-policy"></a>Directiva de registro de autenticación multifactor

Azure autenticación multifactor es un método para comprobar quién es usted que requiere el uso de más que un nombre de usuario y contraseña. Proporciona una segunda capa de seguridad para inicios de sesión de usuario y las transacciones.  
Le recomendamos que requieren la autenticación multifactor Azure para inicios de sesión de usuario porque se:

- Ofrece autenticación segura con una gama de opciones de comprobación fácil

- Se reproduce una función clave en la preparación de su organización a proteger y recuperar de compromisos de cuenta

![Directiva de ridk de usuario] (./media/active-directory-identityprotection/1019.png "Directiva de ridk de usuario")



Para obtener más detalles, consulte [¿Qué es la autenticación multifactor de Azure?](../multi-factor-authentication/multi-factor-authentication.md)


Protección de identidades de AD Azure le ayuda a administrar la puesta en servicio de registro de autenticación multifactor mediante la configuración de una directiva que le permite: 



- Configurar los usuarios y grupos que se aplica la directiva: 

    ![Directiva MFA] (./media/active-directory-identityprotection/1020.png "Directiva MFA")



- Establecer los controles para que se ejecute cuando activa la directiva::  

    ![Directiva MFA] (./media/active-directory-identityprotection/1021.png "Directiva MFA")


- Cambiar el estado de la directiva:

    ![Directiva MFA] (./media/active-directory-identityprotection/403.png "Directiva MFA")

- Ver el estado actual de registro: 

    ![Directiva MFA] (./media/active-directory-identityprotection/1022.png "Directiva MFA")


Para obtener información general de la experiencia del usuario relacionados, vea:

- [Flujo de registro de autenticación multifactor](active-directory-identityprotection-flows.md#multi-factor-authentication-registration).  

- [Registro de autenticación multifactor durante una sesión arriesgada](active-directory-identityprotection-flows.md#multi-factor-authentication-registration-during-a-risky-sign-in).  





**Para abrir el cuadro de diálogo de configuración**:

1. En el módulo de **protección de la identidad de Azure AD** , en la sección **Configurar** , haga clic en **el registro de autenticación multifactor**.

    ![Directiva MFA] (./media/active-directory-identityprotection/1019.png "Directiva MFA")





## <a name="next-steps"></a>Pasos siguientes

 - [Canal 9: Azure AD y mostrar identidad: vista previa de protección de identidad](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)
 - [Tipos de eventos de riesgo detectados por protección de identidad de Azure Active Directory](active-directory-identityprotection-risk-events-types.md)
 - [Vulnerabilidad detectados por la protección de identidad de Azure Active Directory](active-directory-identityprotection-vulnerabilities.md)
 - [Notificaciones de protección de identidad de Active Directory de Azure](active-directory-identityprotection-notifications.md)
 - [Guía de protección de identidad de Active Directory de Azure](active-directory-identityprotection-playbook.md)
 - [Glosario de protección de identidad de Active Directory de Azure](active-directory-identityprotection-glossary.md)

 - [Experiencias de inicio de sesión con protección de la identidad de Azure AD](active-directory-identityprotection-flows.md)

 - [Al habilitar la protección de identidad de Azure Active Directory](active-directory-identityprotection-enable.md)
 - [Azure Active Directory identidad Protection - cómo desbloquear usuarios](active-directory-identityprotection-unblock-howto.md)

 - [Introducción a la protección de identidad de Azure Active Directory y Microsoft Graph](active-directory-identityprotection-graph-getting-started.md)


