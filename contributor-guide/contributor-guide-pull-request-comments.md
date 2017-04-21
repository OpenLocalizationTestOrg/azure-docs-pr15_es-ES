# <a name="pull-request-comment-automation"></a>Automatización de comentario de solicitud de extracción

Usamos automatización de comentario de los comentarios de la solicitud de extracción para permitir a los colaboradores y proceso de revisión autores para asignar etiquetas que controlan la solicitud de extracción.

| Comentario | Qué hace | Disponibilidad|
| -------- |-------------|-------------|
|#Cierre de sesión | Cuando el autor de un artículo escribe el comentario **signo # desactivar** en la secuencia de comentario, se asigna la etiqueta **listo para combinar** . | Públicos y privados|
|#Cierre de sesión | Si un colaborador que no se encuentra el autor enumerados intenta aprobar una solicitud de extracción público con el comentario **signo # desactivar** , se escribe un mensaje a la solicitud de extracción que indica que la etiqueta sólo se puede asignar el autor. | Público |
|#Desactivar suspensión | Si escribe **hold desactivar #** en un comentario de solicitud de extracción, se quita la etiqueta **listo para combinar** - en caso de que se cambia de opinión o comete un error. En la repo privado, se asigna la etiqueta de **combinación de no hacer** . | Públicos y privados |
| #Inicie y cierre | Autores pueden escribir el comentario **#please y cierre** en la secuencia de comentario de una solicitud de extracción para cerrarla si no desea que los cambios combinados. | Público |

##<a name="troubleshooting-sign-offs-in-the-public-repo"></a>Solución de problemas de aprobaciones en la repo público

El inicio de sesión de repo público fuera automatización es permite solo al autor de cerrar la sesión. Puede que se necesiten el procesamiento de algunas excepciones manual:

- **Los autores de artículo**: para usar la automatización de comentario repositorio público, su cuenta de GitHub real debe coincidir exactamente con la cuenta de GitHub que aparece en los metadatos del artículo. Importa el uso de mayúsculas de su cuenta. Si se impide la firma debido a este problema, enviar correo a los alias de azdocprs.

- **Otros empleados**: si es un empleado que es la firma en nombre del autor y están bloqueados por la automatización, póngase en contacto con azdocprs con el vínculo de solicitud de extracción. Indicar que se encuentra: jefes de proyecto de la misma equipo de producto, compañeros en el equipo de escritura y escribir los administradores de grupo se consideran fuentes de confianza.



## <a name="related"></a>Relacionados

- [Etiqueta de la solicitud de extracción y los procedimientos recomendados para colaboradores de Microsoft](contributor-guide-pull-request-etiquette.md)

- [Revisión los criterios de calidad de la solicitud de extracción](contributor-guide-pr-criteria.md)
