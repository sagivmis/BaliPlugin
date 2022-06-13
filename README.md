# BaliGames Blockchain Plugin
### It's time to plugin your crypto wallet into your game

**BaliPlugin**, an easy plug and play asset for game developers to interact with the blockchain and smart contracts in-game.

### Functionality
- Expose your blockchain account
- Latest block number
- User balance
- Wallet address
- Transactions history
- Send transactions
- Buy/Sell  in-game / real tokens
- Use tokens in-game / on the platform
- Interact with smart contracts, and 
the blockchain



## Unity Plugin
### API:
----
**Latest block number**
```csharp                
(IEnumerator) GetBlockNumber()
```
**`Usage`**
```csharp
StartCoroutine(GetBlockNumber());
```
----

**Wallet address** - imported from web3.jslib
```csharp                
[DllImport("__Internal")] private static extern string WalletAddress()
```

**`Usage`**
```csharp
string walletAddress = WalletAddress();
```
----
**User balance**
```csharp                
(IEnumerator) GetAccountBalance(string address, System.Action<decimal> callback)
```
`Example callback:`
```csharp
public void SetEthBalance(decimal balance)
{
    ethBalance = (balance).ToString();
    uiController.SetEthBalance();
}
```
**`Usage`**
```csharp
StartCoroutine(GetAccountBalance(walletAddress, SetEthBalance));
```
----
**Http Get Request**
```csharp                
(IEnumerator) HttpGetRequest(string uri, string method)
```
`Example callback:`
```csharp
public void SetEthBalance(decimal balance)
{
    ethBalance = (balance).ToString();
    uiController.SetEthBalance();
}
```
**`Usage`**
```csharp
StartCoroutine(HttpGetRequest(${SERVER_URL}/api/mintBaliTokens/{playerAddress}/{amountInEth}
/{playerPrivateKey}", "mint"));
```




## API - microservice

An external service running on node.js server
Tech-stack: Node.js, fastify, ethers

**Endpoints**

*endpoints assuming the server isn't deployed and running on localhost on port 8080*
  
***Check server status.***
```http
http://localhost:8080/api/
```
***response***
```js
{ hello:  "from API"  }
```
-------
***Get ETH  balance of {address}***
```http
http://localhost:8080/api/getEthBalance/:address
ex: 
	0x4A091a403C140DF363b328e47F2f360F9977b4fc has 1.44 E
```
***response***
```js
{ ethAddress: address, balance: balance }
```
--------
***Get Token balance of {address}***
```http
http://localhost:8080/api/getBaliBalance/:address
ex: 
	0x4A091a403C140DF363b328e47F2f360F9977b4fc has 1436000000 $BaliGames
```
```http
http://localhost:8080/api/getCustomTokenBalance/:address
ex: 
	0x4A091a403C140DF363b328e47F2f360F9977b4fc has 1436000000 $Custom
```
***response***
```js
{ balance: baliBalance || customTokenBalance }
```
--------

***Mint {amountInEth} / tokenPrice(from cfg.json) of $Token to {playerAddress}.***
```http
http://localhost:8080/api/mintBaliTokens/:playerAddress/:amountInEth/:playerPrivateKey/
ex: 
	0.1 E / 0.001E(tokenPrice) = 100 $BaliGames tokens minted to 0x4A091a403C140DF363b328e47F2f360F9977b4fc
```

```http
http://localhost:8080/api/mintCustomTokens/:playerAddress/:amountInEth/:playerPrivateKey/
ex: 
	0.1 E / 0.00005E(customTokenPrice) = 2000 $Custom tokens minted to 0x4A091a403C140DF363b328e47F2f360F9977b4fc
```
***response***
```js
{ txHash: hash }
```
----
***Burn/use {amountToBurn} of $Token from {playerAddress}.***
```http
http://localhost:8080/api/burnBaliTokens/:playerAddress/:amountToBurn/:playerPrivateKey/
ex: 
	Burn 1000000 $BaliGames from 0x4A091a403C140DF363b328e47F2f360F9977b4fc
```


```http
http://localhost:8080/api/burnCustomTokens/:playerAddress/:amountToBurn/:playerPrivateKey/

Burn/use {amountToBurn} of $Custom token from {playerAddress}.
ex: 
	Burn 999999900 $Custom from 0x4A091a403C140DF363b328e47F2f360F9977b4fc
```

***response***
```js
{

	"type": 2,

	"chainId": 4,

	"nonce": 77,

	"maxPriorityFeePerGas": {

		"type": "BigNumber",

		"hex": "0x59682f00"

	},

	"maxFeePerGas": {

		"type": "BigNumber",

		"hex": "0x59682f12"

	},

	"gasPrice": null,

	"gasLimit": {

		"type": "BigNumber",

		"hex": "0x01c9c380"

	},

	"to": "0xBA82a6d506093bb699BEA12ACC85DC604CDF82f3",

	"value": {

		"type": "BigNumber",

		"hex": "0x00"

	},

	"data": "0x79cc67900000000000000000000000004a091a403c140df363b328e47f2f360f9977b4fc00000000000000000000000000000000000000000000000000000000000015b3",

	"accessList": [],

	"hash": "0x91e52acb582cf9eeb5d2491f0e1be693219cb5cddd4933fcac0ea32a954c705e",

	"v": 1,

	"r": "0xc9a1b9bebccd05fe2788d49a06785eac2ffa01075ae71dab435d6522cd5ceec5",

	"s": "0x2b7ccae939e0f1481410ea859b16aacd8e95f9e66e22822de7d67f6dfbaf6dbd",

	"from": "0x4A091a403C140DF363b328e47F2f360F9977b4fc",

	"confirmations": 0

}
```


## Config file
A config file that works with both the Unity plugin aswell as the server microservice (same file)
Required information:
```json
	"gameOwnerAddress": Developer's ETH wallet address
	"ownerPrivateKey": Developer's ETH wallet private key
	"apiKey": ETH node provider's API key (Reccommended provider: Alchemy)
	"apiUrl": Complete API url.
```
Optional information:
```json
	"baliTokenAddress": $BaliGames token address, not needed IF using custom token.
	"customTokenAddress": $Custom token address, not needed IF using $BaliGames base token.
	"baliTokenPrice":  $BaliGames token price, not needed IF using custom token.
	"tokenPrice": $Custom token price, not needed IF using $BaliGames base token.
```
