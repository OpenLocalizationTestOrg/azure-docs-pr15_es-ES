## <a name="scenario"></a>Escenario

Para ilustrar mejor cómo crear UDRs, este documento utilizará la situación siguiente.

![DESCRIPCIÓN DE LA IMAGEN](./media/virtual-network-create-udr-scenario-include/figure1.png)

En este escenario creará uno UDR para la *subred final de frente* y otra UDR para la *subred de copia final* , tal como se describe a continuación: 

- **UDR-front-end**. El front-end UDR se aplicará a la subred *front-end* y contienen una ruta:  
    - **RouteToBackend**. Esta ruta enviará todo el tráfico a la subred de back-end para la máquina virtual de **FW1** .
- **UDR-back-end**. El back-end UDR se aplicará a la subred *back-end* y contienen una ruta: 
    - **RouteToFrontend**. Esta ruta enviará todo el tráfico a la subred de front-end para la máquina virtual de **FW1** .

La combinación de estas rutas se asegurará de que todo el tráfico destinado subred redirigirá a la máquina virtual de **FW1** , que se utiliza como un dispositivo virtual. También debe activar el reenvío de IP de la máquina, para asegurarse de que pueda recibir el tráfico destinado a otras.
