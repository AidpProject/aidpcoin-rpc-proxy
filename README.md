# Aidpcoin-rpc-proxy

## A Web API for Aidpcoin

**Purpose**: make Aidpcoin blockchain available via HTTP/WEB by exposing the RPC-API via a Proxy that only allows safe procedures.

Check out this software live at https://rpc-main.ai-depin.org/
![image](https://user-images.githubusercontent.com/9694984/226344965-7f01cee1-99ef-4a7f-b9db-8cfce4ccb5e8.png)


## How do I use this software?

When your local proxy is up and running you send requests using HTTP Post.
The body of the request should contain string **method** and array **params** 

### Examples

### Live examples on Code Pen
Feel free to fork it and play around
- Get block count https://codepen.io/RavenRebels/full/jOpmErK
- Address balances https://codepen.io/RavenRebels/pen/RwBVEbW

### Example for web browser and Node.js 18+
```
//Get block count
rpc("getblockcount", []).then(function (count) {
    console.log("Block count", count);
});

//Get transactions in mempool
rpc("getrawmempool", []).then(function (data) {
    console.log("There are", data.length, "transactions in mempool right now");
});

//Get specific transaction
rpc("getrawtransaction", ["301ec56896153463576c47ac40956e58d2b9fa7de87fad39128c5ae0af66b6a4", true]).then(transaction => {
    console.log("Transaction", transaction.hash, "has", transaction.confirmations, "confirmations");
})

//Get address balance
rpc("getaddressbalance", [{ "addresses": ["RXissueSubAssetXXXXXXXXXXXXXWcwhwL"] }]).then(balance => {
    const sum = balance.balance / 1e8;//divide by 100 000 000;
    console.log("RXissueSubAssetXXXXXXXXXXXXXWcwhwL balance", sum.toLocaleString());
})


async function rpc(method, params) {
    const data = { method, params };
    const URL = 'https://aidp-rpc-mainnet.ai-depin.org/rpc'; //replace with your endpoint
    const response = await fetch(URL, {
        method: 'POST',
        headers: {
            'Content-Type': 'application/json'
        },
        body: JSON.stringify(data) // body data type must match "Content-Type" header
    });
    const obj = await response.json(); // parses JSON response into native JavaScript objects 
    return obj.result;
} 
``` 
## Features and limitations

This software lives up to parts of the JSON-RPC 2.0 Specification
https://www.jsonrpc.org/specification

According to JSON-RPC 2.0 a request object could contain four attributes, jsonrpc, method, params and id.
- This software only supports **method** and **params**.
- This software does NOT support **id**
- This software hardcodes **jsonrpc** to "2.0"
- This sofware does NOT support batch calls.

## How to install
```
git clone https://github.com/aidpproject/aidpcoin-rpc-proxy.git
cd ravencoin-rpc-proxy
npm install 
```

### Sir, how do I configure this software?

Configure SSL 

   1 Install snapd:

      sudo apt install snapd

   2 Ensure that snapd is up to date:

      sudo snap install core; sudo snap refresh core

   3 Install certbot (full install instructions for different operating systems and configurations can be found here: https://certbot.eff.org/instructions):

      sudo snap install --classic certbot

   4 Prepare the certbot command:

      sudo ln -s /snap/bin/certbot /usr/bin/certbot

   5 Follow the steps below to configure the Express webserver for use with TLS/SSL:

If you haven't already done so, run the setcap cmd from the Use Setcap to Safely Grant User Permissions Instructions which will allow node.js to bind to port 443 without needing root permissions.

There are different options for generating a valid TLS/SSL certificate via certbot. If you are running the explorer on port 80 you can run the cmd on step 2A), otherwise run the cmd on step 2B) if the explorer is running on any port # other than 80. This step is important because certbot will automatically renew your TLS/SSL certificate periodically and it will fail to renew if the wrong option is chosen:

A. The Webroot method is used when port 80 is already in use by the explorer. Be sure to change the webroot-path to the absolute path of the explorer/public directory:

NOTE: The explorer must be running for this cmd to work properly:

      sudo certbot certonly --webroot --webroot-path /path/to/explorer/public

B. The Standalone method is used when port 80 is not already in use by the explorer:

      sudo certbot certonly --standalone

Certbot will ask a few simple questions and generate the necessary TLS/SSL certificate files for your domain. It will also install the necessary files to automatically renew the certificates when they are about to expire, so you shouldn't need to do anything special to keep them up to date.

Once the TLS/SSL certificates are generated, you will need to grant permission to non-root users with the following commands:

      sudo chmod -R 755 /etc/letsencrypt/live/
      sudo chmod -R 755 /etc/letsencrypt/archive/


Configure ./index.js
```
const sslOptions = {
  key: fs.readFileSync('/path/to/private.key'),  // SSL Private Key
  cert: fs.readFileSync('/path/to/certificate.crt')  // SSL Certificate
};

  ```


