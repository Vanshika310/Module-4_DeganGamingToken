# Degan Gaming Token - ERC20 Smart Contract

The README file describes the smart contract used for the Avalanche network-deployed Degen Gaming Token (DGN). Within the Degen Gaming platform, the smart contract allows token creation, exchange, restoration, and destruction. It gives in-depth details on the implementation, capabilities, and user interactions of the contract.

## Degan.sol

    // SPDX-License-Identifier: MIT
    pragma solidity ^0.8.0;
    
    contract DegenGamingToken {
        string public name = "Degan";
        string public symbol = "DGN";
        uint8 public decimals = 18;
        uint256 public totalSupply;
        address public owner;
    
        struct Lock {
            uint256 amount;
            uint256 unlockTime;
        }
        
        mapping(address => uint256) public balanceOf;
        mapping(address => mapping(address => uint256)) public allowance;
        mapping(address => Lock) public locks;
    
        event Transfer(address indexed from, address indexed to, uint256 value);
        event Approval(address indexed owner, address indexed spender, uint256 value);
        event Mint(address indexed to, uint256 value);
        event Burn(address indexed from, uint256 value);
        event RedeemPrize(address indexed user, string prize, uint256 cost);
        event LockTokens(address indexed from, uint256 amount, uint256 unlockTime);
        event UnlockTokens(address indexed from, uint256 amount);
    
        modifier onlyOwner() {
            require(msg.sender == owner, "Only the owner can perform this action");
            _;
        }
    
         constructor(uint256 initialSupply) {
            totalSupply = initialSupply * 10**uint256(decimals);
            balanceOf[msg.sender] = totalSupply;
            owner = msg.sender;
        }
    
        function transfer(address to, uint256 value) external returns (bool) {
            require(to != address(0), "Invalid recipient address");
            require(value <= balanceOf[msg.sender], "Insufficient balance");
            
            balanceOf[msg.sender] -= value;
            balanceOf[to] += value;
            
            emit Transfer(msg.sender, to, value);
            return true;
        }
        
        function approve(address spender, uint256 value) external returns (bool) {
            allowance[msg.sender][spender] = value;
            
            emit Approval(msg.sender, spender, value);
            return true;
        }
    
        function mint(address to, uint256 value) external onlyOwner {
            require(to != address(0), "Invalid recipient address");
            
            balanceOf[to] += value;
            totalSupply += value;
            
            emit Mint(to, value);
        }
    
        function burn(uint256 value) external {
            require(value <= balanceOf[msg.sender], "Insufficient balance");
            
            balanceOf[msg.sender] -= value;
            totalSupply -= value;
            
            emit Burn(msg.sender, value);
        }

        function redeemPrize(string memory prize, uint256 cost) external returns (bool) {
            require(cost <= balanceOf[msg.sender], "Insufficient balance for redemption");
            require(cost > 0, "Cost must be greater than zero");

            balanceOf[msg.sender] -= cost;
            emit RedeemPrize(msg.sender, prize, cost);

            return true;
        }
    
        function lockTokens(uint256 amount, uint256 lockDuration) external {
            require(amount <= balanceOf[msg.sender], "Insufficient balance");
            require(amount > 0, "Amount must be greater than zero");
            
            uint256 unlockTime = block.timestamp + lockDuration;
            
            balanceOf[msg.sender] -= amount;
            locks[msg.sender] = Lock(amount, unlockTime);
            
            emit LockTokens(msg.sender, amount, unlockTime);
    
        }
    
        function unlockTokens() external {
            Lock memory playerLock = locks[msg.sender];
            require(playerLock.amount > 0, "No locked tokens found");
            require(block.timestamp >= playerLock.unlockTime, "Tokens are still locked");
    
            balanceOf[msg.sender] += playerLock.amount;
            emit UnlockTokens(msg.sender, playerLock.amount);
    
            delete locks[msg.sender];
        }
    
    }

It gives users the chance to take part in the gaming community, receive prizes, trade tokens, exchange them for in-game goods, and manage the tokens they have.

Name of the Contract: DegenGamingToken (DGN)
Symbol: DGN
Decimals: 18
Total Supply: The quantity of tokens in use is represented by the initial supply, which is determined upon deployment.
Owner: The contract's owner is the address that deployed it, and this address has specific rights.

_**Minting New Tokens:**_ The exclusive authority to produce new tokens rests with the platform owner. The mint feature enables the owner to create extra tokens and give them away as prizes to players. _**Transferring Tokens:**_ By using the transfer feature, players may send their DGN tokens to different addresses. Peer-to-peer token transfers are made possible via this capability, making it easier for members of the Degen Gaming community to trade and give gifts. _**Exchange of Tokens:**_ Players may trade their DGN tokens for a variety of in-game products or advantages on the Degen Gaming platform. Although the redemption procedure takes place off-chain, users can still burn redeemed tokens after they have used._** Verifying the Token Balance:**_ The balanceOf function allows users to view their current DGN token balance at any moment. Users can check how many tokens they presently possess using this method. _**Tokens that Burn:**_ By invoking the burn feature, any token holder can withdraw all of their tokens from circulation forever. When users no longer require their tokens and want to lower their token holdings, this option is quite helpful. **_Redeem Prize Function:_** The redeemPrize function is a smart contract function written in Solidity, designed to be executed on the Ethereum blockchain. It allows token holders to redeem a prize by spending a certain amount of their tokens. The function ensures that the caller has a sufficient balance to cover the redemption cost and that the redemption cost is a valid positive value. _**Tokens for locking and unlocking:**_ With the help of the lockTokens function, token owners can lock a set number of their tokens for a specified period. These tokens can only be accessed again when the lock period has passed. Users can utilize the unlockTokens method to get their tokens back after the lock period.


## Lincense

This project is licensed under the MIT License.




