---
title: Bitcoin Difficulty and Hashrate
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

</br>
## Difficulty

[How Is Difficulty Calculated](https://bitcoin.stackexchange.com/questions/5838/how-is-difficulty-calculated)
[Bitcoin Target and Difficulty](https://medium.com/@dongha.sohn/bitcoin-6-target-and-difficulty-ee3bc9cc5962)
[Bitcoin book - Mining and Consensus](https://github.com/bitcoinbook/bitcoinbook/blob/develop/ch10.asciidoc)
</br>
Difficulty in Bitcoin measure the difficulty to find a hash below a given target. The formula below calculate the difficulty. 
MAX_TARGET is the ‘first difficulty’, which is the level of difficulty set when the Bitcoin blockchain first run, which means the value of ‘1’ and is written as ‘1d00ffff’ in four bytes.

```
Difficulty = MAX_TARGET / current_target
```
Using an example of block 501509. The target difficulty or called 'bits' has the value of 0x18009645. It is in coefficient/exponent format, with the first two hexadecimal digits for the exponent and the next six hex digits as the coefficient. 
In this block, therefore, the exponent is 0x18 and the coefficient is 0x009645.
formula target = coefficient * 2^(8*(exponent–3))
target = 0x009645 * 2^0x08*(0x18-0x03)
target = 0x009645 * 2^0x08*(0x15)
target = 0x009645 * 2^8*(21) # converted to decimal
target = 0x009645 * 2^168 

```
$ bitcoin-cli getblockheader 0000000000000000006c5532f4fd9ee03e07f94df165c556b89c495e97680147
{
  ...
  "height": 501509,
  ...
  "bits": "18009645",
  "difficulty": 1873105475221.611,
  ...
}
```
Here is the working sheet to calculate the difficulty for block 501509. The difficulty is 1,873,105,475,221.611 or 1.7T as 1T = 2^40 = 1024^4.
```
[0x00000000,0x00000000,0x009645,{0x00..0x00}]
                                {0x00..0x00} at above has 21 bytes 0x00.
that is  0x009645 * (256 ^ 21） 
The genesis block's target is  ( 0x00ffff * 256 ^ 26 ）which is the difficulty unit '1.0'.
So, the difficulty 
= (0x00ffff * 256 ^ 26）/ (0x009645 * 256 ^ 21)
= 65535/38469 * (256^5)  # converted to decimal
= 1.703579505575918 * 2^40
= 1873105475221.611
```

Bitcoin maintain a 10 mins on average block production. This is achieve by adjusting the difficulty every 2 weeks or precisely every 2,016 blocks. The equation for retargeting measures the time it took to find the last 2,016 blocks and compares that to expected time of 20,160 minutes (2,016 blocks * 10 mins). The ratio between the actual timespan and desired timespan is calculated. Based on the ratio it is adjusted target difficulty is adjusted 
upward or downward.
```
New Target = Old Target * (Actual Time of Last 2016 Blocks / 20160 minutes)
```

## Hashrate
[How Are Bitcoins Difficulty And Hashrate Calculated](https://minerdaily.com/2021/how-are-bitcoins-difficulty-and-hash-rate-calculated/)
</br>
Bitcoin hashrate is the hashes per second from the miners to solve the next block puzzle. We can estimate the network hashrate using the current difficulty.
In this equation the probability that a miner finds a block is 1/(2^32), meaning that the average unique hash attempts to find a block is approximately 2^32. This hash work is then multiplied by the current difficulty, over the 600 second (10 minute) target block pace. 
```
Hash Rate = Current Difficulty * 2^32 / 600
```



