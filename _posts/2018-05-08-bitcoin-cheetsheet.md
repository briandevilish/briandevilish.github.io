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


## Serialization format [source] (https://github.com/jimmysong/programmingbitcoin/blob/master/ch04.asciidoc)
* public key = private key * Generator Point (P=eG)
* standard for serializing ECDSA public keys is Standards for Efficient Cryptography (SEC), which could be uncompressed or compressed.

* Uncompressed SEC format:
1. Start with the prefix byte, which is 0x04.
2. Next, append the x coordinate in 32 bytes as a big-endian integer.
3. Next, append the y coordinate in 32 bytes as a big-endian integer.

* Compressed SEC Format: 
1. Start with the prefix byte. If y is even, its 0x02; otherwise, its 0x03.
2. Next, append the x coordinate in 32 bytes as a big-endian integer.

* Standard for serializing signatures is called Distinguished Encoding Rules (DER) format. 
* DER signature format is:
1. Start with the 0x30 byte.
2. Encode the length of the rest of the signature (usually 0x44 or 0x45) and append.
3. Append the marker byte, 0x02.
4. Encode r as a big-endian integer, but prepend it with the 0x00 byte if rs first byte = `0x80. Prepend the resulting length to r. Add this to the result.
5. Append the marker byte, 0x02.
6. Encode s as a big-endian integer, but prepend with the 0x00 byte if ss first byte = `0x80. Prepend the resulting length to s. Add this to the result.

* Because we know r is a 256-bit integer, r will be at most 32 bytes expressed as big-endian. Its also possible the first byte could be = 0x80, so #4 can be at most 33 bytes. However, if r is a relatively small number, it could be less than 32 bytes. The same goes for s and #6.


## Pay-to-PubKey-Hash (P2PKH)
* [Transaction](https://en.bitcoin.it/wiki/Transaction)
* Example of a Bitcoin transaction with 1 input and 1 output only
<pre>
Input:
Previous tx: f5d8ee39a430901c91a5917b9f2dc19d6d1a0e9cea205b009ca73dd04470b9a6
Index: 0
scriptSig: 304502206e21798a42fae0e854281abd38bacd1aeed3ee3738d9e1446618c4571d10
90db022100e2ac980643b0b82c0e88ffdfec6b64e3e6ba35e7ba5fdd7d5d6cc8d25c6b241501

Output:
Value: 5000000000
scriptPubKey: OP_DUP OP_HASH160 404371705fa9bd789a2fcd52d2c580b65d35549d OP_EQUALVERIFY OP_CHECKSIG
</pre>

* 
<pre>
scriptPubKey: OP_DUP OP_HASH160 &lt;pubKeyHash&gt; OP_EQUALVERIFY OP_CHECKSIG
scriptSig: &lt;sig&gt; &lt;pubKey&gt;
</pre>

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

