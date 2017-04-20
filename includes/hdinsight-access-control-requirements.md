Si usa una suscripción de Azure donde se encuentra no es el administrador o propietario, como una empresa que pertenecen a la suscripción, debe comprobar lo siguiente antes de siguiendo los pasos de este documento:

* El inicio de sesión de Azure debe tener al menos acceso de __Colaborador__ para el grupo de recursos de Azure que utiliza al crear HDInsight (y otros recursos de Azure).

* Alguien con al menos de __Colaborador__ de acceso a la suscripción de Azure debe ha registrado anteriormente el proveedor para el recurso que se está utilizando. Registro de proveedor ocurre cuando un usuario con acceso de colaborador para la suscripción, crea un recurso por primera vez en la suscripción. También puede realizarse sin necesidad de crear un recurso por [registrar un proveedor con el resto](https://msdn.microsoft.com/library/azure/dn790548.aspx).

Para obtener más información sobre cómo trabajar con la administración de acceso, consulte los siguientes documentos:

* [Introducción a la administración de acceso en el portal de Azure](../articles/active-directory/role-based-access-control-what-is.md)
* [Usar las asignaciones de roles para administrar el acceso a los recursos de suscripción de Azure](../articles/active-directory/role-based-access-control-configure.md)
