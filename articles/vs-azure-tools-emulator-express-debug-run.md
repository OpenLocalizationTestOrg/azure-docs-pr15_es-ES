<properties
   pageTitle="Usar emulador Express para ejecutar y depurar un servicio de nube en un equipo local | Microsoft Azure"
   description="Usar emulador Express para ejecutar y depurar un servicio de nube en un equipo local"
   services="visual-studio-online"
   documentationCenter="n/a"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="visual-studio-online"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="08/15/2016"
   ms.author="tarcher" />


# <a name="using-emulator-express-to-run-and-debug-a-cloud-service-on-a-local-machine"></a>Usar emulador Express para ejecutar y depurar un servicio de nube en un equipo local

Usando emulador Express, puede probar y depurar un servicio de nube sin ejecutar Visual Studio como administrador. Puede establecer la configuración del proyecto para usar emulador Express o el emulador completo, dependiendo de los requisitos de su servicio de nube. Para obtener más información sobre el emulador completo, vea [ejecutar una aplicación de Azure en el emulador calcular](./storage/storage-use-emulator.md). Express emulador se incluyó primero en Azure SDK 2.1 y, a partir de Azure SDK 2.3, es el emulador de forma predeterminada.

## <a name="using-emulator-express-in-the-visual-studio-ide"></a>Uso de emulador Express en Visual Studio IDE

Cuando se crea un nuevo proyecto en Azure SDK 2.3 o posterior, emulador Express ya está seleccionado. Para los proyectos existentes que se crearon con una versión anterior de SDK, siga estos pasos para seleccionar emulador Express.

### <a name="to-configure-a-project-to-use-emulator-express"></a>Para configurar un proyecto para utilizar emulador Express

1. En el menú contextual para el proyecto de Azure, elija **Propiedades**y, a continuación, elija la ficha **Web** .

1. En el **Servidor de desarrollo Local**, elija el botón de **opción usar IIS Express** . Emulador Express no es compatible con el servidor Web de IIS.

1. En el **emulador**, elija el botón de opción **Usar emulador Express** .

    ![Emulador Express](./media/vs-azure-tools-emulator-express-debug-run/IC673363.gif)

## <a name="launching-emulator-express-at-a-command-prompt"></a>Iniciar emulador Express en un símbolo del sistema

En el símbolo del sistema, puede iniciar la versión express de Azure calcular emulador, csrun.exe, mediante la opción /useemulatorexpress.

## <a name="limitations"></a>Limitaciones

Antes de utilizar emulador Express, debe tener en cuenta algunas limitaciones:

- Emulador Express no es compatible con el servidor Web de IIS.

- Su servicio de nube puede contener varias funciones, pero cada rol está limitado a una instancia.

- No puede acceder a los números de puerto inferior a 1000. Por ejemplo, si utiliza un proveedor de servicios de autenticación que usa normalmente un puerto inferior a 1000, debe cambiar este valor a un número de puerto que es superior a 1000.

- Las limitaciones que se aplican al emulador calcular Azure también se aplicarán a emulador Express. Por ejemplo, no puede tener más de 50 instancias de rol por implementación. Vea [ejecutar una aplicación de Azure en el emulador de cálculo](http://go.microsoft.com/fwlink/p/?LinkId=623050)

## <a name="next-steps"></a>Pasos siguientes

[Servicios de nube de depuración](https://msdn.microsoft.com/library/azure/ee405479.aspx)
