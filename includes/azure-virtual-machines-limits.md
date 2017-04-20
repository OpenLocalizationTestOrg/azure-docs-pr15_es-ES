Recursos|Límite predeterminado|Límite máximo
---|---|---
[Máquinas virtuales](../articles/virtual-machines/virtual-machines-linux-about.md) por el servicio de nube<sup>1</sup>|50|50
Entrada extremos por servicio de nube<sup>2</sup>|150|150

<sup>1</sup> Máquinas virtuales creadas en administración del servicio (en lugar de administrador de recursos) se almacenan automáticamente en un servicio de nube. Puede agregar más máquinas virtuales a dicho servicio de nube para equilibrio de carga y la disponibilidad. Consulte [cómo conectar máquinas virtuales con una red Virtual o un servicio de nube](../articles/virtual-machines/virtual-machines-linux-classic-connect-vms.md).

<sup>2</sup> Entrada extremos permitirán las comunicaciones a una máquina virtual desde fuera de servicio de nube de la máquina virtual. Servicio de nube de máquinas virtuales de Windows en la misma o una red virtual puede comunican automáticamente. Consulte [cómo configurar los extremos de una máquina Virtual](../articles/virtual-machines/virtual-machines-windows-classic-setup-endpoints.md). 
