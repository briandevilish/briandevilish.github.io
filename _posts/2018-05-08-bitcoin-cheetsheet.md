---
title: Bitcoin tips include command line help and format of bitcoin protocol
layout: default
---

{{ page.title }}
================
<head>
<style>
table, th, td {
    border: 1px solid black;
    border-collapse: collapse;
    margin: 5px 0;
    text-align: left;
    vertical-align: top;
}
th { background-color: #99ccff; }
tr { background-color: #e6f2ff; }
pre {
    background: #eee !important;
    display: block;
    font-family: monospace;
    white-space: pre;
    margin: 1em 0;
}
</style>
</head>


# Bitcoin

## Handy links:


* [Bitcoin core config generator](https://jlopp.github.io/bitcoin-core-config-generator)
* [List of address prefixes](https://en.bitcoin.it/wiki/List_of_address_prefixes)

## Pay-to-PubKey-Hash (P2PKH)

* Payment address comprising a hashed public key.
* Can be seen in the *address* attribute of UTXO, use 'listunspent'.
* Example output below where the P2PKH address of mvbnrCX3bg1cDRUu8pkecrvP6vQkSLDSou:

<pre>
    {
        "txid" : "263c018582731ff54dc72c7d67e858c002ae298835501d\
                  80200f05753de0edf0",
        "vout" : 1,
        "address" : "mvbnrCX3bg1cDRUu8pkecrvP6vQkSLDSou",
        "account" : "",
        "scriptPubKey" : "76a914a57414e5ffae9ef5074bacbe10a320bb\
                          2614e1f388ac",
        "amount" : 10.00000000,
        "confirmations" : 0,
        "spendable" : true,
        "solvable" : true
    }
</pre>

* To see the public key of P2PKH, use 'validateaddress mvbnrCX3bg1cDRUu8pkecrvP6vQkSLDSou':

<pre>
{
  "isvalid": true,
  "address": "mvbnrCX3bg1cDRUu8pkecrvP6vQkSLDSou",
  "scriptPubKey": "76a914a57414e5ffae9ef5074bacbe10a320bb2614e1f388ac",
  "ismine": false,
  "iswatchonly": false,
  "isscript": false,
  "iswitness": false
}
</pre>

## Pay-to-Script-Hash (P2SH)

* Complex script as P2SH:

<table border="1">
<tr>
<td>Redeem Script:</td>
<td> 
<pre>
[minimum signatories] <public key #1> <public key #2> <public key #n> [number of public key] CHECKMULTISIG
</pre>
</td>
</tr>
<tr>
<td>Locking Script:</td>
<td><pre>
HASH160 <20-byte hash of redeem script> EQUAL
</pre>
</td>
</tr>
<tr>
<td>Unlocking Script:</td> 
<td>
<pre>
Sig1 Sign &lt;redeem script&gt; 
</code></pre>
</td>
</tr>
</table>

* Reference: [multi-sig example command-line](https://bitcoin.org/en/developer-examples#p2sh-multisig)
* Note: remember to use public key in redeem script instead of p2pkh address.
* Using bx command to create 'redeem script':

<pre>
./bx-linux-x64-qrcode script-to-address "2 [02b66fcb1064d827094685264aaa90d0126861688932eafbd1d1a4ba149de3308b] [025cab5e31095551582630f168280a38eb3a62b0b3e230b20f8807fc5463ccca3c] [021098babedb3408e9ac2984adcf2a8e4c48e56a785065893f76d0fa0ff507f010] 3 checkmultisig"
2N1f1LZE6ySfXNWmwUo9CMZoiWYtijaVfyo
</pre>
