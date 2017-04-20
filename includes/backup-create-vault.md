## <a name="create-a-backup-vault"></a>Crear un depósito de copia de seguridad
Para hacer una copia archivos y datos de Windows Server o administrador de protección de datos (DPM) a Azure o cuando copia de seguridad de máquinas virtuales de IaaS en Azure, debe crear un depósito de copia de seguridad en la región geográfica donde desee almacenar los datos.

Los siguientes pasos le guiará a través de la creación de la cámara utilizada para almacenar las copias de seguridad.

1. Inicie sesión en el [Portal de administración](https://manage.windowsazure.com/)
2. Haga clic en **nuevo** > **Data Services** > **Servicios de recuperación** > **Depósito de copia de seguridad** y elija **Crear rápido**.

    ![Depósito de creación](./media/backup-create-vault/createvault1.png)

3. Para el parámetro **nombre** , escriba un nombre descriptivo para identificar el depósito de copia de seguridad. Debe ser única para cada suscripción.

4. Para el parámetro de **región** , seleccione la región geográfica para el depósito de copia de seguridad. La opción determina la región geográfica a la que se envían los datos de copia de seguridad. Al elegir una región geográfica cerca de su ubicación, puede reducir la latencia de red cuando una copia de seguridad en Azure.

5. Haga clic en **Crear depósito** para completar el flujo de trabajo. Puede pasar un tiempo para la copia de seguridad depósito que debe crearse. Para comprobar el estado, puede supervisar las notificaciones en la parte inferior del portal.

    ![Crear depósito](./media/backup-create-vault/creatingvault1.png)

6. Una vez creado el depósito de copia de seguridad, un mensaje le indica que la cámara se ha creado correctamente. La cámara también aparece en los recursos para los servicios de recuperación como **activa**.

    ![Creación de estado de la cámara](./media/backup-create-vault/backupvaultstatus1.png)


### <a name="azure-backup---storage-redundancy-options"></a>Copia de seguridad de Azure - opciones de redundancia de almacenamiento

>[AZURE.IMPORTANT] El mejor momento para identificar su opción de redundancia de almacenamiento es justo después de la creación de cámara y antes de que los equipos están registrados para la cámara. Una vez que se ha registrado un elemento a la cámara, la opción de redundancia de almacenamiento está bloqueada y no se puede modificar.

Necesidades empresariales deben determinar la redundancia de almacenamiento del almacenamiento de información de back-end de copia de seguridad de Azure. Si está utilizando Azure como un extremo de almacenamiento de copia de seguridad principal (por ejemplo, copia de seguridad en Azure desde un servidor de Windows), debe tener en cuenta la opción de almacenamiento Geo redundantes de selección (opción predeterminada). Esto se ve en la opción de **configuración** de la cámara de copia de seguridad.

![GRS](./media/backup-create-vault/grs.png)

#### <a name="geo-redundant-storage-grs"></a>Almacenamiento geo redundantes (GRS)
GRS mantiene seis copias de sus datos. Con GRS, los datos se duplica tres veces dentro de la región principal y también se duplica tres veces en una región secundaria cientos de millas fuera de la región principal, proporcionar el nivel de duración. En caso de error en la región principal, al almacenar los datos en GRS, copia de seguridad de Azure garantiza que sus datos son resistentes en dos regiones independientes.

#### <a name="locally-redundant-storage-lrs"></a>Almacenamiento redundante local (LRS)
Almacenamiento redundante local (LRS) mantiene tres copias de los datos. LRS se duplica tres veces dentro de una sola instalación en una única región. LRS protege los datos de errores de hardware normal, pero no de error en una instalación de Azure todo.

Si está utilizando Azure como un extremo de almacenamiento de copia de seguridad de tercer (por ejemplo, va a usar SCDPM para hacer una copia de seguridad local copia local y el uso de Azure para necesidades de su retención a largo plazo), debe considerar la posibilidad de elegir almacenamiento redundante localmente en la opción de **configuración** de la cámara de copia de seguridad. Esto incorpora el coste de almacenar los datos en Azure, al tiempo que proporciona un nivel inferior de duración para los datos que podrían ser aceptables para el tercer copias.

![LRS](./media/backup-create-vault/lrs.png)
