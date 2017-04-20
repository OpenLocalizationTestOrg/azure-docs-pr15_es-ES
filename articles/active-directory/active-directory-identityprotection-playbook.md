<properties
    pageTitle="Guía de protección de identidad de Active Directory Azure | Microsoft Azure"
    description="Obtenga información sobre cómo protección de identidad de Azure AD le permite limitar la capacidad de un intruso para aprovechar un dispositivo o identidad comprometida y proteger una identidad o un dispositivo que sospecha o conocido que pueden comprometida anteriormente."
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
    ms.date="08/22/2016"
    ms.author="markvi"/>

#<a name="azure-active-directory-identity-protection-playbook"></a>Guía de protección de identidad de Active Directory de Azure 

Esta guía le ayuda a:

- Rellenar datos en el entorno de protección de identidad vulnerabilidad y simular eventos de riesgo
- Configurar directivas de acceso condicional basada en riesgos y probar el impacto de estas directivas


## <a name="simulating-risk-events"></a>Simulación de eventos de riesgo

Esta sección proporciona pasos para simular los siguientes tipos de evento de riesgo:

- Inicios de sesión de las direcciones IP anónimas (fáciles)
- Inicios de sesión de ubicaciones desconocidas (moderada)
- Viajes impide a ubicaciones atípicos (difíciles)

Otros eventos de riesgo no se pueden simular de forma segura.


### <a name="sign-ins-from-anonymous-ip-addresses"></a>Las direcciones IP de inicios de sesión de anónimos

Este tipo de evento de riesgo identifica los usuarios que han iniciado sesión correctamente desde una dirección IP que se ha identificado como una dirección IP de proxy anónimo. Estos servidores proxy se utilizan los usuarios que desea ocultar la dirección IP de su dispositivo y puede utilizarse para malintencionados.

**Para simular una sesión de una dirección IP anónima, realice los pasos siguientes**:

