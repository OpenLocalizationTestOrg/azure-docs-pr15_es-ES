<properties
    pageTitle="Azure Active B2C de directorio: Escala de producción frente preview B2C inquilinos | Microsoft Azure"
    description="Un tema en los tipos de Azure Active Directory B2C inquilinos"
    services="active-directory-b2c"
    documentationCenter=""
    authors="swkrish"
    manager="mbaldwin"
    editor="bryanla"/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/30/2016"
    ms.author="swkrish"/>

# <a name="azure-active-directory-b2c-production-scale-vs-preview-b2c-tenants"></a>Azure Active B2C de directorio: Escala de producción frente preview B2C inquilinos

Si va a escribir una aplicación de producción en B2C de Azure Active Directory (AD Azure), debe asegurarse de que tiene el inquilino derecha "tipo" para comenzar a trabajar en. Para ver qué está instalada, siga estos pasos para [desplazarse hasta el módulo de características B2C](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade) en el portal de Azure y busque en **tipo de inquilinos**.

## <a name="summary"></a>Resumen

Azure AD B2C es compatible con las aplicaciones de producción sólo en B2C de **escala de producción** inquilinos en América del Norte.

| Tipo de inquilinos | Países o regiones | ¿Disponible de forma general? |
| ----------- | -------------- | --------------------- |
| **Inquilino de escala de producción** | América del Norte países o regiones | Sí |
| **Inquilino de escala de producción** | Todos los países o regiones excepto Norteamérica | No |
| **Inquilino de vista previa** | Todos los países o regiones | No |

> [AZURE.NOTE]
Inquilinos de Azure AD B2C (para empresas) no están disponibles actualmente en algunos países o regiones donde están disponibles los inquilinos de Azure AD (para empleados). Lea las siguientes secciones para obtener más detalles.

## <a name="production-scale-b2c-tenant-in-north-america"></a>Inquilino de escala de producción B2C en América del Norte

Si [creó el inquilino B2C](active-directory-b2c-get-started.md) en América del Norte, por ejemplo, en uno de los países o regiones siguientes: United Estados, Canadá, Costa Rica, República Dominicana, El Salvador, Guatemala, México, Panamá, Puerto Rico y Trinidad y Tobago y el **tipo del inquilino** en la interfaz de usuario de administración de B2C dice **escala de producción**, el inquilino de puede usarse para las aplicaciones de producción.

> [AZURE.NOTE]
Los inquilinos de escala de producción son capaces de escalar a 100s de millones de identidades de los consumidores por cada inquilino.

![Captura de pantalla de un inquilino de escala de producción](./media/active-directory-b2c-reference-tenant-type/production-scale-b2c-tenant.png)

## <a name="preview-b2c-tenant-in-any-countryregion"></a>Obtener una vista previa a B2C inquilino de cualquier país o región

Si ha creado a un inquilino B2C durante el período de vista previa de Azure AD B2C, es probable que el **inquilino escriba** dice **obtener una vista previa de los inquilinos**. Si este es el caso, debe utilizar a su inquilino sólo para desarrollo y pruebas y no para las aplicaciones de producción.

> [AZURE.IMPORTANT]
No hay ninguna ruta de migración de una vista previa B2C inquilino a un inquilino de escala de producción B2C. Tenga en cuenta que existen conocidos problemas al eliminar a un inquilino de vista previa B2C y volver a crear a un inquilino de escala de producción B2C con el mismo nombre de dominio. Debe crear a un inquilino de escala de producción B2C con otro nombre de dominio.

![Captura de pantalla de un inquilino de vista previa](./media/active-directory-b2c-reference-tenant-type/preview-b2c-tenant.png)

## <a name="production-scale-b2c-tenant-outside-of-north-america"></a>Inquilino de escala de producción B2C fuera de Norteamérica

Azure AD B2C no está generalmente disponibles fuera de Norteamérica. Sin embargo puede crear y usar los inquilinos de escala de producción para desarrollo y pruebas fines en uno de los países o regiones siguientes: Argelia, Austria, Azerbaiyán, Bahréin, Belarús, Bélgica, Bulgaria, Croacia, Chipre, República Checa, Dinamarca, Egipto, Estonia, Finlandia, Francia, Alemania, Grecia, Hungría, Islandia, Irlanda, Israel, Italia, Jordania, Kazajstán, Kenia, Kuwait, Letonia, Líbano, Liechtenstein, Lituania, Luxemburgo, ERY de Macedonia, Malta, Montenegro, Marruecos, países bajos, Nigeria, Noruega , Omán, Pakistán, Polonia, Portugal, Qatar, Rumanía, Rusia, Arabia Saudí, Serbia, Eslovaquia, Eslovenia, Sudáfrica, España, Suecia, Suiza, Túnez, Turquía, Ucrania, United Emiratos árabes y Reino Unido.

Una vez Azure AD B2C anuncia la disponibilidad general en encima de los países o regiones, aún puede usar a estas inquilinos de escala de producción y poner en marcha con las aplicaciones de producción sin pérdida de datos.

## <a name="availability-of-b2c-tenants"></a>Disponibilidad de los inquilinos B2C

B2C inquilinos no están disponibles actualmente en los siguientes países o regiones: Afganistán, Argentina, Australia, Brasil, Chile, Colombia, Ecuador, Hong Kong (RAE), India, Indonesia, Irak, Japón, Corea, Malasia, Nueva Zelanda, Paraguay, Perú, Filipinas, Singapur, Sri Lanka, Taiwán, Tailandia, Uruguay y Venezuela. Tenemos previsto incluirlos en el futuro.
