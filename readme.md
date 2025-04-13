# 🏦 CurvanceTimeVault

**CurvanceTimeVault** is a yield-generating smart vault that accepts ERC20 deposits and issues NFTs as proof-of-deposit. Users can later reclaim their yield and initial stake by burning the NFTs. The system supports bribes, fees, compounding via ERC4626 strategies, and upgradability through UUPS proxy pattern.

---

## ✨ Features

- 🔐 **Upgradeable** via OpenZeppelin UUPS pattern  
- 🎟️ **NFT-based Vault Entries** using a custom `TimeNft` contract  
- 🔁 **Automated Yield Compounding** using ERC4626 vaults  
- 💸 **Claimable Yield** post lock period with optional platform fee  
- 🪙 **Bribe Support** (multi-token)  
- 🛑 **Pausable NFT minting**  
- 📈 **Upgradeable & Modular Design**  

---

## 🛠 Contracts Overview

### `CurvanceTimeVault.sol`

Handles:
- User deposits in ERC20
- NFT minting on join
- ERC4626 yield generation & compounding
- Yield + bribe distribution on claim
- Upgradability and fee management

### `TimeNft.sol`

- ERC721 NFT with enumerable, pausable, burnable, royalty-support
- Mintable by owner or linked vault
- Supports OpenSea royalties via `IERC2981`

### `EnhancedERC4626` Interface

Extends `IERC4626` with:
- `redeem(uint256 assets, address receiver)`
- `mint(uint256 assets)`

Used by vaults to automate staking and yield generation.

---

## ⛓️ Workflow

1. **Initialize Vault**: Deploy and initialize with parameters.
2. **Join Vault**: Users deposit ERC20 tokens and receive NFTs.
3. **Compound**: Vault compounds yield periodically via the ERC4626 partner contract.
4. **Claim Period**: Users burn NFTs to claim yield + bribes (minus fees).
5. **Bribes**: Anyone can send bribes (in ERC20 tokens) to incentivize participation.
6. **Fees**: Owner can collect platform fees.

---

## 📦 Setup & Deployment

### Prerequisites

- Hardhat / Foundry
- OpenZeppelin Contracts ^5.0
- Solidity ^0.8.22

### Installation

```bash
npm install @openzeppelin/contracts-upgradeable @openzeppelin/contracts
```

### Initialization Parameters

```solidity
initialize(
  uint256 _nftPrice,
  uint256 _nftLimitPerAddress,
  address initialOwner,
  uint256 _nftLimit,
  uint256 _joiningPeriod,
  uint256 _claimingPeriod,
  address _PartnerContract,
  address _erc20Address,
  uint256 _prejoinPeriod
)
```

### Example Call

```solidity
vault.initialize(
  100e6, // NFT Price in USDC
  5,     // NFT mint limit per address
  msg.sender,
  1000,  // Total NFT cap
  block.timestamp + 1 days,
  block.timestamp + 10 days,
  address(partnerContract),
  address(usdc),
  block.timestamp
);
```

---

## 🧠 Vault States

```solidity
function getState() public view returns (uint256)
```

| State Code | Meaning            |
|------------|--------------------|
| 0          | Joining period     |
| 1          | Compounding period |
| 2          | Claiming period    |
| 3          | Prejoin            |

---

## 🧾 Key Functions

| Function                  | Description                                       |
|---------------------------|---------------------------------------------------|
| `joinVault()`             | Deposit ERC20 and mint NFTs                       |
| `claimBack()`             | Burn NFTs and claim principal + yield + bribes    |
| `automateCoumpounding()`  | Redeem + re-stake assets via ERC4626              |
| `bribe()`                 | Deposit bribes (in ERC20)                         |
| `changeTimePeriod()`      | Update time windows                               |
| `collectFee()`            | Collect platform fees                             |
| `pauseNft()`              | Pause minting in the NFT contract                 |

---

## 📜 Events

- `VaultJoined(address user, uint256 amount)`
- `Compounded(uint256 amount)`
- `FeesCollected(uint256 amount)`
- `FundsWithdrawn(address receiver)`
- `ExternalFundsDeposited(uint256 amount)`

---

## 🔐 Security

- Uses `ReentrancyGuardUpgradeable` for reentrancy protection  
- NFT minting restricted to vault and owner  
- Platform is pausable  
- Follows UUPS upgrade pattern  

---

## 🧪 To-Do / Ideas

- Add front-end for NFT preview + deposit  
- Add optional lock duration per user  
- Add governance/DAO control  
- Integrate with Chainlink Automation for auto-compounding  

---

## 👨‍💻 Author

**MeowFi Labs** | 2025  
🔗 Powered by Curvance, Fastlane, and OpenZeppelin

---

📝 Licensed under MIT