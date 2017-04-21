<properties
   pageTitle="NetWeaver SAP en Windows máquinas virtuales (VM): Guía de implementación de | Microsoft Azure"
   description="NetWeaver SAP en Windows máquinas virtuales (VM): Guía de implementación"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="MSSedusch"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"
   keywords=""/>
<tags
   ms.service="virtual-machines-windows"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="08/18/2016"
   ms.author="sedusch"/>

# <a name="sap-netweaver-on-windows-virtual-machines-vms--deployment-guide"></a>NetWeaver SAP en Windows máquinas virtuales (VM): Guía de implementación

[767598]:https://service.sap.com/sap/support/notes/767598
[773830]:https://service.sap.com/sap/support/notes/773830
[826037]:https://service.sap.com/sap/support/notes/826037
[965908]:https://service.sap.com/sap/support/notes/965908
[1031096]:https://service.sap.com/sap/support/notes/1031096
[1139904]:https://service.sap.com/sap/support/notes/1139904
[1173395]:https://service.sap.com/sap/support/notes/1173395
[1245200]:https://service.sap.com/sap/support/notes/1245200
[1409604]:https://service.sap.com/sap/support/notes/1409604
[1558958]:https://service.sap.com/sap/support/notes/1558958
[1585981]:https://service.sap.com/sap/support/notes/1585981
[1588316]:https://service.sap.com/sap/support/notes/1588316
[1590719]:https://service.sap.com/sap/support/notes/1590719
[1597355]:https://service.sap.com/sap/support/notes/1597355
[1605680]:https://service.sap.com/sap/support/notes/1605680
[1619720]:https://service.sap.com/sap/support/notes/1619720
[1619726]:https://service.sap.com/sap/support/notes/1619726
[1619967]:https://service.sap.com/sap/support/notes/1619967
[1750510]:https://service.sap.com/sap/support/notes/1750510
[1752266]:https://service.sap.com/sap/support/notes/1752266
[1757924]:https://service.sap.com/sap/support/notes/1757924
[1757928]:https://service.sap.com/sap/support/notes/1757928
[1758182]:https://service.sap.com/sap/support/notes/1758182
[1758496]:https://service.sap.com/sap/support/notes/1758496
[1772688]:https://service.sap.com/sap/support/notes/1772688
[1814258]:https://service.sap.com/sap/support/notes/1814258
[1882376]:https://service.sap.com/sap/support/notes/1882376
[1909114]:https://service.sap.com/sap/support/notes/1909114
[1922555]:https://service.sap.com/sap/support/notes/1922555
[1928533]:https://service.sap.com/sap/support/notes/1928533
[1941500]:https://service.sap.com/sap/support/notes/1941500
[1956005]:https://service.sap.com/sap/support/notes/1956005
[1973241]:https://service.sap.com/sap/support/notes/1973241
[1984787]:https://service.sap.com/sap/support/notes/1984787
[1999351]:https://service.sap.com/sap/support/notes/1999351
[2002167]:https://service.sap.com/sap/support/notes/2002167
[2015553]:https://service.sap.com/sap/support/notes/2015553
[2039619]:https://service.sap.com/sap/support/notes/2039619
[2121797]:https://service.sap.com/sap/support/notes/2121797
[2134316]:https://service.sap.com/sap/support/notes/2134316
[2178632]:https://service.sap.com/sap/support/notes/2178632
[2191498]:https://service.sap.com/sap/support/notes/2191498
[2233094]:https://service.sap.com/sap/support/notes/2233094
[2243692]:https://service.sap.com/sap/support/notes/2243692

[azure-cli]:../xplat-cli-install.md
[azure-portal]:https://portal.azure.com
[azure-ps]:../powershell-install-configure.md
[azure-quickstart-templates-github]:https://github.com/Azure/azure-quickstart-templates
[azure-script-ps]:https://go.microsoft.com/fwlink/p/?LinkID=395017
[azure-subscription-service-limits]:../azure-subscription-service-limits.md
[azure-subscription-service-limits-subscription]:../azure-subscription-service-limits.md#subscription

[dbms-guide]:virtual-machines-windows-sap-dbms-guide.md (SAP NetWeaver en Windows máquinas virtuales (VM): Guía de implementación de DBMS) [dbms-guide-2.1]:virtual-machines-windows-sap-dbms-guide.md#c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f (caché de máquinas virtuales y VHD) [dbms-guide-2.2]:virtual-machines-windows-sap-dbms-guide.md#c8e566f9-21b7-4457-9f7f-126036971a91 (Software RAID) [dbms-guide-2.3]:virtual-machines-windows-sap-dbms-guide.md#10b041ef-c177-498a-93ed-44b3441ab152 (Microsoft Azure almacenamiento) [dbms-guide-2]:virtual-machines-windows-sap-dbms-guide.md#65fa79d6-a85f-47ee-890b-22e794f51a64 (estructura de una implementación de RDBMS) [dbms-guide-3]:virtual-machines-windows-sap-dbms-guide.md#871dfc27-e509-4222-9370-ab1de77021c3 (alta disponibilidad y recuperación de máquinas virtuales de Azure) [dbms-guide-5.5.1]:virtual-machines-windows-sap-dbms-guide.md# 0fef0e79-d3fe-4ae2-85AF-73666a6f7268 (SQL Server 2012 SP1 CU4 y posteriores) [dbms-guide-5.5.2]:virtual-machines-windows-sap-dbms-guide.md#f9071eff-9d72-4f47-9da4-1852d782087b (SQL Server 2012 SP1 CU3 y versiones anteriores) [dbms-guide-5.6]:virtual-machines-windows-sap-dbms-guide.md#1b353e38-21b3-4310-aeb6-a77e7c8e81c8 (con imágenes de SQL Server fuera de Microsoft Azure Marketplace) [dbms-guide-5.8]:virtual-machines-windows-sap-dbms-guide.md#9053f720-6f3b-4483-904d-15dc54141e30 (General de SQL Server para SAP en resumen de Azure) [dbms-guide-5]:virtual-machines-windows-sap-dbms-guide.md#3264829e-075e-4d25-966e-a49dad878737 (específica en RDBMS de SQL Server) [dbms-guide-8.4.1]:virtual-machines-windows-sap-dbms-guide.md#b48cfe3b-48e9-4f5b-a783-1d29155bd573 (configuración del almacenamiento) [dbms-guide-8.4.2]:virtual-machines-windows-sap-dbms-guide.md# 23c78d3b-ca5a-4e72-8a24-645d141a3f5d (hacer copia de seguridad y restaurar) [dbms-guide-8.4.3]:virtual-machines-windows-sap-dbms-guide.md#77cd2fbb-307e-4cbf-a65f-745553f72d2c (consideraciones de rendimiento para la copia de seguridad y restauración) [dbms-guide-8.4.4]:virtual-machines-windows-sap-dbms-guide.md#f77c1436-9ad8-44fb-a331-8671342de818 (otros) [dbms-guide-900-sap-cache-server-on-premises]:virtual-machines-windows-sap-dbms-guide.md#642f746c-e4d4-489d-bf63-73e80177a0a8

[dbms-guide-figure-100]:./media/virtual-machines-shared-sap-dbms-guide/100_storage_account_types.png
[dbms-guide-figure-200]:./media/virtual-machines-shared-sap-dbms-guide/200-ha-set-for-dbms-ha.png
[dbms-guide-figure-300]:./media/virtual-machines-shared-sap-dbms-guide/300-reference-config-iaas.png
[dbms-guide-figure-400]:./media/virtual-machines-shared-sap-dbms-guide/400-sql-2012-backup-to-blob-storage.png
[dbms-guide-figure-500]:./media/virtual-machines-shared-sap-dbms-guide/500-sql-2012-backup-to-blob-storage-different-containers.png
[dbms-guide-figure-600]:./media/virtual-machines-shared-sap-dbms-guide/600-iaas-maxdb.png
[dbms-guide-figure-700]:./media/virtual-machines-shared-sap-dbms-guide/700-livecach-prod.png
[dbms-guide-figure-800]:./media/virtual-machines-shared-sap-dbms-guide/800-azure-vm-sap-content-server.png
[dbms-guide-figure-900]:./media/virtual-machines-shared-sap-dbms-guide/900-sap-cache-server-on-premises.png

[deployment-guide]:virtual-machines-windows-sap-deployment-guide.md (SAP NetWeaver en Windows máquinas virtuales (VM): Guía de implementación) [deployment-guide-2.2]:virtual-machines-windows-sap-deployment-guide.md#42ee2bdb-1efc-4ec7-ab31-fe4c22769b94 (recursos de SAP) [deployment-guide-3.1.2]:virtual-machines-windows-sap-deployment-guide.md#3688666f-281f-425b-a312-a77e7db2dfab (implementar una máquina virtual con una imagen personalizada) [deployment-guide-3.2]:virtual-machines-windows-sap-deployment-guide.md#db477013-9060-4602-9ad4-b0316f8bb281 (escenario 1: implementar una máquina virtual fuera de la Azure Marketplace para SAP) [deployment-guide-3.3]:virtual-machines-windows-sap-deployment-guide.md#54a1fc6d-24fd-4feb-9c57-ac588a55dff2 (escenario 2: implementar una máquina virtual con una imagen personalizada para SAP) [deployment-guide-3.4]:virtual-machines-windows-sap-deployment-guide.md# a9a60133-a763-4de8-8986-ac0fa33aa8c1 (escenario 3: mover una máquina virtual locales con un disco duro Azure no generalizado virtual SAP) [deployment-guide-3]:virtual-machines-windows-sap-deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e (implementación de escenarios de máquinas virtuales de SAP en Microsoft Azure) [deployment-guide-4.1]:virtual-machines-windows-sap-deployment-guide.md#604bcec2-8b6e-48d2-a944-61b0f5dee2f7 (cmdlets de PowerShell de Azure implementar) [deployment-guide-4.2]:virtual-machines-windows-sap-deployment-guide.md#7ccf6c3e-97ae-4a7a-9c75-e82c37beb18e (descarga e importar SAP relevantes cmdlets de PowerShell) [deployment-guide-4.3]:virtual-machines-windows-sap-deployment-guide.md#31d9ecd6-b136-4c73-b61e-da4a29bbc9cc (unirse a VM en dominio local - sólo Windows) [deployment-guide-4.4.2]:virtual-machines-windows-sap-deployment-guide.md# 6889ff12-eaaf-4f3c-97e1-7c9edc7f7542 (Linux) [deployment-guide-4.4]:virtual-machines-windows-sap-deployment-guide.md#c7cbb0dc-52a4-49db-8e03-83e7edc2927d (descargar, instalar y activar agente de Azure VM) [deployment-guide-4.5.1]:virtual-machines-windows-sap-deployment-guide.md#987cf279-d713-4b4c-8143-6b11589bb9d4 (Azure PowerShell) [deployment-guide-4.5.2]:virtual-machines-windows-sap-deployment-guide.md#408f3779-f422-4413-82f8-c57a23b4fc2f (Azure CLI) [deployment-guide-4.5]:virtual-machines-windows-sap-deployment-guide.md#d98edcd3-f2a1-49f7-b26a-07448ceb60ca (configurar Azure mejorado supervisión extensión para SAP) [deployment-guide-5.1]:virtual-machines-windows-sap-deployment-guide.md#bb61ce92-8c5c-461f-8c53-39f5e5ed91f2 (preparación para comprobar Azure mejorado supervisión para SAP) [5.2 de la Guía de implementación]: Virtual-Machines-Windows-SAP-Deployment-Guide.MD#e2d592ff-b4ea-4a53-a91a-e5521edb6cd1 (comprobación del estado de configuración de la infraestructura de supervisión de Azure) [deployment-guide-5.3]:virtual-machines-windows-sap-deployment-guide.md#fe25a7da-4e4e-4388-8907-8abc2d33cfd8 (solucionar problemas de infraestructura de supervisión de Azure para SAP)

