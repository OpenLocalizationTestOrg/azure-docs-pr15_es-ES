<properties
    pageTitle="Vista SAML devuelto por el servicio de Control de acceso (Java)"
    description="Obtenga información sobre cómo ver SAML devuelto por el servicio de Control de acceso en aplicaciones de Java hospedadas en Azure."
    services="active-directory" 
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor="" />

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/11/2016" 
    ms.author="robmcm" />

# <a name="how-to-view-saml-returned-by-the-azure-access-control-service"></a>Cómo ver SAML devuelto por el servicio de Control de acceso de Azure

Esta guía le mostrará cómo ver la subyacente seguridad aserción lenguaje de marcado (SAML) devuelve a la aplicación por el servicio de Control de acceso (ACS) de Azure. La guía se basa en el tema [cómo autenticar usuarios Web con Azure Access Control Service utilizando Eclipse][] , proporcionando código que muestra la información de SAML. La aplicación completada tendrá un aspecto similar al siguiente.

![Ejemplo de salida SAML][saml_output]

Para obtener más información sobre ACS, vea la sección [pasos siguientes](#next_steps) .

> [AZURE.NOTE]
> El filtro de Control de servicios de acceso de Azure es una vista previa de tecnología de comunidad. Software de versión preliminar, no es formal compatible con Microsoft.

## <a name="prerequisites"></a>Requisitos previos

Para completar las tareas en esta guía, complete la muestra cómo [autenticar usuarios Web con Azure Access Control Service utilizando Eclipse][] y usarlo como punto de partida para este tutorial.

## <a name="add-the-jspwriter-library-to-your-build-path-and-deployment-assembly"></a>Agregar la biblioteca de JspWriter a su ensamblado de ruta de acceso e implementación de compilación

Agregar la biblioteca que contiene la clase **javax.servlet.jsp.JspWriter** a su ensamblado de ruta de acceso e implementación de compilación. Si está utilizando Tomcat, la biblioteca está **jsp api.jar**, que se encuentra en la carpeta de **biblioteca** Apache.

1. En explorador del proyecto de Eclipse secundario **MyACSHelloWorld**, haga clic en **Generar la ruta de acceso**, haga clic en **Configurar la ruta de acceso de crear**, haga clic en la pestaña de **bibliotecas** y, a continuación, haga clic en **Agregar JAR externos**.
2. En el cuadro de diálogo de **Selección JAR** , desplácese hasta el TARRO necesario, seleccionarlo y, a continuación, haga clic en **Abrir**.
3. Con el cuadro de diálogo de **Propiedades de MyACSHelloWorld** aún abierto, haga clic en **Implementación ensamblado**.
4. En el cuadro de diálogo **Ensamblado de implementación de Web** , haga clic en **Agregar**.
5. En el cuadro de diálogo **Nueva directiva de ensamblado** , haga clic en **Las entradas de Java generar ruta de acceso** y, a continuación, haga clic en **siguiente**.
6. Seleccione la biblioteca correspondiente y haga clic en **Finalizar**.
7. Haga clic en **Aceptar** para cerrar el cuadro de diálogo de **Propiedades de MyACSHelloWorld** .

## <a name="modify-the-jsp-file-to-display-saml"></a>Modificar el archivo JSP para mostrar SAML

Modificar **index.jsp** para usar el código siguiente.

    <%@ page language="java" contentType="text/html; charset=UTF-8"
        pageEncoding="UTF-8"%>
        <%@ page import="javax.xml.parsers.*"
                 import="javax.xml.transform.*"
                 import="org.w3c.dom.*"
                 import="java.io.*"
                 import="javax.xml.transform.stream.*"
                 import="javax.xml.transform.dom.*"
                 import="javax.xml.xpath.*"
                 import="javax.servlet.jsp.JspWriter" %>
    <!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
    <html>
    <head>
    <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
    <title>Sample ACS Filter</title>
    </head>
    <body>
        <h3>SAML information from sample ACS program</h3>
        <%!
        void displaySAMLInfo(Node node, String parent, JspWriter out)
        {
        
            try
            {
                String nodeName;
                int nChild, i;
                
                nodeName = node.getNodeName();
                out.println("<br>");
                out.println("<u>Examining <b>" + parent + nodeName + "</b></u><br>");
                   
                   // Attributes.
                   NamedNodeMap attribsMap = node.getAttributes();
                   if (null != attribsMap)
                   {
                         for (i=0; i < attribsMap.getLength(); i++)
                         {
                                Node attrib = attribsMap.item(i);
                                out.println("Attribute: <b>" + attrib.getNodeName() + "</b>: " + attrib.getNodeValue()  + "<br>");
                         }
                   }
                   
                   // Child nodes.
                   NodeList list = node.getChildNodes();
                   if (null != list)
                   {
                          nChild = list.getLength();
                          if (nChild > 0)
                          {                    
    
                                 // If it is a text node, just print the text.
                                 if (list.item(0).getNodeName() == "#text")
                                 {
                                     out.println("Text value: <b>" + list.item(0).getTextContent() + "</b><br>");
                                 }
                                 else
                                 {
                                     // Print out the child node names.
                                     out.print("Contains " + nChild + " child node(s): ");   
                                     for (i=0; i < nChild; i++)
                                     {
                                        Node temp = list.item(i);
                                        
                                        out.print("<b>" + temp.getNodeName() + "</b>");
                                        if (i < nChild - 1)
                                        {
                                            // Separate the names.
                                            out.print(", ");
                                        }
                                        else
                                        {
                                            // Finish the sentence.
                                            out.print(".");
                                        }
                                            
                                     }
                                     out.println("<br>");
                                     
                                     // Process the child nodes.
                                     for (i=0; i < nChild; i++)
                                     {
                                        Node temp = list.item(i);
                                        displaySAMLInfo(temp, parent + nodeName + "\\", out);
                                     }
                               }
                          }
                      }
                  }
                catch (Exception e)
                {
                    System.out.println("Exception encountered.");
                    e.printStackTrace();            
                }
            }
        %>
    
        <%
        try 
        {
            String data  = (String) request.getAttribute("ACSSAML");
            
            DocumentBuilder docBuilder;
            Document doc = null;
            DocumentBuilderFactory docBuilderFactory = DocumentBuilderFactory.newInstance();
            docBuilderFactory.setIgnoringElementContentWhitespace(true);
            docBuilder = docBuilderFactory.newDocumentBuilder();
            byte[] xmlDATA = data.getBytes();
            
            ByteArrayInputStream in = new ByteArrayInputStream(xmlDATA); 
            doc = docBuilder.parse(in);
            doc.getDocumentElement().normalize();
            
            // Iterate the child nodes of the doc.
            NodeList list = doc.getChildNodes();
    
            for (int i=0; i < list.getLength(); i++)
            {
                displaySAMLInfo(list.item(i), "", out);
            }
        }
        catch (Exception e) 
        {
            out.println("Exception encountered.");
            e.printStackTrace();
        }
        
        %>
    </body>
    </html>

## <a name="run-the-application"></a>Ejecute la aplicación

1. Ejecute la aplicación en el emulador de equipo o implementar en Azure, con los pasos documentados a [cómo autenticar usuarios Web con Azure Access Control Service utilizando Eclipse][].
2. Inicie un explorador y abrir la aplicación web. Después de iniciar sesión en la aplicación, verá la información de SAML, incluida la aserción de seguridad proporcionada por el proveedor de identidades.

## <a name="next-steps"></a>Pasos siguientes

Aún más explorar la funcionalidad de ACS y para experimentar con escenarios más complejos, vea [2.0 de servicio de Control de acceso][].

[Prerequisites]: #pre
[Modify the JSP file to display SAML]: #modify_jsp
[Add the JspWriter library to your build path and deployment assembly]: #add_library
[Run the application]: #run_application
[Next steps]: #next_steps
[Servicio de Control de Access 2.0]: http://go.microsoft.com/fwlink/?LinkID=212360
[Cómo autenticar a los usuarios de la Web con el servicio de Control de acceso de Azure con Eclipse]: ../active-directory-java-authenticate-users-access-control-eclipse
[saml_output]: ./media/active-directory-java-view-saml-returned-by-access-control/SAML_Output.png
 