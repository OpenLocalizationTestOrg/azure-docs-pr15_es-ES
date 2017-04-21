<properties
 pageTitle="Obtener las herramientas (Windows 7 +) | Microsoft Azure"
 description="Descargar e instalar el software para la primera aplicación de ejemplo para el Pi y herramientas necesarias en Windows 7 y versiones posteriores."
 services="iot-hub"
 documentationCenter=""
 authors="shizn"
 manager="timlt"
 tags=""
 keywords=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="multiple"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="10/21/2016"
 ms.author="xshi"/>

# <a name="12-get-the-tools-windows-7-"></a>1.2 obtener las herramientas (Windows 7 +) 

> [AZURE.SELECTOR]
- [Windows 7 +](iot-hub-raspberry-pi-kit-node-lesson1-get-the-tools-win32.md)
- [Ubuntu 16.04](iot-hub-raspberry-pi-kit-node-lesson1-get-the-tools-ubuntu.md)
- [Mac OS 10.10](iot-hub-raspberry-pi-kit-node-lesson1-get-the-tools-mac.md)

## <a name="121-what-you-will-do"></a>1.2.1 lo que debe hacer

Descargue las herramientas de desarrollo y el software de la primera aplicación de ejemplo para el Pi frambuesas 3. Si se cumple algún problema, buscar soluciones en la [página de solución de problemas](iot-hub-raspberry-pi-kit-node-troubleshooting.md).

## <a name="122-what-you-will-learn"></a>1.2.2 lo que aprenderá
- Cómo instalar Git y Node.js
  - [GIT](https://git-scm.com) es un sistema de control de versiones de código abierto distribuido. La aplicación de ejemplo en esta lección se almacena en Git.
  - [Node.js](https://nodejs.org/en/) es un tiempo de ejecución de JavaScript con un ecosistema paquete enriquecido.
- Cómo utilizar NPM para instalar herramientas de desarrollo de Node.js adicionales.
  - El requisito de versión mínima de Node.js es 4.5 LTADOS.
  - [NPM](https://www.npmjs.com) es uno de los jefes de paquete para Node.js.

## <a name="123-what-you-need"></a>1.2.3 lo que necesita

- Una conexión a Internet para descargar el software y las herramientas de desarrollo
- Un equipo que ejecuta Windows

## <a name="124-install-git-and-nodejs"></a>1.2.4 instalar Git y Node.js

Haga clic en los vínculos siguientes para descargar e instalar Git y Node.js LTADOS para Windows.

- [Obtener Git para Windows](https://git-scm.com/download/win/)
- [Obtener Node.js LTADOS para Windows](https://nodejs.org/en/)

## <a name="125-install-additional-nodejs-development-tools"></a>1.2.5 instalar herramientas de desarrollo de Node.js adicionales

Use [gulp.js](http://gulpjs.com) para automatizar la implementación de la aplicación de ejemplo para el Pi. También se pueden utilizar la [detección de dispositivo de cli](https://github.com/Azure/device-discovery-cli) para recuperar información de la red acerca de los dispositivos IoT.

Inicie un símbolo del sistema como administrador. Instalar `gulp` y `device-discovery-cli` , ejecute el siguiente comando:

```bash
npm install -g device-discovery-cli gulp
```
    
Si experimenta problemas de instalación Node.js y estas herramientas de desarrollo de Node.js adicionales en el equipo, consulte la [Guía de solución de problemas](iot-hub-raspberry-pi-kit-node-troubleshooting.md) de soluciones a problemas comunes.

## <a name="126-install-visual-studio-code"></a>1.2.6 instalar el código de Visual Studio

[Descargar](https://code.visualstudio.com/docs/setup/windows) e instalar código de Visual Studio. Código de Visual Studio es un editor de código fuente eficaz para Windows, Linux y Mac OS. Use este editor más adelante en el tutorial para editar el código de ejemplo.

## <a name="127-summary"></a>1.2.7 resumen de

Ha instalado las herramientas de desarrollo necesarias y el software para la primera aplicación de ejemplo. En la siguiente sección, crear, implementar y ejecutar la aplicación de ejemplo en el Pi.

## <a name="next-steps"></a>Pasos siguientes

[1.3 crear e implementar la aplicación de ejemplo intermitente](iot-hub-raspberry-pi-kit-node-lesson1-deploy-blink-app.md)
