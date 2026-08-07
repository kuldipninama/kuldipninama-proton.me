# ¿Qué es Base?

Base es una red Layer 2 de Ethereum creada por Coinbase. Su objetivo es hacer que el onboarding a Web3 sea lo más simple posible para millones de usuarios.

Características principales:
- Bajos costos de gas
- Alta velocidad de transacciones
- Seguridad heredada de Ethereum
- Compatible con todas las herramientas de EVM (Hardhat, Foundry, Remix, etc.)

Base no tiene su propio token nativo de gas (usa ETH) y está enfocada en ser el hogar de la próxima generación de aplicaciones on-chain.
# Primeros pasos en Base

1. Añade la red Base a tu wallet (Chain ID: 8453)
2. Consigue ETH en Base (bridge oficial o exchanges)
3. Usa Hardhat o Foundry para desplegar
4. Explora el explorador: https://basescan.org

Documentación oficial: https://docs.base.org

# Base Guild Events

Repositorio para coordinar eventos, talleres, spaces y meetups del Guild de Base.

Base no es solo tecnología: es una comunidad de builders. Aquí organizamos todo lo relacionado con actividades presenciales y online.

# Base Guild Governance

Espacio de gobernanza y toma de decisiones del Guild de Base.

Base representa una visión de Web3 más accesible y orientada a la adopción masiva. Este repositorio sirve para coordinar cómo el guild participa y contribuye a ese ecosistema de forma organizada.

# Principios del Guild en Base

1. Transparencia en las decisiones
2. Foco en utilidad real y adopción
3. Colaboración open-source
4. Apoyo a builders nuevos y experimentados
5. Alineación con la misión de Base: llevar blockchain a la siguiente generación de usuarios

Base no es solo otra L2, es una apuesta por la usabilidad.
# Cómo tomamos decisiones

- Propuestas abiertas
- Discusión en comunidad
- Votación cuando sea necesario
- Ejecución transparente

Queremos que el guild sea un ejemplo de organización eficiente dentro del ecosistema Base.
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract SimpleStorage {
    uint256 private storedValue;
    address public owner;

    event ValueChanged(uint256 newValue, address indexed changedBy);

    constructor() {
        owner = msg.sender;
    }

    function set(uint256 value) external {
        storedValue = value;
        emit ValueChanged(value, msg.sender);
    }

    function get() external view returns (uint256) {
        return storedValue;
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract HelloBase {
    string public greeting = "Hello Base";
    address public lastSender;

    event GreetingChanged(string newGreeting, address indexed by);

    function setGreeting(string calldata newGreeting) external {
        greeting = newGreeting;
        lastSender = msg.sender;
        emit GreetingChanged(newGreeting, msg.sender);
    }

    function getGreeting() external view returns (string memory) {
        return greeting;
    }
}
