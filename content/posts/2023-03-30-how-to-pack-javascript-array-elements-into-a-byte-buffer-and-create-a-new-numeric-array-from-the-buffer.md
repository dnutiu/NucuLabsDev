---
author: "Denis Nu\u021Biu"
categories:
- Programming
- Tutorial
date: '2023-03-30T20:43:49+00:00'
guid: https://nuculabs.dev/?p=2693
id: 2693
layout: post
permalink: /2023/03/30/how-to-pack-javascript-array-elements-into-a-byte-buffer-and-create-a-new-numeric-array-from-the-buffer/
tags:
- bit
- bits
- bytes
- JavaScript
- low-level
title: How to Pack JavaScript Array Elements into a Byte Buffer and Create a New Numeric
  Array from the Buffer
---
Hello 👋,


In this short article we’ll talk about packing integers into binary data in JavaScript using the Buffer, ArrayBuffer and DataView classes.


Sometimes in JavaScript, it may be necessary to pack the elements of an array into a byte buffer and then create a new numeric array from the buffer’s contents. This can be achieved using the `DataView` object in JavaScript.


First, create an array of elements you want to pack, for example:


```
let a = [5, 13];
```


Next, create a new `ArrayBuffer` of the required size:


```
let buffer = new ArrayBuffer(8);
```


Create a `DataView` object using this buffer:


```
let view = new DataView(buffer);
```


Loop through each element of the original array, and use the `setInt32` method of the `DataView` object to pack each element into the buffer:


```
for(let i = 0; i < a.length; i++) {
  view.setInt32(i*4, a[i], false); // false for little-endian
}
```


Note that the `setInt32` method takes three arguments:


- The first argument is the byte offset at which to set the value.
- The second argument is the value to set.
- The third argument is a boolean indicating whether to use little-endian or big-endian byte order.


To create a new numeric array from the buffer’s contents, create a new `ArrayBuffer` of the same size as the original buffer:


```
let newBuffer = new ArrayBuffer(8);
```


Create a new `DataView` object using this buffer:


```
let newView = new DataView(newBuffer);
```


Loop through each element of the original array again, and use the `getInt32` method of the original `DataView` object to get the value of each element from the original buffer. Then use the `setInt32` method of the new `DataView` object to set the same value in the new buffer:


```
for (let i = 0; i < a.length; i++) {
  let value = view.getInt32(i*4, false); // get value from original buffer
  newView.setInt32(i*4, value, false); // set value in new buffer
}
```


Finally, create a new numeric array using the new buffer:


```
let newArray = new Int32Array(newBuffer);
```


This will give you a new numeric array containing the same values as the original array, packed into a byte buffer.


Another way to accomplish the task is to use the shortcut:


```
let bytes = new Uint8Array(buf.buffer)
let int32Data = new Int32Array(bytes.buffer)
```


Thanks for reading!


#### full code:


```
let a = [5, 13];
let buffer = new ArrayBuffer(8);
let view = new DataView(buffer);
for(let i = 0; i < a.length; i++) {
    view.setInt32(i*4, a[i], true); // false for little-endian
}


let newBuffer = new ArrayBuffer(8); // create new buffer of size 8
let newView = new DataView(newBuffer);


for (let i = 0; i < a.length; i++) {
    let value = view.getInt32(i*4, true); // get value from original buffer
    newView.setInt32(i*4, value, true); // set value in new buffer
}


let newArray = new Int32Array(newBuffer);
console.log(newArray)
```