Configure your setup in ./config.json
```
{
  "concurrency": 4,
  "endpoint": "https://aidp-rpc-mainnet.ai-depin.org/rpc",
  "environment": "Aidpcoin",
  "local_port": 443,
  "nodes": [
    {
      "name": "Node number 1",
      "username": "dauser",
      "password": "dapassword",
      "aidp_url": "http://localhost:18001"
    },
    {
      "name": "Nody two tower", 
      "aidp_url": "http://127.0.0.1:18001",
      "password": "supermega2354ergsecret",
      "username": "supermegas3435ecwertwertret"
    }
  ]
}

  ```

### Sir, how should my Aidp core node be configured?
Here is a recommendation
```
server=1 
listen=1

#Maintains the full transaction index on your node. Needed if you call getrawtransaction. Default is 0.
txindex=1

#Maintains the full Address index on your node. Needed if you call getaddress* calls. Default is 0.
addressindex=1

#Maintains the full Asset index on your node. Needed if you call getassetdata. Default is 0.
assetindex=1

#Maintains the full Timestamp index on your node. Default is 0.
timestampindex=1

#Maintains the full Spent index on your node. Default is 0.
spentindex=1

#Username and password - set secure username/password
rpcuser=secret
rpcpassword=secret

#What IP address is allowed to make calls to the RPC server.
rpcallowip=127.0.0.1

dbcache=4096
```

## Sir, how do I start this application?

```
npm start
```

