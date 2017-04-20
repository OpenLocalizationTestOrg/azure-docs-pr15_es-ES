## <a name="scenario"></a>Escenario

Este documento le guiará a través de una implementación que usa varias NIC en máquinas virtuales en un escenario concreto. En este escenario, tiene una carga de trabajo de IaaS de dos niveles hospedado en Azure. Cada nivel se implementa en su propia subred en una red virtual (VNet). El nivel de front-end se compone de varios servidores web, agrupados en un equilibrador de carga para alta disponibilidad. El nivel de back-end se compone de varios servidores de base de datos. Estos servidores de base de datos se implementará con dos NIC cada, uno para el acceso de la base de datos, el otro para la administración. El escenario también incluye los grupos de seguridad de red (NSGs) para controlar qué tráfico se permite a cada subred y NIC de la implementación. La figura siguiente muestra la arquitectura básica de este escenario.  

![Escenario de MultiNIC](./media/virtual-network-deploy-multinic-scenario-include/Figure1.png)

