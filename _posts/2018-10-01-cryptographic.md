---
title: Cryptographic in C
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

* General pattern in cryptographic when hashing is using init-update-finalize style. Start by calling init(), then call update() any number of times to add next chunk of data and finally call final() to get the hash. The reason for this pattern is to minimize memory requirements. For instance, with a 1GB RAM system, how could you hash a file of 2GB? What you can do, instead, is to call SHA_Init, read 1MB of the file into RAM, SHA_Update that 1MB, read the next 1MB of the file (overwriting the previous one), call SHA_Update on that 1MB, repeat 1998 more times and then call SHA_Final. This requires only 1MB RAM to hash a 2GB file. 
[stackexchange - cryptographic-hasing-functions-init-update-final](https://crypto.stackexchange.com/questions/52136/cryptographic-hasing-functions-init-update-final)
[stackexchange - what-is-the-purpose-of-update-in-popular-hash-apis](https://crypto.stackexchange.com/questions/9193/what-is-the-purpose-of-update-in-popular-hash-apis)
