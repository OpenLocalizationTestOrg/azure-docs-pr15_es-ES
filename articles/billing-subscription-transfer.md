<properties
   pageTitle="Transferir la propiedad de una suscripción de Azure | Microsoft Azure"
   description="Cómo transferir una suscripción de Azure a otro usuario y algunas preguntas más frecuentes (P+F) sobre el proceso"
   services=""
   documentationCenter=""
   authors="genlin"
   manager="stevenpo"
   editor=""
   tags="billing,top-support-issue"/>

<tags
   ms.service="billing"
   ms.workload="na"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="10/10/2016"
   ms.author="genli"/>

# <a name="transferring-ownership-of-an-azure-subscription"></a>Transferir la propiedad de una suscripción de Azure

Y tú:

- ¿Tenga a mano sobre la facturación de la suscripción a otra persona Azure propiedad?
- ¿Desea cambiar la cuenta utilizada para iniciar sesión en Azure? Quizás utiliza su Account Microsoft pero pensado usar su trabajo o escuela cuenta en su lugar?
- ¿Desea mover la suscripción Azure de un directorio a otro?
- ¿Tiene Azure y Office 365 en diferentes inquilinos y desea consolidar?

Puede ahora hacerlo fácilmente en la Microsoft Azure cuenta Centro - para pago por uso, MSDN, Action Pack o BizSpark suscripciones.  Hemos agregado la capacidad de transferir la suscripción a otro usuario. En otras palabras, ahora puede cambiar el Administrador de cuenta en cualquier pago por uso, MSDN, Action Pack o BizSpark suscripción que es el propietario, no importa qué país que trabaja. Ahora admitimos a la transferencia de compras de Azure Marketplace para estos tipos de suscripción.

