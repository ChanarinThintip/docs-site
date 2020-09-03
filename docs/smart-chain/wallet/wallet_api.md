Binance Chain Wallet injects a global API into websites visited by its users at `window.BinanceChain`.

This API borrowed heavily from API metamask provided considered the massive adoption. So web3 site developer could easily connect their app with the Binance Chain Wallet. It allows websites to request users' Binance Smart Chain accounts, read data from the blockchain the user is connected to, and suggest that the user sign messages and transactions.
The presence of the provider object indicates an Binance Chain user.

The API this extension wallet provides includes API specified by [EIP-1193](https://eips.ethereum.org/EIPS/eip-1193) and API defined by [MetaMask](https://docs.metamask.io/guide/ethereum-provider.html) (including some massively relied legacy ones).

## Table of Contents

## Upcoming Breaking Changes

!!! warning

    Important Information

    On **November 16, 2020**, MetaMask is making changes to their provider API that will be breaking for some web3 sites.
    These changes are _upcoming_, but you can prepare for them today.
    Follow [this GitHub issue](https://github.com/MetaMask/metamask-extension/issues/8077) for updates.

    In this implementation, some APIs will be supported as [Legacy API](#legacy-api) (For example we will still implement the `chainIdChanged` on BinanceChain object until MetaMask formally deprecate it).

## Basic Usage

For any non-trivial Binance Smart Chain web application — a.k.a. web3 site — to work, you will have to:

- Detect the Binance Smart Chain provider (`window.BinanceChain`)
- Detect which Binance Smart Chain network the user is connected to
- Get the user's Binance Smart Chain account(s)

You can learn how to accomplish the other two by reviewing the snippet in the [Using the Provider section](#using-the-provider).

The provider API is all you need to create a full-featured web3 application.

That said, many developers use a convenience library, such as [ethers](https://www.npmjs.com/package/ethers) and [web3.js](https://www.npmjs.com/package/web3), instead of using the provider directly.
If you are in need of higher-level abstractions than those provided by this API, we recommend that you use a convenience library.

## Chain IDs

::: warning
At the moment, the [`BinanceChain.chainId`](#ethereum-chainid) property and the [`chainChanged`](#chainchanged) event should be preferred over the `eth_chainId` RPC method.
Their chain ID values are correctly formatted, per the table below.

`eth_chainId` returns an incorrectly formatted (0-padded) chain ID for the chains in the table below, e.g. `0x01` instead of `0x1`.
See the [Upcoming Breaking Changes section](#upcoming-breaking-changes) for details on when the `eth_chainId` RPC method will be fixed.

Custom RPC endpoints are not affected; they always return the chain ID specified by the user.
:::

These are the IDs of the Binance Smart chains that Binance Chain Wallet supports by default.

| Hex  | Decimal | Network                                    |
| ---- | ------- | ------------------------------------------ |
| 0x38 | 56      | Binance Smart Chain Main Network (MainNet) |
| 0x61 | 97      | Binance Smart Chain Test Network           |

## Properties

### BinanceChain.chainId

!!! warning

    The value of this property can change at any time, and should not be exclusively relied upon. See the [`chainChanged`](#chainchanged) event for details.

**NOTE:** See the [Chain IDs section](#chain-ids) for important information about the Binance Chain Wallet provider's chain IDs.


A hexadecimal string representing the current chain ID.

### BinanceChain.autoRefreshOnNetworkChange

As the consumer of this API, it is your responsbility to handle chain changes using the [`chainChanged` event](#chainChanged).
We recommend reloading the page on `chainChange` unless you have good reason not to.


By default, this property is `true`.

If this property is truthy, Binance Chain Wallet will reload the page in the following cases:

- When the connected chain (network) changes, if `window.BinanceChain` has been accessed during the page lifecycle
- When `window.BinanceChain` is accessed, if the connected chain (network) has changed during the page lifecycle

To disable this behavior, set this property to `false` immediately after detecting the provider:

```javascript
BinanceChain.autoRefreshOnNetworkChange = false;
```

## Methods

### BinanceChain.isConnected()

Please refer to [MetaMask Doc](https://docs.metamask.io/guide/ethereum-provider.html#ethereum-isconnected), the only difference is we injected a different object.

```typescript
BinanceChain.isConnected(): boolean;
```

### BinanceChain.request(args)

Please refer to [MetaMask Doc](https://docs.metamask.io/guide/ethereum-provider.html#ethereum-request-args), the only difference is we injected a different object.

```typescript
interface RequestArguments {
  method: string;
  params?: unknown[] | object;
}

BinanceChain.request(args: RequestArguments): Promise<unknown>;
```

#### Example

The code snippet below is as same as [MetaMask's example](https://docs.metamask.io/guide/ethereum-provider.html#example), only difference is we injected a different object.

```javascript
params: [
  {
    from: '0xb60e8dd61c5d32be8058bb8eb970870f07233155',
    to: '0xd46e8dd67c5d32be8058bb8eb970870f07244567',
    gas: '0x76c0', // 30400
    gasPrice: '0x9184e72a000', // 10000000000000
    value: '0x9184e72a', // 2441406250
    data:
      '0xd46e8dd67c5d32be8d46e8dd67c5d32be8058bb8eb970870f072445675058bb8eb970870f072445675',
  },
];

BinanceChain
  .request({
    method: 'eth_sendTransaction',
    params,
  })
  .then((result) => {
    // The result varies by by RPC method.
    // For example, this method will return a transaction hash hexadecimal string on success.
  })
  .catch((error) => {
    // If the request fails, the Promise will reject with an error.
  });
```

## Events

Please refer to [MetaMask Doc](https://docs.metamask.io/guide/ethereum-provider.html#events), the only difference is we injected a different object.


```javascript
BinanceChain.on('accountsChanged', (accounts) => {
  // Handle the new accounts, or lack thereof.
  // "accounts" will always be an array, but it can be empty.
});

BinanceChain.on('chainChanged', (chainId) => {
  // Handle the new chain.
  // Correctly handling chain changes can be complicated.
  // We recommend reloading the page unless you have a very good reason not to.
  window.location.reload();
});
```

### connect

Please refer to [MetaMask Doc](https://docs.metamask.io/guide/ethereum-provider.html#connect), the only difference is we injected a different object.

```typescript
interface ConnectInfo {
  chainId: string;
}

BinanceChain.on('connect', handler: (connectInfo: ConnectInfo) => void);
```

### disconnect

Please refer to [MetaMask Doc](https://docs.metamask.io/guide/ethereum-provider.html#disconnect), the only difference is we injected a different object.

```typescript
BinanceChain.on('disconnect', handler: (error: ProviderRpcError) => void);
```

### accountsChanged

Please refer to [MetaMask Doc](https://docs.metamask.io/guide/ethereum-provider.html#accountschanged), the only difference is we injected a different object.

```typescript
BinanceChain.on('accountsChanged', handler: (accounts: Array<string>) => void);
```

### chainChanged

Please refer to [MetaMask Doc](https://docs.metamask.io/guide/ethereum-provider.html#chainchanged), the only difference is we injected a different object.

```typescript
BinanceChain.on('chainChanged', handler: (chainId: string) => void);
```

```javascript
BinanceChain.on('chainChanged', (_chainId) => window.location.reload());
```

### message

Please refer to [MetaMask Doc](https://docs.metamask.io/guide/ethereum-provider.html#message), the only difference is we injected a different object.


```typescript
interface ProviderMessage {
  type: string;
  data: unknown;
}

BinanceChain.on('message', handler: (message: ProviderMessage) => void);
```

## Errors

Please refer to [MetaMask Doc](https://docs.metamask.io/guide/ethereum-provider.html#errors), the only difference is we injected a different object.

## Using the Provider

This snippet explains how to accomplish the three most common requirements for web3 sites:

- Detect which BinanceChain network the user is connected to
- Get the user's BinanceChain account(s)

```
/**********************************************************/
/* Handle chain (network) and chainChanged (per EIP-1193) */
/**********************************************************/

// Normally, we would recommend the 'eth_chainId' RPC method, but it currently
// returns incorrectly formatted chain ID values.
let currentChainId = BinanceChain.chainId;

BinanceChain.on('chainChanged', handleChainChanged);

function handleChainChanged(_chainId) {
  // We recommend reloading the page, unless you must do otherwise
  window.location.reload();
}

/***********************************************************/
/* Handle user accounts and accountsChanged (per EIP-1193) */
/***********************************************************/

let currentAccount = null;
BinanceChain
  .request({ method: 'eth_accounts' })
  .then(handleAccountsChanged)
  .catch((err) => {
    // Some unexpected error.
    // For backwards compatibility reasons, if no accounts are available,
    // eth_accounts will return an empty array.
    console.error(err);
  });

// Note that this event is emitted on page load.
// If the array of accounts is non-empty, you're already
// connected.
BinanceChain.on('accountsChanged', handleAccountsChanged);

// For now, 'eth_accounts' will continue to always return an array
function handleAccountsChanged(accounts) {
  if (accounts.length === 0) {
    // Binance Chain Wallet is locked or the user has not connected any accounts
    console.log('Please connect to Binance Chain Wallet.');
  } else if (accounts[0] !== currentAccount) {
    currentAccount = accounts[0];
    // Do any other work!
  }
}

/*********************************************/
/* Access the user's accounts (per EIP-1102) */
/*********************************************/

// You should only attempt to request the user's accounts in response to user
// interaction, such as a button click.
// Otherwise, you popup-spam the user like it's 1999.
// If you fail to retrieve the user's account(s), you should encourage the user
// to initiate the attempt.
document.getElementById('connectButton', connect);

function connect() {
  BinanceChain
    .request({ method: 'eth_requestAccounts' })
    .then(handleAccountsChanged)
    .catch((err) => {
      if (err.code === 4001) {
        // EIP-1193 userRejectedRequest error
        // If this happens, the user rejected the connection request.
        console.log('Please connect to MetaMask.');
      } else {
        console.error(err);
      }
    });
}
```

## Legacy API

!!! warning
    You should **never** rely on any of these methods, properties, or events in practice.

This section documents MetaMask's legacy provider API.

To be compatible with existing dApps that support MetaMask, Binance Chain Wallet implement them as well, but please don't rely on them. We may deprecate them soon in future.

## Legacy Properties

### BinanceChain.networkVersion (DEPRECATED)

Please refer to [MetaMask Doc](https://docs.metamask.io/guide/ethereum-provider.html#legacy-properties), the only difference is we injected a different object.

### BinanceChain.selectedAddress (DEPRECATED)

Please refer to [MetaMask Doc](https://docs.metamask.io/guide/ethereum-provider.html#ethereum-selectedaddress-deprecated), the only difference is we injected a different object.

## Legacy Methods

### BinanceChain.enable() (DEPRECATED)

Please refer to [MetaMask Doc](https://docs.metamask.io/guide/ethereum-provider.html#ethereum-enable-deprecated), the only difference is we injected a different object.

### BinanceChain.sendAsync() (DEPRECATED)

Please refer to [MetaMask Doc](https://docs.metamask.io/guide/ethereum-provider.html#ethereum-sendasync-deprecated), the only difference is we injected a different object.


```typescript
interface JsonRpcRequest {
  id: string | undefined;
  jsonrpc: '2.0';
  method: string;
  params?: Array<any>;
}

interface JsonRpcResponse {
  id: string | undefined;
  jsonrpc: '2.0';
  method: string;
  result?: unknown;
  error?: Error;
}

type JsonRpcCallback = (error: Error, response: JsonRpcResponse) => unknown;

BinanceChain.sendAsync(payload: JsonRpcRequest, callback: JsonRpcCallback): void;
```


### BinanceChain.send() (DEPRECATED)

Please refer to [MetaMask Doc](https://docs.metamask.io/guide/ethereum-provider.html#ethereum-send-deprecated), the only difference is we injected a different object.

```typescript
BinanceChain.send(
  methodOrPayload: string | JsonRpcRequest,
  paramsOrCallback: Array<unknown> | JsonRpcCallback,
): Promise<JsonRpcResponse> | void;
```

This method behaves unpredictably and should be avoided at all costs.
It is essentially an overloaded version of [`BinanceChain.sendAsync()`](#BinanceChain-sendasync-deprecated).

`BinanceChain.send()` can be called in three different ways:

```typescript
// 1.
BinanceChain.send(payload: JsonRpcRequest, callback: JsonRpcCallback): void;

// 2.
BinanceChain.send(method: string, params?: Array<unknown>): Promise<JsonRpcResponse>;

// 3.
BinanceChain.send(payload: JsonRpcRequest): unknown;
```

You can think of these signatures as follows:

1. This signature is exactly like `BinanceChain.sendAsync()`

2. This signature is like an async `BinanceChain.sendAsync()` with `method` and `params` as arguments, instead of a JSON-RPC payload and callback

3. This signature enables you to call the following RPC methods synchronously:

   - `eth_accounts`
   - `eth_coinbase`
   - `eth_uninstallFilter`
   - `net_version`

## Legacy Events

### close (DEPRECATED)

Please refer to [MetaMask Doc](https://docs.metamask.io/guide/ethereum-provider.html#close-deprecated), the only difference is we injected a different object.

```typescript
BinanceChain.on('close', handler: (error: Error) => void);
```

### chainIdChanged (DEPRECATED)

Please refer to [MetaMask Doc](https://docs.metamask.io/guide/ethereum-provider.html#chainidchanged-deprecated), the only difference is we injected a different object.

```typescript
BinanceChain.on('chainChanged', handler: (chainId: string) => void);
```

### networkChanged (DEPRECATED)

Please refer to [MetaMask Doc](https://docs.metamask.io/guide/ethereum-provider.html#networkchanged-deprecated), the only difference is we injected a different object.

```typescript
BinanceChain.on('networkChanged', handler: (networkId: string) => void);
```

### notification (DEPRECATED)

Please refer to [MetaMask Doc](https://docs.metamask.io/guide/ethereum-provider.html#notification-deprecated), the only difference is we injected a different object.

```typescript
BinanceChain.on('notification', handler: (payload: any) => void);
```
