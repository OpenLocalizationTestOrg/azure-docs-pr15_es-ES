## <a name="scenario"></a>Escenario

Para ilustrar mejor cómo crear un VNet y subredes, este documento utilizará la situación siguiente.

![Escenario de VNet](./media/virtual-networks-create-vnet-scenario-include/vnet-scenario.png)

En este escenario creará un VNet denominada **TestVNet** con un bloque CIDR reservado de **192.168.0.0./16**. Su VNet contendrá las siguientes subredes: 

- **Front-end**, utilizando **192.168.1.0/24** como su bloque CIDR
- **Back-end**, con **192.168.2.0/24** como su bloque CIDR

 