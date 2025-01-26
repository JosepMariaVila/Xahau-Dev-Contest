## Project Overview 📖

- **Project Name:** Xai Protocol
- **Folder Project Name Inside submissions Branch:** Josep Maria Vila Ridworld
- **Project Description:** Xai Protocol is an oracle based stablecoin hook on the Xahau network. Xai hook provides an automated way to issue and redeem XAI USD, an over-collateralised stablecoin backed with XAH.

## Project Details 🛠

- [x] **Included Project Files (code, documentation, etc.)**
- [x] **Detailed `README.md` file containing:**
  - **Project Description**
  - **Instructions on How to Use the Project**
  - **Integration Details for Xahau Hooks**
    
## Introduction

Xai hook protocol provides an automated way to issue and redeem XAI USD, an over-collateralised stablecoin backed with XAH.

Sending XAH to it creates a vault and sends back the corresponding amount of XAI USD stablecoin based on the current XAH price. XAI USD can be redeemed at any time against the vault to get back the original XAH.

The collateralization ratio is set to 200%, so for each $10 value of XAH collateralized the protocol issues a loan of $5 value in XAI USD stablecoin, so a 5 XAI USD loan. The liquidation ratio is set to 120%, so if XAH price goes down and the collateral value falls bellow 120%, then anyone who wants to top up the vault can come along and take it over.

Consider a vault with an initial XAH diposit of 10 XAI USD value and a 5 XAI USD loan issued. Let's say XAH price is 10 XAI USD and falls to the point that the initial position has now a 5 XAI USD value, which results in a 100% collateralization ratio. That means the collateralization is bellow 120% and for that reason the vault can be taken over.

In Loan-to-Value (LTV) terms: loan amount divided by XAH value collateralized and the result multiplied by 100. Result: an initial loan at 50%. The position gets liquidated if the LTV ratio goes just above 83%, so a 33% secure margin from the initial position is enforced by the protocol.

For demostration purposes we will create 2 different examples, so we will create 2 similar hooks, one that works with an oracle that reproduces different hypothetical market conditions regarding XAH price, and another one that works with an oracle that follows current XAH price.

Example 1: as this project launches on the Xahau mainnet, XAH price in the market doesn't change as required for testing and explanations purposes, so we will use Example 1 to show how the protocol works. In order to do that we need to change the price to show hook behavior in such escenarios. Example 1 uses an oracle where we set XAH price arbitrarily to simulate different market conditions. 

Example 2: here we will create a similar hook that works with XAH real price, we will use Wietse Wind XRPL Labs oracle to obtain XAH price. XAH price is relatively stable so here we can't go beyond the current price, so we will just show how in fact the protocol behaves considering the current XAH price.

## How to Use the Project 🚀

## EXAMPLE 1 (XAH price is fixed and the oracle is set and changed by us arbitrarily for demonstration purposes)

### Accounts required

4 accounts are required: Carol, Alice, Carlos and Charlie.

Carol = XAI USD stablecoin issuer, user sends XAH to this account which is the hook account:
https://xahau.xrplwin.com/account/r9DSnJkkyva4NdShyxw32Q217B7PMZuB9c
https://xahauexplorer.com/explorer/r9DSnJkkyva4NdShyxw32Q217B7PMZuB9c

Alice = XAI USD stablecoin user, sends XAH to the issuer-hook to diposit it as collateral to obtain XAI USD:
https://xahau.xrplwin.com/account/rsF3rBFyQP2zCheJ2woHv46XHvUyKVRK86
https://xahauexplorer.com/explorer/rsF3rBFyQP2zCheJ2woHv46XHvUyKVRK86

Carlos = oracle that stablishes XAH price, low account = oracle_lo
https://xahauexplorer.com/explorer/rHBsTkCaTR86RGKVt3bTUhZAu2NSTZbNSv
https://xahau.xrplwin.com/account/rHBsTkCaTR86RGKVt3bTUhZAu2NSTZbNSv

