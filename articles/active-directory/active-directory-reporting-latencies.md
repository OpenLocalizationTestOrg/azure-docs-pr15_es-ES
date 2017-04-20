<properties
   pageTitle="Azure Active Directory Reporting latencia | Microsoft Azure"
   description="Cantidad de tiempo necesario para los informes de eventos para mostrarse en Azure Active Directory"
   services="active-directory"
   documentationCenter=""
   authors="dhanyahk"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="03/07/2016"
   ms.author="dhanyahk"/>

# <a name="azure-active-directory-report-latencies"></a>Latencia de informe de Azure Active Directory

*Esta documentación forma parte de [Azure Active Directory Reporting guía](active-directory-reporting-guide.md).*

Informe                                                  | Mínimo  | Promedio    | Valor máximo
------------------------------------------------------- | -------- | ---------- | ----------
**Informes de seguridad**                                    |          |            |
Actividad de inicio de sesión irregular                              | 2 horas  | 4 horas    | 8 horas
Inicios de sesión desde orígenes desconocidos                           | 2 horas  | 4 horas    | 8 horas
Complementos de inicio de sesión después de varios errores                        | 2 horas  | 4 horas    | 8 horas
Inicios de sesión de diversas localidades                      | 2 horas  | 4 horas    | 8 horas
Inicios de sesión de las direcciones IP con la actividad sospechosa     | 2 horas  | 4 horas    | 8 horas
Inicios de sesión desde dispositivos posiblemente infectados                 | 2 horas  | 4 horas    | 8 horas
Usuarios con actividades de inicio de sesión irregulares                   | 2 horas  | 4 horas    | 8 horas
Usuarios con credenciales perdidas                           | 2 horas  | 4 horas    | 8 horas
**Informes de la aplicación**                                 |          |            |
Aprovisionamiento de actividad de la cuenta                           | 2 horas  | 4 horas    | 8 horas
Errores de aprovisionamiento de la cuenta                             | 2 horas  | 4 horas    | 8 horas
Uso de la aplicación                                       | 2 horas  | 4 horas    | 8 horas
Estado de la conversión de contraseña                                | 2 horas  | 4 horas    | 8 horas
**Auditoría e informes de actividad**                            |          |            |
Informe de auditoría                                            | 1 minuto | 15 minutos | 30 minutos
Actividad (Azure AD) de restablecimiento de contraseña                      | 2 horas  | 4 horas    | 8 horas
Actividad (Administrador de identidades) de restablecimiento de contraseña              | 2 horas  | 4 horas    | 8 horas
Actividad de registro (Azure AD) de restablecimiento de contraseña         | 2 horas  | 4 horas    | 8 horas
Actividad de registro (Administrador de identidades) de restablecimiento de contraseña | 2 horas  | 4 horas    | 8 horas
Autoservicio de actividad de los grupos (Azure AD)                 | 2 horas  | 4 horas    | 8 horas
Autoservicio de actividad de los grupos (Administrador de identidades)         | 2 horas  | 4 horas    | 8 horas
**Informes de RMS**                                         |          |            |
Usuarios de RMS más activos                                   | 2 horas  | 4 horas    | 8 horas
Uso de RMS                                               | 2 horas  | 4 horas    | 8 horas
Uso del dispositivo de RMS                                        | 2 horas  | 4 horas    | 8 horas
Uso de la aplicación de RMS habilitado                           | 2 horas  | 4 horas    | 8 horas
**Informes de vista previa de privado**                             |          |            |
Todas las actividades de inicio de sesión de usuario                               | 2 horas  | 4 horas    | 8 horas
