<properties 
   pageTitle="Configure el CHAP para el dispositivo StorSimple | Microsoft Azure"
   description="Describe cómo configurar el protocolo de autenticación por desafío mutuo (CHAP) en un dispositivo de StorSimple."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="TBD"
   ms.date="08/17/2016"
   ms.author="alkohli" />

# <a name="configure-chap-for-your-storsimple-device"></a>Configure el CHAP para el dispositivo StorSimple

En este tutorial se explica cómo configurar el CHAP para el dispositivo StorSimple. El procedimiento descrito en este artículo se aplica a la serie 8000 StorSimple, así como StorSimple 1200 dispositivos.

CHAP significa Protocolo de autenticación. Es un esquema de autenticación usado servidores para validar la identidad de los clientes remotos. La comprobación se basa en una contraseña compartida o secreto. CHAP puede ser unidireccional (unidireccional) o mutuo (bidireccional). CHAP unidireccional es cuando el destino autentica el iniciador. CHAP mutuo o inverso, por otro lado, requiere que el destino autenticar el iniciador y, a continuación, el iniciador autentique el destino. Puede implementar la autenticación del iniciador sin autenticación de destino. Sin embargo, se puede implementar la autenticación de destino sólo si también se implementa la autenticación del iniciador. 

Como práctica recomendada, le recomendamos que use CHAP para mejorar la seguridad iSCSI.

>[AZURE.NOTE] Tenga en cuenta que IPSEC no es compatible actualmente en los dispositivos StorSimple.

La configuración de CHAP en el dispositivo StorSimple se puede configurar de las siguientes maneras:

- Autenticación unidireccional o unidireccional

- Autenticación mutua o inversa o bidireccional

En cada uno de estos casos, el portal para el dispositivo y el software del iniciador de iSCSI de servidor debe estar configurado. En el siguiente tutorial, se describen los pasos detallados para esta configuración.

## <a name="unidirectional-or-one-way-authentication"></a>Autenticación unidireccional o unidireccional

En la autenticación unidireccional, el destino autentica el iniciador. Esta autenticación requiere que configure la configuración de iniciador CHAP en el iniciador de software en el host de iSCSI y el dispositivo de StorSimple. Los procedimientos detallados para su dispositivo StorSimple y el host de Windows se describen a continuación.

#### <a name="to-configure-your-device-for-one-way-authentication"></a>Para configurar el dispositivo para autenticación unidireccional

1. En el portal de clásico Azure, en la página de **dispositivos** , haga clic en la ficha **Configurar** .

    ![Iniciador CHAP](./media/storsimple-configure-chap/IC740943.png)

2. Desplácese hacia abajo en esta página y en la sección **Iniciador CHAP** :
                                                    
    1. Proporcione un nombre de usuario para el iniciador CHAP.

    2. Proporcionar una contraseña para el iniciador CHAP.

         > [AZURE.IMPORTANT] El nombre de usuario CHAP debe contener menos de 233 caracteres. La contraseña CHAP debe estar entre 12 y 16 caracteres. Un nombre de usuario o contraseña más larga se producirá un error de autenticación en el host de Windows.
    
    3. Confirme la contraseña.

4. Haga clic en **Guardar**. Se mostrará un mensaje de confirmación. Haga clic en **Aceptar** para guardar los cambios.

#### <a name="to-configure-one-way-authentication-on-the-windows-host-server"></a>Para configurar la autenticación unidireccional en el servidor de host de Windows

1. En el servidor de host de Windows, inicie el iniciador iSCSI.

2. En la ventana **Propiedades del iniciador iSCSI** , realice los pasos siguientes:
                                                    
    1. Haga clic en la ficha **detección** .

        ![propiedades del iniciador iSCSI](./media/storsimple-configure-chap/IC740944.png)

    2. Haga clic en **descubrir Portal**.

3. En el cuadro de diálogo **Descubrir Portal de destino** :
                                                    
    1. Especifique la dirección IP del dispositivo.

    3. Haga clic en **Avanzadas**.

        ![Descubra el portal de destino](./media/storsimple-configure-chap/IC740945.png)

4. En el cuadro de diálogo **Configuración avanzada** :
                                                    
    1. Seleccione la casilla de verificación **Habilitar CHAP iniciar sesión** .

    2. En el campo **nombre** , proporcione el nombre de usuario que haya especificado para el iniciador CHAP en el portal de clásico.

    3. En el campo **secreto de destino** , proporcione la contraseña que ha especificado para el iniciador CHAP en el portal de clásico.

    4. Haga clic en **Aceptar**.

        ![Configuración avanzada general](./media/storsimple-configure-chap/IC740946.png)

5. En la ficha **destinos** de la ventana de **Propiedades del iniciador iSCSI** , debería aparecer el estado del dispositivo **conectado**. Si está usando un dispositivo de StorSimple 1200, cada volumen se montará como un destino iSCSI tal como se muestra a continuación. Por lo tanto, deberá repetir para cada volumen pasos 3 y 4.

    ![Volúmenes montados como destinos independientes](./media/storsimple-configure-chap/chap4.png)

    > [AZURE.IMPORTANT] Si cambia el nombre iSCSI, se utilizará el nuevo nombre para nuevas sesiones iSCSI. Nueva configuración no se utiliza para existentes sesiones hasta que cierre la sesión e inicie sesión en nuevo.

