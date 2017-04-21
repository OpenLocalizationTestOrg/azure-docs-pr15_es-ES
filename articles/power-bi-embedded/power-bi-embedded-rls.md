<properties
   pageTitle="Seguridad a nivel de fila con Power BI incrustados"
   description="Detalles sobre la seguridad de nivel de fila con Power BI incrustado"
   services="power-bi-embedded"
   documentationCenter=""
   authors="guyinacube"
   manager="erikre"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="10/04/2016"
   ms.author="asaxton"/>

# <a name="row-level-security-with-power-bi-embedded"></a>Seguridad de las filas con Power BI incrustado

Seguridad a nivel de fila (RLS) puede usarse para restringir el acceso de usuario a datos determinado dentro de un informe o un conjunto de datos, lo que permite varios usuarios diferentes usar el mismo informe mientras todos los datos diferentes ver. Power BI incrustado ahora es compatible con conjuntos de datos configurado con RLS.

![](media\power-bi-embedded-rls\pbi-embedded-rls-flow-1.png)

Para aprovechar las ventajas de RLS, es importante que comprende tres conceptos principales; Usuarios, funciones y reglas. Echemos un vistazo cada uno:

**Los usuarios** , estos son los usuarios finales reales visualización de informes. En Power BI incrustado, los usuarios se identifican mediante la propiedad del nombre de usuario en un símbolo de la aplicación.

**Roles** : los usuarios que pertenecen a las funciones. Una función es un contenedor de reglas y puede ser un nombre como "Administrador de ventas" o "Representante de ventas". En Power BI incrustados, los usuarios se identifican por la propiedad de roles en un símbolo de la aplicación.

**Reglas** : funciones tienen reglas y las reglas son los filtros reales que se van a aplicar a los datos. Esto podría ser tan sencillo como "país = Estados Unidos" o algo mucho más dinámico.

### <a name="example"></a>Ejemplo

