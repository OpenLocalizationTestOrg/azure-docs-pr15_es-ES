<properties 
   pageTitle="Interfaces de hardware para StorSimple 10 GbE | Microsoft Azure"
   description="Describe los transceivers de factor de forma pequeño compatibles acoplable (SFP), cables y modificadores para las interfaces de red GbE 10 en el dispositivo StorSimple."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="09/21/2016"
   ms.author="alkohli" />

# <a name="supported-hardware-for-the-10-gbe-network-interfaces-on-your-storsimple-device"></a>Hardware compatible para las interfaces de red GbE 10 en el dispositivo StorSimple

## <a name="overview"></a>Información general

En este artículo se proporciona información sobre el hardware adicional que funciona con el dispositivo de Microsoft Azure StorSimple.

## <a name="list-of-devices-tested-by-microsoft"></a>Lista de dispositivos probado por Microsoft

Microsoft ha probado los siguientes transceivers (SFP) acoplable de factor de forma pequeño, cables y cambia a garantizar que funcionan correctamente con dispositivos. (En las siguientes tablas se actualizarán mientras se prueba nuevo hardware.)

### <a name="sfp-transceivers"></a>Transceptores SFP +

|Asegúrese de|Modelo|
|---|---|
|Cisco|SFP-10G-SR|

### <a name="cables"></a>Cables

|S. No. |Asegúrese de|Modelo|
|---|---|---|
| 1.|Cisco|SFP-H10GB-CU1M|
| 2.|Cisco|SFP-H10GB-CU2M|
| 3.|Cisco|SFP-H10GB-CU3M|
| 4.|Tipp interacción|N820 - 05M (OM3)|

### <a name="switches"></a>Modificadores

|S. No.|Asegúrese de|Modelo|
|---|---|---|
| 1. |Cisco|N3K-C3172PQ-10GE|
| 2. |Cisco|N3K-C3048-ZM-F|
| 3. |Cisco|N5K-C5596UP-FA|

## <a name="list-of-devices-tested-in-the-field"></a>Lista de dispositivos probado en el campo

Esta sección contiene la lista de dispositivos que se han implementado correctamente en el campo StorSimple clientes. No se ha probado por Microsoft, pero es probable que funcione con su dispositivo StorSimple.
 
| Parámetro                         | Valor                                    |
|-----------------------------------|------------------------------------------|
| Asegúrese de cambiar                     | Juniper                                  |
| Conmutador de modelo                    | ex4550 32F                               |
| Versión del sistema operativo de conmutador | JunOS 12.3R9.4                           |
| Modelo de placa                     | Puertos incorporado (PIC 0)                    |
| Creación de transceptor                  | Juniper                                  |
| Modelo de transceptor               | Número de pieza 021308 740 <br></br> Número de pieza 030658 740                   |
| Versión de firmware transceptor    | Acelerar 01 versión 0.0 (notificada)            |
| Modelo de cable                     | Puente dúplex LC/LC 50/125µ, OM3, LSZH |
| StorSimple modelo                | 8600                                     |
| Versión del software StorSimple     | 6.3.9600.17491                           |


## <a name="list-of-devices-tested-by-oem-provider-mellanox"></a>Lista de dispositivos probado por proveedor OEM (Mellanox)  

Mellanox ha probado los siguientes transceivers (SFP) acoplable de factor de forma pequeño, cables y cambia a garantizar que funcionan correctamente con Mellanox interfaces de red, como las interfaces de red GbE 10 en el dispositivo StorSimple.

### <a name="cables-and-modules-supported-by-mellanox"></a>Cables y módulos compatibles con Mellanox 

La siguiente tabla enumeran los cables y módulos compatibles con Mellanox. No se ha probado por Microsoft, pero es probable que funcione con su dispositivo StorSimple.

