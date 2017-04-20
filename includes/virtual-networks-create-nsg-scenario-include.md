## <a name="scenario"></a>Escenario

Para ilustrar mejor cómo crear NSGs, este documento utilizará la situación siguiente.

![Escenario de VNet](./media/virtual-networks-create-nsg-scenario-include/figure1.png)

En este escenario creará un GSN para cada subred de la red virtual **TestVNet** , como se describe a continuación: 

- **GSN-front-end**. El front-end GSN se aplicará a la subred *front-end* y contienen dos reglas:  
    - **regla de rdp**. Esta regla le permitirá tráfico RDP a la subred *front-end* .
    - **regla de web**. Esta regla permitirá tráfico HTTP a la subred *front-end* .
- **GSN-back-end**. El back-end GSN se aplicará a la subred *back-end* y contienen dos reglas: 
    - **regla de sql**. Esta regla permite el tráfico SQL únicamente desde la subred *front-end* .
    - **regla de web**. Esta regla impide que todo internet enlazado el tráfico de la subred *back-end* .

La combinación de estas reglas crear un escenario como DMZ, donde la subred de back-end solo para SQL puede recibir el tráfico entrante desde la subred front-end y no tiene acceso a Internet, mientras que la subred front-end puede comunicarse con Internet y recibe solicitudes HTTP entrantes solo.
 