Para el resto de este artículo, le proporcionamos un ejemplo de RLS de creación y, a continuación, consumo dentro de una aplicación incrustada. Nuestro ejemplo usa el archivo PBIX de [Ejemplo de análisis por menor](http://go.microsoft.com/fwlink/?LinkID=780547) .

![](media\power-bi-embedded-rls\pbi-embedded-rls-scenario-2.png)

Nuestro ejemplo de análisis de comercio por menor muestra las ventas de todos lo almacena en una determinada cadena de distribución. Sin RLS, no importa qué distrito administrador inicia sesión y ve el informe, verán los mismos datos. Directivos ha determinado cada jefe sólo debería ver las ventas de la comercios que administran y, para ello, podemos utilizar RLS.

RLS se crearon en Power BI Desktop. Cuando se abren el informe y un conjunto de datos, nos podemos cambiar a vista de diagrama para ver el esquema:

![](media\power-bi-embedded-rls\pbi-embedded-rls-diagram-view-3.png)

Estas son algunas cosas que debe tener en cuenta con este esquema:

-   Todas las medidas, como el **Total de ventas**, se almacenan en la tabla de hechos de **ventas** .
-   Hay cuatro tablas de dimensiones relacionadas adicionales: **elemento**, **hora**, **almacén**y **distrito**.
-   Las flechas en las líneas de relación indican cómo pueden flujo filtros de una tabla a otra. Por ejemplo, si se coloca un filtro de **tiempo [fecha]**, en el esquema actual podría solo filtrar valores en la tabla **Sales** . No ¿se verá afectados el otras tablas por este filtro ya que todas las flechas en las líneas de relación apunten a la tabla sales y no ausente.
-   La tabla **distrito** indica quién es el administrador para cada distrito:

    ![](media\power-bi-embedded-rls\pbi-embedded-rls-district-table-4.png)

Basado en este esquema si se aplica un filtro a la columna del **Jefe** de la tabla de distrito y si dicho filtro coincidencias al usuario ver el informe, filtro también filtrará hacia abajo de las tablas de **almacenamiento** y **ventas** para mostrar solo los datos para ese determinado distrito manager.

Le mostramos cómo:

1.  En la pestaña de modelado, haga clic en **Administrar Roles**.  
![](media\power-bi-embedded-rls\pbi-embedded-rls-modeling-tab-5.png)

2.  Crear una nueva función denominada **Administrador**.  
![](media\power-bi-embedded-rls\pbi-embedded-rls-manager-role-6.png)

3.  En la tabla de **distrito** , escriba la siguiente expresión de DAX: **[distrito Manager] = USERNAME()**  
![](media\power-bi-embedded-rls\pbi-embedded-rls-manager-role-7.png)

4.  Para asegurarse de que están trabajando las reglas, en la pestaña **modelado** , haga clic en **Ver como Roles**y, a continuación, escriba lo siguiente:  
![](media\power-bi-embedded-rls\pbi-embedded-rls-view-as-roles-8.png)

    Los informes ahora mostrarán los datos como si se han iniciado sesión como **Andrew Ma**.

Aplicar el filtro, la manera en que hemos hecho aquí, filtrará hacia abajo todos los registros de las tablas de **distrito**, **almacenamiento**y **ventas** . Sin embargo, debido a la dirección de filtro en las relaciones entre las **ventas** y la **hora**, las tablas de **ventas** y **elemento**y **elemento** y **tiempo** no pueden filtrarse hacia abajo.

![](media\power-bi-embedded-rls\pbi-embedded-rls-diagram-view-9.png)

Que puede aceptar de este requisito, sin embargo, si no queremos administradores para ver los elementos que no tienen las ventas, podríamos activar bidireccional un filtrado cruzado para la relación y el filtro de seguridad en ambas direcciones de flujo. Esto puede hacerse editando la relación entre **ventas** y **elemento**, similar a esta:

![](media\power-bi-embedded-rls\pbi-embedded-rls-edit-relationship-10.png)

Ahora, también pueden flujo filtros de la tabla de ventas a la tabla de **elemento** :

![](media\power-bi-embedded-rls\pbi-embedded-rls-diagram-view-11.png)

**Nota** Si usa el modo DirectQuery para sus datos, debe habilitar el filtrado de bidireccional entre seleccionando estas dos opciones:

1.  **Archivo** -> **Opciones y configuración** -> **Características de vista previa** -> **Habilitar cruzada filtrado en ambas direcciones para DirectQuery**.
2.  **Archivo** -> **Opciones y configuración de** -> **DirectQuery** -> **Permitir medida sin restricciones en el modo DirectQuery**.


Para obtener más información sobre el filtrado de cruz bidireccional, descargue las notas del producto [bidireccional filtros cruzados en SQL Server Analysis Services 2016 y Power BI Desktop](http://download.microsoft.com/download/2/7/8/2782DF95-3E0D-40CD-BFC8-749A2882E109/Bidirectional cross-filtering in Analysis Services 2016 and Power BI.docx) .

Esto se ajusta todo el trabajo que debe hacerse en Power BI Desktop, pero hay uno más trabajo que debe hacerse para hacer que la RLS reglas definido trabajo en Power BI incrustado. Los usuarios se autentican y autorizados por la aplicación y tokens de aplicación se utilizan para conceder el usuario acceso a un informe de Power BI incrustado específico. Power BI incrustado no tiene información específica en ¿quién es el usuario. Para que RLS para que funcione, debe pasar un contexto adicional como parte de su token de aplicación:
-   **nombre de usuario** (opcional): se usa con RLS es una cadena que se pueden usar para identificar el usuario al aplicar reglas RLS. Consulte el uso de la fila incrustado de seguridad con Power BI
-   **funciones** : una cadena que contiene los roles para seleccionar al aplicar las reglas de seguridad de nivel de fila. Si pasar más de una función, se deben pasar como una matriz de cadena.

Si está presente la propiedad del nombre de usuario, se debe pasar al menos un valor en roles.

El token de aplicación completo tendrá un aspecto parecido al siguiente:

![](media\power-bi-embedded-rls\pbi-embedded-rls-app-token-string-12.png)

Ahora, con todos los elementos juntos, cuando un usuario inicia sesión en la aplicación para ver el informe, solo podrá ver los datos que se pueden ver, según se define en nuestra seguridad a nivel de fila.

![](media\power-bi-embedded-rls\pbi-embedded-rls-dashboard-13.png)

## <a name="see-also"></a>Vea también
[Seguridad a nivel de fila (RLS) con Power](https://powerbi.microsoft.com/en-us/documentation/powerbi-admin-rls/)
