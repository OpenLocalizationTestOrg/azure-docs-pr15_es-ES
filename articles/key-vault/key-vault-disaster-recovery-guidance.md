<properties
    pageTitle="Qué hacer en caso de un Azure servicio interrupciones que afecta a Azure clave depósito | Microsoft Azure"
    description="Obtenga información sobre qué hacer en caso de una interrupción de servicio Azure que afecta a Azure clave depósito."
    services="key-vault"
    documentationCenter=""
    authors="adamglick"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="key-vault"
    ms.workload="key-vault"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/26/2016"
    ms.author="sumedhb;aglick"/>


# <a name="azure-key-vault-availability-and-redundancy"></a>Azure redundancia y disponibilidad de la cámara de clave

Características de Azure depósito de clave de varias capas de redundancia para asegurarse de que sus claves y la información confidencial siguen estando disponibles en la aplicación incluso si los errores de componentes individuales del servicio.

El contenido de la cámara clave se duplican dentro de la región y a una región secundaria al menos 150 millas ausente pero dentro de la misma geografía. Esto mantiene alta duración de sus claves y la información confidencial.

Si no componentes individuales en el servicio de cámara clave, componentes alternativos en la región de paso de para atender su solicitud para asegurarse de que no hay ninguna degradación de funcionalidad. No es necesario que realice ninguna acción para desencadenar esto. Ocurre automáticamente y será transparente a usted.

Raro toda una región Azure no está disponible, las solicitudes de Azure clave depósito en esa región son redirigida automáticamente (*error sobre*) a una región secundaria. Cuando la región principal esté disponible de nuevo, las solicitudes se enrutan atrás (*error vuelva*) al área principal. De nuevo, no es necesario que realice ninguna acción porque esto sucede automáticamente.

Hay algunas condiciones que debería considerar:

* En caso de una migración tras error de región, puede tardar unos minutos para que conmutar el servicio. Las solicitudes realizadas durante este período pueden fallar hasta que se complete la migración tras error.
* Una vez completado un error, su cámara clave está en modo de solo lectura. Las solicitudes que son compatibles con este modo son:
 * Lista de depósitos claves
 * Obtener las propiedades de claves depósitos
 * Información confidencial de lista
 * Obtener información confidencial
 * Teclas de la lista
 * Obtener claves (propiedades de)
 * Cifrar
 * Descifrar
 * Ajustar
 * Desajustar
 * Comprobar
 * Inicio de sesión
 * Copia de seguridad
* Cuando se produce un error volver un error, todos los tipos de solicitud (incluidas las solicitudes de lectura *y* escritura) están disponibles.