## Help with Aidpcoin RPC calls, arguments and stuff
Go to https://aidp-rpc-mainnet.ai-depin.org/ for in depth description of each RPC call
![image](https://user-images.githubusercontent.com/9694984/212323158-6ed00511-cfcc-4338-990c-ebb57f590cf0.png)


## List of Aidpcoin RPC calls
This is a raw list, a lot of these calls are not whitelisted.
For example we do NOT let developers call procedure `dumpprivkey`
```
== Addressindex ==
getaddressbalance
getaddressdeltas
getaddressmempool
getaddresstxids
getaddressutxos

== Assets ==
getassetdata "asset_name"
getcacheinfo 
getsnapshot "asset_name" block_height
issue "asset_name" qty "( to_address )" "( change_address )" ( units ) ( reissuable ) ( has_ipfs ) "( ipfs_hash )"
issueunique "root_name" [asset_tags] ( [ipfs_hashes] ) "( to_address )" "( change_address )"
listaddressesbyasset "asset_name" (onlytotal) (count) (start)
listassetbalancesbyaddress "address" (onlytotal) (count) (start)
listassets "( asset )" ( verbose ) ( count ) ( start )
listmyassets "( asset )" ( verbose ) ( count ) ( start ) (confs) 
purgesnapshot "asset_name" block_height
reissue "asset_name" qty "to_address" "change_address" ( reissuable ) ( new_units) "( new_ipfs )" 
transfer "asset_name" qty "to_address" "message" expire_time "change_address" "asset_change_address"
transferfromaddress "asset_name" "from_address" qty "to_address" "message" expire_time "aidp_change_address" "asset_change_address"
transferfromaddresses "asset_name" ["from_addresses"] qty "to_address" "message" expire_time "aidp_change_address" "asset_change_address"

== Blockchain ==
clearmempool
decodeblock "blockhex"
getbestblockhash
getblock "blockhash" ( verbosity ) 
getblockchaininfo
getblockcount

getblockhash height
getblockhashes timestamp
getblockheader "hash" ( verbose )
getchaintips
getchaintxstats ( nblocks blockhash )
getdifficulty
getmempoolancestors txid (verbose)
getmempooldescendants txid (verbose)
getmempoolentry txid
getmempoolinfo
getrawmempool ( verbose )
getspentinfo
gettxout "txid" n ( include_mempool )
gettxoutproof ["txid",...] ( blockhash )
gettxoutsetinfo
preciousblock "blockhash"
pruneblockchain
savemempool
verifychain ( checklevel nblocks )
verifytxoutproof "proof"

== Control ==
getinfo
getmemoryinfo ("mode")
getrpcinfo
help ( "command" )
stop
uptime

== Generating ==
generate nblocks ( maxtries )
generatetoaddress nblocks address (maxtries)
getgenerate
setgenerate generate ( genproclimit )

== Messages ==
clearmessages 
sendmessage "channel_name" "ipfs_hash" (expire_time)
subscribetochannel 
unsubscribefromchannel 
viewallmessagechannels 
viewallmessages 

== Mining ==
getblocktemplate ( TemplateRequest )
getkawpowhash "header_hash" "mix_hash" nonce, height, "target"
getmininginfo
getnetworkhashps ( nblocks height )
pprpcsb "header_hash" "mix_hash" "nonce"
prioritisetransaction <txid> <dummy value> <fee delta>
submitblock "hexdata"  ( "dummy" )

== Network ==
addnode "node" "add|remove|onetry"
clearbanned
disconnectnode "[address]" [nodeid]
getaddednodeinfo ( "node" )
getconnectioncount
getnettotals
getnetworkinfo
getpeerinfo
listbanned
ping
setban "subnet" "add|remove" (bantime) (absolute)
setnetworkactive true|false

== Rawtransactions ==
combinerawtransaction ["hexstring",...]
createrawtransaction [{"txid":"id","vout":n},...] {"address":(amount or object),"data":"hex",...}
decoderawtransaction "hexstring"
decodescript "hexstring"
fundrawtransaction "hexstring" ( options )
getrawtransaction "txid" ( verbose )
sendrawtransaction "hexstring" ( allowhighfees )
signrawtransaction "hexstring" ( [{"txid":"id","vout":n,"scriptPubKey":"hex","redeemScript":"hex"},...] ["privatekey1",...] sighashtype )
testmempoolaccept ["rawtxs"] ( allowhighfees )

== Restricted assets ==
addtagtoaddress tag_name to_address (change_address) (asset_data)
checkaddressrestriction address restricted_name
checkaddresstag address tag_name
checkglobalrestriction restricted_name
freezeaddress asset_name address (change_address) (asset_data)
freezerestrictedasset asset_name (change_address) (asset_data)
getverifierstring restricted_name
issuequalifierasset "asset_name" qty "( to_address )" "( change_address )" ( has_ipfs ) "( ipfs_hash )"
issuerestrictedasset "asset_name" qty "verifier" "to_address" "( change_address )" (units) ( reissuable ) ( has_ipfs ) "( ipfs_hash )"
isvalidverifierstring verifier_string
listaddressesfortag tag_name
listaddressrestrictions address
listglobalrestrictions
listtagsforaddress address
reissuerestrictedasset "asset_name" qty to_address ( change_verifier ) ( "new_verifier" ) "( change_address )" ( new_units ) ( reissuable ) "( new_ipfs )"
removetagfromaddress tag_name to_address (change_address) (asset_data)
transferqualifier "qualifier_name" qty "to_address" ("change_address") ("message") (expire_time) 
unfreezeaddress asset_name address (change_address) (asset_data)
unfreezerestrictedasset asset_name (change_address) (asset_data)

== Restricted ==
viewmyrestrictedaddresses 
viewmytaggedaddresses 

== Rewards ==
cancelsnapshotrequest "asset_name" block_height
distributereward "asset_name" snapshot_height "distribution_asset_name" gross_distribution_amount ( "exception_addresses" ) ("change_address") ("dry_run")
getdistributestatus "asset_name" snapshot_height "distribution_asset_name" gross_distribution_amount ( "exception_addresses" )
getsnapshotrequest "asset_name" block_height
listsnapshotrequests ["asset_name" [block_height]]
requestsnapshot "asset_name" block_height

== Util ==
createmultisig nrequired ["key",...]
estimatefee nblocks
estimatesmartfee conf_target ("estimate_mode")
signmessagewithprivkey "privkey" "message"
validateaddress "address"
verifymessage "address" "signature" "message"

== Wallet ==
abandontransaction "txid"
abortrescan
addmultisigaddress nrequired ["key",...] ( "account" )
addwitnessaddress "address"
backupwallet "destination"
bumpfee has been deprecated on the aidp Wallet.
dumpprivkey "address"
dumpwallet "filename"
encryptwallet "passphrase"
getaccount "address"
getaccountaddress "account"
getaddressesbyaccount "account"
getbalance ( "account" minconf include_watchonly )
getmasterkeyinfo
getmywords ( "account" )
getnewaddress ( "account" )
getrawchangeaddress
getreceivedbyaccount "account" ( minconf )
getreceivedbyaddress "address" ( minconf )
gettransaction "txid" ( include_watchonly )
getunconfirmedbalance
getwalletinfo
importaddress "address" ( "label" rescan p2sh )
importmulti "requests" ( "options" )
importprivkey "privkey" ( "label" ) ( rescan )
importprunedfunds
importpubkey "pubkey" ( "label" rescan )
importwallet "filename"
keypoolrefill ( newsize )
listaccounts ( minconf include_watchonly)
listaddressgroupings
listlockunspent
listreceivedbyaccount ( minconf include_empty include_watchonly)
listreceivedbyaddress ( minconf include_empty include_watchonly)
listsinceblock ( "blockhash" target_confirmations include_watchonly include_removed )
listtransactions ( "account" count skip include_watchonly)
listunspent ( minconf maxconf  ["addresses",...] [include_unsafe] [query_options])
listwallets
lockunspent unlock ([{"txid":"txid","vout":n},...])
move "fromaccount" "toaccount" amount ( minconf "comment" )
removeprunedfunds "txid"
rescanblockchain ("start_height") ("stop_height")
sendfrom "fromaccount" "toaddress" amount ( minconf "comment" "comment_to" )
sendfromaddress "from_address" "to_address" amount ( "comment" "comment_to" subtractfeefromamount replaceable conf_target "estimate_mode")
sendmany "fromaccount" {"address":amount,...} ( minconf "comment" ["address",...] replaceable conf_target "estimate_mode")
sendtoaddress "address" amount ( "comment" "comment_to" subtractfeefromamount replaceable conf_target "estimate_mode")
setaccount "address" "account"
settxfee amount
signmessage "address" "message"

```
