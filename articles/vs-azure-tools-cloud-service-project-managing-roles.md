<properties
   pageTitle="Proyectos de Visual Studio de servicios de administración de roles en la nube de Azure | Microsoft Azure"
   description="Obtenga información sobre cómo agregar nuevas funciones a su proyecto de servicio de nube Azure o quite los roles existentes mediante Visual Studio."
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# <a name="managing-roles-in-the-azure-cloud-services-projects-with-visual-studio"></a>Administración de roles en los proyectos de servicios de nube de Azure con Visual Studio

Después de haber creado un proyecto de servicio de nube de Azure, puede agregar nuevas funciones a él o quite las funciones existentes. También puede importar un proyecto existente y convertirla en una función. Por ejemplo, puede importar una aplicación web ASP.NET y designar como una función web.

## <a name="adding-or-removing-roles"></a>Agregar o quitar roles

**Para agregar una función**

En el **Explorador de soluciones**, abra el menú contextual para el nodo **funciones** en el proyecto de servicio de nube y haga clic en **Agregar**. Puede seleccionar una función de web existente o de trabajo de la solución actual o crear un nuevo proyecto de rol web o de trabajo. O bien, puede seleccionar un proyecto adecuado, como un proyecto de aplicación web ASP.NET y asociarlo con un rol.

**Para quitar una asociación de rol**

En el nodo **funciones** del proyecto de servicio de nube en Explorador de soluciones, abra el menú contextual para la función que desea quitar y seleccione **Quitar**.

## <a name="removing-and-adding-roles-in-your-cloud-service"></a>Eliminar y agregar funciones en el servicio de nube

Si eliminar un rol de su proyecto de servicio de nube pero posteriormente, decide volver a agregar el rol al proyecto, se agregan sólo la declaración de función y atributos básicos, como extremos y diagnósticos de la información. No hay recursos adicionales o referencias se agregan al archivo ServiceDefinition.csdef o el archivo ServiceConfiguration.cscfg. Si desea agregar esta información, debe agregarlo manualmente en estos archivos.

Por ejemplo, puede quitar una función de servicio web y más tarde decide volver a agregar este rol a su solución. Si hace esto, se producirá un error. Para evitar este error, primero tiene que agregar el `<LocalResources>` elemento mostrado en el siguiente código XML en el archivo de ServiceDefinition.csdef. Use el nombre de la función de servicio web que haya agregado al proyecto como parte del atributo nombre para la **<LocalStorage>** elemento. En este ejemplo el nombre de la función del servicio web es **WCFServiceWebRole1**.

    <WebRole name="WCFServiceWebRole1">
        <Sites>
          <Site name="Web">
            <Bindings>
              <Binding name="Endpoint1" endpointName="Endpoint1" />
            </Bindings>
          </Site>
        </Sites>
        <Endpoints>
          <InputEndpoint name="Endpoint1" protocol="http" port="80" />
        </Endpoints>
        <Imports>
          <Import moduleName="Diagnostics" />
        </Imports>
       <LocalResources>
          <LocalStorage name="WCFServiceWebRole1.svclog" sizeInMB="1000" cleanOnRoleRecycle="false" />
       </LocalResources>
    </WebRole>

## <a name="next-steps"></a>Pasos siguientes

Obtenga información sobre cómo configurar los roles en Visual Studio lea [configurar los Roles de un servicio de nube de Azure con Visual Studio](vs-azure-tools-configure-roles-for-cloud-service.md).