[deployment-guide-configure-monitoring-scenario-1]:virtual-machines-windows-sap-deployment-guide.md#ec323ac3-1de9-4c3a-b770-4ff701def65b (Configure Monitoring)
[deployment-guide-configure-proxy]:virtual-machines-windows-sap-deployment-guide.md#baccae00-6f79-4307-ade4-40292ce4e02d (Configure Proxy)
[deployment-guide-figure-100]:./media/virtual-machines-shared-sap-deployment-guide/100-deploy-vm-image.png
[deployment-guide-figure-1000]:./media/virtual-machines-shared-sap-deployment-guide/1000-service-properties.png
[deployment-guide-figure-11]:virtual-machines-windows-sap-deployment-guide.md#figure-11
[deployment-guide-figure-1100]:./media/virtual-machines-shared-sap-deployment-guide/1100-azperflib.png
[deployment-guide-figure-1200]:./media/virtual-machines-shared-sap-deployment-guide/1200-cmd-test-login.png
[deployment-guide-figure-1300]:./media/virtual-machines-shared-sap-deployment-guide/1300-cmd-test-executed.png
[deployment-guide-figure-14]:virtual-machines-windows-sap-deployment-guide.md#figure-14
[deployment-guide-figure-1400]:./media/virtual-machines-shared-sap-deployment-guide/1400-azperflib-error-servicenotstarted.png
[deployment-guide-figure-300]:./media/virtual-machines-shared-sap-deployment-guide/300-deploy-private-image.png
[deployment-guide-figure-400]:./media/virtual-machines-shared-sap-deployment-guide/400-deploy-using-disk.png
[deployment-guide-figure-5]:virtual-machines-windows-sap-deployment-guide.md#figure-5
[deployment-guide-figure-50]:./media/virtual-machines-shared-sap-deployment-guide/50-forced-tunneling-suse.png
[deployment-guide-figure-500]:./media/virtual-machines-shared-sap-deployment-guide/500-install-powershell.png
[deployment-guide-figure-6]:virtual-machines-windows-sap-deployment-guide.md#figure-6
[deployment-guide-figure-600]:./media/virtual-machines-shared-sap-deployment-guide/600-powershell-version.png
[deployment-guide-figure-7]:virtual-machines-windows-sap-deployment-guide.md#figure-7
[deployment-guide-figure-700]:./media/virtual-machines-shared-sap-deployment-guide/700-install-powershell-installed.png
[deployment-guide-figure-760]:./media/virtual-machines-shared-sap-deployment-guide/760-azure-cli-version.png
[deployment-guide-figure-900]:./media/virtual-machines-shared-sap-deployment-guide/900-cmd-update-executed.png
[deployment-guide-figure-azure-cli-installed]:virtual-machines-windows-sap-deployment-guide.md#402488e5-f9bb-4b29-8063-1c5f52a892d0
[deployment-guide-figure-azure-cli-version]:virtual-machines-windows-sap-deployment-guide.md#0ad010e6-f9b5-4c21-9c09-bb2e5efb3fda
[deployment-guide-install-vm-agent-windows]:virtual-machines-windows-sap-deployment-guide.md#b2db5c9a-a076-42c6-9835-16945868e866
[deployment-guide-troubleshooting-chapter]:virtual-machines-windows-sap-deployment-guide.md#564adb4f-5c95-4041-9616-6635e83a810b (Checks and Troubleshooting for End-to-End Monitoring Setup for SAP on Azure)

[deploy-template-cli]:../resource-group-template-deploy.md#deploy-with-azure-cli-for-mac-linux-and-windows
[deploy-template-portal]:../resource-group-template-deploy.md#deploy-with-the-preview-portal
[deploy-template-powershell]:../resource-group-template-deploy.md#deploy-with-powershell

[dr-guide-classic]:http://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:virtual-machines-windows-sap-get-started.md
[getting-started-dbms]:virtual-machines-windows-sap-get-started.md#1343ffe1-8021-4ce6-a08d-3a1553a4db82
[getting-started-deployment]:virtual-machines-windows-sap-get-started.md#6aadadd2-76b5-46d8-8713-e8d63630e955
[getting-started-planning]:virtual-machines-windows-sap-get-started.md#3da0389e-708b-4e82-b2a2-e92f132df89c

[getting-started-windows-classic]:virtual-machines-windows-classic-sap-get-started.md
[getting-started-windows-classic-dbms]:virtual-machines-windows-classic-sap-get-started.md#c5b77a14-f6b4-44e9-acab-4d28ff72a930
[getting-started-windows-classic-deployment]:virtual-machines-windows-classic-sap-get-started.md#f84ea6ce-bbb4-41f7-9965-34d31b0098ea
[getting-started-windows-classic-dr]:virtual-machines-windows-classic-sap-get-started.md#cff10b4a-01a5-4dc3-94b6-afb8e55757d3
[getting-started-windows-classic-ha-sios]:virtual-machines-windows-classic-sap-get-started.md#4bb7512c-0fa0-4227-9853-4004281b1037
[getting-started-windows-classic-planning]:virtual-machines-windows-classic-sap-get-started.md#f2a5e9d8-49e4-419e-9900-af783173481c

[ha-guide-classic]:http://go.microsoft.com/fwlink/?LinkId=613056

[ha-guide]:virtual-machines-windows-sap-high-availability-guide.md

[install-extension-cli]:virtual-machines-linux-enable-aem.md

[Logo_Linux]:./media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:./media/virtual-machines-shared-sap-shared/Windows.png

[msdn-set-azurermvmaemextension]:https://msdn.microsoft.com/library/azure/mt670598.aspx

[planning-guide]:virtual-machines-windows-sap-planning-guide.md (SAP NetWeaver en máquinas virtuales de Windows (VM): Guía de implementación y planificación) [planning-guide-1.2]:virtual-machines-windows-sap-planning-guide.md#e55d1e22-c2c8-460b-9897-64622a34fdff (recursos) [planning-guide-11]:virtual-machines-windows-sap-planning-guide.md#7cf991a1-badd-40a9-944e-7baae842a058 (alta disponibilidad y recuperación de desastres (DR) para NetWeaver de SAP que se ejecuta en Azure máquinas virtuales de Windows) [planning-guide-11.4.1]:virtual-machines-windows-sap-planning-guide.md#5d9d36f9-9058-435d-8367-5ad05f00de77 (alta disponibilidad para servidores de aplicaciones de SAP) [planning-guide-11.5]:virtual-machines-windows-sap-planning-guide.md#4e165b58-74ca-474f-a7f4-5e695a93204f (usando iniciar de automáticamente instancias de SAP) [planning-guide-2.1]:virtual-machines-windows-sap-planning-guide.md# 1625df66-4CC6-4d60-9202-de8a0b77f803 (sólo nube - implementaciones de máquina Virtual en Azure sin dependencias en la red local del cliente) [planning-guide-2.2]:virtual-machines-windows-sap-planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10 (entre local - implementación de único o múltiples máquinas virtuales de SAP en Azure con el requisito de que se integra completamente en la red local) [planning-guide-3.1]:virtual-machines-windows-sap-planning-guide.md#be80d1b9-a463-4845-bd35-f4cebdb5424a (regiones de Azure) [planning-guide-3.2.1]:virtual-machines-windows-sap-planning-guide.md#df49dc09-141b-4f34-a4a2-990913b30358 (dominios de error) [planning-guide-3.2.2]:virtual-machines-windows-sap-planning-guide.md#fc1ac8b2-e54a-487c-8581-d3cc6625e560 (actualizar dominios) [planning-guide-3.2.3]:virtual-machines-windows-sap-planning-guide.md#18810088- F9BE - 4c 97-958a - 27996255c 665 (conjuntos de disponibilidad de Azure) [planning-guide-3.2]:virtual-machines-windows-sap-planning-guide.md#8d8ad4b8-6093-4b91-ac36-ea56d80dbf77 (el concepto Microsoft Azure Máquina Virtual) [planning-guide-3.3.2]:virtual-machines-windows-sap-planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 (almacenamiento de Azure Premium) [planning-guide-5.1.1]:virtual-machines-windows-sap-planning-guide.md#4d175f1b-7353-4137-9d2f-817683c26e53 (mover una máquina virtual de local a Azure con un disco no generalizado) [planning-guide-5.1.2]:virtual-machines-windows-sap-planning-guide.md#e18f7839-c0e2-4385-b1e6-4538453a285c (implementar una máquina virtual con una imagen específica del cliente) [planning-guide-5.2.1]:virtual-machines-windows-sap-planning-guide.md#1b287330-944b-495d-9ea7-94b83aff73ef (preparación para mover una máquina virtual de local a Azure con un disco no generalizado) [planning-guide-5.2.2]:virtual-machines-windows-sap-planning-guide.md#57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3 (de preparación para implementar una máquina virtual con una imagen específica del cliente de SAP) [planning-guide-5.2]:virtual-machines-windows-sap-planning-guide.md#6ffb9f41-a292-40bf-9e70-8204448559e7 (preparación VM con SAP para Azure) [planning-guide-5.3.1]:virtual-machines-windows-sap-planning-guide.md#6e835de8-40b1-4b71-9f18-d45b20959b79 (diferencia entre un disco de Azure y Azure imagen) [planning-guide-5.3.2]:virtual-machines-windows-sap-planning-guide.md#a43e40e6-1acc-4633-9816-8f095d5a7b6a (cargar un disco duro virtual locales con Azure) [planning-guide-5.4.2]:virtual-machines-windows-sap-planning-guide.md#9789b076-2011-4afa-b2fe-b07a8aba58a1 (copiar discos entre cuentas de almacenamiento de Azure) [planificación de guía-5.5.1]: Virtual-Machines-Windows-SAP-Planning-Guide.MD#4efec401-91e0-40c0-8e64-f2dceadff646 (estructura VM/disco duro virtual para implementaciones de SAP) [planning-guide-5.5.3]:virtual-machines-windows-sap-planning-guide.md#17e0d543-7e8c-4160-a7da-dd7117a1ad9d (montaje de configuración para discos conectados) [planning-guide-7.1]:virtual-machines-windows-sap-planning-guide.md#3e9c3690-da67-421a-bc3f-12c520d99a30 (solo VM con SAP NetWeaver escenario de formación y demostración) [planning-guide-7]:virtual-machines-windows-sap-planning-guide.md#96a77628-a05e-475d-9df3-fb82217e8f14 (implementación de conceptos de Cloud-Only de instancias de SAP) [planning-guide-9.1]:virtual-machines-windows-sap-planning-guide.md#6f0a47f3-a289-4090-a053-2521618a28c3 (Azure supervisión de solución para SAP) [planning-guide-azure-premium-storage]:virtual-machines-windows-sap-planning-guide.md# ff5ad0f9-f7f4-4022-9102-af07aef3bc92 (almacenamiento de Azure Premium)

[planning-guide-figure-100]:./media/virtual-machines-shared-sap-planning-guide/100-single-vm-in-azure.png
[planning-guide-figure-1300]:./media/virtual-machines-shared-sap-planning-guide/1300-ref-config-iaas-for-sap.png
[planning-guide-figure-1400]:./media/virtual-machines-shared-sap-planning-guide/1400-attach-detach-disks.png
[planning-guide-figure-1600]:./media/virtual-machines-shared-sap-planning-guide/1600-firewall-port-rule.png
[planning-guide-figure-1700]:./media/virtual-machines-shared-sap-planning-guide/1700-single-vm-demo.png
[planning-guide-figure-1900]:./media/virtual-machines-shared-sap-planning-guide/1900-vm-set-vnet.png
[planning-guide-figure-200]:./media/virtual-machines-shared-sap-planning-guide/200-multiple-vms-in-azure.png
[planning-guide-figure-2100]:./media/virtual-machines-shared-sap-planning-guide/2100-s2s.png
[planning-guide-figure-2200]:./media/virtual-machines-shared-sap-planning-guide/2200-network-printing.png
[planning-guide-figure-2300]:./media/virtual-machines-shared-sap-planning-guide/2300-sapgui-stms.png
[planning-guide-figure-2400]:./media/virtual-machines-shared-sap-planning-guide/2400-vm-extension-overview.png
[planning-guide-figure-2500]:./media/virtual-machines-shared-sap-planning-guide/2500-vm-extension-details.png
[planning-guide-figure-2600]:./media/virtual-machines-shared-sap-planning-guide/2600-sap-router-connection.png
[planning-guide-figure-2700]:./media/virtual-machines-shared-sap-planning-guide/2700-exposed-sap-portal.png
[planning-guide-figure-2800]:./media/virtual-machines-shared-sap-planning-guide/2800-endpoint-config.png
[planning-guide-figure-2900]:./media/virtual-machines-shared-sap-planning-guide/2900-azure-ha-sap-ha.png
[planning-guide-figure-300]:./media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png
[planning-guide-figure-3000]:./media/virtual-machines-shared-sap-planning-guide/3000-sap-ha-on-azure.png
[planning-guide-figure-3200]:./media/virtual-machines-shared-sap-planning-guide/3200-sap-ha-with-sql.png
[planning-guide-figure-400]:./media/virtual-machines-shared-sap-planning-guide/400-vm-services.png
[planning-guide-figure-600]:./media/virtual-machines-shared-sap-planning-guide/600-s2s-details.png
[planning-guide-figure-700]:./media/virtual-machines-shared-sap-planning-guide/700-decision-tree-deploy-to-azure.png
[planning-guide-figure-800]:./media/virtual-machines-shared-sap-planning-guide/800-portal-vm-overview.png
[planning-guide-microsoft-azure-networking]:virtual-machines-windows-sap-planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd (Microsoft Azure Networking)
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:virtual-machines-windows-sap-planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f (Storage: Microsoft Azure Storage and Data Disks)

