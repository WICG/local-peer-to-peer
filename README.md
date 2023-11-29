# Local Peer-to-Peer API

[Local Peer-to-Peer](https://WICG.github.io/local-peer-to-peer/) is a Web platform API proposal for local communication between browsers without the aid of a server.

```js
const conn = await new LP2PRequest(options).start();
const channel = conn.createDataChannel("chat");

channel.onopen = (event) => {
  channel.send("Hi you!");
};
```

For a more in-dept overview of the proposal, please see the [Explainer](EXPLAINER.md).

## Status

This specification is a work in progress.

## Links

- [Explainer](EXPLAINER.md)
- [Specification](https://WICG.github.io/local-peer-to-peer/)

## Feedback

We welcome feedback via the [issue tracker](https://github.com/WICG/local-peer-to-peer/issues) of this GitHub repo. [Contributions](CONTRIBUTING.md) are welcome via pull requests too.
