# Network requests status tracking for Axios

This package can be used to trace status of all Axios requests for the purpose of application-wide progress indicator

## Installation:

`npm install axios-status --save`

## Usage:

First, create a new instance of AxiosStatus, and register your axios instances with it

```js
const axiosStatus = new AxiosStatus()

axiosStatus.register(axios)
axiosStatus.register(mySpecialAxiosInstance)
```

Then, you can subscribe on `AxiosStatus` events to be able to show them in UI. 

* The `busy` event has values of `true / false` and signals that there are XHR requests in progress
* The `offline` event has values of `true / false` and signals that request failed because network is temporariy down
* The `timer` event emits a number that signals seconds to next auto-retry


```js
axiosStatus.on('busy', (val) => this.loadingProgress = val)
axiosStatus.on('offline', (val) => this.disconnected = val)
axiosStatus.on('timer', (val) => this.secondsToReconnect = val)
```

To be able to correctly run a callback on auto-retry, you need to use `axiosStatus.request` with a `success` callback 

```js
axiosStatus.request({
  method: 'get',
  url: API_URL + '/me',
  success: (res) => {
    this.user = res.data
  }
})
```

To retry manually, you can just call `axiosStatus.retry()` to retry all calls initiated when offline