Charlie = oracle that stablishes XAH price, high account = oracle_hi                                                                                                                                                        
https://xahauexplorer.com/explorer/rJ2GzPBeCoK9NjmJ9vcVntVj2poEvjfvEv
https://xahau.xrplwin.com/account/rJ2GzPBeCoK9NjmJ9vcVntVj2poEvjfvEv

### Set up process

Run decode.js, twice, to convert Carlos and Charlie raddresses to binary form, save the values somewhere. You can obtain the same result using this tool: https://hooks.services/tools/raddress-to-accountid

If the Carlos account number (the "Account index" number in explorers) is numerically higher than Charlie, switch the accounts (either re-import them, or just switch their names in the following text).

Set up trust limit for the stablecoin user for 10.000.000.000, by running trust-user.js. The script requires 2 parameters:
The user account (Alice) sends the TrustSet transaction, so that the script requires its private key.
The hook account (Carol) is set up as the trusted issuer. Example:
Trustline transaction: https://xahauexplorer.com/explorer/91C4054C478EB0AAFFE929EB346895D48A5B01D1325AD961FB465F7C9CC6C76B

Set up trust limit on the oracle, by running trust-oracle.js. The script requires 2 parameters:
The low account (Carlos) sends the TrustSet transaction, so that the script requires its private key.
The high account (Charlie) is set up as the trusted issuer. In this case we set it initially at 1 XAH = 20 XAI USD:
Trustline transaction: https://xahauexplorer.com/explorer/D8EEC528734E8B79505F523F4086CD4211C603EAC6BC253A5434C68096B85477

The file xai.c contains the hook code and it has to be compiled to .wasm, it can be compiled using Hooks Tookit: 
https://hooks-toolkit.com/ 

Then the xai.wasm file can be converted to binary code using a tool like this one:
https://wasdk.github.io/wasmcodeexplorer/

