## <a name="defining-a-backup-policy"></a>Definir una directiva de copia de seguridad

Una directiva de copia de seguridad define una matriz de cuando se toman instantáneas de datos y, a continuación, ¿cuánto tiempo se conservan esas instantáneas. Al definir una directiva para una copia de seguridad una máquina virtual, puede desencadenar un trabajo de copia de seguridad *una vez al día*. Cuando se crea una nueva directiva, se aplica a la cámara. La interfaz de directiva de copia de seguridad es similar a esta:

![Directiva de copia de seguridad](./media/backup-create-policy-for-vms/backup-policy.png)

Para crear una directiva:

1. Escriba un nombre para el **nombre de la directiva**.

2. Pueden tomar instantáneas de los datos a intervalos diaria o semanal. Use el menú desplegable **Frecuencia de copia de seguridad** para elegir si instantáneas de datos se deben diaria o semanal.

    - Si elige un intervalo diario, use el control resaltado para seleccionar la hora del día para la instantánea. Para cambiar la hora, anule la selección de la hora y a continuación, seleccione la nueva hora.

    ![Directiva de copia de seguridad diaria](./media/backup-create-policy-for-vms/backup-policy-daily.png) <br/>

    - Si elige un intervalo semanal, use los controles de resaltado para seleccionar los días de la semana y la hora del día para tomar la instantánea. En el menú del día, seleccione uno o varios días. En el menú de hora, seleccione una hora. Para cambiar la hora, anule la selección de la hora seleccionada y seleccione la nueva hora.

    ![Directiva de copia de seguridad semanal](./media/backup-create-policy-for-vms/backup-policy-weekly.png)

3. De forma predeterminada, se seleccionan todas las opciones de **Intervalo de retención** . Desactive cualquier límite de intervalo de retención que no desea usar. A continuación, especifique la interval(s) usar.

    Mensual o anual rangos de retención le permiten especificar las instantáneas basadas en un incremento diario o semanal.

    >[AZURE.NOTE] Cuando se protege una máquina virtual, un trabajo de copia de seguridad ejecuta una vez al día. El tiempo cuando se ejecuta la copia de seguridad es el mismo para cada rango de retención.

4. Después de establecer todas las opciones de la directiva, en la parte superior de la hoja, haga clic en **Guardar**.

    La nueva directiva se aplica inmediatamente a la cámara.