1.  Descargue el [Explorador de términos de referencia](https://www.torproject.org/projects/torbrowser.html.en).
2.  Usar el Tor Browser, vaya a [https://myapps.microsoft.com](https://myapps.microsoft.com).   
3.  Escriba las credenciales de la cuenta que desee que aparezca en el informe de **inicios de sesión de direcciones IP anónimas** .

En el inicio de sesión se muestra en el panel de protección de identidad durante 5 minutos. 


###<a name="sign-ins-from-unfamiliar-locations"></a>Inicios de sesión de ubicaciones desconocidas

El riesgo de ubicaciones desconocido es un mecanismo de evaluación de inicio de sesión en tiempo real que considera más allá de las ubicaciones de inicio de sesión (IP, latitud y longitud y ASN) para determinar las ubicaciones nuevos o desconocidas. El sistema almacena direcciones IP anterior, de latitud y longitud y ASNs de un usuario y considera que sean ubicaciones. Una ubicación de inicio de sesión se considera desconocida si la ubicación de inicio de sesión no coincide con cualquiera de las ubicaciones familiares existentes.

Protección de la identidad de Azure Active Directory:  

 - tiene un período de aprendizaje inicial de 14 días durante el cual no marca las nuevas ubicaciones como ubicaciones desconocidas.
 - pasa por alto los inicios de sesión desde dispositivos familiares y ubicaciones que están geográfico cerca de una ubicación familiar existente.

Para simular desconocidas ubicaciones, debe iniciar sesión desde una ubicación y un dispositivo que la cuenta no ha firmado en de antes. 


**Para simular una sesión desde una ubicación desconocida, siga estos pasos**:

1.  Elija una cuenta que tenga al menos un historial de inicio de sesión de 14 días. 

2.  Realice uno:
    
    una. Mientras utiliza una VPN, vaya a [https://myapps.microsoft.com](https://myapps.microsoft.com) y escriba las credenciales de la cuenta que desea simular el evento de riesgo.

    b. Pida a un compañero en una ubicación diferente para iniciar sesión con las credenciales de la cuenta (no recomendadas).

En el inicio de sesión se muestra en el panel de protección de identidad durante 5 minutos.
 
### <a name="impossible-travel-to-atypical-location"></a>Imposible viajes atípicos ubicación
Simular la condición de viaje impide es difícil porque utiliza el algoritmo de aprendizaje descartar falsos positivos como viajes impide desde dispositivos familiares o inicios de sesión de la VPN de otros usuarios en el directorio. Además, el algoritmo requiere un historial de inicio de sesión de 3 a 14 días para el usuario antes de que comience la generación de eventos de riesgo.

**Para simular un viaje impide a atípica ubicación, realice los pasos siguientes**:

1.  Mediante el explorador estándar, vaya a [https://myapps.microsoft.com](https://myapps.microsoft.com).  

2.  Escriba las credenciales de la cuenta que desea generar un evento de riesgo viajes impide para.

3.  Cambiar al agente de usuario. Puede cambiar el agente de usuario en Internet Explorer de herramientas de desarrollador, o cambiar al agente de usuario en Firefox o Chrome con un complemento de conmutador de agente de usuario.

4.  Cambiar la dirección IP. Puede cambiar la dirección IP a través de una VPN, un complemento de términos de referencia, o que gira un nuevo equipo en Azure en un centro de datos diferente.

5.  Inicio de sesión de [https://myapps.microsoft.com](https://myapps.microsoft.com) con las mismas credenciales como antes y dentro de unos minutos después del inicio de sesión anterior.

En el inicio de sesión se muestra en el panel de protección de identidad dentro de 2 a 4 horas.<br>
Debido a la máquina compleja que implica los modelos de aprendizaje, es probable que no obtenga seleccionado hacia arriba.<br> Desea replicar estos pasos para varias cuentas de Azure AD.


## <a name="simulating-vulnerabilities"></a>Simulación de vulnerabilidad 
Vulnerabilidad es puntos débiles en un entorno de Azure AD puede aprovecharse un actor incorrecto. Actualmente se expuesto 3 tipos de vulnerabilidad en protección de identidad de Azure AD que aprovechar otras características de Azure AD. Estas vulnerabilidad se mostrará en el panel de protección de identidad automáticamente una vez que estas características están configuradas.

-   Azure AD [autenticación multifactor?](../multi-factor-authentication/multi-factor-authentication.md)
-   [Detección de aplicación de nube](active-directory-cloudappdiscovery-whatis.md)de Azure AD.
-   [Administración de identidades con privilegios](active-directory-privileged-identity-management-configure.md)de Azure AD. 



##<a name="user-compromise-risk"></a>Riesgo de peligro de usuario

**Para probar el riesgo de compromiso de usuario, siga estos pasos**:

1.  Inicie sesión en [https://portal.azure.com](https://portal.azure.com) con credenciales de administrador global de su inquilino.

2.  Vaya a la **protección de la identidad**. 

3.  En el módulo de **protección de la identidad de Azure AD** principal, haga clic en **configuración**. 

4.  En el módulo de la **Configuración del Portal** en **las reglas de seguridad**, haga clic en **riesgos de peligro de usuario**. 

5.  En el módulo **riesgos de inicio de sesión** , desactivar **Habilitar regla** y, a continuación, haga clic en **Guardar** configuración.

6.  Para una cuenta de usuario determinado, simular una desconocido ubicaciones o un evento de riesgo IP anónimo. Esto elevar el nivel de riesgo de usuario para ese usuario en **medio**.

7.  Espere unos minutos y, a continuación, compruebe que el nivel de usuario para el usuario es **medio**.

8.  Vaya a la hoja de **Configuración del Portal** .

9.  En el módulo de **Riesgo de compromiso de usuario** , en **Habilitar regla**, seleccione **activado** . 

10. Seleccione una de las siguientes opciones:

    una. Para bloquear, seleccione **medio** en **bloque de inicio de sesión**.

    b. Para forzar el cambio de contraseña segura, seleccione **medio** en **Requerir autenticación multifactor**.

13. Haga clic en **Guardar**.

14. Ahora puede probar el acceso condicional basada en riesgos iniciando sesión con un usuario con un nivel de riesgo elevados. Si el riesgo de usuario es medio, según la configuración de la directiva, en el inicio de sesión es ser bloqueada o deberá cambiar la contraseña. 
<br><br>
![Guía](./media/active-directory-identityprotection-playbook/201.png "Playbook")
<br>

 
##<a name="sign-in-risk"></a>Inicio de sesión de riesgo

 
**Para probar un signo en riesgo, realice los pasos siguientes:**

1.  Inicie sesión en [https://portal.azure.com](https://portal.azure.com) con credenciales de administrador global de su inquilino.

2.  Vaya a la **protección de la identidad**.

3.  En el módulo de **protección de la identidad de Azure AD** principal, haga clic en **configuración**. 

4.  En el módulo de la **Configuración del Portal** en **las reglas de seguridad**, haga clic en **iniciar sesión en el riesgo**.

5.  En el módulo **riesgos de inicio de sesión **, seleccione **en** **Habilitar la regla**. 

7.  Seleccione una de las siguientes opciones:

    una. Para bloquear, seleccione **medio** en **bloque de inicio de sesión**

    b. Para forzar el cambio de contraseña segura, seleccione **medio** en **Requerir autenticación multifactor**.

8.  Para bloquear, seleccione Media en bloque de inicio de sesión.

9.  Para exigir la autenticación multifactor, seleccione **medio** en **Requerir autenticación multifactor**.

10. Haga clic en **Guardar**.

11. Ahora puede probar el acceso condicional basado en el riesgo mediante la simulación de las ubicaciones de desconocido o eventos de riesgo IP anónimos porque son los eventos de riesgo **medio** .

<br>
![Guía](./media/active-directory-identityprotection-playbook/200.png "Playbook")
<br>


## <a name="see-also"></a>Vea también

 - [Protección de la identidad de Azure Active Directory](active-directory-identityprotection.md)
