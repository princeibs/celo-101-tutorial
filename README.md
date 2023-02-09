# Building a Naming System on the Celo Blockchain using React and react-celo Library

## Introduction 
The recent exponential growth of the Web3 and blockchain ecosystem has lead to the increase in its user and developer base. A lot of users already accustomed to the traditional Web2 ecosystem and trying out the Web3 ecosystem for the first time usually get’s overwhelmed by the complexities involved in the space. While it is a fact that these complexities are put in place to ensure the space is secured, new users in the space mostly finds it hard to adapt. One of the pain point for new users is wallet management. Each wallet has a set of addresses, and the addresses are usually long random sequence of characters. The fact that they are long and random is what makes hard to deal with. Several solutions have tried to tackle this problem but the most prominent solution is the Naming Service.

The Naming System, similar to DNS (Domain Name Service) is a system that maps wallet addresses to a unique name of your choice. So if you want to send funds to a user who is registered on the Naming Service, you don’t need to go through the hassle of typing the user’s long and cumbersome wallet address. You can simply enter the user’s unique name on the Naming Service and it will resolve to the user’s wallet address. Example of names on the Naming System are `wallet.eth`, `me.crypto` etc.

## Learning Objective
In this tutorial we will be learning how to build a Naming System on the Celo blockchain. What you will learn in this tutorial are as follows:
- Learn how to write the smart contract for the Naming System using Solidity
- Learn how to deploy smart contracts on the Celo blockchain
- Learn how to build a React frontend for your app
- Learn how to integrate the `react-celo` library in your React frontend

## Tech Stack
We will use the following tools and languages in this tutorial
1. Remix IDE
2. VSCode
3. A web browser
4. Solidity
5. React
6. `react-celo` 
7. Chakra-UI

## Prerequisites
- Basics of programming with JavaScript and it’s frontend libraries like React, Vue, or Angular
- Basic knowledge of programming with Solidity
- Basic knowledge of using the command line
- Basic knowledge of Web3 and how wallets work