[powershell-install-configure]:../powershell-install-configure.md
[resource-group-authoring-templates]:../resource-group-authoring-templates.md
[resource-group-overview]:../azure-resource-manager/resource-group-overview.md
[resource-groups-networking]:../virtual-network/resource-groups-networking.md
[sap-pam]:https://support.sap.com/pam (SAP Product Availability Matrix)
[sap-templates-2-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-2-tier-os-disk]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-disk%2Fazuredeploy.json
[sap-templates-2-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-image%2Fazuredeploy.json
[sap-templates-3-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-3-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-user-image%2Fazuredeploy.json
[storage-azure-cli]:../storage/storage-azure-cli.md
[storage-azure-cli-copy-blobs]:../storage/storage-azure-cli.md#copy-blobs
[storage-introduction]:../storage/storage-introduction.md
[storage-powershell-guide-full-copy-vhd]:../storage/storage-powershell-guide-full.md#how-to-copy-blobs-from-one-storage-container-to-another
[storage-premium-storage-preview-portal]:../storage/storage-premium-storage.md
[storage-redundancy]:../storage/storage-redundancy.md
[storage-scalability-targets]:../storage/storage-scalability-targets.md
[storage-use-azcopy]:../storage/storage-use-azcopy.md
[template-201-vm-from-specialized-vhd]:https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd
[templates-101-simple-windows-vm]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-simple-windows-vm
[templates-101-vm-from-user-image]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image
[virtual-machines-linux-attach-disk-portal]:virtual-machines-linux-attach-disk-portal.md
[virtual-machines-windows-attach-disk-portal]:virtual-machines-windows-attach-disk-portal.md
[virtual-machines-azure-resource-manager-architecture]:../resource-manager-deployment-model.md
[virtual-machines-azurerm-versus-azuresm]:virtual-machines-windows-compare-deployment-models.md
[virtual-machines-windows-classic-configure-oracle-data-guard]:virtual-machines-windows-classic-configure-oracle-data-guard.md
[virtual-machines-linux-cli-deploy-templates]:virtual-machines-linux-cli-deploy-templates.md (Deploy and manage virtual machines by using Azure Resource Manager templates and the Azure CLI)
[virtual-machines-deploy-rmtemplates-powershell]:virtual-machines-windows-ps-manage.md (Manage virtual machines using Azure Resource Manager and PowerShell)
[virtual-machines-linux-agent-user-guide]:virtual-machines-linux-agent-user-guide.md
[virtual-machines-linux-agent-user-guide-command-line-options]:virtual-machines-linux-agent-user-guide.md#command-line-options
[virtual-machines-linux-capture-image]:virtual-machines-linux-capture-image.md
[virtual-machines-linux-capture-image-capture]:virtual-machines-linux-capture-image.md#capture-the-vm
[virtual-machines-windows-capture-image]:virtual-machines-windows-capture-image.md
[virtual-machines-windows-capture-image-capture]:virtual-machines-windows-capture-image.md#capture-the-vm
[virtual-machines-linux-configure-lvm]:virtual-machines-linux-configure-lvm.md
[virtual-machines-linux-configure-raid]:virtual-machines-linux-configure-raid.md
[virtual-machines-linux-classic-create-upload-vhd-step-1]:virtual-machines-linux-classic-create-upload-vhd.md#step-1-prepare-the-image-to-be-uploaded
[virtual-machines-linux-create-upload-vhd-suse]:virtual-machines-linux-suse-create-upload-vhd.md
[virtual-machines-linux-redhat-create-upload-vhd]:virtual-machines-linux-redhat-create-upload-vhd.md
[virtual-machines-linux-how-to-attach-disk]:virtual-machines-linux-add-disk.md
[virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]:virtual-machines-linux-add-disk.md#connect-to-the-linux-vm-to-mount-the-new-disk
[virtual-machines-linux-tutorial]:virtual-machines-linux-quick-create-cli.md
[virtual-machines-linux-update-agent]:virtual-machines-linux-update-agent.md
[virtual-machines-manage-availability]:virtual-machines-windows-manage-availability.md
[virtual-machines-ps-create-preconfigure-windows-resource-manager-vms]:virtual-machines-windows-ps-create.md
[virtual-machines-sizes]:virtual-machines-windows-sizes.md
[virtual-machines-windows-classic-ps-sql-alwayson-availability-groups]:virtual-machines-windows-classic-ps-sql-alwayson-availability-groups.md
[virtual-machines-windows-classic-ps-sql-int-listener]:virtual-machines-windows-classic-ps-sql-int-listener.md
[virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions]:virtual-machines-windows-sql-high-availability-dr.md
[virtual-machines-sql-server-infrastructure-services]:virtual-machines-windows-sql-server-iaas-overview.md
[virtual-machines-sql-server-performance-best-practices]:virtual-machines-windows-sql-performance.md
[virtual-machines-upload-image-windows-resource-manager]:virtual-machines-windows-upload-image.md
[virtual-machines-windows-tutorial]:virtual-machines-windows-hero-tutorial.md
[virtual-machines-workload-template-sql-alwayson]:https://azure.microsoft.com/documentation/templates/sql-server-2014-alwayson-dsc/
[virtual-network-deploy-multinic-arm-cli]:../virtual-network/virtual-network-deploy-multinic-arm-cli.md
[virtual-network-deploy-multinic-arm-ps]:../virtual-network/virtual-network-deploy-multinic-arm-ps.md
[virtual-network-deploy-multinic-arm-template]:../virtual-network/virtual-network-deploy-multinic-arm-template.md
[virtual-networks-configure-vnet-to-vnet-connection]:../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md
[virtual-networks-create-vnet-arm-pportal]:../virtual-network/virtual-networks-create-vnet-arm-pportal.md
[virtual-networks-manage-dns-in-vnet]:../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md
[virtual-networks-multiple-nics]:../virtual-network/virtual-networks-multiple-nics.md
[virtual-networks-nsg]:../virtual-network/virtual-networks-nsg.md
[virtual-networks-reserved-private-ip]:../virtual-network/virtual-networks-static-private-ip-arm-ps.md
[virtual-networks-static-private-ip-arm-pportal]:../virtual-network/virtual-networks-static-private-ip-arm-pportal.md
[virtual-networks-udr-overview]:../virtual-network/virtual-networks-udr-overview.md
[vpn-gateway-about-vpn-devices]:../vpn-gateway/vpn-gateway-about-vpn-devices.md
[vpn-gateway-create-site-to-site-rm-powershell]:../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md
[vpn-gateway-cross-premises-options]:../vpn-gateway/vpn-gateway-plan-design.md
[vpn-gateway-site-to-site-create]:../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md
[vpn-gateway-vpn-faq]:../vpn-gateway/vpn-gateway-vpn-faq.md
[xplat-cli]:../xplat-cli-install.md
[xplat-cli-azure-resource-manager]:../xplat-cli-azure-resource-manager.md

Microsoft Azure permite a las empresas adquirir recursos de proceso y almacenamiento en un tiempo mínimo sin ciclos de adquisiciones larga. Máquinas virtuales de Windows Azure permite a las empresas implementar aplicaciones clásicas, como SAP NetWeaver según aplicaciones en Azure y ampliar su disponibilidad y confiabilidad sin tener más recursos disponibles en local. Microsoft Azure también admite la conectividad entre local, que permite a las empresas integrar conocidas máquinas virtuales de Azure en sus dominios en local, sus nubes privadas y su entorno de sistema SAP.

Estas notas del producto paso a paso describe cómo una máquina Virtual de Azure está preparado para la implementación de aplicaciones de SAP NetWeaver según. Se supone que la información contenida en la [planificación y la Guía de implementación] [Guía de planificación] se conoce. Si no es así, debe leer primero el documento respectivo.

El documento sirve de complemento de la documentación de instalación de SAP y notas de SAP que representan los recursos principales para las instalaciones y las implementaciones de software SAP en dada plataformas.

[AZURE.INCLUDE [windows-warning](../../includes/virtual-machines-linux-sap-warning.md)]

## <a name="introduction"></a>Introducción
Una gran cantidad de empresas en todo el mundo usar aplicaciones SAP NetWeaver según – más destacada SAP Business Suite – para ejecutar sus objetivos crítica procesos empresariales. Mantenimiento del sistema, por tanto, es un activo fundamental y la capacidad de proporcionar soporte técnico de la empresa en caso de un funcionamiento incorrecto, incluidas las incidencias de rendimiento, se convierte en un requisito esencial.
Microsoft Azure proporciona instrumentación de plataforma superior para adaptarse a los requisitos de compatibilidad de todas las aplicaciones empresariales críticas. Esta guía se asegura de que un equipo de Microsoft Azure Virtual orientada a la implementación de Software SAP configuración de soporte de enterprise puede ofrecer, sin tener en cuenta que se crea la forma la máquina Virtual, ya sea fuera de Azure Marketplace o usar una imagen específica del cliente.
En la configuración siguiente, todos los pasos se describen en detalle.

## <a name="prerequisites-and-resources"></a>Requisitos previos y recursos
### <a name="prerequisites"></a>Requisitos previos
Antes de empezar, asegúrese de que se cumplen los requisitos previos que se describen en los siguientes capítulos.

#### <a name="local-personal-computer"></a>Equipo local
La configuración de una máquina Virtual de Azure para su implementación de Software de SAP consta de varios pasos. Para administrar máquinas virtuales de Windows o máquinas virtuales de Linux, debe usar un script de PowerShell y el Portal de Microsoft Azure. Para ello, es necesario un equipo local con Windows 7 o posterior. Si solo desea administrar máquinas virtuales Linux y desea usar una máquina Linux para esta tarea, también puede usar la interfaz de línea de comandos de Azure (Azure CLI).

#### <a name="internet-connection"></a>Conexión a Internet
Para descargar y ejecutar las herramientas necesarias y secuencias de comandos, se requiere una conexión a Internet. Además, el equipo de Microsoft Azure Virtual ejecuta la extensión de supervisión mejorada de Azure necesita obtener acceso a Internet. En caso de que este VM Azure es parte de una red Virtual de Azure o dominio local, asegúrese de que la configuración de proxy correspondientes se establece como se describe en [Configurar Proxy] de capítulo[ deployment-guide-configure-proxy] en este documento.

#### <a name="microsoft-azure-subscription"></a>Suscripción de Microsoft Azure
Ya existe una cuenta de Azure y se conocen acuerdas credenciales de inicio de sesión.

#### <a name="topology-consideration-and-networking"></a>Cuenta de topología y redes
La arquitectura de la implementación de SAP en Azure y topología deben definirse. Arquitectura en relación con:

* Cuentas de Microsoft Azure almacenamiento para usarse
* Red virtual para implementar el sistema de SAP
* Grupo de recursos para implementar el sistema de SAP
* Región de Azure para implementar el sistema SAP
* Configuración de SAP (2 o 3 niveles)
* Tamaños VM y el número de VHD adicionales para instalarse a la VM(s)
* Configuración del sistema de transporte SAP y corrección

Azure cuentas de almacenamiento o redes Virtual de Azure como tales deben se han creado y configurado ya. Cómo crear y configurar ellos se abordan con [planificación y la Guía de implementación] [Guía de planificación].

#### <a name="sap-sizing"></a>Tamaño de SAP
* La carga de trabajo SAP proyectado se ha determinado, por ejemplo, mediante el uso de Quicksizer de SAP, y se conoce el número SAP de acuerdo 
* Debe conocer el consumo de memoria y recursos de CPU necesario del sistema SAP
* Deben conocer las operaciones de i/OS necesarias por segundo
* Se conoce el ancho de banda de red requerido en comunicación final entre diferentes máquinas virtuales de Azure
* El ancho de banda de red necesario entre los activos local y la Azure implementado SAP se conoce sistemas

#### <a name="resource-groups"></a>Grupos de recursos
Grupos de recursos son un nuevo concepto que contienen todos los recursos que tengan el mismo ciclo de vida, por ejemplo, se crean y se eliminan al mismo tiempo. Lea [este artículo] [ resource-group-overview] para obtener más información acerca de los grupos de recursos. 

### <a name="42ee2bdb-1efc-4ec7-ab31-fe4c22769b94"></a>Recursos SAP
Durante el trabajo de configuración, se necesitan los siguientes recursos:

* Nota SAP [1928533]
    * la lista de tamaños de máquina Virtual de Azure, que son compatibles con la implementación de Software de SAP 
    * información importante capacidad por el tamaño de la máquina Virtual de Azure
    * software compatible de SAP y combinación de sistema operativo y DB
* Nota [2015553] listado requisitos previos para ser compatible con SAP al implementar software SAP en Microsoft Azure de SAP.
* Nota [1999351] que contiene información adicional de solución de problemas de mejorado Azure seguimiento para SAP de SAP.
* Nota [2178632] que contiene información detallada en todas las estadísticas de supervisión disponibles para SAP en Microsoft Azure de SAP. 
* Nota [1409604] que contiene la versión de agente de Host de SAP necesaria para Windows en Microsoft Azure al implementar el Administrador de recursos en Azure nuevo de SAP.
* Nota [2191498] que contiene la versión de agente de Host de SAP necesaria para Linux en Microsoft Azure al implementar el Administrador de recursos en Azure nuevo de SAP.
* SAP nota [2243692] que contiene información sobre la licencia de SAP en Linux en Azure
* Nota [1984787] que contiene información general acerca de SUSE LINUX Enterprise Server 12 de SAP
* SAP nota [2002167] que contiene información general rojos sombrero Enterprise Linux 7.x
* [Pant](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) que contiene todos los necesarios notas de SAP para Linux
* Cmdlets de PowerShell específicos que forman parte de la [Azure PowerShell] de SAP[azure-ps]
* SAP específico Azure CLI que forman parte de la [CLI de Azure][azure-cli]
* [Portal de Microsoft Azure][azure-portal]

[comment]: <> (Nivel de revisión de MSSedusch TODO agregar ARM para agente de Host de SAP en SAP nota 1409604)
 
Las guías siguientes cubren el tema de SAP en Microsoft Azure:

* [NetWeaver SAP en máquinas virtuales de Windows (VM): Guía de implementación y planificación] [Guía de planificación]
* [NetWeaver SAP en Windows máquinas virtuales (VM): Guía de implementación (este documento)] [Guía de implementación]
* [NetWeaver SAP en Windows máquinas virtuales (VM): Guía de implementación de DBMS] [Guía de dbms]
* [NetWeaver SAP en Windows máquinas virtuales (VM): Guía de implementación de alta disponibilidad][ha-guide]

## <a name="b3253ee3-d63b-4d74-a49b-185e76c4088e"></a>Escenarios de implementación de máquinas virtuales de SAP en Microsoft Azure
En este capítulo, obtenga información sobre las distintas maneras de implementación y los pasos único para cada tipo de implementación.

### <a name="deployment-of-vms-for-sap"></a>Implementación de máquinas virtuales de SAP
Microsoft Azure ofrece varias formas de implementar máquinas virtuales y discos asociados. Lo que es muy importante comprender las diferencias desde preparados de las máquinas virtuales pueden diferir depende de la forma de implementación. En general, observe en las situaciones siguientes:

#### <a name="deploying-a-vm-out-of-the-azure-marketplace"></a>Implementar una máquina virtual de Azure Marketplace
Como un Microsoft o parte 3ª proporcionado imagen fuera de Azure Marketplace para implementar la máquina virtual. Después de implementar la VM en Microsoft Azure, siga las mismas instrucciones y herramientas para instalar el software SAP dentro de la máquina virtual como lo haría en un entorno local. Para instalar el software SAP dentro de la máquina virtual de Azure, SAP y Microsoft recomiendan cargar y almacenar el medio de instalación de SAP en Azure VHD o para crear una máquina virtual de Azure funciona como un servidor de archivos' ' que contiene todos los medios de instalación de SAP necesarios.

[comment]: <> (¿MSSedusch TODO por qué debemos recomienda una administración de archivos, por ejemplo, el servidor de archivos o el disco duro virtual? ¿Es tan diferente de local?)

Para obtener más detalles, vea capítulo [escenario 1: implementar una máquina virtual fuera de la Azure Marketplace para SAP] [3.2 de la Guía de implementación].

#### <a name="3688666f-281f-425b-a312-a77e7db2dfab"></a>Implementar una máquina virtual con una imagen personalizada
Debido a los requisitos de revisión específica con respecto a la versión del sistema operativo o DBMS, las imágenes proporcionadas fuera de Azure Marketplace no pueden a sus necesidades. Por consiguiente, debe crear una máquina virtual con su propia imagen de sistema operativo/DB VM 'private' que se puede implementar varias veces después.
Los pasos para crear una imagen privada difieren entre Windows y una imagen de Linux.

___

> ![Windows][Logo_Windows] Windows
>
> Para preparar una imagen de Windows que se pueden usar para implementar varias máquinas virtuales, la configuración de Windows (como Windows SID y el nombre de host) debe ser extraída/generalizado en la máquina virtual local. Esto se puede hacer con sysprep como se describe en <https://technet.microsoft.com/library/cc721940.aspx>.
>
> ![Linux][Logo_Linux] Linux
>
> Para preparar una imagen de Linux que se pueden usar para implementar varias máquinas virtuales, algunas configuraciones de Linux deben ser extraída/generalizado en la máquina virtual local. Esto se puede hacer con waagent-activar como se describe en [este artículo] [ virtual-machines-linux-capture-image] o en [este artículo][virtual-machines-linux-agent-user-guide-command-line-options].

___

Puede configurar el contenido de la base de datos mediante el Administrador de aprovisionamiento de Software de SAP para instalar un nuevo sistema de SAP, restaurar una copia de seguridad de base de datos desde un disco duro virtual que está conectado a la máquina virtual o restaurar directamente una copia de seguridad de base de datos de Azure almacenamiento si lo admite el DBMS. (consulte [Guide][dbms-guide]) de implementación de DBMS. Si ya ha instalado un sistema SAP en su VM local (especialmente para sistemas de nivel 2), puede adaptar la configuración del sistema SAP después de la implementación de Azure VM mediante el procedimiento de cambiar el nombre de sistema compatible con el Administrador de aprovisionamiento de Software de SAP (SAP nota [1619720]). En caso contrario, puede instalar el software SAP después de la implementación de la máquina virtual de Azure.

Para obtener más detalles, vea capítulo [escenario 2: implementar una máquina virtual con una imagen personalizada para SAP] [3.3 de la Guía de implementación].

#### <a name="moving-a-vm-from-on-premises-to-microsoft-azure-with-a-non-generalized-disk"></a>Mover una máquina virtual de local a Microsoft Azure con un disco no generalizado
Plan mover un sistema SAP específico de local a Microsoft Azure. Para ello puede cargar el disco duro virtual que contiene el sistema operativo, los archivos binarios SAP y final binarios DBMS además de los VHD con los archivos de datos y de registro del DBMS a Microsoft Azure. Opuesto en el escenario descrito en capítulo [implementar una máquina virtual con una imagen personalizada] [implementación-Guía-3.1.2] encima, mantener el nombre de host, SAP SID y las cuentas de usuario SAP en la máquina virtual de Azure tal como estaban configuradas en el entorno local. Por lo tanto, no es necesario generalización el sistema operativo. En este caso se aplicará principalmente para escenarios entre local donde se ejecuta una parte del entorno SAP local y elementos en Microsoft Azure.

Para obtener más detalles, vea capítulo [escenario 3: mover una máquina virtual locales con un disco duro Azure no generalizado virtual SAP] [3.4 de la Guía de implementación].

### <a name="db477013-9060-4602-9ad4-b0316f8bb281"></a>Escenario 1: Implementar una máquina virtual fuera de la Azure Marketplace para SAP
Microsoft Azure ofrece la posibilidad de implementar una instancia de máquina virtual de Azure Marketplace, que ofrece algunas imágenes de sistema operativo estándares de Windows Server y diferentes distribuciones de Linux. También es posible implementar una imagen que incluya DBMS SKU, por ejemplo, SQL Server. Para usar estas imágenes con DBMS SKU de detalles, consulte la [DBMS Guía de implementación] [Guía de dbms]

La secuencia específica de SAP de pasos implementar una máquina virtual de Azure Marketplace tendrá un aspecto similar:

![Diagrama de flujo de implementación de máquina virtual para sistemas SAP mediante una imagen de máquina virtual de Azure Marketplace][deployment-guide-figure-100]

Siguiendo el diagrama de flujo deben ejecutar los siguientes pasos:

#### <a name="create-virtual-machine-using-the-azure-portal"></a>Crear la máquina virtual con el Portal de Azure
La manera más sencilla de crear una nueva máquina virtual con una imagen de Azure Marketplace es a través del Portal de Azure. Vaya a <https://portal.azure.com/#create>. Especifique el tipo de sistema operativo que desea implementar en el campo de búsqueda, por ejemplo, Windows, SLES o RHEL y seleccione la versión. Asegúrese de seleccionar el modelo de implementación "Administrador de recursos de Azure" y haga clic en crear.

El asistente le guiará a través de los parámetros necesarios para crear la máquina virtual junto con todos los recursos necesarios como interfaces de red o cuentas de almacenamiento. Algunos de estos parámetros son:

1. Conceptos básicos
    1. Nombre: El nombre del recurso, es decir, el nombre de máquina virtual
    1. Nombre de usuario y contraseña/SSH clave pública: escriba el nombre de usuario y la contraseña del usuario que se crea durante el proceso de aprovisionamiento. Para una máquina virtual Linux, también puede escribir la clave pública SSH que desea utilizar para iniciar sesión en el equipo mediante SSH.
    1. Suscripción: Seleccione la suscripción que desea usar para aprovisionar la nueva máquina virtual.
    1. Grupo de recursos: El nombre del grupo de recursos. Puede insertar el nombre de un nuevo grupo de recursos o de un grupo de recursos que ya existe
    1. Ubicación: Seleccione la ubicación donde se debe implementar la nueva máquina virtual. Si desea conectar la máquina virtual a su red local, asegúrese de seleccionar la ubicación de la red Virtual que se conecta Azure a su red local. Para obtener más detalles, vea capítulo [Redes de Microsoft Azure] [ planning-guide-microsoft-azure-networking] [manual de planeamiento de] [Guía de planificación].
1. Tamaño: Lea SAP nota [1928533] para obtener una lista de tipos VM compatibles. También asegúrese de seleccionar el tipo correcto si desea usar almacenamiento Premium. No todos los tipos VM admiten el almacenamiento de Premium. Consulte el capítulo [almacenamiento: Microsoft Azure almacenamiento y discos de datos] [ planning-guide-storage-microsoft-azure-storage-and-data-disks] [almacenamiento de Azure Premium] y [planificación guía-azure-premium-almacenamiento] en el [Guía de planificación] [Guía de planificación] para obtener más detalles.
1. Configuración
    1. Cuenta de almacenamiento: Puede seleccionar una cuenta de almacenamiento existente o cree uno nuevo. Lea capítulo [Microsoft Azure almacenamiento] [2.3 de guía de dbms] de [DBMS Guide] [Guía de dbms] para obtener más detalles sobre los tipos de almacenamiento diferente. Tenga en cuenta que no todos los tipos de almacenamiento son compatibles para ejecutar las aplicaciones SAP.
    1. Red virtual y subred: seleccione la red virtual que está conectada a su red local si desea integrar la máquina virtual en la intranet.
    1. Dirección IP pública: seleccione la dirección IP pública que desea usar, o especifique los parámetros para crear una nueva dirección IP pública. Puede usar una dirección IP pública para tener acceso a su máquina virtual a través de internet. Asegúrese de que crear un grupo de seguridad de la red para filtrar el acceso a su equipo virtual.
    1. Grupo de seguridad de red: consulte [¿Qué es un grupo de seguridad de la red (GSN)] [ virtual-networks-nsg] para obtener más detalles.
    1. Supervisión: Puede deshabilitar a la configuración de diagnósticos. Estará habilitado automáticamente cuando ejecute los comandos para habilitar la supervisión de mejorada de Azure como se describe en [Configurar supervisión]de capítulo[deployment-guide-configure-monitoring-scenario-1].
    1. Disponibilidad: Seleccione un conjunto de disponibilidad o especifique los parámetros para crear un nuevo conjunto de disponibilidad. Para obtener más información, vea capítulo [conjuntos de disponibilidad de Azure] [planificación de guía-3.2.3].
1. Resumen: Validar la información incluida en la página de resumen y haga clic en Aceptar.

Una vez finalizado el asistente, la máquina virtual se implementará en el grupo de recursos seleccionado.

#### <a name="create-virtual-machine-using-a-template"></a>Crear la máquina virtual mediante una plantilla
También puede crear una implementación mediante una de las plantillas SAP publicadas en el [repositorio de plantillas de azure tutorial github][azure-quickstart-templates-github]. También puede crear una máquina virtual con el [Portal de Azure][virtual-machines-windows-tutorial], [PowerShell] [ virtual-machines-ps-create-preconfigure-windows-resource-manager-vms] o [Azure CLI] [ virtual-machines-linux-tutorial] manualmente.

* [plantilla de configuración de nivel 2 (una máquina virtual solo)] [ sap-templates-2-tier-marketplace-image] Use esta plantilla si desea crear un sistema de nivel 2 mediante una máquina virtual solo.
* [plantilla de configuración de nivel 3 (varias máquinas virtuales)] [ sap-templates-3-tier-marketplace-image] Use esta plantilla si desea crear un sistema de nivel 3 utilizando varios equipos virtuales.

Después de abrir una de las plantillas anteriores, el Portal de Azure se desplaza al panel Modificar parámetros. Escriba la información siguiente:

* **sapSystemId**: Id. de sistema SAP
* **osType**: sistema operativo que desea implementar, por ejemplo, Windows Server 2012 R2, SLES 12 o RHEL 7.2
    * La lista solo contiene versiones que son compatibles con SAP en Microsoft Azure
* **sapSystemSize**: el tamaño del sistema SAP
    * La cantidad de SAP le proporcionará el nuevo sistema. Si no está seguro cuántos SAP que requiere el sistema, póngase en contacto con su asociado de tecnología de SAP o la integración del sistema
* **systemAvailability**: (solo la plantilla de nivel 3) disponibilidad del sistema 
    * Haga clic en una configuración que es adecuada para una instalación HA HA. Dos servidores de base de datos y se creará dos servidores para la ASC.
* storageType: (solo la plantilla de nivel 2) tipo de almacenamiento que se debe usar 
    * Sistemas más grande, se recomienda el uso de almacenamiento de Premium. Para obtener más información sobre los tipos de almacenamiento diferente, lea 
        * [Microsoft Azure almacenamiento] [dbms-guía-2.3] de [DBMS Guide] [Guía de dbms]
        * [Almacenamiento de Premium: El almacenamiento de alto rendimiento para cargas de trabajo de Azure Máquina Virtual][storage-premium-storage-preview-portal]
        * [Introducción al almacenamiento de Azure][storage-introduction]
* **adminUsername** y **adminPassword**: nombre de usuario y contraseña
    * Se crea un nuevo usuario que puede utilizar para iniciar sesión en el equipo.
* **newOrExistingSubnet**: determina si se deben crear una nueva red virtual y subred o una subred existente que se debe usar. Si ya tiene una red virtual que está conectada a su red local, seleccione existente.
* **subnetId**: el identificador de la subred a la que los equipos virtuales debe estar conectados a. Seleccione la subred de su red virtual VPN o Express ruta para conectar la máquina virtual a su red local. El número de identificación suele ser similar a /subscriptions/`<subscription id`> /resourceGroups/`<resource group name`> /providers/Microsoft.Network/virtualNetworks/`<virtual network name`> /subnets/`<subnet name`>

Una vez que haya escrito todos los parámetros, seleccione la suscripción y el grupo de recursos que desea usar. Puede seleccionar un grupo de recursos existente o cree uno nuevo seleccionando "+ nueva" en el menú desplegable. Si crea un nuevo grupo de recursos, debe seleccionar la región donde se creará el grupo de recursos y de la máquina virtual.

Revise las condiciones legales, aceptarla y haga clic en crear.

Tenga en cuenta que el agente de Azure VM se implementa de forma predeterminada cuando se usa una imagen de Azure Marketplace.

#### <a name="configure-proxy-settings"></a>Establecer la configuración de Proxy
Según la configuración de su red local, podrían ser necesario para configurar al servidor proxy en su máquina virtual si está conectado a su red local a través de la VPN o la ruta de Express. En caso contrario, la máquina virtual puede no pueda tener acceso a internet y, por tanto, no puede descargar la extensiones necesarias o recopilar datos de supervisión. Consulte [Configurar Proxy] de capítulo[ deployment-guide-configure-proxy] de este documento.

#### <a name="join-domain-windows-only"></a>Unirse a dominio (sólo Windows)
En el caso de que la implementación de Azure está conectada a la local AD y DNS a través Azure sitios o distribuir Express (se hace referencia también como entre local en el [Diseño e implementación Guide][planning-guide]), se espera que la máquina virtual se unan a un dominio local. Consideraciones de este paso se describen en capítulo [unirse a VM en dominio local (sólo Windows)] [4.3 de guía de implementación] de este documento.

#### <a name="ec323ac3-1de9-4c3a-b770-4ff701def65b"></a>Configurar la supervisión
Configurar la Azure mejorado supervisión extensión para SAP como se describe en el capítulo [configurar Azure mejorado supervisión extensión para SAP] [implementación-Guía-4.5] de este documento.

Compruebe los requisitos previos para la supervisión de SAP para las versiones mínimos necesarios de Kernel de SAP y un agente de Host de SAP en los recursos enumerados en el capítulo [recursos SAP] [2.2 de la Guía de implementación] de este documento.

#### <a name="monitoring-check"></a>Comprobación de supervisión
Comprobar si la supervisión funciona como se describe en el capítulo [comprueba y solución de problemas de configuración de supervisión - to-End de SAP en Azure][deployment-guide-troubleshooting-chapter].

#### <a name="post-deployment-steps"></a>Pasos posteriores a la implementación
Después de crear la máquina virtual, se implementará y es después de instalar todos los componentes de software necesarios en la máquina virtual. Por lo tanto, este tipo de implementación de VM requeriría software esté instalado beeing ya están disponibles en Microsoft Azure en algunas otra VM o como disco que se puede vincular. O podemos observar en escenarios entre local donde conectividad a los activos de local (instalar recursos compartidos) es un determinado.

### <a name="54a1fc6d-24fd-4feb-9c57-ac588a55dff2"></a>Escenario 2: Implementar una máquina virtual con una imagen personalizada para SAP
Como se describe en [planificación y la Guía de implementación] [Guía de planificación] ya está en conocer los pasos detallados hay una forma para preparar y crear una imagen personalizada y usarlo para crear varias máquinas virtuales nuevas. La secuencia de pasos en el diagrama de flujo tendrá un aspecto similar:
 
![Diagrama de flujo de implementación de máquina virtual para sistemas SAP mediante una imagen de máquina virtual en privado Marketplace][deployment-guide-figure-300]

Siguiendo el diagrama de flujo deben ejecutar los siguientes pasos:

#### <a name="create-virtual-machine"></a>Crear la máquina virtual
Para crear una implementación mediante una imagen de sistema operativo privada a través del Portal de Azure, utilice una de las plantillas SAP publicadas en el [repositorio de plantillas de azure tutorial github][azure-quickstart-templates-github].
También puede crear una máquina virtual mediante la [PowerShell] [ virtual-machines-upload-image-windows-resource-manager] manualmente. 

* [plantilla de configuración de nivel 2 (una máquina virtual solo)] [ sap-templates-2-tier-user-image] Use esta plantilla si desea crear un sistema de nivel 2 con solo una máquina virtual y su propia imagen de sistema operativo.
* [plantilla de configuración de nivel 3 (varias máquinas virtuales)] [ sap-templates-3-tier-user-image] Use esta plantilla si desea crear un sistema de nivel 3 con varias máquinas virtuales y su propia imagen de sistema operativo.

Después de abrir una de las plantillas anteriores, el Portal de Azure se desplaza al panel Modificar parámetros. Escriba la información siguiente:

* **sapSystemId**: Id. de sistema SAP
* **osType**: tipo de sistema operativo que desea implementar Windows o Linux
* **sapSystemSize**: el tamaño del sistema SAP
    * La cantidad de SAP le proporcionará el nuevo sistema. Si no está seguro cuántos SAP que requiere el sistema, póngase en contacto con su asociado de tecnología de SAP o la integración del sistema
* **systemAvailability**: (solo la plantilla de nivel 3) disponibilidad del sistema 
    * Haga clic en una configuración que es adecuada para una instalación HA HA. Dos servidores de base de datos y se creará dos servidores para la ASC.
* **storageType**: (solo la plantilla de nivel 2) tipo de almacenamiento que se debe usar 
    * Sistemas más grande, se recomienda el uso de almacenamiento de Premium. Para obtener más información sobre los tipos de almacenamiento diferente, lea 
        * [Microsoft Azure almacenamiento] [dbms-guía-2.3] de [DBMS Guide] [Guía de dbms]
        * [Almacenamiento de Premium: El almacenamiento de alto rendimiento para cargas de trabajo de Azure Máquina Virtual][storage-premium-storage-preview-portal]
        * [Introducción al almacenamiento de Azure][storage-introduction]
* **adminUsername** y **adminPassword**: nombre de usuario y contraseña
    * Se crea un nuevo usuario que puede utilizar para iniciar sesión en el equipo.
* **userImageVhdUri**: URI del disco duro virtual de OS imagen privado por ejemplo https://`<accountname`>.blob.core.windows.net/vhds/userimage.vhd
* **userImageStorageAccount**: nombre de la cuenta de almacenamiento donde la imagen del SO privada se almacena, por ejemplo, `<accountname`> en el ejemplo anterior de URI
* **newOrExistingSubnet**: determina si se deben crear una nueva red virtual y subred o una subred existente que se debe usar. Si ya tiene una red virtual que está conectada a su red local, seleccione existente.
* **subnetId**: el identificador de la subred a la que los equipos virtuales debe estar conectados a. Seleccione la subred de su red virtual VPN o Express ruta para conectar la máquina virtual a su red local. El número de identificación suele ser similar a /subscriptions/`<subscription id`> /resourceGroups/`<resource group name`> /providers/Microsoft.Network/virtualNetworks/`<virtual network name`> /subnets/`<subnet name`>

Una vez que haya escrito todos los parámetros, seleccione la suscripción y el grupo de recursos que desea usar. Puede seleccionar un grupo de recursos existente o cree uno nuevo seleccionando "+ nueva" en el menú desplegable. Si crea un nuevo grupo de recursos, debe seleccionar la región donde se creará el grupo de recursos y de la máquina virtual.

Revise las condiciones legales, aceptarla y haga clic en crear.

#### <a name="install-vm-agent-linux-only"></a>Instalar a agente VM (sólo Linux)
Ya debe estar instalado el agente de Linux en la imagen de usuario si desea usar las plantillas anteriores. En caso contrario, se producirá un error en la implementación. Descargar e instalar el agente de VM en la imagen de usuario, como se describe en el capítulo [descargar, instalar y activar agente de Azure VM] [implementación-Guía-4.4] de este documento.
Si no usa las plantillas anteriores, también puede instalar al agente de VM posteriormente.

#### <a name="join-domain-windows-only"></a>Unirse a dominio (sólo Windows)
En el caso de que la implementación de Azure está conectada a la local AD y DNS a través Azure sitios o distribuir Express (se hace referencia también como entre local en el [Diseño e implementación Guide][planning-guide]), se espera que la máquina virtual se unan a un dominio local. Consideraciones de este paso se describen en capítulo [unirse a VM en dominio local (sólo Windows)] [4.3 de guía de implementación] de este documento.

#### <a name="configure-proxy-settings"></a>Establecer la configuración de Proxy
Según la configuración de su red local, podrían ser necesario para configurar al servidor proxy en su máquina virtual si está conectado a su red local a través de la VPN o la ruta de Express. En caso contrario, la máquina virtual puede no pueda tener acceso a internet y, por tanto, no puede descargar la extensiones necesarias o recopilar datos de supervisión. Consulte [Configurar Proxy] de capítulo[ deployment-guide-configure-proxy] de este documento.

#### <a name="configure-monitoring"></a>Configurar la supervisión
Configurar la extensión de supervisión de Azure para SAP como se describe en el capítulo [configurar Azure mejorado supervisión extensión para SAP] [implementación-Guía-4.5] de este documento.
Compruebe los requisitos previos para la supervisión de SAP para las versiones mínimos necesarios de Kernel de SAP y un agente de Host de SAP en los recursos enumerados en el capítulo [recursos SAP] [2.2 de la Guía de implementación] de este documento.

#### <a name="monitoring-check"></a>Comprobación de supervisión
Comprobar si la supervisión funciona como se describe en el capítulo [comprueba y solución de problemas de configuración de supervisión - to-End de SAP en Azure][deployment-guide-troubleshooting-chapter].

### <a name="a9a60133-a763-4de8-8986-ac0fa33aa8c1"></a>Escenario 3: Mover una máquina virtual locales con un disco duro Azure no generalizado virtual de SAP
Este escenario es escribir direcciones en el caso de un sistema SAP simplemente moviéndose en el formulario actual y la forma de local a Azure. Medios nombre no cambiar el nombre de host de Windows o Linux y SAP SID o algo que lleve a cabo. En este caso, el disco duro virtual no se hace referencia como una imagen durante la implementación, pero se usa directamente como el disco de sistema operativo. Con respecto a la implementación, en este caso es distinto de los dos casos antiguos por el hecho de que el agente de VM no se instala automáticamente durante la implementación. Por lo tanto, el agente de Azure VM es necesario descargar de Microsoft y debe estar instalado y activado dentro de la máquina virtual manualmente más adelante. Después de que dicha tarea se realizó correctamente, puede continuar iniciar la extensión de Azure de supervisión de Host de SAP y su configuración. Para obtener detalles sobre la función del agente de Azure VM, consulte este artículo:

[comment]: <> (MSSedusch TODO vínculo de Windows Update siguiente) 

___

> ![Windows][Logo_Windows] Windows
>
> <http://blogs.msdn.com/b/wats/Archive/2014/02/17/BGInfo-guest-Agent-Extension-for-Azure-VMs.aspx>
>
> ![Linux][Logo_Linux] Linux
>
> [Guía de usuario de Azure agente Linux][virtual-machines-linux-agent-user-guide]

___

El flujo de trabajo de los diferentes pasos tiene el siguiente aspecto:
 
![Diagrama de flujo de implementación de máquina virtual para sistemas SAP con un disco de VM][deployment-guide-figure-400]

Suponiendo que el disco ya se cargó y se define en Azure (vea [Diseño e implementación Guide][planning-guide]), siga estos pasos

#### <a name="create-virtual-machine"></a>Crear la máquina virtual
Para crear una implementación mediante un disco de sistema operativo privado a través del Portal de Azure, use la plantilla SAP publicada en el [repositorio de plantillas de azure tutorial github][azure-quickstart-templates-github]. También puede crear una máquina virtual usando la [PowerShell o Azure CLI manualmente.

* [plantilla de configuración de nivel 2 (una máquina virtual solo)][sap-templates-2-tier-os-disk]
    * Use esta plantilla si desea crear un sistema de nivel 2 mediante una máquina virtual solo.

Después de abrir la plantilla anterior, el Portal de Azure se desplaza al panel Modificar parámetros. Escriba la información siguiente:

* **sapSystemId**: Id. de sistema SAP
* **osType**: tipo de sistema operativo que desea implementar Windows o Linux
* **sapSystemSize**: el tamaño del sistema SAP
    * La cantidad de SAP le proporcionará el nuevo sistema. Si no está seguro cuántos SAP que requiere el sistema, póngase en contacto con su asociado de tecnología de SAP o la integración del sistema
* **storageType**: (solo la plantilla de nivel 2) tipo de almacenamiento que se debe usar 
    * Sistemas más grande, se recomienda el uso de almacenamiento de Premium. Para obtener más información sobre los tipos de almacenamiento diferente, lea 
        * [Microsoft Azure almacenamiento] [dbms-guía-2.3] de [DBMS Guide] [Guía de dbms]
        * [Almacenamiento de Premium: El almacenamiento de alto rendimiento para cargas de trabajo de Azure Máquina Virtual][storage-premium-storage-preview-portal]
        * [Introducción al almacenamiento de Azure][storage-introduction]
* **osDiskVhdUri**: URI del SO privado en disco por ejemplo https://`<accountname`>.blob.core.windows.net/vhds/osdisk.vhd
* **newOrExistingSubnet**: determina si se deben crear una nueva red virtual y subred o una subred existente que se debe usar. Si ya tiene una red virtual que está conectada a su red local, seleccione existente.
* **subnetId**: el identificador de la subred a la que los equipos virtuales debe estar conectados a. Seleccione la subred de su red virtual VPN o Express ruta para conectar la máquina virtual a su red local. El número de identificación suele ser similar a /subscriptions/`<subscription id`> /resourceGroups/`<resource group name`> /providers/Microsoft.Network/virtualNetworks/`<virtual network name`> /subnets/`<subnet name`>

Una vez que haya escrito todos los parámetros, seleccione la suscripción y el grupo de recursos que desea usar. Puede seleccionar un grupo de recursos existente o cree uno nuevo seleccionando "+ nueva" en el menú desplegable. Si crea un nuevo grupo de recursos, debe seleccionar la región donde se creará el grupo de recursos y de la máquina virtual.

Revise las condiciones legales, aceptarla y haga clic en crear.

#### <a name="install-vm-agent"></a>Instalar a agente VM
El agente de VM ya debe estar instalado en el disco de sistema operativo si desea usar las plantillas anteriores. En caso contrario, se producirá un error en la implementación. Descargue e instale el agente de máquina virtual en la máquina virtual como se describe en el capítulo [descargar, instalar y activar agente de Azure VM] [implementación-Guía-4.4] de este documento.

Si no usa las plantillas anteriores, también puede instalar al agente de VM posteriormente.

#### <a name="join-domain-windows-only"></a>Unirse a dominio (sólo Windows)
En el caso de que la implementación de Azure está conectada a la local AD y DNS a través Azure sitios o distribuir Express (se hace referencia también como entre local en el [Diseño e implementación Guide][planning-guide]), se espera que la máquina virtual se unan a un dominio local. Consideraciones de este paso se describen en capítulo [unirse a VM en dominio local (sólo Windows)] [4.3 de guía de implementación] de este documento.

#### <a name="configure-proxy-settings"></a>Establecer la configuración de Proxy
Según la configuración de su red local, podrían ser necesario para configurar al servidor proxy en su máquina virtual si está conectado a su red local a través de la VPN o la ruta de Express. En caso contrario, la máquina virtual puede no pueda tener acceso a internet y, por tanto, no puede descargar la extensiones necesarias o recopilar datos de supervisión. Consulte [Configurar Proxy] de capítulo[ deployment-guide-configure-proxy] de este documento.

#### <a name="configure-monitoring"></a>Configurar la supervisión
Configurar Azure mejorado supervisión extensión de SAP, como se describe en el capítulo [configurar Azure mejorado supervisión extensión para SAP] [implementación-Guía-4.5] de este documento.

Compruebe los requisitos previos para la supervisión de SAP para las versiones mínimos necesarios de kernel SAP y un agente de Host de SAP en los recursos enumerados en el capítulo [recursos SAP] [2.2 de la Guía de implementación] de este documento.

#### <a name="monitoring-check"></a>Comprobación de supervisión
Comprobar si la supervisión funciona como se describe en el capítulo [comprueba y solución de problemas de configuración de supervisión - to-End de SAP en Azure][deployment-guide-troubleshooting-chapter].

### <a name="scenario-4-updating-the-monitoring-configuration-for-sap"></a>Escenario 4: Actualizar la configuración de supervisión de SAP
Hay casos donde necesarias para actualizar la configuración de supervisión:

* El equipo de Microsoft y SAP conjunto había ampliado las capacidades de supervisión y decidido agregar más contadores o eliminar algunos contadores. 
* Microsoft presenta una nueva versión de la infraestructura de Azure subyacente entregar los datos de supervisión y la Azure mejorado supervisión extensión de SAP es adaptarse a esos cambios.
* Agregar adicionales VHD colocado en su máquina virtual de Azure o quitar un disco duro virtual. En este caso, deberá actualizar la colección de almacenamiento de datos relacionados. Si cambia la configuración al agregar o eliminar extremos o asignar direcciones IP a una máquina virtual, no afectará a la configuración de supervisión.
* Cambiar el tamaño de la máquina virtual de Azure de A5, por ejemplo, para cualquier otro tamaño de máquina virtual.
* Agregar nuevas interfaces de red para su máquina virtual de Azure

Para actualizar la configuración de supervisión, realice lo siguiente:

* Actualizar la infraestructura de supervisión siguiendo los pasos que se explica en capítulo [configurar Azure mejorado supervisión extensión para SAP] [implementación-Guía-4.5] de este documento. Ejecutar volver a la secuencia de comandos que se describen en este capítulo detecta que una configuración de supervisión se implementa y realice los cambios necesarios para la configuración de supervisión. 

___

> ![Windows][Logo_Windows] Windows
>
> Para la actualización del agente de Azure VM, no es necesaria intervención del usuario. Automático de agente VM se actualiza y no requiere un reinicio de la máquina virtual.
>
> ![Linux][Logo_Linux] Linux
>
> Siga los pasos de [este artículo] [ virtual-machines-linux-update-agent] para actualizar el agente de Azure Linux. 

___

## <a name="detailed-single-deployment-steps"></a>Pasos de implementación única detallados

### <a name="604bcec2-8b6e-48d2-a944-61b0f5dee2f7"></a>Implementar los cmdlets de PowerShell de Azure
* Vaya a <https://azure.microsoft.com/downloads/>
* En la sección 'De línea de comandos herramientas' es una sección denominada 'Windows PowerShell'. Siga el vínculo 'Instalar'.
* Administrador de descargas de Microsoft le ventana emergente con una línea de artículo que termina en @ .exe. Seleccione la opción 'Ejecutar'.
* Se descubre una ventana emergente que le pregunta si desea ejecutar el instalador de plataforma Web de Microsoft. Presione Sí
* Aparece una pantalla como este:
 
![Pantalla de instalación para los cmdlets de PowerShell de Azure][deployment-guide-figure-500]
<a name="figure-5"></a>

* Presione la instalación y Aceptar al CLUF.

Con frecuencia, compruebe si se han actualizado los cmdlets de PowerShell. Normalmente, hay actualizaciones en un período mensual. La manera más sencilla de hacerlo es seguir los pasos de instalación, como se describió anteriormente hasta la pantalla de instalación se muestra en [este] [ deployment-guide-figure-5] figura. En esta pantalla, se muestra la fecha de lanzamiento de los cmdlets, así como el número de versión real. A menos que se indique diferente en notas de SAP [1928533] o [2015553], se recomienda para trabajar con la última versión de cmdlets de PowerShell de Azure.

Puede comprobar la versión instalada actualmente de los cmdlets de Azure en el escritorio o portátil con el comando PS:

```powershell
Import-Module Azure
(Get-Module Azure).Version
```

El resultado debe presentarse tal como se muestra a continuación en [esta] [ deployment-guide-figure-6] figura.

![Resultado de comprobación de la versión de Azure PS cmdlet][deployment-guide-figure-600]
<a name="figure-6"></a>

Si la versión de Azure cmdlet instalada en el equipo de escritorio o portátil es la actual, la primera pantalla después de iniciar el instalador de plataforma Web de Microsoft se verá ligeramente diferente en comparación con las que se muestra en [este] [ deployment-guide-figure-5] figura.

Tenga en cuenta el círculo rojo, a continuación, en la [figura] [ deployment-guide-figure-7] debajo.
 
![Pantalla de instalación para los cmdlets de PowerShell de Azure que indica que la versión más reciente de Azure PS cmdlets están instalados][deployment-guide-figure-700]
<a name="figure-7"></a>

Si tiene un aspecto como [encima de]la pantalla[deployment-guide-figure-7], que indica que ya está instalada la versión más reciente de cmdlet Azure, no es necesario para continuar con la instalación. En este caso se puede 'salida' la instalación en esta fase.

### <a name="1ded9453-1330-442a-86ea-e0fd8ae8cab3"></a>Implementación de Azure CLI
* Vaya a <https://azure.microsoft.com/downloads/>
* En la sección 'De línea de comandos herramientas' es una sección denominada 'Interfaz de línea de comandos de Azure'. Siga el vínculo de instalación para su sistema operativo.

Con frecuencia, compruebe si se han actualizado CLI Azure. Normalmente, hay actualizaciones en un período mensual. La manera más sencilla de hacerlo es seguir los pasos de instalación, como se describió anteriormente.

Puede comprobar la versión actual instalada de CLI de Azure en el escritorio o portátil con el comando:

```
azure --version
```

El resultado debe presentarse tal como se muestra a continuación en [esta] [ deployment-guide-figure-azure-cli-version] figura.

![Resultado de comprobación de la versión de CLI de Azure][deployment-guide-figure-760]
<a name="0ad010e6-f9b5-4c21-9c09-bb2e5efb3fda"></a>

### <a name="31d9ecd6-b136-4c73-b61e-da4a29bbc9cc"></a>Unir VM en dominio local (sólo Windows)
En casos donde implementar máquinas virtuales de SAP en un escenario entre local donde AD local y DNS se extiende a Azure, esperamos que las máquinas virtuales se unen en un dominio local. Los pasos detallados de unirse a una máquina virtual a un dominio local y software adicional deben para ser que un miembro de un dominio local es cliente dependiente. Normalmente unirse a una máquina virtual a un dominio local significa instalar software adicional como software de protección contra Malware o varios agentes de software de copia de seguridad o supervisión.

Además, debe asegurarse de que en caso de que la configuración de proxy de Internet tienen al unirse a un dominio, que el Account(S-1-5-18) Local de sistema de Windows en la máquina virtual de invitado tiene estas opciones de configuración. Es más fácil forzar al servidor proxy con la directiva de grupo de dominio que se aplican a sistemas dentro del dominio.

### <a name="c7cbb0dc-52a4-49db-8e03-83e7edc2927d"></a>Descargar, instalar y activar a agente de Azure VM
Los pasos siguientes son necesarios cuando una máquina virtual para SAP se implementa desde las imágenes de sistema operativo que no sea generalizado, por ejemplo, no está preparada con Sysprep para Windows. No es necesario instalar al agente para máquinas virtuales implementado de Azure marketplace. Estas imágenes ya contienen al agente de Azure.

#### <a name="b2db5c9a-a076-42c6-9835-16945868e866"></a>Windows

* Descargue al agente de Azure VM:
    * Descargar el paquete de instalación de Azure VM agente de: <https://go.microsoft.com/fwlink/?LinkId=394789>
    * Almacenar el paquete MSI de agente VM localmente en el equipo portátil o un servidor
* Instalar al agente de Azure VM:
    * Conectar implementado Azure VM con Terminal Services (RDP)
    * Abra una ventana del explorador de Windows en la máquina virtual y un directorio de destino para el archivo MSI del agente VM
    * Arrastre y coloque el archivo MSI de Azure VM agente instalador desde su equipo portátil o servidor local en el directorio de destino del agente en la máquina virtual VM
    * Haga doble clic en el archivo MSI de la máquina virtual
    * Para VM unido a los dominios en local, hacer seguro de que la configuración de proxy de Internet final se aplica para la cuenta del sistema Local de Windows (S-1-5-18) en la máquina virtual, así como se describe en [Configurar Proxy]de capítulo[deployment-guide-configure-proxy]. El agente de máquina virtual se ejecutará en este contexto y necesita que pueda conectarse a Azure.

#### <a name="6889ff12-eaaf-4f3c-97e1-7c9edc7f7542"></a>Linux
Instale al agente de VM para Linux mediante el comando siguiente

- **SLES**

```
sudo zypper install WALinuxAgent
```
- **RHEL**

```
sudo yum install WALinuxAgent
```

### <a name="baccae00-6f79-4307-ade4-40292ce4e02d"></a>Configurar Proxy
Los pasos para configurar al servidor proxy diferencian en Windows y Linux.

#### <a name="windows"></a>Windows
Esta configuración también debe ser válida para la cuenta LocalSystem tener acceso a Internet. Si no se establece la configuración de proxy por directiva de grupo, puede configurar para la cuenta LocalSystem, siga estos pasos para configurarla.

1.  Abra gpedit.msc
1.  Vaya a configuración del equipo –> plantillas administrativas -> componentes de Windows -> Internet Explorer y habilitar "realizar proxy configuración por equipo (en lugar de por el usuario)
1.  Abra el Panel de Control y vaya a redes e Internet -> Opciones de Internet
1.  Abrir la pestaña conexiones y haga clic en configuración de LAN
1.  Deshabilitar "Detectar la configuración automáticamente"
1.  Habilitar "Usar un servidor proxy para la LAN" y escriba el host del servidor proxy y puerto

#### <a name="linux"></a>Linux
Configurar al servidor proxy correcto en el archivo de configuración de Microsoft Azure invitado Agent, que se encuentra en /etc/waagent.conf. Se deben establecer los parámetros siguientes:

```
HttpProxy.Host=<proxy host e.g. proxy.corp.local>
HttpProxy.Port=<port of the proxy host e.g. 80>
```

Reinicie al agente después de haber cambiado su configuración con

```
sudo service waagent restart
```

La configuración de proxy en /etc/waagent.conf también aplica para las extensiones VM necesarias. Si desea utilizar los repositorios de Azure, asegúrese de que el tráfico a estos repositorios no va a través de la intranet local. Si ha creado rutas de definidas por el usuario para habilitar forzado túnel, asegúrese de agregar una ruta que enruta el tráfico a los repositorios directamente a Internet y no a través de su conexión de sitio a otro.

- **SLES** También debe agregar rutas para las direcciones IP enumeradas en /etc/regionserverclnt.cfg. En la siguiente captura de pantalla se muestra un ejemplo. 

- **RHEL** También debe agregar rutas para las direcciones IP de los hosts de /etc/yum.repos.d/rhui-load-balancers. En la siguiente captura de pantalla se muestra un ejemplo.

Para obtener más detalles acerca de las rutas de definidas por el usuario, vea [el artículo][virtual-networks-udr-overview].

![Se obliga túnel][deployment-guide-figure-50]

### <a name="d98edcd3-f2a1-49f7-b26a-07448ceb60ca"></a>Configurar Azure extensión supervisión mejorada para SAP
Una vez que la máquina virtual está preparada como se describe en el capítulo [implementación escenarios de máquinas virtuales de SAP en Microsoft Azure] [implementación-Guía-3], que está instalado el agente de máquina virtual de Azure en el equipo. El siguiente paso es implementar la Azure mejorado supervisión extensión para SAP, que está disponible en el repositorio de extensión de Azure en los centros de datos global de Microsoft Azure. Para obtener más detalles, consulte [planificación y la Guía de implementación] [planificación de guía-9.1]. 

Puede usar PowerShell Azure o CLI de Azure para instalar y configurar la Azure mejorado supervisión extensión de SAP. Lea capítulo [Azure PowerShell] [implementación-Guía-4.5.1] si desea instalar la extensión en Windows o VM Linux con un equipo Windows. Para instalar la extensión en una VM Linux con Linux desktop leer capítulo [Azure CLI] [4.5.2 de la Guía de implementación].

#### <a name="987cf279-d713-4b4c-8143-6b11589bb9d4"></a>Azure PowerShell para Linux y máquinas virtuales de Windows
Para realizar las tareas de instalación la Azure mejorado supervisión extensión de SAP, siga estos pasos:

* Asegúrese de que ha instalado la versión más reciente del cmdlet de PowerShell de Microsoft Azure. Consulte capítulo [cmdlets de PowerShell de Azure implementar] [implementación-Guía-4.1] de este documento.  
* Ejecute el siguiente cmdlet de PowerShell. Para obtener una lista de entornos disponibles, ejecute el cmdlet Get-AzureRmEnvironment. Si desea usar Azure público, su entorno es AzureCloud. Azure en China, seleccione AzureChinaCloud.

```powershell
    $env = Get-AzureRmEnvironment -Name <name of the environment>
    Login-AzureRmAccount -Environment $env
    Set-AzureRmContext -SubscriptionName <subscription name>
    
    Set-AzureRmVMAEMExtension -ResourceGroupName <resource group name> -VMName <virtual machine name>
```

Después de proporcionar los datos de la cuenta y la máquina Virtual de Azure, el script implementar las extensiones necesarias y habilitar las características necesarias. Esto puede tardar varios minutos.
Lea [este artículo MSDN] [ msdn-set-azurermvmaemextension] para obtener más información sobre la AzureRmVMAEMExtension conjunto.
  
![Pantalla de resultado de la ejecución correcta de SAP específicos Azure cmdlet Set-AzureRmVMAEMExtension][deployment-guide-figure-900]

Una ejecución correcta de conjunto AzureRmVMAEMExtension realizará todos los pasos necesarios para configurar el host supervisar la funcionalidad de SAP. 

El resultado de que la secuencia de comandos debe entregar tiene el siguiente aspecto:

* Confirmación de que la configuración de supervisión para el disco duro virtual de Base (que contiene el sistema operativo) además de todos los VHD adicionales colocada en la máquina virtual se ha configurado.
* Los mensajes de dos confirmación la configuración de métrica de almacenamiento para una cuenta de almacenamiento específicos. 
* Una línea de salida dará un estado de la actualización real de la configuración de supervisión.
* Otro mostrará confirmar que la configuración se ha implementado o actualizado.
* La última línea del resultado es informativa que muestra la posibilidad de probar la configuración de supervisión.
* Para comprobar que todos los pasos de la supervisión de mejorada de Azure ha ejecutado correctamente y que la infraestructura de Azure proporciona los datos necesarios, continuar con la comprobación de preparación para Azure mejorado supervisión extensión de SAP, como se describe en el capítulo [disponibilidad busque Azure mejorado supervisión para SAP] [implementación-Guía-5.1] en este documento. 
* Para continuar haciendo esto, espere 15-30 minutos hasta que los diagnósticos de Azure tendrán los datos pertinentes recopilados.

#### <a name="408f3779-f422-4413-82f8-c57a23b4fc2f"></a>Azure CLI para máquinas virtuales de Linux

Para realizar las tareas de instalación la Azure mejorado supervisión extensión de SAP con Azure CLI, siga estos pasos:

1. Instalar CLI de Azure como se describe en [este] [ azure-cli] artículo
1. Inicie sesión con su cuenta de Azure

    ```
    azure login
    ```
1. Cambiar a modo de administrador de recursos de Azure

    ```
    azure config mode arm
    ```
1. Habilitar la supervisión mejorada de Azure

    ```
    azure vm enable-aem <resource-group-name> <vm-name>
    ```  
1. Compruebe que la supervisión de mejorada de Azure está activa en la máquina virtual Linux de Azure. Comprobar si existe el archivo /var/lib/AzureEnhancedMonitor/PerfCounters. Si existe, muestre información recopilada por AEM con:

    ```
    cat /var/lib/AzureEnhancedMonitor/PerfCounters
    ```
    A continuación, obtendrá resultados como:
    
    ```
    2;cpu;Current Hw Frequency;;0;2194.659;MHz;60;1444036656;saplnxmon;
    2;cpu;Max Hw Frequency;;0;2194.659;MHz;0;1444036656;saplnxmon;
    …
    …
    ```

## <a name="564adb4f-5c95-4041-9616-6635e83a810b"></a>Comprobaciones y solución de problemas de configuración de supervisión to-End de SAP en Azure
Después de que han implementado la máquina virtual de Azure y configurar la infraestructura de supervisión de Azure relevante, compruebe si todos los componentes de la supervisión de mejorada de Azure funcionan de manera correcta. 

Por lo tanto, ejecutar la comprobación de preparación para la Azure mejorado supervisión extensión para SAP como se describe en el capítulo [disponibilidad busque Azure mejorado supervisión para SAP] [5.1 de la Guía de implementación]. Si el resultado de esta comprobación es positivo y obtener todos los contadores de rendimiento, la supervisión de Azure ha sido configuración correctamente. En este caso, continúe con la instalación del agente de Host de SAP, como se describe en las notas de SAP enumerados en capítulo [recursos SAP] [2.2 de la Guía de implementación] de este documento. Si el resultado de la comprobación de preparación indica que faltan contadores, vaya a ejecutar la comprobación del estado de la infraestructura de supervisión de Azure como se describe en el capítulo [comprobación del estado de configuración de la infraestructura de supervisión de Azure] [5.2 de la Guía de implementación]. En caso de cualquier problema con la configuración de supervisión de Azure, compruebe capítulo [solucionar problemas de infraestructura de supervisión de Azure para SAP] [implementación-Guía-5.3] para obtener más ayuda sobre solución de problemas.

### <a name="bb61ce92-8c5c-461f-8c53-39f5e5ed91f2"></a>Comprobación de preparación para Azure mejorar la supervisión de SAP
Con esta comprobación se asegura de que las medidas que se muestran dentro de la aplicación de SAP que proporcionan completamente la infraestructura de supervisión de Azure subyacente. 

#### <a name="execute-the-readiness-check-on-a-windows-vm"></a>Ejecutar la comprobación de preparación en una máquina virtual de Windows
Para ejecutar la comprobación de preparación para la máquina Virtual de Azure (cuenta de administrador no es necesario) de inicio de sesión y ejecute los siguientes pasos:

* Abra un símbolo de Windows y cambie a la carpeta de instalación de la extensión de supervisión de Azure de SAP C:\Packages\Plugins\Microsoft.AzureCAT.AzureEnhancedMonitoring.AzureCATExtensionHandler\\`<version`> \drop

La versión en la ruta de acceso a la extensión de supervisión anterior pueden variar. Si ve varias carpetas de la versión de extensión supervisión en la carpeta de instalación, compruebe la configuración del servicio de Windows 'AzureEnhancedMonitoring' y cambie a la carpeta indicada como 'Ruta al ejecutable'.
 
![Propiedades del servicio se ejecuta la Azure mejorado supervisión extensión de SAP][deployment-guide-figure-1000]

* Ejecutar azperflib.exe en la ventana de comandos sin parámetros.

> [AZURE.NOTE] El azperflib.exe se ejecuta en un bucle y los contadores recopilados se actualiza cada 60 segundos. Para finalizar el bucle, cierre la ventana de comandos.

Si la extensión de supervisión mejorada de Azure no está instalada o no se está ejecutando el servicio 'AzureEnhancedMonitoring', la extensión no se han configurado correctamente. En este caso, siga capítulo [solucionar problemas de infraestructura de supervisión de Azure para SAP] [implementación-Guía-5.3] para obtener instrucciones detalladas sobre cómo implementar la extensión.

##### <a name="check-the-output-of-azperflibexe"></a>Compruebe la salida de azperflib.exe
El resultado de azperflib.exe muestra que todos los rellenan Azure contadores para SAP. En la parte inferior de la lista de contadores recopilados, busque un resumen y un indicador de estado, que indica el estado de la supervisión de Azure. 
 
![Resultado de la comprobación del estado ejecutando azperflib.exe que indica que no existe ningún problema][deployment-guide-figure-1100]
<a name="figure-11"></a>

Comprobar el resultado devuelto para la salida de la cantidad de 'Total contadores', que se notifican como vacío y para la comprobación del estado como se muestra en la figura [encima][deployment-guide-figure-11].

Puede interpretar los valores de resultado como sigue:

| Valores de resultado Azperflib.exe | Supervisar el estado de disponibilidad de Azure |
| ------------------------------|----------------------------------- |
| **Total de contadores: vacío** | Los siguientes contadores de almacenamiento de Azure 2 pueden estar vacíos: <ul><li>Lectura del almacenamiento de latencia Op servidor MS</li><li>Lectura del almacenamiento de latencia de Op E2E MS</li></ul>Todos los demás contadores deben contener valores. |
| **Comprobación de estado** | Solo Aceptar si devuelto estado muestra Aceptar |

Si no devuelven valores de azperflib.exe mostrar que todos los contadores llenados se devuelven correctamente, siga las instrucciones de la comprobación del estado para la configuración de la infraestructura de supervisión de Azure como se describe en el capítulo [comprobación del estado de configuración de la infraestructura de supervisión de Azure] [implementación-Guía-5.2] debajo.

#### <a name="execute-the-readiness-check-on-a-linux-vm"></a>Ejecutar la comprobación de preparación de una VM Linux
Para ejecutar la comprobación de preparación, conectar con SSH a la máquina Virtual de Azure y ejecute los siguientes pasos:

* Compruebe la salida de la extensión de supervisión mejorada de Azure
    * más /var/lib/AzureEnhancedMonitor/PerfCounters
        * Debe dar una lista de contadores de rendimiento. El archivo no debe estar vacío
    * cat /var/lib/AzureEnhancedMonitor/PerfCounters | Error de grep
        * Debe devolver una línea donde el error es ninguno, por ejemplo, 3; config; Error; 0; 0; **Ninguno**; 0; 1456416792; tst-servercs;
    * más /var/lib/AzureEnhancedMonitor/LatestErrorRecord
        * Debe estar vacía o no debe estar
* Si la primera comprobación anterior no se realizó correctamente, realice estos pruebas adicionales:
    * Asegúrese de que la waagent instalado e iniciado
        * SUDO ls-al/var/lib/waagent /
            * debe mostrar el contenido del directorio waagent
        * PS-ax | GREP waagent
            * debe mostrar una entrada similar a ' python /usr/sbin/waagent-daemon'
    * Asegúrese de que la extensión de diagnóstico Linux instalada e iniciada
        * SUDO sh - c ' ls-al /var/lib/waagent/Microsoft.OSTCExtensions.LinuxDiagnostic-*'
            * debe mostrar el contenido del directorio Linux diagnóstico extensión
        * PS-ax | GREP diagnóstico
            * debe mostrar una entrada similar a ' python /var/lib/waagent/Microsoft.OSTCExtensions.LinuxDiagnostic-2.0.92/diagnostic.py-daemon'
    * Asegúrese de que la extensión de supervisión mejorada de Azure instalada e iniciada
        * SUDO sh - c ' ls-al /var/lib/waagent/Microsoft.OSTCExtensions.AzureEnhancedMonitorForLinux-*/'
            * debe mostrar el contenido del directorio de extensión de supervisión mejorada de Azure
        * PS-ax | GREP AzureEnhanced
            * debe mostrar una entrada similar a 'daemon de python /var/lib/waagent/Microsoft.OSTCExtensions.AzureEnhancedMonitorForLinux-2.0.0.2/handler.py'
* Instalar al agente de Host de SAP, como se describe en la nota de SAP [1031096] y comprobar el resultado de saposcol
    * Ejecutar /usr/sap/hostctrl/exe/saposcol -d
    * Ejecutar descarga ccm
    * Compruebe si la métrica "Virtualization_Configuration\Enhanced supervisión acceso" es true
* Si ya tiene un servidor de aplicaciones de SAP NetWeaver ABAP instalada, abra transacción ST06 y compruebe si está activada la supervisión mejorada.

Si cualquiera de las comprobaciones encima de éxito, siga capítulo [solucionar problemas de infraestructura de supervisión de Azure para SAP] [implementación-Guía-5.3] para obtener instrucciones detalladas sobre cómo volver a implementar la extensión.

### <a name="e2d592ff-b4ea-4a53-a91a-e5521edb6cd1"></a>Comprobación del estado de configuración de la infraestructura de supervisión de Azure
Si algunos de los controles datos no se ha entregado correctamente, según indica la prueba que se describe en el capítulo [disponibilidad busque Azure mejorado supervisión para SAP] [5.1 de la Guía de implementación], ejecute el cmdlet AzureRmVMAEMExtension de prueba para comprobar si es correcta la configuración actual de la infraestructura de supervisión de Azure y la extensión de supervisión de SAP.

Para probar la configuración de supervisión, ejecute el siguiente orden:

* Asegúrese de que ha instalado la versión más reciente del cmdlet de PowerShell de Microsoft Azure como se describe en el capítulo [cmdlets de PowerShell de Azure implementar] [implementación-Guía-4.1] de este documento.
* Ejecute el siguiente cmdlet de PowerShell. Para obtener una lista de entornos disponibles, ejecute el cmdlet Get-AzureRmEnvironment. Si desea usar Azure público, su entorno es AzureCloud. Azure en China, seleccione AzureChinaCloud.

```powershell
$env = Get-AzureRmEnvironment -Name <name of the environment>
Login-AzureRmAccount -Environment $env
Set-AzureRmContext -SubscriptionName <subscription name>
Test-AzureRmVMAEMExtension -ResourceGroupName <resource group name> -VMName <virtual machine name>
```

* Después de proporcionar los datos de la cuenta y la máquina Virtual de Azure, la secuencia de comandos pruebe la configuración de la máquina virtual que elija.

 
![Pantalla de entrada del cmdlet Azure específico VMConfigForSAP_GUI de prueba SAP][deployment-guide-figure-1200]

Una vez introducido la información sobre su cuenta y la máquina Virtual de Azure, la secuencia de comandos pruebe la configuración de la máquina virtual que elija.
 
![Resultado de prueba correcta de infraestructura de supervisión de Azure para SAP][deployment-guide-figure-1300]

Asegúrese de que cada casilla está marcada con Aceptar. Si algunos de los controles no son Aceptar, ejecute el cmdlet de actualización como se describe en el capítulo [configurar Azure mejorado supervisión extensión para SAP] [implementación-Guía-4.5] de este documento. Otro Espere 15 minutos y realice las comprobaciones se describe en el capítulo [disponibilidad busque Azure mejorado supervisión para SAP] [5.1 de la Guía de implementación] y [comprobación del estado de configuración de la infraestructura de supervisión de Azure] [implementación-Guía-5.2] nuevamente. Si las comprobaciones indican un problema con algunos o todos los contadores, vaya a capítulo [solucionar problemas de infraestructura de supervisión de Azure para SAP] [5.3 de la Guía de implementación].

### <a name="fe25a7da-4e4e-4388-8907-8abc2d33cfd8"></a>Solucionar problemas de infraestructura de supervisión de Azure para SAP

#### <a name="windowslogowindows-azure-performance-counters-do-not-show-up-at-all"></a>![Windows][Logo_Windows] Contadores de rendimiento de Azure no se muestran en absoluto
La colección de las métricas de rendimiento en Azure se realiza mediante el servicio de Windows 'AzureEnhancedMonitoring'. Si no ha instalado correctamente el servicio o si no se ejecuta en la máquina virtual, no hay métricas de rendimiento se pueden recopilar en absoluto.

##### <a name="the-installation-directory-of-the-azure-enhanced-monitoring-extension-is-empty"></a>Directorio de instalación de la extensión de Azure mejorado supervisión está vacío 

###### <a name="issue"></a>Problema
El directorio de instalación C:\Packages\Plugins\Microsoft.AzureCAT.AzureEnhancedMonitoring.AzureCATExtensionHandler\\`<version`> \drop está vacío.

###### <a name="solution"></a>Solución
La extensión no está instalada. Compruebe si es un problema de proxy (como se describió antes). Puede que tenga que reiniciar el equipo y vuelva a ejecutar la secuencia de comandos de script AzureRmVMAEMExtension conjunto de configuración

##### <a name="service-for-azure-enhanced-monitoring-does-not-exist"></a>Servicio de Azure mejorado supervisión no existe 

###### <a name="issue"></a>Problema
Servicio de Windows 'AzureEnhancedMonitoring' no existe. Azperflib.exe: La salida de azperlib.exe produce un error como se muestra en la [figura siguiente][deployment-guide-figure-14].
 
![Ejecución de azperflib.exe indica que no se está ejecutando el servicio de la extensión de supervisión mejorada de Azure para SAP][deployment-guide-figure-1400]
<a name="figure-14"></a>

###### <a name="solution"></a>Solución
Si el servicio no existe tal como se muestra en la [figura anterior][deployment-guide-figure-14], la extensión de supervisión de Azure para SAP no se ha instalado correctamente. Volver a la extensión de acuerdo con los pasos descritos para el escenario de implementación de capítulo [implementación escenarios de máquinas virtuales de SAP en Microsoft Azure] [3 de la Guía de implementación]. 

Después de la implementación de la extensión, vuelva a comprobar si los contadores de rendimiento de Azure se proporcionan dentro de la máquina virtual de Azure después de 1 hora.

##### <a name="service-for-azure-enhanced-monitoring-exists-but-fails-to-start"></a>Servicio de Azure mejorado supervisión existe, pero no se puede iniciar 

###### <a name="issue"></a>Problema
Servicio de Windows 'AzureEnhancedMonitoring' existe y está habilitado, pero no se puede iniciar. Compruebe el registro de eventos de aplicación para obtener más información.

###### <a name="solution"></a>Solución
Configuración incorrecta. Volver a habilitar la extensión de supervisión de la máquina virtual, como se describe en el capítulo [configurar Azure mejorado supervisión extensión para SAP] [4.5 de la Guía de implementación].

#### <a name="windowslogowindows-some-azure-performance-counters-are-missing"></a>![Windows][Logo_Windows] Faltan algunos contadores de rendimiento de Azure
La colección de las métricas de rendimiento en Azure se realiza mediante el servicio de Windows 'AzureEnhancedMonitoring', que obtiene datos desde varios orígenes. Algunos datos de configuración se recopila localmente, se leen los indicadores de rendimiento de diagnósticos de Azure y contadores de almacenamiento se utilizan en el registro en el nivel de suscripción de almacenamiento.

Si la solución de problemas con SAP nota [1999351] no ayuda, vuelva a ejecutar el script AzureRmVMAEMExtension conjunto de configuración. Que tenga que esperar para una hora porque no se pueden crear almacenamiento análisis o diagnósticos contadores inmediatamente después de que están habilitadas. Si el problema persiste, abra un mensaje de soporte al cliente SAP en el componente BC-OP-NT-AZR.

#### <a name="linuxlogolinux-azure-performance-counters-do-not-show-up-at-all"></a>![Linux][Logo_Linux] Contadores de rendimiento de Azure no se muestran en absoluto

La colección de las métricas de rendimiento en Azure se realiza un demonio. Si no se está ejecutando el demonio, no hay métricas de rendimiento se pueden recopilar en absoluto.

##### <a name="the-installation-directory-of-the-azure-enhanced-monitoring-extension-is-empty"></a>Directorio de instalación de la extensión de Azure mejorado supervisión está vacío 

###### <a name="issue"></a>Problema
El directorio/var/biblioteca/waagent/no contiene un subdirectorio para la extensión de Azure mejorado supervisión.

###### <a name="solution"></a>Solución
La extensión no está instalada. Compruebe si es un problema de proxy (como se describió antes). Puede que tenga que reiniciar el equipo y vuelva a ejecutar el script AzureRmVMAEMExtension conjunto de configuración

#### <a name="linuxlogolinux-some-azure-performance-counters-are-missing"></a>![Linux][Logo_Linux] Faltan algunos contadores de rendimiento de Azure

La colección de las métricas de rendimiento en Azure se realiza un demonio, que obtiene datos desde varios orígenes. Algunos datos de configuración se recopila localmente, se leen los indicadores de rendimiento de diagnósticos de Azure y contadores de almacenamiento se utilizan en el registro en el nivel de suscripción de almacenamiento.

Para una completa y actualizada lista de problemas conocidos vea Nota SAP [1999351] que contiene información adicional de solución de problemas de mejorado Azure seguimiento para SAP.

Si la solución de problemas con SAP nota [1999351] no ayuda, vuelva a ejecutar la secuencia de comandos de configuración conjunto AzureRmVMAEMExtension como se describe en el capítulo [configurar Azure mejorado supervisión extensión para SAP] [4.5 de la Guía de implementación]. Que tenga que esperar para una hora porque no se pueden crear almacenamiento análisis o diagnósticos contadores inmediatamente después de que están habilitadas. Si el problema persiste, abra un mensaje de soporte al cliente SAP en el componente BC-OP-NT-AZR para Windows o BC-OP-LNX-AZR para una máquina virtual Linux.
