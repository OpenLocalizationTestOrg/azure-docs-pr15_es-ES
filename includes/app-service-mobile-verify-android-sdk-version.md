Debido a desarrollo en curso, la versión de SDK Android instalada en Android Studio podría no coincidir con la versión en el código. El SDK Android hace referencia en este tutorial es versión 23, la información más reciente en el momento de escritura. Puede aumentar el número de versión como nuevas versiones del SDK aparezcan y, a continuación, le recomendamos que utilice la versión más reciente disponible.

Dos síntomas de error de coincidencia de versión son:

1. Al generar o volver a crear el proyecto, puede recibir mensajes de error de Gradle como "**no se puede encontrar el destino Google Inc.:Google APIs:n**".

2. Objetos de Android estándares de código que se deben resolver basados en `import` instrucciones pueden generar mensajes de error.

Si cualquiera de estos aparecen, la versión del SDK Android instalado en Android Studio podría no coinciden con el destino SDK del proyecto descargado.  Para comprobar la versión, realice los cambios siguientes:


1. Android Studio, haga clic en **Herramientas de** => **Android** => **Manager SDK**. Si no ha instalado la última versión de la plataforma SDK, a continuación, haga clic en para instalarlo. Anote el número de versión.

2. En la pestaña de explorador de proyectos, en **Gradle secuencias de comandos**, abra el archivo **build.gradle (modeule: aplicación)**. Asegurarse de que la **compileSdkVersion** y **buildToolsVersion** a la última versión SDK instalada. Las etiquetas podrían tener este aspecto:
 
            compileSdkVersion 'Google Inc.:Google APIs:23'
            buildToolsVersion "23.0.2"
    
3. En el Explorador de proyectos Studio Android haga clic en el nodo de proyecto, elija **Propiedades**y, en la columna izquierda, elija **Android**. Asegúrese de que el **Destino de compilación de proyecto** se establece en la misma versión SDK que la **targetSdkVersion**.

4. En Android Studio, el archivo manifiesto ya no se utiliza para especificar el destino SDK y la versión mínima de SDK, a diferencia de la caja con Eclipse.