Use that binary format code to deploy the hook to Carol account (hook account, issuer), it has to be set as "ttPayment" and with these 2 parameters (you can use this Xrplwin tool to set the hook, login and paste the binary code and follow instructions: https://xahau.xrplwin.com/tools/hook/from-binary):

Parameter 1. parameter name "oracle_lo" = 6F7261636C655F6C6F, set to Carlos (low oracle) binary account = B1683A099ECF7CD79482728A96675967D03FA8E6

Parameter 2. parameter name "oracle_hi" = 6F7261636C655F6869, set to the Charlie (high oracle) binary account = C0C536597D939AE09DBABBA336795A42422207E6

SetHook transaction (Example 1 code, xai.c, xai.wasm): https://xahau.xrplwin.com/tx/79A484F3085C6076E0119F74A86136D32484B57E6E0D850F29520A7D23CA8D01

### Using the protocol

Set up a payment transaction from Alice (which sends the amount of XAH she wants to collateralize) to Carol (hook, issuer). The hook will issue-mint and send back the corresponding XAI USD stablecoin amount. Currently we have set in the oracle the following price: 1 XAH = 20 XAI USD.

Alice user that wants XAI USD stablecoin sends 1 XAH to the XAI USD issuer-hook:
https://xahauexplorer.com/explorer/C6E7049CF83F7C956DFB03CA41AE6A978F5DFC6666AC09ECF0DDABA53CF2A063

Carol XAI USD issuer-hook when receives 1 XAH mints 10 XAI USD (initial collateralitzation ratio 200%, so for $20 value issues a loan of $10 value) and sends it to Alice: 
https://xahauexplorer.com/explorer/47131A52F5C893B48BFBF628F64EA74A8727706FE0B437F93D8F8792E57970EC

From now on any user-account can collateralize XAH and receive XAI USD stablecoin following these steps:

Step 1: set the XAI USD trustline on the user-account scanning the following QR code:
https://xahau.services/?issuer=r9DSnJkkyva4NdShyxw32Q217B7PMZuB9c&currency=USD&limit=10000000000

Step 2: sent a XAH payment from the user-account to Carol-hook-issuer account. pendent
https://xahauexplorer.com/explorer/9F6D159D5350816D97B60FA2E06EAE5E76C9E744B18FA6D5A9492C7C0AB8D5FF
https://xahauexplorer.com/explorer/739C76E3DA279F47AEEFA230B5522F0DE942C87A0EF66D4924E54ED6C52EA204

### The oracle

The exchange rate between XAH and XAI USD stablecoin is the limit set on a trustline established between two special oracle accounts, account oracle Carlos sets a XAI USD trustline to Charlie oracle account setting as a limit the current XAH price on the market. The price of XAH is representend in XAI USD terms using Carlos and Charlie oracles account. In normal circumstances these accounts will update automatically tracking XAH real price expressed in XAI USD stablecoin terms. In the previous Example 1, we set initially and arbitrarily at 1 XAH = 20 XAI USD. 

### What happens when XAH price changes

Let's assume that XAH price goes down, let's say, to 10 XAI USD, so 1 XAH = 10 XAI USD. Then Carlos oracle account has to change-update the limit on the trustline set to Charlie oracle account, in this case we update it performing and account set transaction updating the trustline limit to 10:
https://xahauexplorer.com/explorer/EE20B7B418BFC68A8A752429B2B32E7431C28A63D2016F8C882BAC6450D960B7

Once the new limit/price is set, Carol hook account sends the new proportional amount of XAI USD corresponding to the new price set. As XAH price went down, the amount of XAI USD sent for the same amount of XAH is much lower. Compare the amounts in the previous examples with the new one. 

The 2 previous examples had XAH price set at 20 XAI USD with these results:

One user (Alice) sent to the hook 1 XAH ($20 value) and received 10 XAI USD:

https://xahauexplorer.com/explorer/C6E7049CF83F7C956DFB03CA41AE6A978F5DFC6666AC09ECF0DDABA53CF2A063
https://xahauexplorer.com/explorer/47131A52F5C893B48BFBF628F64EA74A8727706FE0B437F93D8F8792E57970EC

The other user sent to the hook 2 XAH ($40 value) and received 20 XAI USD:

https://xahauexplorer.com/explorer/9F6D159D5350816D97B60FA2E06EAE5E76C9E744B18FA6D5A9492C7C0AB8D5FF

https://xahauexplorer.com/explorer/739C76E3DA279F47AEEFA230B5522F0DE942C87A0EF66D4924E54ED6C52EA204

Here the results a new user obtains once XAH price has changed from 20 XAI USD to 10 XAI USD and sends XAH to the hook:

The new user sent to the hook 1 XAH but just received 5 XAI USD (instead of the previous 10 XAI USD):

https://xahauexplorer.com/explorer/09A775615A7DB784B38E37FDF69635EBDB4198DF703139B3CCD306864E36C1AA

https://xahauexplorer.com/explorer/5F59C30C52E1B9EC6157723BA0BF916B9CC4BBF1602C50DADAA497108B63726A

Similarly, the new user sent to the hook 2 XAH but just received 10 XAI USD (instead of the previous 20 XAI USD):

https://xahauexplorer.com/explorer/A25A7D5628BDD5E29253FDF37BD6D169F7FCEDC214638EF62C3D0B18B3FDBCCD

https://xahauexplorer.com/explorer/B5A95EB758E18F091C533614ECB06A683DBC6FE79823E05E747781B5265E6EC6

### Recollateralization

What happens to users vaults now that XAH price went down so their vaults are less collateralized?

Now that the price went down, users can send back XAI USD or just send XAH in both cases to increase the collateralization of their position. While users vaults are undercollateralized every XAH the corresponding user sends back to the hook account doesn't generate new XAI USD, it is just used to increase the vault collateralization to return again to the 200% ratio.

See for example what happened to Alice vault once XAH price went down to 10 XAI USD (so she has a $10 loan-debt position and 1 XAH collateralized that now has $10 value, so his position is at 100% collateralization. Remember that a good collateralization ratio should be at 50% (also called 200%, depending on how you calculate it), and the liquidation ratio is set to 83%, which is 33% above the initial ratio. So when she sends XAH to the hook she receives the following response: "Xai: Vault is undercollateralized, absorbing without sending anything."

She sends 0'001 XAH (far from the amount needed to secure the vault): "Xai: Vault is undercollateralized, absorbing without sending anything."

https://xahauexplorer.com/explorer/84CB0A88EF1F6B7601E973973A985A1A7A00FFF602BD3F679ED38A2E37C3C7EC

She sends 0'01 XAH (far from the amount needed to secure the vault): "Xai: Vault is undercollateralized, absorbing without sending anything."

https://xahauexplorer.com/explorer/9FF3FFF159471C1DC4F0B1374C84F01D1D7093B4308F80DB0CE635BF5CF29287

She sends 0'1 XAH (far from the amount needed to secure the vault): "Xai: Vault is undercollateralized, absorbing without sending anything."

https://xahauexplorer.com/explorer/9DC71DBB329011499CCE60B5BD9157DD9BBBA85D571122E310F5488498BA2054

She sends 1 XAH (this, with the already 1 XAH initially diposited, and the small previous amount added, puts the vault in a correct colletarilzation, because there will be again around $20 value collateralized (50% ratio, $10 XAU USD loan divided by the $20 current value). The protocol even issues some more XAI USD because the total amount collateralized is 2'111, so the total issued for that position goes up to 10'55 XAI USD)

https://xahauexplorer.com/explorer/896F7DFC6E9652A1698228AE2FF4DCCBF3CF6C4407E0E610F2E801DC89C38034

https://xahauexplorer.com/explorer/F61AB65666243A17B43703A671FCD77945525A7FC16E1B8EB3E650F388624894

Now, if she sends back the XAI USD borrowed the protocol sends back his XAI and the vault dissapears:

https://xahauexplorer.com/explorer/C93E3895574C6D6B48BD8AA4357D57D729E4AA03A153435DD9C145D5E428F6B4

https://xahauexplorer.com/explorer/86CB7421CB370BB1E82A0FBAEE09B72E19C6E8D6C85BF3722E4237C71D267207

### Take over a vault

Once a vault goes bellow 120% collateralization any user can take over the vault by sending the needed amount of XAH to bring it back to a secure degree of collateralization above 120%. By doing that the user takes control over the vault.

Let's consider the previous example but instead of Alice recollateralize his position, she doesn't, and then another user takes over her vault:

Remember, Alice collateralized 1 XAH at a price of $20 XAI USD, and the protocol issued a loan of $10 value (50% ratio between debt/collateral), then XAH price felt to $10 value, putting the vault in a liquidatable situation because the liquidation ratio starts at 83% and the vault is now at 100%.

A vault is identified with the Vault ID, which consists in the following data combined: Account ID + FFFFFFFF + 0000000000000000

a) Account ID: for example, Alice user account rsF3rBFyQP2zCheJ2woHv46XHvUyKVRK86 has as Account ID: 1ED9A5D3DDC00560601FA55482D7956559002B27

b) If absent a tag (let's assume we have no tags): FFFFFFFF

c) 0 up to 64 characters: 0000000000000000

