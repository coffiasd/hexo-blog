---
title: How to quick start web3.storage
date: 2022-10-31 21:44:16
tags:
  - hackathon
  - web3
categories:
  - hackathon
---

## register an account

- Email
- Github (recommand)

## install the client

```
javascript
npm install web3.storage
golang
go get github.com/web3-storage/go-w3s-client
```

## create a client instance

```
//get the access token
function getAccessToken(){
    return process.env.TOKEN
}
//creata an instance
function makeStorageClient(){
    return new Web3storage({token:getAccessToken()})
}
```

## preparing files for uploading

```
function getFiles(){
    const fileInput = document.querySelector('input[type="file"]')
    return fileInput.files
}
```

## upload files to web3.storage

```
//async upload process
async function storageFiles(files){
    const client = makeStorageClient()
    const cid = await client.put(files)
    return cid
}
```

## directory wrapping

after uploading you'll get a cid of the directory and then the entire link gonna be ipfs://<cid>/<filename>
to make a gateway link :

```
https://<cid>.ipfs.<gateway-host>/<filename>
https://<gateway-host>/ipfs/<cid>/<filename>
```

## storing ipfs content archives?

if you already have some files you can use putCar client function.I don't want to jump into this here.

## how to access data we uploaded above ?

- using an IPFS http gateway
- using client library
- ipfs command line
- system command line

## how to query web3.storage

```
async function checkStatus(cid){
    const client = xxxx
    const status = await client.status(cid)
}
```

return data structure

- cid
- created
- dagSize
- pins
- deals

## how to list files uploaded to web3.storage

```
const client = xxxx
for await (const upload of client.list(){
    console.log(upload.name,upload.cid,upload.dagSize)
})
```

## listing the content of an IPFS directory