> [AZURE.NOTE] Para cambiar su suscripción a una oferta diferente, vea [cambiar la suscripción a otra oferta Azure](billing-how-to-switch-azure-offer.md) para obtener más información. Si necesita más ayuda en cualquier punto de este artículo, póngase [en contacto con soporte técnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para obtener el problema resuelto rápidamente.

## <a name="how-to-transfer-ownership-of-an-azure-subscription"></a>Cómo transferir la propiedad de una suscripción de Azure

> [AZURE.VIDEO transfer-an-azure-subscription]

1.  Iniciar sesión en <https://account.windowsazure.com/Subscriptions>. Debe ser administrador de la cuenta para realizar a una transferencia de la propiedad. Para obtener más información acerca de cómo averiguar quién es el Administrador de la cuenta de la suscripción, consulte las [preguntas más frecuentes](#faq).

2.  Seleccione la suscripción para transferir.

3.  Haga clic en la opción de **Transferencia de suscripción** .

    ![Ficha de suscripciones de la cuenta de Azure](./media/billing-subscription-transfer/image1.png)

4.  Siga las indicaciones para especificar al destinatario.

    ![Cuadro de diálogo de suscripción de transferencia](./media/billing-subscription-transfer/image2.PNG)

5.  El destinatario recibirá automáticamente un correo electrónico con un vínculo de aceptación.

    ![Correo electrónico de la transferencia de suscripción al destinatario](./media/billing-subscription-transfer/image3.png)

6.  El destinatario hace clic en el vínculo y sigue las instrucciones, incluidos introducir su información de pago.

    ![Primera página web de transferencia de suscripción](./media/billing-subscription-transfer/image4.png)

    ![Segunda página web de transferencia de suscripción](./media/billing-subscription-transfer/image5.png)

7. ¡Éxito! La suscripción se transfiere.

<a id="faq"></a>
## <a name="frequently-asked-questions-faq"></a>Preguntas más frecuentes (P+F)

-   **¿Cómo puedo saber quién es el Administrador de la cuenta de la suscripción?**

    Puede confirmar que es el Administrador de la cuenta de la suscripción como sigue:

    1. Inicie sesión en el [portal de Azure](https://portal.azure.com).
    2. En el menú de concentrador, seleccione la **suscripción**.
    3. Seleccione la suscripción que desea comprobar y, a continuación, seleccione **configuración**.
    4. Seleccione **Propiedades**. El Administrador de la cuenta de la suscripción se mostrará en el cuadro **Cuenta de administrador** .  

-   **¿Da como resultado una transferencia de suscripción en ningún tiempo de inactividad de servicio?**

    No hay ningún impacto en el servicio. Este eficaz cancela la suscripción en el Administrador de cuenta actual y crea uno nuevo en la cuenta del destinatario, pero asocia los servicios de Azure subyacentes a la nueva suscripción. El identificador de suscripción es el mismo.

-   **¿Cómo se puede usar este mecanismo para cambiar el directorio de suscripción?**-   
    Se crea una suscripción de Azure en el directorio al que pertenece el Administrador de la cuenta. Por lo tanto, para cambiar el directorio, simplemente transferir la suscripción a una cuenta de usuario en el directorio de destino. Cuando el usuario completa los pasos para aceptar la transferencia, la suscripción pasará automáticamente al directorio de destino.

-   **¿Si tomar el control de facturación de la propiedad de una suscripción de otra organización, seguirá teniendo acceso a Mis recursos?**

    Si la suscripción se transfiere a otro inquilino, los usuarios asociados con el inquilino anterior pierden el acceso a la suscripción. Incluso si un usuario ya no sea un administrador de servicios o administración de Co, puede seguir teniendo acceso a la suscripción a través de otros mecanismos de seguridad. Se incluyen:
    - Certificados de administración que concesión al usuario derechos de administración para recursos de suscripción. Para obtener más información, vea [crear y cargar un certificado de administración de Azure](https://msdn.microsoft.com/library/azure/gg551722.aspx)
    -   Teclas de acceso a servicios como almacenamiento. Para obtener más información, vea [Ver, copiar y las teclas de acceso de almacenamiento regenerar](storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys)
    -   Las credenciales de acceso remotas para servicios como máquinas virtuales de Azure

    No es una lista completa. Debe tener en cuenta al destinatario Actualizar ningún secreto asociado con el servicio si necesita restringir el acceso a sus recursos. Más recursos pueden actualizarse como sigue:

    1.   Vaya al portal de Azure: [ *https://portal.azure.com*](https://portal.azure.com)

    2.    Haga clic en examinar todos -&gt; todos los recursos

    3.    Seleccione el recurso. Se abrirá la hoja de recursos.

    4.    En la hoja de recursos, haga clic en **configuración**. Aquí puede ver y actualizar información confidencial existente.


-   **¿Si puedo transferir la suscripción en el medio del ciclo de facturación, ciclo el pago de destinatarios para la facturación todo?**

    El remitente es responsable de pago para el uso que se ha informado hasta el momento en que se haya completado la transferencia. El destinatario es responsable de uso notificado desde el momento de transferencia en adelante. Es posible que algunos uso que realizó antes de la transferencia, pero se notifica posteriormente. Se incluirán en la factura del destinatario.

-   **¿El destinatario tiene acceso al historial de facturación y de uso?**

    En este momento, la única información revelada al destinatario es la cantidad de la última factura (o el saldo actual, si la suscripción se ha transferido antes de que generó el primer efecto). El resto del historial de facturación y uso no se transfiere con la suscripción.

-   **¿Se puede cambiar la oferta durante una transferencia?**

    La oferta debe permanecer en la misma. Para cambiar su oferta, deberá [ponerse en contacto con soporte técnico](http://go.microsoft.com/fwlink/?LinkID=619338).

-   **¿Puedo transferir una suscripción a una cuenta de usuario en otro país?**

    No, en este momento no es compatible. Cuenta de usuario del destinatario debe estar en el mismo país.

-   **¿El destinatario puede usar mecanismo de pago diferente?**

    Sí. Existen limitaciones aquí: ahora el historial de facturación de la suscripción se divide en dos cuentas. Pero la ventaja es que puede hacer esto sin tener que [ponerse en contacto con soporte técnico](http://go.microsoft.com/fwlink/?LinkID=619338).

-   **¿El método de pago afectará después de transferir una suscripción de Azure?**

    Para aceptar a una transferencia de suscripción, debe proporcionar una tarjeta de crédito o el método de pago similar para pagar la suscripción. Por ejemplo, si Bob transfiere una suscripción a Julia y Julia acepta a la transferencia, Jane también debe proporcionar un método de pago que usará para pagar la suscripción. Una vez completada la transferencia, Bob ya no se cobrará por la suscripción que transfiere a María.

## <a name="next-steps-after-accepting-ownership-of-a-subscription"></a>Pasos siguientes después de aceptar la propiedad de una suscripción

1. Ahora es el Administrador de la cuenta. Revisar y actualizar el administrador del servicio y coadministradores. Administrar los administradores en el [portal de clásica Azure](https://manage.windowsazure.com) , vaya a configuración. [Más información](http://go.microsoft.com/fwlink/?LinkID=533293).
2. También puede usar el control de acceso basado en roles (RBAC) para su suscripción y servicios. Visite el [portal de Azure](https://portal.azure.com) [más información sobre RBAC](http://go.microsoft.com/fwlink/?LinkID=544802)
3. Actualizar credenciales asociadas con los servicios de la suscripción. Se incluyen:
    - Certificados de administración que concesión al usuario derechos de administración para recursos de suscripción. Para obtener más información, vea [crear y cargar una administración de certificados de Azure](https://msdn.microsoft.com/library/azure/gg551722.aspx)
    -   Teclas de acceso a servicios como almacenamiento. Para obtener más información, vea [Ver, copiar y las teclas de acceso de almacenamiento regenerar](storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys)
    -   Las credenciales de acceso remotas para servicios como máquinas virtuales de Azure
4. Actualización de alertas para esta suscripción, en el [Centro de la cuenta de Azure](https://account.windowsazure.com/Subscriptions)[más información](http://go.microsoft.com/fwlink/?LinkID=533292) de facturación  
5.  Si está trabajando con un compañero, considere la posibilidad de actualizar el identificador de partner en esta suscripción. Puede hacerlo en el [Centro de la cuenta de Azure](https://account.windowsazure.com/Subscriptions).

> [AZURE.NOTE] Si aún tiene más preguntas, póngase [en contacto con soporte técnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para obtener el problema resuelto rápidamente.
