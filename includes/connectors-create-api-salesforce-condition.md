Esta condición evaluará el campo de dirección de correo electrónico de cada nuevo responsable de Salesforce. Si la dirección de correo electrónico contiene *amazon.com*, el resultado de la condición es *verdadera*.

1. Seleccione **+ nuevo paso**.  
![Imagen de condición de Salesforce 1](./media/connectors-create-api-salesforce/condition-1.png)   
- Seleccione **Agregar una condición**.    
![Imagen de condición de Salesforce 2](./media/connectors-create-api-salesforce/condition-2.png)  
- Seleccione **Elegir un valor**.    
![Imagen de condición de Salesforce 3](./media/connectors-create-api-salesforce/condition-3.png)  
- Seleccione el símbolo de *correo electrónico* desde el cliente potencial del desencadenador.    
![Imagen de condición de Salesforce 4](./media/connectors-create-api-salesforce/condition-4.png)  
- Seleccione *contiene*.      
![Imagen de condición de Salesforce 5](./media/connectors-create-api-salesforce/condition-5.png)  
- Seleccione **Elegir un valor** en la parte inferior del control.     
![Imagen de condición de Salesforce 6](./media/connectors-create-api-salesforce/condition-6.png)  
- Escriba *amazon.com* como el valor que se desea evaluar la dirección de correo electrónico del cliente potencial nuevo para. Si la dirección de correo electrónico contiene *amazon.com*, la condición se evalúa como *Verdadero* y el resto de los pasos en su aplicación lógica puede continuar.    
![Imagen de condición de Salesforce 7](./media/connectors-create-api-salesforce/condition-7.png)  
- Guarde sus aplicaciones de lógica.  