Para obtener más información acerca de cómo configurar CHAP en el servidor de host de Windows, vaya a [Consideraciones adicionales](#additional-considerations).


## <a name="bidirectional-or-mutual-authentication"></a>Autenticación bidireccional o mutuo

En la autenticación bidireccional, el destino autentica el iniciador y, a continuación, el iniciador autentica el destino. Requiere que el usuario configurar los parámetros de iniciador CHAP, así como la configuración de CHAP inversa en el software del iniciador iSCSI y dispositivo host. Los procedimientos siguientes describen los pasos para configurar la autenticación mutua en el dispositivo y en el host de Windows.

#### <a name="to-configure-your-device-for-mutual-authentication"></a>Para configurar el dispositivo para la autenticación

1. En el portal de clásico Azure, en la página de **dispositivos** , haga clic en la ficha **Configurar** .

    ![Destino CHAP](./media/storsimple-configure-chap/IC740948.png)

2. Desplácese hacia abajo en esta página y en la sección **CHAP de destino** :
                                                    
    1. Proporcione un **nombre de usuario CHAP invertir** para su dispositivo.

    2. Proporcionar una **contraseña invertir CHAP** para el dispositivo.

    3. Confirme la contraseña.

3. En la sección **Iniciador CHAP** :
                                                
    1. Proporcione un **nombre de usuario** de su dispositivo.

    1. Proporcione una **contraseña** para el dispositivo.

    3. Confirme la contraseña.

4. Haga clic en **Guardar**. Se mostrará un mensaje de confirmación. Haga clic en **Aceptar** para guardar los cambios.

#### <a name="to-configure-bidirectional-authentication-on-the-windows-host-server"></a>Para configurar la autenticación bidireccional en el servidor de host de Windows

1. En el servidor de host de Windows, inicie el iniciador iSCSI.

2. En la ventana **Propiedades del iniciador iSCSI** , haga clic en la pestaña **configuración** .

3. Haga clic en **CHAP**.

4. En el cuadro de diálogo **iSCSI iniciador un secreto CHAP mutuo** :
                                                    
    1. Escriba **Invertir contraseña CHAP** que configuró en el portal de clásico de Azure.

    2. Haga clic en **Aceptar**.

        ![secreto CHAP mutuo del iniciador iSCSI](./media/storsimple-configure-chap/IC740949.png)

5. Haga clic en la ficha **destinos** .

6. Haga clic en el botón **Conectar** . 

7. En el cuadro de diálogo **Conectar al destino** , haga clic en **Avanzadas**.

8. En el cuadro de diálogo **Propiedades avanzadas** :
                                                    
    1. Seleccione la casilla de verificación **Habilitar CHAP iniciar sesión** .

    2. En el campo **nombre** , proporcione el nombre de usuario que haya especificado para el iniciador CHAP en el portal de clásico.

    3. En el campo **secreto de destino** , proporcione la contraseña que ha especificado para el iniciador CHAP en el portal de clásico.

    4. Active la casilla de verificación de **realizar la autenticación mutua** .

        ![Autenticación mutuo de configuración avanzada](./media/storsimple-configure-chap/IC740950.png)

    5. Haga clic en **Aceptar** para completar la configuración de CHAP
     
Para obtener más información acerca de cómo configurar CHAP en el servidor de host de Windows, vaya a [Consideraciones adicionales](#additional-considerations).

## <a name="additional-considerations"></a>Consideraciones adicionales

La característica de **Conexión rápida** no admite las conexiones que tienen CHAP habilitado. Cuando se activa CHAP, asegúrese de que use el botón **Conectar** que está disponible en la ficha **destinos** para conectarse a un destino.

![Conectarse al destino](./media/storsimple-configure-chap/IC740947.png)

En el cuadro de diálogo **conectar al destino** que se presenta, seleccione la casilla de verificación **Agregar esta conexión a la lista de destinos favoritos** . Así se garantiza que cada vez que se reinicia el equipo, se realiza un intento para restaurar la conexión a los destinos favoritos de iSCSI.

## <a name="errors-during-configuration"></a>Errores durante la configuración

Si la configuración de CHAP es incorrecta, es probable que vea un mensaje de error de **Error de autenticación** .

## <a name="verification-of-chap-configuration"></a>Comprobación de la configuración de CHAP

Puede comprobar que se utiliza CHAP complete los pasos siguientes.

#### <a name="to-verify-your-chap-configuration"></a>Para comprobar la configuración de CHAP

1. Haga clic en **destinos favoritos**.

2. Seleccione el destino que habilitó la autenticación.

3. Haga clic en **Detalles**.

    ![destinos favoritos de propiedades de iniciador de iSCSI](./media/storsimple-configure-chap/IC740951.png)

4. En el cuadro de diálogo **Detalles de destino favoritos** , tenga en cuenta la entrada en el campo **autenticación** . Si la configuración se realizó correctamente, debe indicar **CHAP**.

    ![Detalles de destino favorito](./media/storsimple-configure-chap/IC740952.png)

## <a name="next-steps"></a>Pasos siguientes

- Más información sobre la [seguridad de StorSimple](storsimple-security.md).

- Más información sobre cómo [usar el servicio de administrador de StorSimple para administrar el dispositivo de StorSimple](storsimple-manager-service-administration.md).
