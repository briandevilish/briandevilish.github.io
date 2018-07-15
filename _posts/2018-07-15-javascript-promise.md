---
title: Javascript Promise Pattern
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
    display: block;
    font-family: monospace;
    white-space: pre;
    margin: 1em 0;
}
</style>
</head>



Promises in JavaScript represent processes which are already happening, which can be chained with callback functions.

Ref: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise

1. Promise.all: Accept an iterable such as array of Promise object. Execute all iterable promise objects in parallel. Stop, when any promise object is rejected/failed.

2. Execute all iterable promise objects in parallel, capture all results of promise regardless of success or failure. Custom example:
<pre>
function settled(promises) {
  var alwaysFulfilled = promises.map( p => {
    return p.then( result => {
      return {result: result, error: undefined}
    }).catch(err => {
      return {result: undefined, error: err}
    })
  })
  return Promise.all(alwaysFulfilled)
}
</pre>

3. Execute callback returning promises in sequential and exit when failure happened.
<pre>
function sequence(arr, callback) {

  return arr.reduce((prevItem, curItem) => {
    return prevItem.then(prevResult => {
      return callback(curItem)
    })
  }, Promise.resolve())
}
</pre>

4. Execute callback returning promises in sequential, capture result from fulfilled promises and exit when failure happened. 
<pre>
function sequenceWithResult(arr, callback) {

  var results = []
  return Promise.resolve(
    arr.reduce((prevItem, curItem) => {
      return prevItem.then(prevResult => {
        return callback(curItem).then(curResult => {
            results.push({result:curResult, error:undefined}) 
          })
      })
    }, Promise.resolve())
  ).then( () => {
    return Promise.resolve(results)
  }).catch(error => {
    results.push({result:undefined, error:error})
    return Promise.reject(results)
  })

}
</pre>

5. Execute callback returning promises in sequential, capture result from fulfilled promises and continue even when there are any failed promises. 
<pre>
function sequenceWithResult2(arr, callback) {
  var results = []
  return Promise.resolve(
    arr.reduce((prevItem, curItem) => {
      return prevItem.then( (prevResult)=> {
        return callback(curItem).then(curResult => {
          results.push({result:curResult, error:undefined}) 
        })
      }).catch(error => {
        results.push({result:undefined, error:error})
      })
    }, Promise.resolve())
  ).then( () => {
    return Promise.resolve(results)
  })  

}
</pre>

