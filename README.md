# winston-azure-storage-transport

Azure Storage transports for `winston >= 3`.

## Install
```
npm install winston@next
npm install winston-azure-storage-transport
npm install azure-storage
```

## Usage
```JavaScript
const { AzureBlobTransport } = require('./index');
const { BlobService } = require('azure-storage');
const winston = require('winston');

const blobTransport = new AzureBlobTransport({
  blobs: new BlobService(),
  containerName: 'mylogs',
  blobName: 'myappendblob',
});

const logger = winston.createLogger({
  level: 'info',
  format: winston.format.json(),
  transports: [ blobTransport ],
});
```

You can write logs 'silently' so that calls to the logger do not block the callback chain. In this case, listen for 'error' events on your AzureBlobTransport to handle IO errors.
```JavaScript
const blobTransport = new AzureBlobTransport({
  blobs: new BlobService(),
  containerName: 'mylogs',
  blobName: 'myappendblob.log',
  silent: true,
}).on('error', console.error);
```

You can customize the blob name using a callback function. The blob will be created if it does not exist.
```JavaScript
const blobTransport = new AzureBlobTransport({
  blobs: new BlobService(),
  containerName: 'mylogs',
  blobName: () => {
    const d = new Date();
    return [
      d.getFullYear(),
      d.getMonth() + 1,
      d.getDate(),
      'myappendblob.log',
    ].join('/');
  },
});
```

You can customize message log. Default using JSON.stringify .
```JavaScript
const blobTransport = new AzureBlobTransport({
  blobs: new BlobService(),
  containerName: 'mylogs',
  format: winston.format.combine(
    winston.format.timestamp()
  ),
  formatMessageLog: (data) => {
      //Custom message log here
      var message = data.message;
      var timestamp = data.timestamp;
      var level = data.level;
      return `${timestamp} [${level}]: ${message}`;
  }
});
```