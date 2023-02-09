# Building a Naming System on the Celo Blockchain Using React and React-Celo Library

## Introduction 
The recent exponential growth of the Web3 and blockchain ecosystem has lead to the increase in its user and developer base. A lot of users already accustomed to the traditional Web2 ecosystem and trying out the Web3 ecosystem for the first time usually get’s overwhelmed by the complexities involved in the space. While it is a fact that these complexities are put in place to ensure the space is secured, new users in the space mostly finds it hard to adapt. 

One of the pain point for new users is wallet management. Each wallet has a set of addresses, and the addresses are usually long random sequence of characters. The fact that they are long and random is what makes hard to deal with. Several solutions have tried to tackle this problem but the most prominent solution is the Naming Service.

The Naming System, similar to  Domain Name Service(DNS)  is a system that maps wallet addresses to a unique name of your choice. So if you want to send funds to a user who is registered on the Naming Service, you don’t need to go through the hassle of typing the user’s long and cumbersome wallet address. You can simply enter the user’s unique name on the Naming Service and it will resolve to the user’s wallet address. Example of names on the Naming System are `wallet.eth`, `me.crypto` etc.

# Table of Contents
- [Building a Naming System on the Celo Blockchain using React and react-celo Library](#building-a-naming-system-on-the-celo-blockchain-using-react-and-react-celo-library)
  * [Introduction](#introduction)
  * [Learning Objective](#learning-objective)
  * [Tech Stack](#tech-stack)
  * [Prerequisites](#prerequisites)
  * [1. Smart Contract Development](#1-smart-contract-development)
- [2. Contract Deployment](#2-contract-deployment)
- [3. Frontend Development with React](#3-frontend-development-with-react)
  * [3.1 Creating React Boilerplate](#31-creating-react-boilerplate)
  * [3.2 Linking Frontend with Celo](#32-linking-frontend-with-celo)
  * [3.3 Building the frontend](#33-building-the-frontend)
  * [3.4 Testing the Dapp](#34-testing-the-dapp)
- [4. Conclusion](#4-conclusion)
- [5. Author](#5-author)

## Learning Objective
In this tutorial, we will be learning how to build a Naming System on the Celo blockchain. What you will learn in this tutorial are as follows:
- Learn how to write the smart contract for the Naming System using Solidity.
- Learn how to deploy smart contracts on the Celo blockchain.
- Learn how to build a React frontend for your app.
- Learn how to integrate the `react-celo` library in your React frontend.

## Tech Stack
We will use the following tools and languages in this tutorial
1. [Remix IDE](remix.ethereum.org)
2. [VSCode](https://code.visualstudio.com/)
3. A web browser
4. [Solidity](https://docs.soliditylang.org/)
5. [React](https://reactjs.org/)
6. [`react-celo`](https://github.com/celo-org/react-celo)
7. [Chakra-UI](https://chakra-ui.com/)
8. [Celo Extension Wallet](https://chrome.google.com/webstore/detail/celoextensionwallet/kkilomkmpmkbdnfelcpgckmpcaemjcdh?hl=en)

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
1. Create a new file named `ENS.sol` in the `contracts` folder or any folder of your choice.
2. On the same you created `ENS.sol`, create another sub-folder and name it `libraries`.
3. Inside the `library` sub-folder, Create a file and name it `StringUtils.sol`. The purpose of this file will be explained later in the tutorial.

At this point you have two files open in your Remix editor - `ENS.sol` and `StringUtils.sol`. The next step is to add the necessary smart contracts to these files.

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

The code snippet above is a library we created, which will be used to check the length of the names in our Naming System so they can be validated. You don’t need to know about the details of the code at this point as it contains more high-level concepts and not our focus for this tutorial.


* `ENS.sol`

Open the `ENS.sol` file and and add the following lines of code:

```solidity
// SPDX-License-Identifier: UNLICENSE
pragma solidity ^0.8.10;

import {StringUtils} from "./libraries/StringUtils.sol";

contract ENS {}
``` 
* The first line of the code snippet above defines the license of our smart contract. For this contract, we will use the *UNLICENSE* keyword. Using this keyword for the licence field of our contract means the contract is open to everyone and it is not guided by any licence. Check out [Solidity docs](https://docs.soliditylang.org/en/v0.8.17/layout-of-source-files.html#spdx-license-identifier) and [SPDX website](https://spdx.dev) to learn more about licensing.
* The second line defines the version of Solidity compiler we will use for our contract. In the case of our contract, we will be use any compiler version starting from 0.8.10 to 0.8.x (where x is greater than 10).
* The third line of code is an import statement that imports the contract in `StringUtils.sol` file we created earlier.
* The next line is the smart contract definition. Solidity is an object-oriented and a curly bracket language just like [Java](https://www.java.com/) and [C++](https://en.wikipedia.org/wiki/C%2B%2B), that’s the reason why their constructs look similar. From the code, we are creating a contract with the name `ENS`. This name can be anything you wish to use, but for this tutorial, that is what we will use.

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
* We also created an array named `names` which will be used to store all the names we created in the Naming System. The reason for this variable is because mappings in solidity are not iterable. This implies that we cannot access all the keys that has been assigned a value from the mapping. We can only access them one at a time.
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
* `validName(string calldata name)` checks and validates the length of a name. It returns a boolean indicating whether a name is valid or not.

Let’s add more functions to the contract: 

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
    * The next lines of code concatenates both the top-level name and the custom name together and store the result in the `domain` variable. Maps the user’s wallet address to the name and add the name to the array of existing names. Adding all created names to an array will let the frontend be able to check all assigned names.
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
* Compile the code from the Remix interface. Please ensure you are using the latest compiler version in order for the code to compile properly.

<img width="383" alt="image" src="https://user-images.githubusercontent.com/64266194/217687335-91972d77-c093-40d1-8b61-b8d54d1c27ba.png">

* Ensure you have the Celo Extension Wallet installed. If you don’t have it, you can search for it from the extensions section and add it to Remix.

<img width="376" alt="image" src="https://user-images.githubusercontent.com/64266194/217686838-1c3294b2-0883-4e9a-88d3-198e0ef71f38.png">

* Click on the “Deploy” button to deploy the contract on the Celo blockchain.

<img width="376" alt="image" src="https://user-images.githubusercontent.com/64266194/217686961-ce0c960e-77ad-4918-a6c7-759d54fa9e09.png">

After deployment, an interface to interact with the contract will appear in the left section of Remix IDE. You can start interacting and testing the contract functionalities from that section.

<img width="376" alt="image" src="https://user-images.githubusercontent.com/64266194/217687071-bbe270b6-0402-4c7e-bef8-e542886a9937.png">


# 3. Frontend Development with React
In this section of the tutorial, we will be building a React frontend for our Naming System and connecting to the Celo blockchain using the **react-celo** library. 

## 3.1 Creating React Boilerplate
The following steps will guide you to creating a React boilerplate code:
1. Open your terminal and navigate to any directory of your choice.
2. Ensure you have Node installed by running the command in your terminal:
```bash
node -v
```
The command will print out the version of node installed in your computer (If Node has been installed already).
3. Run the command:
```bash
npx create-react-app umbrella-domains
```
to create a React boilerplate inside the `umbrella-domains` directory that will be created. You can change the name of the directory to any name of your choice.
4. Locate `umbrella-domains` folder from your file manager and open it with a code editor. 
5. Delete all the files and folders we won't be using for this project and you will end up with a similar folder structure to this:

<img width="257" alt="image" src="https://user-images.githubusercontent.com/64266194/217689979-5cd2fa29-d1f2-4782-b6b8-7de707244170.png">

6. Open the `package.json` file and replace the code inside with this:

```json
{
  "name": "umbrella-domains",
  "version": "0.1.0",
  "private": true,
  "dependencies": {
    "@celo/contractkit": "^3.2.0",
    "@celo/react-celo": "^4.3.0",
    "@chakra-ui/react": "^1.0.0",
    "@emotion/react": "^11.0.0",
    "@emotion/styled": "^11.0.0",
    "@openzeppelin/contracts": "^4.8.1",
    "dotenv": "^16.0.3",
    "framer-motion": "^4.0.0",
    "react": "^17.0.2",
    "react-dom": "^17.0.2",
    "react-icons": "^3.11.0",
    "react-scripts": "^4.0.3",
    "web-vitals": "^2.1.4"
  },
  "scripts": {
    "start": "react-scripts start",
    "predeploy": "npm run build",
    "deploy": "gh-pages -d build",
    "build": "react-scripts build",
    "test": "react-scripts test",
    "eject": "react-scripts eject"
  },
  "eslintConfig": {
    "extends": "react-app"
  },
  "browserslist": {
    "production": [
      ">0.2%",
      "not dead",
      "not op_mini all"
    ],
    "development": [
      "last 1 chrome version",
      "last 1 firefox version",
      "last 1 safari version"
    ]
  },
  "devDependencies": {
    "gh-pages": "^4.0.0"
  }
}
```
We added Chakra-UI and React-Celo packages which we use later in building the UI and connecting it to the blockchain respectively.

7. Run the command
```bash
npm i
```
to add download the newly added packages to the `node_modules` folder.

8. Inside the `src` directory, create a folder named `contracts`.
9. Inside the `contracts` folder, create two files named `ENS.sol` and `ens-abi.json`, and a folder named `libraries`.
10. Inside the `libraries` folder, create a file named `StringUtils.sol`.
11. Now return to Remix IDE, copy the code from the files `ENS.sol` and `StringUtils.sol` and paste them inside inside their respective files in your code editor. Also copy the contract ABI from Remix IDE and paste it in `ens-abi.json`. To copy the ABI from Remix, click on the copy icon and it will be copied to your clipboard.

<img width="396" alt="image" src="https://user-images.githubusercontent.com/64266194/217741293-ff9a3df8-7397-4e2b-beef-9f9ffc750d87.png">


## 3.2 Linking Frontend with Celo

Open `index.js` file and add the following code:

```javascript
import React, { StrictMode } from 'react';
import ReactDOM from 'react-dom';
import App from './App';
import "./index.css"
import { CeloProvider } from '@celo/react-celo';
import "@celo/react-celo/lib/styles.css";

ReactDOM.render(
  <CeloProvider
    dapp={{
      name:"Umbrella domains",
      description:"An ENS on the Celo blockchain",
      url: "https://example.com"
    }}
    connectionModal={{
      title: <span>Connect your wallet</span>
    }}
    >
    <StrictMode>
      <App />
    </StrictMode>
  </CeloProvider>,
  document.getElementById("root")
);
```
The code is straight forward. We imported `CeloProvider` from `@celo/react-celo` package we installed earlier. `CeloProvider` is a wallet that we can use to connect and make transactions to the Celo blockchain. We imported the files necessary to style the wallet. We then added some details about our dapp and a message to show before connecting the wallet.

## 3.3 Building the frontend

Open the `App.js` file and add the following code:

```javascript
import React, { useState, useEffect } from 'react'
import {
  ChakraProvider,
  Button,
  Flex,
  Input,
  Text
} from "@chakra-ui/react";
import { useCelo } from '@celo/react-celo';
import ABI from "./contracts/ens-abi.json"
import BigNumber from 'bignumber.js';

const contractAddress = "0x00"

const App = () => {
  const [newDomain, setNewDomain] = useState()
  const [contract, setContract] = useState()
  const [searchVal, setSearchVal] = useState()
  const [searchRes, setSearchRes] = useState()
  const [domains, setDomains] = useState()
  const [tx, setTx] = useState()

  const { connect, address, performActions, getConnectedKit } = useCelo()

  const connectWallet = async () => {
    await connect()
    window.location.reload()
  }

  const connection = async () => {
    const kit = await getConnectedKit()
    const contract = new kit.connection.web3.eth.Contract(ABI, contractAddress)
    setContract(contract)
  }

  const createDomain = async () => {
    const [name, tld] = newDomain.split(".")
    const cost = name.length === 2 ? '.1' : name.length === 3 ? '0.05' : name.length === 4 ? '0.03' : '0.01'
    await performActions(async (kit) => {
      const { defaultAccount } = kit.connection;
      try {
        const _cost = new BigNumber(cost).shiftedBy(18).toString()
        const tx = await contract.methods.setName(name, tld).send({ from: defaultAccount, value: _cost })
        setTx(tx)
        alert("Create domain successfully")
      } catch (e) {
        console.log(e)
      }
    })
  }

  const searchDomain = async () => {
    const tld = searchVal[1]
    const name = searchVal[0]
    try {
      const address = await contract.methods.getAddress(tld, name).call()
      setSearchRes(address)
    } catch (e) {
      console.log(e)
    }
  }

  const allCreatedDomains = async () => {
    try {
      const domains = await contract.methods.getAllNames().call()
      setDomains(domains)
    } catch (e) {
      console.log(e)
    }
  }


  useEffect(() => {
    connection()
    allCreatedDomains()
  }, [])

  useEffect(() => {
    allCreatedDomains();
  }, [tx, contract])

...
```
A breakdown of the code snippet above is as follows:

* We first included all the necessary imports for the file.
    * We imported the React library
    * We import the components we will need from Chakra-UI library
    * We imported `use-celo` from React-Celo package
    * We also import the smart contract ABI (Application Binary Inferface) from the file we created and added it in earlier in the tutorial
    * Lastly, we imported the BigNumber class to handle large numbers JavaScript can't handle

* We also created a variable called `contractAddress`. This variable stores the address our contract was deployed to. You can get the contract directly from Remix after the contract deployment is completed.

<img width="315" alt="image" src="https://user-images.githubusercontent.com/64266194/217706176-c399e2ff-faf5-41ab-9de1-d91eb2f093dd.png">

* Next, we created a React component called `App` which will contains most of the code for our frontend. 
    * Inside the `App` component, we first created all the states that needs to be tracked. This was made possible using a [React hook](https://reactjs.org/docs/hooks-overview.html)  called `useState()`. React comes built-in with a lot of hooks among which are `useEffect()`, `useReducer()`, and `useRef()`. `useEffect()` provides us with a function and a variable. The function provided is used to update the state of the variable.
    * We also import some functions and objects from `useCelo` which allows us to utilize the functionalities Celo blockchain provides.
* The remaining parts of the code snippet contains all the functions we will be using in the dapp. Below is a brief explanation of what the functions do:
    * `connectWallet()` - This function utilizes the `connect()` function provided by `useCelo()` to connect the Celo Wallet to our dapp
    * `connection()` - Creates a contract object using the ABI and contract address of our smart contract. It then stores the value into the `contract`          variable we created earlier.
    * `createDomain()` - This function creates a Name from what the user entered and adds it to the blockchain. It first splits the name from the top-level domain, it then computes the const of adding the name to our contract. Finally, it calls the `setName()` method of our contract passing in the necessary value and then send the transaction along with the cost of creating the Name.
    * `searchDomain()` - This function checks and returns the address of a corresponding Name from the smart contract.
    * `allCreatedDomains()` - This function returns all names ever created and stored in the contract and save it to the `domains` variable.
    * Lastly, we have the `useEffect()` hooks that executes some function when some certain conditions are met i.e when the values inside the                   `useEffect()` array changes.
 
Now, let's add the last part of our `App` component which will render whatever is displayed in the UI.

```javascript
  return (
    <ChakraProvider>
      {address ?
        <Flex direction={"column"} align="center" w="100vw" minH="100vh" gap={"20px"}>
          <Text>Connected to {address}</Text>
          <Flex direction={"column"} w="500px" bgColor={"gray.200"} mt="50px" p={"30px"}>
            <Text>Search for domain names</Text>
            <Flex mb="10px" mt={"15px"} gap="10px">
              <Input onChange={e => setSearchVal(e.target.value.split("."))} outline={"1px solid blue"} fontWeight="700"></Input>
              <Button onClick={searchDomain}>Search</Button>
            </Flex>
            <Input outline={"1px solid blue"} disabled fontWeight={"700"} value={searchRes}></Input>
          </Flex>
          <Flex direction={"column"} w="500px" bgColor={"gray.300"} p="25px" borderRadius="3px">
            <Text>Create domain name</Text>
            <Flex mt={"10px"} gap="10px">
              <Input value={newDomain} onChange={e => setNewDomain(e.target.value)} fontFamily="monospace"></Input>
              <Button onClick={createDomain}>Create</Button>
            </Flex>
          </Flex>
          <Flex mt="50px" direction='column'>
            <Text fontWeight={"bold"} textDecor="underline" fontSize={"2xl"}>Domains created so far</Text>
            <Flex direction={"column"}>
              {domains?.map(domain =>
                <Text fontFamily={"monospace"} fontSize="15px">{domain}</Text>
              )}
            </Flex>
          </Flex>
        </Flex> :
        <Flex w={"100vw"} h={"100vh"} align="center" justify={"center"}>Please{" "}<Button onClick={connectWallet}>Connect</Button> "Celo Extension Wallet" to use this dapp</Flex>
      }
    </ChakraProvider>
  )
```
This part of the `App` component is pretty straight forward. We wrapped the whole section inside `ChakraProvider` so that we can other Chakra-UI components our app. We first confirm if thehe  address is set, and then proceed to display the UI with all it's component. And if the address is not set, we display a UI that allows the users to connect thier wallet to our dapp by utilizing the `connect()` function provided by `useCelo()`.

The other part code builds the page layout and added some styling using Chakra-UI.

The last part of the file exports the `App()` component so it can be assessible from outside the file.

## 3.4 Testing the Dapp
By now, our frontend code for the Naming System app is complete and can now be tested.
Navigate to the terminal in the same directory where you created the React app (`umbrella-domains` in our case) and run the command to start the app:
```bash
npm start
```
This command will run the server on a port which can be accessed from your browser.

<img width="1680" alt="image" src="https://user-images.githubusercontent.com/64266194/217715732-19617626-f60c-473c-bdf7-cfd8a083bcc9.png">

From your browser you can now:
    * Search for address of existing names
    * Create new names
    * View all names created and saved in the contract.
    
 # 4. Conclusion
 This tutorial laid a foundation of writing smart contracts and deploying on the Celo blockchain. We also solved a real life problem using the blockchain, which is what the ecosystem needs at this point in time in order to get more users in it. With the knowledge gained in this tutorial, you can use it as a starting point to build your own amazing projects on the Celo blockchain and ship it out for users to interract with it. Check the [Celo documentation](https://docs.celo.org) to learn more about the amazing technology and how you can build amazing projects with it.

The code for this tutorial can be found in this [repository](https://github.com/princeibs/celo-101-tut-code)

# 5. Author
Ibrahim Suleiman is a software developer who loves to share his knowledge and experience of software development through technical writing and open source. You can reach out him on [Twitter](https://twitter.com/prince_ibs) if you have any more questions about this tutorial.

