## wcs-nodejs-sdk

## Prerequisites
- Cloud Storage is activated.
- The AccessKey and SecretKey are created
- System: Above H5

Notes:
There is no token creation module in Wcs-JavaScript-SDK. For more security, we suggest that customer should set up a server for token calculation.

## Install
#### 1. Direct reference
```
<script type="text/javascript" src="/dist/wcs.min.js"></script>
```
Introduce the file via the ==script== tag, and it will generate an object called WCS globally.


#### 2. Install npm
```
npm install wcs-js-sdk
```

## Iniatialization
- SDK controls upload behavior through a ***uploadObj*** object, which can trigger uploads, callbacks, stops, etc.
- SDK determines whether to use direct upload or multipart upload according to the file size and block size ***BLOCK_SIZE***.    1) If the file size is greater than ***BLOCK_SIZE***: use multipart upload; 2) If the file size is smaller than or equal to ***BLOCK_SIZE***: Use direct upload

```

import * as wangsu from 'wcs-js-sdk'

Other introduce methods:
1. import * as wangsu from 'wcs-js-sdk'           call wangsu.wcsUpload()
2. import { wcsUpload } from 'wcs-js-sdk'         call wcsUpload()  

var uploadObj = wangsu.wcsUpload(file, token, uploadUrl, extraConfig);

Parameters:
file // The file need to be uploaded
token // Token required by the backend server
uploadUrl // Upload URL
extraConfig={
    timeout: 0, //Timeout, default value is 0, it will retry upload when timeout
    concurrentRequestLimit:3, //Concurrency, default value is 3. Notes: The browsers have limitation in the request resources for the connection. For example, Chrome's request limitation is 6, so there will be a situation where if you write 10 concurrently, but only 6 are actually uploaded.
    retryCount:0 //Retry upload, default value is 0.
}

```

### Upload
```
uploadObj.putFile();

```

### Callback the uploading status
```
uploadObj.uploadProgress = function (progress) {}
- progress format
{
    total:{
        loaded: ?, //Loaded size
        size: ?,   //Total file size 
        percent: ? //ratio
    },
    chunks:[     //Block info, it is in array format 
        {loaded: ?, size: ?, percent: ?},
        {loaded: ?, size: ?, percent: ?}
    ]
}

```

### Callback Errors
```
uploadObj.onError = function (error) {}
- error format
{
    code: ?,  // Error code, please note that there isn't code in uploadObj.stop() and timeout retry
    message: "",  //Error info
    isRequestError: true //Is it a request error? That is, after normal upload, the server returns an error. This is a normal error, and there is no need to re-upload.
}

```

### Finish Callback
```
uploadObj.onComplete = function(res){}
- res format
{
    data: jsonObj/String   //The result returned by the server. Note: If the code string is returned directly, the JSON object is returned if the chunk size is exceeded and multipart uploads is used.
}

```

### Stop
```
uploadObj.stop();


```

[Demo](https://github.com/CDNetworks-Object-Storage/wcs-js-sdk/tree/master/test/demo1)
