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
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract NameRegistry {
    mapping(address => string) public names;

    event NameSet(address indexed user, string name);

    function setName(string calldata name) external {
        names[msg.sender] = name;
        emit NameSet(msg.sender, name);
    }

    function getName(address user) external view returns (string memory) {
        return names[user];
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract Whitelist {
    address public owner;
    mapping(address => bool) public isWhitelisted;

    event Added(address indexed account);
    event Removed(address indexed account);

    constructor() {
        owner = msg.sender;
    }

    modifier onlyOwner() {
        require(msg.sender == owner, "Not owner");
        _;
    }

    function add(address account) external onlyOwner {
        isWhitelisted[account] = true;
        emit Added(account);
    }

    function remove(address account) external onlyOwner {
        isWhitelisted[account] = false;
        emit Removed(account);
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract Faucet {
    address public owner;
    uint256 public amountAllowed = 0.01 ether;
    mapping(address => uint256) public lastRequest;

    event Requested(address indexed user, uint256 amount);

    constructor() {
        owner = msg.sender;
    }

    function request() external {
        require(block.timestamp >= lastRequest[msg.sender] + 1 days, "Wait 24h");
        require(address(this).balance >= amountAllowed, "Faucet empty");

        lastRequest[msg.sender] = block.timestamp;
        (bool success, ) = msg.sender.call{value: amountAllowed}("");
        require(success, "Transfer failed");
        emit Requested(msg.sender, amountAllowed);
    }

    function donate() external payable {}

    function withdraw() external {
        require(msg.sender == owner, "Not owner");
        (bool success, ) = owner.call{value: address(this).balance}("");
        require(success, "Withdraw failed");
    }

    receive() external payable {}
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract BalanceTracker {
    mapping(address => uint256) public deposits;
    uint256 public totalDeposits;

    event Deposited(address indexed user, uint256 amount);

    function deposit() external payable {
        require(msg.value > 0, "Must send ETH");
        deposits[msg.sender] += msg.value;
        totalDeposits += msg.value;
        emit Deposited(msg.sender, msg.value);
    }

    function getMyDeposit() external view returns (uint256) {
        return deposits[msg.sender];
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract Pausable {
    address public owner;
    bool public paused;

    event Paused(address indexed by);
    event Unpaused(address indexed by);

    constructor() {
        owner = msg.sender;
    }

    modifier onlyOwner() {
        require(msg.sender == owner, "Not owner");
        _;
    }

    modifier whenNotPaused() {
        require(!paused, "Contract is paused");
        _;
    }

    function pause() external onlyOwner {
        paused = true;
        emit Paused(msg.sender);
    }

    function unpause() external onlyOwner {
        paused = false;
        emit Unpaused(msg.sender);
    }

    function doSomething() external whenNotPaused {
        // función de ejemplo que solo funciona si no está pausado
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract Donation {
    address public beneficiary;
    uint256 public totalDonated;
    mapping(address => uint256) public donations;

    event Donated(address indexed donor, uint256 amount);

    constructor(address _beneficiary) {
        beneficiary = _beneficiary;
    }

    function donate() external payable {
        require(msg.value > 0, "Must send ETH");
        donations[msg.sender] += msg.value;
        totalDonated += msg.value;
        emit Donated(msg.sender, msg.value);
    }

    function withdraw() external {
        require(msg.sender == beneficiary, "Not beneficiary");
        uint256 amount = address(this).balance;
        (bool success, ) = beneficiary.call{value: amount}("");
        require(success, "Withdraw failed");
    }
}
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract CheckIn {
    mapping(address => uint256) public lastCheckIn;
    mapping(address => uint256) public checkInCount;

    event CheckedIn(address indexed user, uint256 timestamp, uint256 totalCheckIns);

    function checkIn() external {
        lastCheckIn[msg.sender] = block.timestamp;
        checkInCount[msg.sender] += 1;
        emit CheckedIn(msg.sender, block.timestamp, checkInCount[msg.sender]);
    }

    function getCheckInInfo(address user) external view returns (uint256 last, uint256 total) {
        return (lastCheckIn[user], checkInCount[user]);
    }
}

// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract Deadline {
    uint256 public deadline;
    address public owner;

    event DeadlineSet(uint256 newDeadline);

    constructor(uint256 durationInSeconds) {
        owner = msg.sender;
        deadline = block.timestamp + durationInSeconds;
    }

    function setDeadline(uint256 durationInSeconds) external {
        require(msg.sender == owner, "Not owner");
        deadline = block.timestamp + durationInSeconds;
        emit DeadlineSet(deadline);
    }

    function isExpired() external view returns (bool) {
        return block.timestamp >= deadline;
    }

    function timeLeft() external view returns (uint256) {
        if (block.timestamp >= deadline) return 0;
        return deadline - block.timestamp;
    }
}