So, in this case, Alice Vault ID is: 1ED9A5D3DDC00560601FA55482D7956559002B27FFFFFFFF0000000000000000

The Vault ID coincides with the HookStateKey field, present in the HookState. You can check it for example in the raw transaction data (Raw TX data) in any payment the user did to the hook account to create or top a vault:
https://xahau.xrplwin.com/tx/B6A77D7B0D8605E6BF16CA9FAA918A5521E16DF6D83BCDF7E301834791CADEAA#raw

The Vault ID is used to take over a vault, so we use the Vault ID as Invoice ID. Any user in order to take over a vault has the send a payment transaction which contains the InvoiceID field. The InvoiceID field has to contain the Vault ID the user wants to take over.

Take over transaction example:

{
"TransactionType": "Payment",
"Account": "rha1Dgwr2f822JuD5eSiZ7L9mqprCLQDNo",
"Destination": "r9DSnJkkyva4NdShyxw32Q217B7PMZuB9c",
"InvoiceID": "1ED9A5D3DDC00560601FA55482D7956559002B27FFFFFFFF0000000000000000",
"Amount": "1000000",
"NetworkID": 21337
}

"Account": "rha1Dgwr2f822JuD5eSiZ7L9mqprCLQDNo" = is the user non-owner of the vault that wants to take over the vaoult.

