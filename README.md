# whived-rpc

[![Build Status](https://github.com/nicholas-mwaura/whived-rpc/actions/workflows/test.yml/badge.svg)](https://github.com/nicholas-mwaura/whived-rpc/actions/workflows/test.yml)
[![NPM Package](https://img.shields.io/npm/v/@nicholas-mwaura/whived-rpc.svg)](https://www.npmjs.org/package/@nicholas-mwaura/whived-rpc)

Whive Client Library to connect to Whive Core (whived) via RPC

## Install

whived-rpc runs on [node](http://nodejs.org/), and can be installed via [npm](https://npmjs.org/):

```bash
npm install @nicholas-mwaura/whived-rpc
```

## Usage

### RpcClient

Config parameters : 

	- protocol : (string - optional) - (default: 'https') - Set the protocol to be used. Either `http` or `https`.
	- user : (string - optional) - (default: 'user') - Set the user credential.
	- pass : (string - optional) - (default: 'pass') - Set the password credential.
	- host : (string - optional) - (default: '127.0.0.1') - The host you want to connect with.
	- port : (integer - optional) - (default: 9998) - Set the port on which perform the RPC command.

Promise vs callback based

  - `require('@nicholas-mwaura/whived-rpc/promise')` to have promises returned
  - `require('@nicholas-mwaura/whived-rpc')` to have callback functions returned
	
### Examples

Config:

```javascript
var config = {
    protocol: 'http',
    user: 'whive',
    pass: 'local321',
    host: '127.0.0.1',
    port: 19998
};
```

Promise based:

```javascript
var RpcClient = require('@nicholas-mwaura/whived-rpc/promise');
var rpc = new RpcClient(config);

rpc.getRawMemPool()
    .then(ret => {
        return Promise.all(ret.result.map(r => rpc.getRawTransaction(r)))
    })
    .then(rawTxs => {
        rawTxs.forEach(rawTx => {
            console.log(`RawTX: ${rawTx.result}`);
        })
    })
    .catch(err => {
        console.log(err)
    })
```

Callback based (legacy):

```javascript
var run = function() {
  var bitcore = require('@nicholas-mwaura/whivecore-lib');
  var RpcClient = require('@nicholas-mwaura/whived-rpc');
  var rpc = new RpcClient(config);

  var txids = [];

  function showNewTransactions() {
    rpc.getRawMemPool(function (err, ret) {
      if (err) {
        console.error(err);
        return setTimeout(showNewTransactions, 10000);
      }

      function batchCall() {
        ret.result.forEach(function (txid) {
          if (txids.indexOf(txid) === -1) {
            rpc.getRawTransaction(txid);
          }
        });
      }

      rpc.batch(batchCall, function(err, rawtxs) {
        if (err) {
          console.error(err);
          return setTimeout(showNewTransactions, 10000);
        }

        rawtxs.map(function (rawtx) {
          var tx = new bitcore.Transaction(rawtx.result);
          console.log('\n\n\n' + tx.id + ':', tx.toObject());
        });

        txids = ret.result;
        setTimeout(showNewTransactions, 2500);
      });
    });
  }

  showNewTransactions();
};
```

### Help

You can dynamically access to the help of each method by doing

```
const RpcClient = require('@nicholas-mwaura/whived-rpc');
var client = new RPCclient({
    protocol:'http',
    user: 'whive',
    pass: 'pass', 
    host: '127.0.0.1', 
    port: 1867
});

var cb = function (err, data) {
    console.log(data)
};

// Get full help
client.help(cb);

// Get help of specific method
client.help('getinfo',cb);
```

## Contributing

Feel free to dive in! [Open an issue](https://github.com/nicholas-mwaura/whive-std-template/issues/new) or submit PRs.

## License

[MIT](LICENSE) &copy; Whive Core Group, Inc.
