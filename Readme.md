# wcs-nodejs-sdk

## Install
### 1. Install by npm
```
npm install wcs-nodejs-sdk
```
### 2. Install manually

Download code from GitHub, and type `npm install` under root directory.

## How to use
Use **require** if you installed by npm.
```
const wcs = require('wcs-nodejs-sdk');

let client = new wcs.wcsClient(config);
```

Require file **index.js** under root directory if you installed manually.
```
const wcs = require('wcs-nodejs-sdk/index');

let client = new wcs.wcsClient(config)
```

## Configuration

Before using wcs-nodejs-sdk, you have to:
- Get AK/SK
- Create a bucket

After finish the above, you should create a config file in your project as following:
```
var config = {
    AccessKey: '<YOUR ACCESS KEY>',         
    SecretKey: '<YOUR SECRET KEY>',        
    UploadDomain: '<YOUR UPLOAD DOMAIN>',   
    MgrDomain: '<YOUR MGR DOOMAIN>',        
    BlockSize: <SIZE OF BLOCK>,         // 4194304 by default(4MB, not recommended to change). If you have to change the size, it has to be integer multiple of 4MB with int type 
    HttpTimeout: <HTTP TIMEOUT>,        // int type, 120000（120 seconds）by defualt
};

module.exports = config;
```

## Normal upload
We reccomend to use normal upload for file with size less than 20MB.

example
```
const wcs = require('wcs-nodejs-sdk');
const config = require('./config');

let putPolicy = {
    scope: bucket+':'+key,
    deadline: '<deadline>',
};

let client = new wcs.wcsClient(config);
var callback = function(err, data, res) {
    console.log('callback');
    if (err) {
        console.log(err);
    }
    else {
        console.log(res.statusCode);
        console.log(wcs.utils.urlSafeBase64Decode(data));
    }
}

// normal upload
let filePath = __dirname+'/test';
client.uploadByPath(filePath, putPolicy, null, callback);
```
## Multipart upload
- Multipart upload is recommended to use when size of upload file is larger than 20MB.
- Size of blocks for multipart can be customized. But it has to be integer multiple of 4MB.
- Upload will be resumed on break-point when upload is interrupted if you have specified a recordfile to save upload logs.

example

```
const wcs = require('wcs-nodejs-sdk');
const config = require('./config');

let putPolicy = {
    scope: bucket+':'+key,
    deadline: '<deadline>',
};

let client = new wcs.wcsClient(config);
var callback = function(err, data, res) {
    console.log('callback');
    if (err) {
        console.log(err);
    }
    else {
        console.log(res.statusCode);
        console.log(wcs.utils.urlSafeBase64Decode(data));
    }
}

// callback multipart upload progress
var progressCallback = function(readLength, fileSize) {
    console.log(readLength + '/' + fileSize + ' finished');
}

// multipart upload
let filePath = __dirname+'/test20M';

// to resume upload on break-point
let recordFile = __dirname+'/resume.record';
client.resumeUploadByPath(filePath, putPolicy, {deadline:3, mimeType: 'application/text', progressCallback: progressCallback}, callback);
```
To have more examples in **wcs-nodejs-sdk/demos**.