| S. No. | Velocidad | Modelo                 | Descripción                                            | Asegúrese de                |
|--------|-------|-----------------------|--------------------------------------------------------|-----------------------|
| 1.     | 10 GbE| CAB-SFP-SFP - 1M        | cable de cobre pasivo SFP + 10 Gb/s 1m                   | Arista                |
| 2.     | 10 GbE| CAB-SFP-SFP - 2M        | cable de cobre pasivo SFP + 10 Gb/s 2m                   | Arista                |
| 3.     | 10 GbE| CAB SFP-SFP - 3M        | cable de cobre pasivo SFP + 10 Gb/s 3m                   | Arista                |
| 4.     | 10 GbE| CAB-SFP-SFP - 5M        | cable de cobre pasivo SFP + 10 Gb/s 5m                   | Arista                |
| 5.     | 10 GbE| Cisco SFP-H10GBCU1M   | Cable de Cisco SFP +                                       | Cisco                 |
| 6.     | 10 GbE| Cisco SFP-H10GBCU3M   | Cable de Cisco SFP +                                       | Cisco                 |
| 7.     |10 GbE | Cisco SFP-H10GBCU5M   | Cable de Cisco SFP +                                       | Cisco                 |
| 8.     | 10 GbE| HP J9281B X242 10 G    | SFP + SFP + 1m conexión directa de Cable de cobre             | HP                    |
| 9.     | 10 GbE| 455883-B21 HP BLc     | 10Gb SR SFP + participar                                       | HP                    |
| 10.    | 10 GbE| 455886-B21 HP BLc     | 10Gb LR SFP + participar                                       | HP                    |
| 11.    |10 GbE | 487649-B21 HP BLc     | Cable de cobre SFP + 0,5 m 10GbE                           | HP                    |
| 12.    |10 GbE | 487652-B21 HP BLc     | Cable de cobre SFP + 1m 10GbE                             | HP                    |
| 13.    |10 GbE | 487655-B21 HP BLc     | Cable de cobre SFP + 3m 10GbE                             | HP                    |
| 14.    |10 GbE | 487658-B21 HP BLc     | Cable de cobre SFP + 7m 10GbE                             | HP                    |
| 15.    |10 GbE | 537963-B21 HP BLc     | Cable de cobre SFP + 5m 10GbE                             | HP                    |
| 16.    |10 GbE | HP AP784A             | Cable serie C pasivo cobre SFP + de 3 m                  | HP                    |
| 17.    |10 GbE | HP AP785A             | Cable serie C pasivo cobre SFP + de 5 m                  | HP                    |
| 18.    |10 GbE | HP AP818A             | serie B SFP de cobre Active + Cable de 1m                   | HP                    |
| 19.    |10 GbE | HP AP819A             | serie B SFP de cobre Active + Cable de 3m                   | HP                    |
| 20.    |10 GbE | HP J9150A             | X132 10 G SFP + LC SR transceptor                        | HP                    |
| 21.    |10 GbE | HP J9151A             | X132 10 G SFP + LC LR transceptor                        | HP                    |
| 22.    |10 GbE | HP J9283B             | X242 10 G SFP + SFP + CAD Cable de 3 m                        | HP                    |
| 23.    |10 GbE | HP J9285B             | X242 10 G SFP + SFP + 7 m CAD Cable                        | HP                    |
| 24.    | 10 GbE| HP JD095B             | X240 10 G SFP + SFP + m 0,65 CAD Cable                     | HP                    |
| 25.    | 10 GbE| HP JD096B             | X240 10 G SFP + SFP + m 1,2 CAD Cable                      | HP                    |
| 26.    | 10 GbE| HP JD097B             | X240 10 G SFP + SFP + PAPÁ Cable de 3 m                        | HP                    |
| 27.    | 10 GbE| Mellanox MAM1Q00A QSA | QSFP a adaptador SFP +                                   | Tecnologías de Mellanox |
| 28.    | 10 GbE| 006 MC2309124 Mt      | Cable de cobre pasivo 1 x SFP+ a 24awg de 10 Gb/s QSFP 7 m   | Tecnologías de Mellanox |
| 29.    | 10 GbE| MC2309124 007 Mt      | Cable de cobre pasivo 1 x SFP+ a 24awg de 10 Gb/s QSFP 7 m   | Tecnologías de Mellanox |
| 30.    | 10 GbE| 003 MC2309130 Mt      | Cable de cobre pasivo 1 x SFP+ a 30awg de 10 Gb/s QSFP 3 m   | Tecnologías de Mellanox |
| 31.    | 10 GbE| MC2309130 00A Mt      | Cable de cobre pasivo 1 x SFP+ a 30awg de 10 Gb/s QSFP 0,5 m | Tecnologías de Mellanox |
| 32.    | 10 GbE| MC3309124-005 Mt      | 1 24awg de 10 Gb/s x SFP+ de Cable de cobre pasivo 5 m           | Tecnologías de Mellanox |
| 33.    | 10 GbE| MC3309124 007 Mt      | Cable de cobre pasivo 24awg de 10 Gb/s x SFP+ 1 m 7           | Tecnologías de Mellanox |
| 34.    | 10 GbE| 003 MC3309130 Mt      | 1 30awg de 10 Gb/s x SFP+ de Cable de cobre pasivo 3 m           | Tecnologías de Mellanox |
| 35.    | 10 GbE| MC3309130 00A Mt      | 1 30awg de 10 Gb/s x SFP+ de Cable de cobre pasivo 0,5 m         | Tecnologías de Mellanox |

### <a name="switches-supported-by-mellanox"></a>Modificadores admitidos por Mellanox 

La siguiente tabla enumera los modificadores admitidos por Mellanox. No se ha probado por Microsoft, pero es probable que funcione con su dispositivo StorSimple.

| S. No. | Velocidad | Modelo | Descripción                                                         | Asegúrese de              |
|--------|-------|-------------|---------------------------------------------------------------------|-------------|
| 1.     | 10GbE | 516733-B21  | HP ProCurve 6120XG 10GbE Ethernet Switch de placa                      | HP    |
| 2.     | 10GbE | 538113-B21  | Módulo de 10GbE HP (PTM)                                  | HP    |
| 3.     | 10GbE | EN4093      | Módulo de conmutador Scalable IBM PureFlex sistema tela EN4093 10 Gigabit | IBM   |
| 4.     | 1GbE  | 3020        | Módulo de conmutador de Cisco Catalyst 3020 1GbE                               | Cisco |
| 5.     | 1GbE  | 3020 X       | Módulo de conmutador Cisco Catalyst 3020 X 1GbE                              | Cisco |
| 6.     | 1GbE  | 438030-B21  | Módulo de conmutador 1GbE HP - GbE2c nivel 2 o 3 Ethernet placa cambiar       | HP    |
| 7.     | 1GbE  | G 6120       | Módulo de conmutador 1GbE HP ProCurve 6120G/XG                              | HP    |

## <a name="next-steps"></a>Pasos siguientes

[Obtenga más información acerca de los componentes de hardware de StorSimple y estado](storsimple-monitor-hardware-status.md).