"Destination": "r9DSnJkkyva4NdShyxw32Q217B7PMZuB9c" = is the hook account.

"InvoiceID": "1ED9A5D3DDC00560601FA55482D7956559002B27FFFFFFFF0000000000000000" = it's the way to refer to the owner and the vault we want to take over, in this case Alice rsF3rBFyQP2zCheJ2woHv46XHvUyKVRK86.

See for example the response we receive when let's say rha1Dgwr2f822JuD5eSiZ7L9mqprCLQDNo, not the owner of the valt, tries to take over a vault which has a 10 XAI USD loan debt, but doesn't send enough XAH or XAI USD to take over it, it gets this response "Xai: Vault is undercollateralized and your deposit would not redeem it", as expected. 

See the result when the user sends just 0'0001 XAI USD: "Xai: Vault is undercollateralized and your deposit would not redeem it"
https://xahauexplorer.com/explorer/02242933F79D8F2CAA877459CC2FA2B23B6AF75EFED687895663F146260F2BC9

See the result when the user sends just 0'001 XAH USD: "Xai: Vault is undercollateralized and your deposit would not redeem it"
https://xahauexplorer.com/explorer/4758169B006BBCFBA5C21578152AE1080F7328F5D1DADD1FCB280B9C459C45C6

See the result when the user sends just 0'01 XAH USD: "Xai: Vault is undercollateralized and your deposit would not redeem it"
https://xahauexplorer.com/explorer/C6AAE900DD7A499F229DEF3D466C1D016ACDC8EE12319EF50A5A997FF45DFBAE

See the result when the user sends just 0'1 XAH USD: "Xai: Vault is undercollateralized and your deposit would not redeem it"
https://xahauexplorer.com/explorer/1047BB03C3D743020A30FA91FA6334E7F49590A5BA92A0DB5766CC09E03C3D57