## 1. Smart Contract Development
In this section of this tutorial, we will be developing the smart contract for the Naming System.
The first step is to navigate to the Remix IDE [website](https://remix.ethereum.org) because we will be using Remix IDE to write and deploy our smart contract to the Celo blockchain.  When you open the Remix IDE website, it looks like this at first:

<img width="1392" alt="remix-capture" src="https://user-images.githubusercontent.com/64266194/217634292-1792e55b-89fc-4c82-94cb-2a3f81a7ecfd.png">

Follow the steps below to get Remix IDE setup for writing our smart contracts:
- Create a new file named `ENS.sol` in the `contracts` folder or any folder of your choice.
- On the same you created `ENS.sol`, create another sub-folder and name it `libraries`.
- Inside the `library` sub-folder, Create a file and name it `StringUtils.sol`. The purpose of this file will be explained later in the tutorial.

At this point you have two files open in your Remix editor - `ENS.sol` and `StringUtils.sol`. In the step is to add the necessary smart contracts to these files.

* `StringUtils.sol`
Open the `StringUtils.sol` file and paste the following code:

```solidity
// SPDX-License-Identifier: MIT
// Source:
// https://github.com/ensdomains/ens-contracts/blob/master/contracts/ethregistrar/StringUtils.sol
pragma solidity >=0.8.4;

library StringUtils {
    /**
     * @dev Returns the length of a given string
     *
     * @param s The string to measure the length of
     * @return The length of the input string
     */
    function strlen(string memory s) internal pure returns (uint) {
        uint len;
        uint i = 0;
        uint bytelength = bytes(s).length;
        for(len = 0; i < bytelength; len++) {
            bytes1 b = bytes(s)[i];
            if(b < 0x80) {
                i += 1;
            } else if (b < 0xE0) {
                i += 2;
            } else if (b < 0xF0) {
                i += 3;
            } else if (b < 0xF8) {
                i += 4;
            } else if (b < 0xFC) {
                i += 5;
            } else {
                i += 6;
            }
        }
        return len;
    }
}
```

The code snippet above is a library we created which will be used to check the length of the names in our Naming System so they can be validated. You don’t need to know about the details of the code at this point as it contains more high-level concepts and not our focus for this tutorial.


* `ENS.sol`

Open the `ENS.sol` file and and add the following lines of code:

```solidity
// SPDX-License-Identifier: UNLICENSE
pragma solidity ^0.8.10;

import {StringUtils} from "./libraries/StringUtils.sol";

contract ENS {}
``` 
* The first line of the code snippet above defines the license of our smart contract. For this contract, we will be using the *UNLICENSE* keyword. Using this keyword for the licence field of our contract means the contract is open to everyone and it is not guided by any licence. Check out [Solidity docs](https://docs.soliditylang.org/en/v0.8.17/layout-of-source-files.html#spdx-license-identifier) and [SPDX website](https://spdx.dev) to learn more about licensing.
* The second line defines the version of Solidity compiler we will be using for our contract. In the case of our contract, we will be using any compiler version starting from 0.8.10 to 0.8.x (where x is greater than 10).
* The third line of code is an import statement that imports the contract in `StringUtils.sol` file we created earlier.
* The next line is the smart contract definition. Solidity is an object-oriented and a curly bracket language just like Java and C++, that’s the reason why their constructs look similar. From the code, we are creating a contract with the name `ENS`. This name can be anything you wish to use, but for this tutorial, that is what we will be using.

Now let’s add some code inside the body of our smart contract.

```solidity
    address payable public immutable owner;

    // tld = Top Level Domain (e.g .crypto, .eth)
    // tld => name => address
    mapping(string => mapping(string => address)) private register;
    string[] private names;

    event RegisterName(string name, address indexed owner);
    event UpdateData(string name);

    error NameAlreadyExists();
    error Unauthorized();
    /// `sent` - amount sent by user
    /// `expected` - expected amount to send
    error InvalidFunds(uint expected, uint sent);
    error InvalidName(string name);

    modifier onlyOwner() {
        if (msg.sender != owner) revert Unauthorized();
        _;
    }

    constructor () payable {
       owner = payable(msg.sender);
    }
```
* In the first line we created a variable named `owner`. It will store the address of the owner of the contract and give administrative privileges to the address only. We added the `immutable` modifier to indicate that the address can only be set one time - construction time.
* We also created a mapping named `register`. This mapping will map the unique names to their respective addresses. It is a very important aspect of our Naming System as this is where all the names are linked to their respective addresses.
* We also created an array named `names` which will be used to store all the names we have been created in the Naming System. The reason for this variable is because mappings in solidity are not iterable. This implies that we cannot access all the keys that has been assigned a value from the mapping. We can only access them one at a time.
* In the next two lines, we created events named `RegisterName` and `UpdateData`  to be emitted on the event of some actions.
* The error statements defined in the next lines does the following:
    * `error NameAlreadyExists()` - is thrown when the name entered into the smart contract has already been claimed by another user.
    * `error Unauthorized()` - is thrown when an unauthorised user tried to access a function. 
    * `error InvalidName()` - is thrown when the user entered an invalid name.
    * `error InvalidFunds() - is thrown when the funds send to a function is invalid.
* The modifier `onlyOwner` is added to the any function to restrict that function access to the owner.
* The expression inside the constructor get’s the contract deployer and assign to the `owner` variable.

Let’s add the functionalities of our Naming System contract in the next step. Add the following code snippet below constructor function.

```solidity
   // Calculate the cost for creating domain names
    function cost(string calldata name) private pure returns (uint) {
        uint len = StringUtils.strlen(name);
        require(len > 0);
        if (len == 2) {
            return 10 * 10**16; // .1 ether
        } else if (len == 3) {
            return 5 * 10**16; // .05 ether
        } else if (len == 4) {
            return 3 * 10**16; // .03 ether
        } else {
            return 1 * 10**16; // .01 ether
        }
    }  

    // Check the validity of a name
    function validName(string calldata name) private pure returns (bool) {
        return StringUtils.strlen(name) >= 2 && StringUtils.strlen(name) <= 64;
    }
```
The code snippet above contains two functions: `cost(string calldata name)` and `validName(string calldata name)`
* `cost(string calldata name)` takes in the name to be used for the Naming System and determine the the cost of using the name based on it’s length. The function first validates the name before returning the amount in eth of using the name.
* `validName(string calldata name)`  checks and validates the length of a name. It returns a boolean indicating whether a name is valid or not.

Let’s add some more functions to the contract: 

```solidity
    // Add new name to the register
    function setName(string calldata _name, string calldata _tld) external payable {
        uint nameCost = cost(_name);   
        if (!validName(_name)) revert InvalidName(_name);
        if (msg.value < nameCost) revert InvalidFunds(nameCost, msg.value);
        if (register[_tld][_name] != address(0)) revert NameAlreadyExists();         

        string memory domain = string.concat(_name, ".", _tld);
        register[_tld][_name] = msg.sender;        
        names.push(domain);

        emit RegisterName(string.concat(_name, ".", _tld), msg.sender);        
    }

    // Get address corresponding to a domain name
    function getAddress(string calldata _tld, string calldata _name) public view returns (address) {
        address _address = register[_tld][_name];
        return _address;
    }
```
The above code snippet contains two functions: `setName(string calldata _name, string calldata _tld)` and `getAddress(string calldata _tld, string calldata _name)`.
* `setName(string calldata _name, string calldata _tld)`
    * This function starts by getting the cost of a new name and store it in the `nameCost` variable.
    * It then proceeds to validate the name entered, the amount sent, and the address to be assigned to the name.
    * The next lines of code concatenates both the top-level name and the custom name together and store in the `domain` variable. Maps the user’s wallet address to the name and add the name to the array of existing names. Adding all created names to an array will let the frontend be able to check all assigned names.
    * The last line of the function emitted the `RegisterName` event created earlier. Event’s like this can be used to notify the frontend about activities occurring in the contract.
* `getAddress(string calldata _tld, string calldata _name)` 
    * A getter function used to retrieve the wallet address associated with a name.

Let’s add the last set of functions in the smart contract:

```solidity
  // Get all names created so far
    function getAllNames() external view returns (string[] memory) {
        string[] memory _names = names;
        return _names;
    }

    // Withdraw funds stored in contract
    function withdraw() external payable onlyOwner {
        uint bal = address(this).balance;
        payable(msg.sender).transfer(bal);
    } 
```

* `getAllNames()` is used to retrieve all the names saved in the contract. It returns all the names from the `names[]` array we created earlier.
* `withdraw()` is a payable function used to withdraw all the funds sent to the contract. This function can only be called by the contract owner (i.e. `owner`)

And that’s all for our Naming Service contract. In the next section, we will be deploying the contract to the Celo blockchain using the Remix IDE.


# 2. Contract Deployment

In this section of the tutorial, we will deploy the contract we wrote earlier to the Celo blockchain right from Remix IDE interface. 
* Compile the code from the Remix interface.
* Ensure you have the Celo extension installed. If you don’t have it, you can search for it from the extensions section and add it to Remix.
* Click on the “Deploy” button to deploy the contract on the Celo blockchain.

After deployment, an interface to interact with the contract will appear in the left section of Remix IDE. You can start interacting and testing the contract functionalities from that section.


# 3. Frontend Development with React
