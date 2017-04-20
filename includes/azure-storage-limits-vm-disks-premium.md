**Máquina virtual discos: por los límites de la cuenta**

Recursos|Límite predeterminado
---|---
Capacidad total de disco por cuenta|35 TB
Capacidad total instantánea por cuenta|10 TB
Ancho de banda de Max por cuenta (entrada + salida<sup>1</sup>)|< = 50 GB/s

<sup>1</sup> *Entrada* se refiere a todos los datos (solicitudes) que se envían a una cuenta de almacenamiento. *Salida* se refiere a todos los datos (respuestas) recibidos desde una cuenta de almacenamiento.

**Máquina virtual discos: por los límites de disco**

Tipo de disco de almacenamiento de Premium | P10 | P20 | P30
---|---|---|---
Tamaño del disco | GiB 128 | 512 giB | 1024 giB (1 TB)
Max IOPS por disco | 500 | 2300 | 5000
Rendimiento máximo por disco | 100 MB por segundo | 150 MB por segundo | 200 MB por segundo
Número máximo de discos por cuenta de almacenamiento | 280 | 70 | 35

**Máquina virtual discos: por los límites VM**

Recursos|Límite predeterminado
---|---
Max IOPS por máquina virtual|80.000 IOPS con GS5 VM<sup>1</sup>
Rendimiento máximo por máquina virtual|2.000 MB/s con GS5 VM<sup>1</sup>

<sup>1</sup> Consulte [Tamaño de memoria virtual](../articles/virtual-machines/virtual-machines-linux-sizes.md) para límites en otros tamaños de máquina virtual. 
