Un equilibrador de carga de Azure es un equilibrador de carga de la capa 4 (TCP, UDP). El equilibrador de carga proporciona alta disponibilidad al distribuir tráfico entrante entre instancias de servicio saludable en los servicios de nube o máquinas virtuales en un conjunto de equilibrador de carga. Azure equilibrador de carga también puede presentar dichos servicios en varios puertos, varias direcciones IP o ambos.

Puede configurar un equilibrador de carga para:

* Cargar saldo tráfico entrante de Internet a las máquinas virtuales (VMs). Nos referimos a un equilibrador de carga en este escenario como un [equilibrador de carga a través de Internet](../articles/load-balancer/load-balancer-internet-overview.md).
* Carga saldo tráfico entre máquinas virtuales en una red virtual (VNet), entre las máquinas virtuales en los servicios de nube o entre equipos locales y máquinas virtuales en una red virtual entre locales. Nos referimos a un equilibrador de carga en este escenario como un [equilibrador de carga interno (DCI)](../articles/load-balancer/load-balancer-internal-overview.md).
* Reenvíe el tráfico externo a una instancia específica de la máquina virtual.
