## <a name="peering-across-subscriptions"></a>Interconexión entre suscripciones

En este escenario creará una interconexión entre dos VNets que pertenecen a diferentes suscripciones.

![escenario de sub cruzada](./media/virtual-networks-create-vnetpeering-scenario-crosssub-include/figure01.PNG)

VNet interconexión se basa en el control de acceso basado en roles (RBAC) para la autorización. Para el escenario entre suscripciones, debe conceder permisos suficientes a los usuarios que creará el vínculo de interconexión:

> [AZURE.NOTE] Si el mismo usuario tiene los privilegios sobre ambas suscripciones, puede omitir siguiente paso 1-4.
