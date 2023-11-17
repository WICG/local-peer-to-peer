# Local Peer-to-Peer API

[Local Peer-to-Peer](https://WICG.github.io/local-peer-to-peer/) is a Web platform API proposal for local communication between browsers without the aid of a server.

```js
let session = await navigator.lp2p.findPeers();

await peer.send("Hello there!");
```

For a more in-dept overview of the proposal, please see the [Explainer](EXPLAINER.md).

## Status

This specification is a work in progress.

## Links

- [Explainer](EXPLAINER.md)
- [Specification](https://WICG.github.io/local-peer-to-peer/)

## Feedback

We welcome feedback [issue tracker](https://github.com/WICG/local-peer-to-peer/issues) of this GitHub repo. [Contributions](CONTRIBUTING.md) are welcome via pull requests too.