But see the result when the user sends 1'1 XAH  which covers all -but just- the pending debt (so $10 value for a debt of $10 value): (this has to be improved, it should give all XAH directly and sends a small amount of USD (0'5). IT seems that just XAI has to be send back to take over the vault, so the protocol gives you the needed XAI USD to receive the corresponding amount with XAH. So just send this XAI USD amount to the hook and now it will sent back the proper XAH amount you take over (1'6 XAH, around 16 USD):
https://xahauexplorer.com/explorer/539266A45BBC3087A8EE20A363332F5A71D85D5FAC26259FC0FC51D93782CC13
https://xahauexplorer.com/explorer/AD9D9570D2B8780F398D093EAF12DD5ED70DCAD2C2970AC618DBDF8B116CBF3E
https://xahauexplorer.com/explorer/BE2CC99415DAEAA54C82326543BEA8296350FEF2DDFBC67E29804147B5FB3C14
https://xahauexplorer.com/explorer/E7B0A49DAD5DFF0B0E31A64DBC563E8F85213D9146BACB3CDF9AF52FA7E9717F

See that the vault once taken over doesn't exist anymore:
https://xahauexplorer.com/explorer/025924EED5C956628559DECECE0866AD73D4C51C2F61A4950F631462BB8AD7CD

## EXAMPLE 2 (XAH price is set using Wietse Wind oracle)

### Accounts required

4 accounts are required: Carol, Alice, Carlos and Charlie.

Carol = XAI USD stablecoin issuer, user sends XAH to this account which is the hook account:
https://xahau.xrplwin.com/account/rM9T2RiKDamqz4j5YYvPnVkefgWip3195N
https://xahauexplorer.com/explorer/rM9T2RiKDamqz4j5YYvPnVkefgWip3195N

Alice = XAI USD stablecoin user, sends XAH to the issuer-hook to diposit it as collateral to obtain XAI USD:
https://xahau.xrplwin.com/account/rLni95nuk7NZw7d2ZRqoikxB1h2ZyoojbP
https://xahauexplorer.com/explorer/rLni95nuk7NZw7d2ZRqoikxB1h2ZyoojbP

Carlos = oracle that stablishes XAH price, low account = oracle_lo
https://xahauexplorer.com/explorer/rXUMMaPpZqPutoRszR29jtC8amWq3APkx
https://xahau.xrplwin.com/account/rXUMMaPpZqPutoRszR29jtC8amWq3APkx

Charlie = oracle that stablishes XAH price, high account = oracle_hi
https://xahauexplorer.com/explorer/r9PfV3sQpKLWxccdg3HL2FXKxGW2orAcLE
https://xahau.xrplwin.com/account/r9PfV3sQpKLWxccdg3HL2FXKxGW2orAcLE

### Set up process

Run decode.js, twice, to convert Carlos and Charlie raddresses to binary form, save the values somewhere. You can obtain the same result using this tool: https://hooks.services/tools/raddress-to-accountid

Set up trust limit for the stablecoin user, by running trust-user.js. The script requires 2 parameters:
The user account (Alice) sends the TrustSet transaction, so that the script requires its private key.
The issuer hook account (Carol) is set up as the trusted issuer.
Trustline transaction: https://xahauexplorer.com/explorer/D86D91A9CEBA04DC58FFADBDF6EEC3404DCA79CA67B0FFE5F1B4331E8E24E83F

In this case, as the oracle is set by Wietse Wind XRPL Labs, we don't need to do set up the oracle. If for any reason you are setting an oracle yourself, consider this:
Set up trust limit on the oracle, by running trust-oracle.js. The script requires 2 parameters:
The low account (Carlos) sends the TrustSet transaction, so that the script requires its private key.
The high account (Charlie) is set up as the trusted issuer. 

The file xai2.c contains the hook code and it has to be compiled to .wasm, it can be compiled using Hooks Tookit: 
https://hooks-toolkit.com/ 

Then the xai2.wasm file can be converted to binary code using a tool like this one:
https://wasdk.github.io/wasmcodeexplorer/

Use that binary format code to deploy the hook to Carol account (hook account, issuer), it has to be set as "ttPayment" and with these 2 parameters (you can use this Xrplwin tool to set the hook, login and paste the binary code and follow instructions: https://xahau.xrplwin.com/tools/hook/from-binary):

Parameter 1. parameter name "oracle_lo" = 6F7261636C655F6C6F, set to Carlos (low oracle) binary account = 05B5F43AF717B81948491FB7079E4F173F4ECEB3

Parameter 2. parameter name "oracle_hi" = 6F7261636C655F6869, set to the Charlie (high oracle) binary account = 5BEF921A217D57FDA5B56D5B40BEE40D1AC1127F

SetHook transaction (Example 2 code, xai2.c, xai2.wasm): https://xahau.xrplwin.com/tx/2095A3169ABC3E0535122EC3173A8AF30230ADF498423702993C30543E01E41D

### Using the protocol

Set up a payment transaction from Alice (which sends the amount of XAH she wants to collateralize) to Carol (hook, issuer). The hook will issue-mint and send back the corresponding XAI USD stablecoin amount. In this case we are dealing with XAH real price, at the time of writting this document XAH price was around 0'08 USD.

Alice user that wants XAI USD stablecoin sends 5 XAH to the XAI USD issuer-hook:
https://xahauexplorer.com/explorer/A033EA9746B47CABD22215260A19E17F3680D13E35F58BC8AE8188A160A6A6B0

Carol XAI USD issuer-hook when receives 5 XAH mints 0'21 XAI USD and sends it to Alice: 
https://xahauexplorer.com/explorer/58C77CCA37E0FD084A5323C29BECE640BA90FDC4DF1FF322A0FA90156BE96C96

From now on any user-account can collateralize XAH and receive XAI USD stablecoin following these steps (you can try it yourself following these 2 steps, sending a small amount of XAH just for testing purposes):

Step 1: set the XAI USD trustline on the user-account scanning the following QR code:
https://xahau.services/?issuer=rM9T2RiKDamqz4j5YYvPnVkefgWip3195N&currency=USD&limit=10000000000

Step 2: sent a XAH payment from the user-account to Carol-hook-XAI-USD-issuer account.
https://xahauexplorer.com/explorer/D9D06EF43A367577D3C2927813361F059E630DA8F405320D995BCA53D3E3451F
https://xahauexplorer.com/explorer/8861E7B9AAC7399E4707671476C71FA8DA8F98CE29F992F45CB9A7E22E1033EE

### The oracle

The exchange rate between XAH and XAI USD stablecoin is the limit set on a trustline established between two special oracle accounts, Carlos oracle account sets a XAI USD trustline to Charlie oracle account setting as a limit the current XAH price on the market. The price of XAH is representend in XAI USD terms using Carlos and Charlie oracles account. In this case this oracle set for XAH on Xahau (https://xahauexplorer.com/explorer/rXUMMaPpZqPutoRszR29jtC8amWq3APkx) is set by Wietse Wind from XRPL Labs, it fetches XAH price information using exchange API's and updates the oracle trustline limit with that price, see his proposal here: https://dev.to/wietse/aggregated-xrp-usd-price-info-on-the-xrp-ledger-1087, and the code here: https://github.com/XRPL-Labs/XRP-Price-Aggregator, and here: https://github.com/XRPL-Labs/XRPL-Persist-Price-Oracle.

### What happens when XAH price changes

In this case we can't show what happens if the price goes up or down because the oracle shows the current XAH price, which is relatively stable, and we can't modifiy it for demostration purposes. To se how the behavior the protocol has when XAH price goes down see EXAMPLE 1 where we change oracle XAH price from 33 XAI USD to 10 XAI USD. When data is available for Example 2 we will update this section.

## Online demo

Online demo (pending...): https://skunk-proper-smoothly.ngrok-free.app/tools/xaiprotocol/xaiprotocol

## Mandatory Tweets 🐦

Submissions will be shared and amplified by the @XahauNetwork account, giving your work additional visibility within the community.
- [x] **First Mandatory Tweet** announcing your participation:

  - **Link to Tweet:** https://x.com/ainittomai/status/1878919910472270251

  **Example Tweet:**
  > We are participating in the Build on Xahau contest! Learn about our project here: [https://github.com/JosepMariaVila/XAI-PROTOCOL]

- [x] **Second Mandatory Tweet** upon submission for final review:

  - **Link to Tweet:** https://x.com/ainittomai/status/1883442794834071812

  **Example Tweet:**
  > We have submitted [Project Name] to the Build on Xahau contest, see our submission here: [PR Link]

## Additional Information 📄

### Acknowledgements

@ekiserrepe, @XRPLWin, @angell_denis, @WietseWind, @nixerFFM

### Credit

This project is based on Richard Holland's peggy hook from 2021.
