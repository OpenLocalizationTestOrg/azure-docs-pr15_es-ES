<!--author=SharS last changed: 01/15/2016-->

#### <a name="to-install-update-12-from-the-azure-classic-portal"></a>Para instalar la actualización 1.2 desde el portal de clásico de Azure

1. En la página de servicio StorSimple, seleccione el dispositivo. Vaya a **dispositivos** > **Mantenimiento**.

2. En la parte inferior de la página, haga clic en **Examinar actualizaciones**. Se creará un trabajo para buscar actualizaciones disponibles. Se le notificará cuando el trabajo se ha completado correctamente.

3. En la sección de **Actualizaciones de Software** en la misma página, verá que hay disponibles nuevas actualizaciones de software. Se recomienda que lea las notas antes de aplicar la actualización 1.2 en su dispositivo.

    ![Instalar actualizaciones de software](./media/storsimple-install-update-via-portal/InstallUpdate12_11M.png)

4. En la parte inferior de la página, haga clic en **Instalar actualizaciones**.

5. Se le pedirá confirmación. Haga clic en **Aceptar**.

6. Se presentará un cuadro de diálogo **Instalar actualizaciones** . El dispositivo debe cumplir las comprobaciones mencionadas en este cuadro de diálogo. Se han completado estos pasos antes de la actualización. Seleccione **entender el requisito anterior y estoy listo para actualizar el dispositivo**. Haga clic en el icono de verificación.

    ![Mensaje de confirmación](./media/storsimple-install-update-via-portal/InstallUpdate12_2M.png)

7. Ahora se iniciará un conjunto de comprobaciones previa automáticas. Se incluyen:

    - **Comprobaciones de estado de los controladores** para comprobar que los controladores de dispositivo están en línea y en buen estado.
    
    - **Comprobaciones de estado del componente de hardware** para comprobar que todos los componentes de hardware en el dispositivo StorSimple correcto.
    
    - **Datos 0 comprueba** para comprobar que los datos 0 está habilitados en el dispositivo. Si no está habilitada esta interfaz, debe habilitarlo y, a continuación, vuelva a intentarlo.
    
    - **Comprobaciones de datos 2 y 3 de datos** para comprobar que no están habilitadas interfaces de red de datos 2 y 3 de datos. Si estas interfaces están habilitadas, deberá deshabilitar ellos y, a continuación, intente actualizar el dispositivo. Esta comprobación se realiza solo si está actualizando desde un dispositivo de software GA. Dispositivos que ejecutan versiones 0,1, 0,2 o 0,3 no tendrán esta comprobación.
    
    - **Comprobar la puerta de enlace** en cualquier dispositivo que ejecute una versión anterior a 1 de actualización. Esta comprobación se realiza en todo el dispositivo anteriores a la actualización de software 1, pero se produce un error en los dispositivos que tienen una puerta de enlace configurada para una interfaz de red que no sean datos 0.
 
    Actualización 1.2 solo se aplicará si todas las comprobaciones anteriores anteriores a la actualización se ha completado correctamente. Se le notificará que son comprobaciones anteriores a la actualización en curso.
  
    ![Notificación de comprobación preliminar](./media/storsimple-install-update-via-portal/InstallUpdate12_3M.png)

    A continuación se muestra un ejemplo en el que la comprobación antes de la actualización falló. Deberá comprobar que los controladores de dispositivo están en línea y en buen estado. También necesitará comprobar el estado de los componentes de hardware. En este ejemplo, controlador 0 y 1 controlador componentes necesitan atención. Debe ponerse en contacto con Microsoft Support si no puede resolver estos problemas por sí mismo.

     ![Error de comprobación preliminar](./media/storsimple-install-update-via-portal/HCS_PreUpgradeChecksFailed-include.png)

    > [AZURE.NOTE] Después de aplicar actualización 1.2 en su dispositivo StorSimple, controles de datos 2 y 3 de datos y la comprobación de la puerta de enlace ya no será necesarios para futuras actualizaciones. Las comprobaciones de anteriores seguirán siendo necesarias.


8. Cuando las comprobaciones antes de la actualización se ha completado correctamente, se creará un trabajo de actualización. Se le notificará cuando se ha creado correctamente el trabajo de actualización.
 
    ![Creación de trabajo de actualización](./media/storsimple-install-update-via-portal/InstallUpdate12_44M.png)

    A continuación, se aplicará la actualización en el dispositivo.
 
9. Para supervisar el progreso de la tarea de actualización, haga clic en **Vista de trabajo**. En la página **tareas** , puede ver el progreso de la actualización. 

    ![Actualizar el progreso de tarea](./media/storsimple-install-update-via-portal/InstallUpdate12_5M.png)

10. La actualización tardará unas horas en completarse. Puede ver los detalles de la tarea en cualquier momento.

    ![Actualizar detalles del trabajo](./media/storsimple-install-update-via-portal/InstallUpdate12_6M.png)

11. Una vez completada la tarea, vaya a la página de **Mantenimiento** y desplácese hacia abajo hasta **Las actualizaciones de Software**.

12. Compruebe que el dispositivo está ejecutando **StorSimple 8000 Series actualización 1.2 (6.3.9600.17584)**. También se debe modificar la **última actualización de fecha** .

    ![Página de mantenimiento](./media/storsimple-install-update-via-portal/InstallUpdate12_10M.png)

13. Ahora, verá que hay disponibles actualizaciones de modo de mantenimiento. Estas actualizaciones son actualizaciones interrupciones que provocan el tiempo de inactividad del dispositivo y solo se pueden aplicar a través de la interfaz de Windows PowerShell de su dispositivo. Siga las instrucciones de [instalar las actualizaciones de modo de mantenimiento](storsimple-update-device.md#install-maintenance-mode-updates-via-windows-powershell-for-storsimple) para instalar estas actualizaciones a través de Windows PowerShell para StorSimple.

> [AZURE.NOTE] En algunos casos, se mostrará el mensaje que indica que hay actualizaciones de modo de mantenimiento de 24 horas después de aplicaron correctamente las actualizaciones de modo de mantenimiento en el dispositivo.  


