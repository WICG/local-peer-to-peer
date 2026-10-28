# Local Peer-to-Peer API Explained

The Local Peer-to-Peer API enables browsers to connect securely over a [local communication medium](https://wicg.github.io/local-peer-to-peer/#local-communication-medium), without the aid of a server in the middle.

The API provides a powerful new building block for developers while ensuring a seamless, secure and privacy preserving experience for the user. By putting the user-agent in control during discovery and authentication, bare minimal [communication medium topology](https://wicg.github.io/local-peer-to-peer/#communication-medium-topology) information is exposed to an origin.

Please refer to the [specification](https://WICG.github.io/local-peer-to-peer/) for the the formal draft spec.

### Motivation

The local network is not a first-class citizen of the web. It’s easier for a browser to trust a faraway server than your NAS, TV or thermostat sitting right next to you.

![motivation](./images/motivation.svg)

As a blast from the past:

> Tim Berners-Lee's [vision for the World Wide Web](https://www.w3.org/People/Berners-Lee/1996/ppf.html) was close to a P2P network [...] The early Internet was more open than the present day, where _two machines connected to the Internet could send packets to each other_ without firewalls and other security measures. ([Wikipedia](https://en.wikipedia.org/wiki/Peer-to-peer#Historical_development))

This proposal sets to make a part of this Tim's vision a reality while adhering to the modern security and privacy requirements expected of modern web capabilities.

### Goals

Build a generic Local Peer-to-Peer API and provide an arbitrary bidirectional communication channel to the web for devices in the context of a [local communication medium](https://wicg.github.io/local-peer-to-peer/#local-communication-medium), including:

- Methods to discover, request, and connect to peers on the local communication medium
- Listeners to notify if these method calls succeed or fail
- Listeners to notify if the connection is received or its status is updated
- Means to send and receive data after connection to a peer device has been established
- Means to enable secure HTTPS connections on the local communication medium

The API is transport-agnostic. It is designed to be backed by an authenticated, streams-based transport, and the specification defines it against a small set of abstract operations (discover, authenticate, connect, open channels). A transport binding maps these onto a concrete protocol. As a commitment to an open standards-based implementation path, the specification defines one binding, to the [Open Screen Network Protocol](https://www.w3.org/TR/openscreen-network/). Other bindings are anticipated, for example WebTransport over QUIC with peers authenticated on the local network as explored by the IETF [SETTLE](https://datatracker.ietf.org/wg/settle/about/) working group.

### Non-goals

- Providing direct access to TCP/UDP sockets.
- Compromising Cross-Origin Resource Sharing (CORS) in any way.

## Use Cases

### UC1: Offline collaboration

- Collaboration tools that work during an internet outage or emergency situations
- Ephemeral groups support: Share files to a group with a single “push” vs. sending to each friend one at a time

![G](./images/n2.svg)

Figure 1: Collaborate using local communication in Google Doc

### UC2: Disaster relief

- The ability for Web-based tools to work on ad-hoc networks in the absence of internet infrastructure.
- An app that allows humanitarian field workers in remote areas with no connectivity to gather, synchronize, review, and edit data offline for several days, the data can then be synchronized with the central server when internet connection becomes available
- A non-profit educational organization is concerned about the large file download size required for on-device speech recognition in the browser. This is problematic in low bandwidth, high latency environments such as classrooms, particularly in emerging countries. Their goal is for one student to download the file first and then easily share/distribute it with classmates without low speed internet connections any more to save time and cost

### UC3: Cross-device workflows

- Send and receive files instantly, including photos or videos, between mobile phone, tablet, and personal computer without using mobile data or internet connection
- Add the "Import file nearby" and “Export to nearby” buttons in web version of Figma on desktop to access images from mobile devices"
- Open files in "Nearby" tab in "Open a file" dialog of Google doc
- Video editing web app that allows users to ingest footage directly from their phone.
- Seamlessly sync private keys and other identity credentials across personal devices, securely transfer one-time pads to encrypt and decrypt messages

![Web Drop](./images/drop.svg)

Figure 2: Web Drop, an In-App Sharing feature based on Local Peer-to-Peer API compare with cloud-client solution

### UC4: Home Services & IoT

- Seamlessly connecting to your NAS or your home security system. Avoiding the need for workarounds including self-signed certificates or a cloud proxy services.
- Allow web apps to connect to your robot mower, vacuum or robotic assistant folding the laundry.
- Avoiding the need for companion native apps for services running in the home environment, e.g.: Home Assistant, Plex, BitWarden, NextCloud, ...
- Users value the ability to connect to a baby monitor even when their ISP or a cloud service is out of action. A huge percentage of the time the viewer and the monitor are on the same LAN. There are cost/privacy benefits too. Reference: [#50](https://github.com/WICG/local-peer-to-peer/issues/50).

### UC5: Local multi-player

- In-App Sharing, quickly share group photos or videos with friends without relying on cloud services
- Run a game in web app on the smart TV, use mobile phone as the game controller via this local peer-to-peer API to send control messages

- Run a 2 players web game on two mobile phones, synching messages between two players instantly

![Game 1](./images/n3.svg)

Figure 3: Play web game cross smart TV and mobile phone

### Requirements

The following are the high-level requirements derived from the use cases:

- R1: Discover device(s) on the local communication medium
- R2: Advertise yourself to device(s) on the local communication medium
- R3: Establish a bi-directional communication channel between two devices on the local communication medium
- R4: Allow secure connection to web pages hosted on the local communication medium.
- R5: User consent and delegation per web origin.

### Prerequisites

What is a prerequisite for all these use cases is that the participating devices can be connected via a [local communication medium](https://wicg.github.io/local-peer-to-peer/#local-communication-medium) because they are physically nearby. Connections need an IP link between the devices, direct or through the local network, for example Wi-Fi via an access point, Wi-Fi Direct or Ethernet. Discovery and the trust ceremony may also use other channels, such as Bluetooth Low Energy, NFC or a QR code. This connection technology and its details are abstracted out by both the Web API exposed to web developers as well as the UI/UX visible to the user.

In summary, the following are the prerequisites:

- The participating devices share a local communication medium.

### References

- https://github.com/WICG/proposals/issues/103
- https://github.com/WICG/private-network-access/issues/23
- https://github.com/WICG/local-peer-to-peer/issues/19
- https://github.com/ipfs/local-offline-collab
- https://offlinefirst.org/
- https://github.com/pion/offline-browser-communication

## Solution Approach

The Local Peer-to-Peer API lets a page connect directly to a nearby device or browser. The user agent discovers peers and mutually authenticates them, for example with a PIN the user enters once. The user then grants the page access to a specific peer, and the page gets an authenticated connection carrying data channels or WebTransport sessions. Discovery and authentication are defined by a transport binding, such as the Open Screen Network Protocol.

_Note_: The same trust anchors can also validate HTTPS servers on the local communication medium, see the separate [Local HTTPS](https://wicg.github.io/local-peer-to-peer/local-https.html) draft.

### Architecture

The main concepts:

- **Peer:** a device or browser on the local communication medium, or a service running on one.
- **Advertising and discovery:** a page can make its user agent discoverable, or look for peers, possibly over channels other than the IP link. Discovery results are untrusted hints, and are never exposed to pages.
- **Trust ceremony:** a one-time step the user confirms, such as entering a PIN, scanning a QR code or an NFC tap. It mutually authenticates two peers and adds a trust anchor.
- **Local trust store:** the trust anchors the user added, kept separate from the Web PKI root store. A peer is trusted if its credentials chain to an anchor. Anchors can also be parties that vouch for devices, such as a hub.
- **Peer grant:** the user's consent, given in a picker, for one origin to use a peer. A grant is for one peer; with Local HTTPS, it also covers the names under that device's name, such as its services.
- **Session:** an authenticated connection with a peer for one origin. The side that starts it is the client, and the side that accepts it is the server. The requesting origin is asserted by the peer's user agent.
- **Channels:** data channels and WebTransport sessions, carried over a Local Peer-to-Peer session.
- **Transport binding:** maps all of the above onto a concrete protocol over IP, such as the Open Screen Network Protocol.

### API Design

Peer-to-Peer communication starts with service discovery. For this purpose, the LP2PReceiver API allows an origin to advertise itself. The LP2PRequest API allows discovery of a peer on the local communication medium. The design of these APIs is inspired by the work of the Presentation API's PresentationRequest & PresentationReceiver. Data exchange itself is provided by two APIs: `LP2PDataChannel` & `LP2PQuicTransport`. The [LP2PDataChannel API](https://wicg.github.io/local-peer-to-peer/#lp2p-data-channel) is inspired by the RTCDataChannel API. This API caters to simple message passing use-cases and developers familiar with WebRTC. The API design augments that of the WebRTC RTCDataChannel API. The [LP2PQuicTransport API](https://wicg.github.io/local-peer-to-peer/#lp2p-quic-transport) design is inspired by the WebTransport API. This API is designed for use-cases where more direct control over the underlying transport is preferred.

## Shorthand APIs

The specification provides shorthand APIs, in the `lp2p` namespace, for the most common use cases. More granular APIs are discussed below.

```js
// Peer A
for await (const transport of lp2p.makeDiscoverable({ ...options })) {
  // Incoming transports are already ready.
}
```

```js
// Peer B
const transport = await lp2p.connect({ ...options });
```

`makeDiscoverable()` returns one stream of incoming transports across all connections, combining an `LP2PReceiver` with an `LP2PQuicTransportListener` on each of its connections, which it owns. `connect()` returns a promise for a transport, which resolves once a peer is picked and the session is up, combining an `LP2PRequest` with `new LP2PQuicTransport(connection)`; it creates no listener, and a `signal` option cancels it. On both sides, `transport.connection` gives access to the connection, to open data channels or `close()` it. The shorthand covers transports only: a connection over which the peer only opens data channels never shows up in `makeDiscoverable()`. See below for the full APIs.

_Note_: the shorthand APIs are a work in progress and may change, see [#35](https://github.com/WICG/local-peer-to-peer/issues/35).

## Peer discovery

Before any connection can be established, two peers on the local communication medium must find each other. This is done using the `LP2PReceiver` and `LP2PRequest` interfaces:

```js
// Peer A
const receiver = new LP2PReceiver({
  nickname: "Peer A",
  // Optional: also accept connections from pages of another origin.
  allowedOrigins: ["https://partner.example"],
});

receiver.onconnection = (e) => {
  const conn = e.connection;
  console.log("Receiver: Got a connection!");
  conn.onclose = () => console.log("Receiver: Connection closed.");
};

// Asks the user for permission to advertise.
await receiver.start();

// Later: stop advertising. Existing connections stay open.
receiver.stop();
```

Please refer to the [LP2PReceiver Interface](https://wicg.github.io/local-peer-to-peer/#lp2p-receiver) in the specification for more details.

```js
// Peer B
const request = new LP2PRequest({
  nickname: "Peer B",
});

// Shows a peer picker. Rejects with NotAllowedError if the user dismisses
// it, or if the chosen peer refuses the connection.
const conn = await request.start();
console.log("Requester: Got a connection!");

// Later: close the connection on both sides.
conn.close();
```

Please refer to the [LP2PRequest Interface](https://wicg.github.io/local-peer-to-peer/#lp2p-request) in the specification for more details.

_Note_: The exact attributes used for peer discovery and filtering are still being discussed, see [#15](https://github.com/WICG/local-peer-to-peer/issues/15)

## Simple message exchange

Once two peers have found each other, they can establish a connection. The `LP2PDataChannel` interfaces allow sending and receiving messages over the connection:

```js
// Peer A
conn.ondatachannel = (e) => {
  const channel = e.channel;

  channel.send("Good day to you, requester!");
};
```

```js
// Peer B
const channel = conn.createDataChannel("My Channel");

channel.onmessage = (e) => {
  console.log(`Receiver: Received message: ${e.data}`);
};
```

Please refer to the [LP2PDataChannel Interface](https://wicg.github.io/local-peer-to-peer/#lp2p-data-channel) in the specification for more details.

## Advanced data exchange

The `LP2PQuicTransportListener` and `LP2PQuicTransport` interfaces allow opening a [WebTransport](https://www.w3.org/TR/webtransport) connection between two peers.

```js
// Peer A
receiver.onconnection = async (e) => {
  const listener = new LP2PQuicTransportListener(e.connection);

  for await (const transport of listener.incomingTransports) {
    const stream = await transport.createBidirectionalStream();
  }
};
```

```js
// Peer B
const transport = new LP2PQuicTransport(conn);

await transport.ready;
```

Either peer can open a transport on a connection; the other receives it through its listener. Closing a transport leaves the connection open, while closing the connection closes its transports.

Please refer to the [LP2PQuicTransport Interface](https://wicg.github.io/local-peer-to-peer/#lp2p-quic-transport) in the specification for more details.

## User Interaction Considerations

ℹ️ This section is informative.

This section represents concepts of how a user could discover, connect and share files from one device to the other device nearby.

![User flow](./images/userflow.svg)

Figure 4: User flow to discover and share files

## Security and Privacy

The Local Peer-to-Peer API has been designed specifically with security and privacy in mind. Please refer to the [Security and Privacy Self-Review](./security-privacy-questionnaire.md) for more details. The spec separates three guarantees: peer authentication, origin isolation in each browser, and application authorization; see [Security and privacy considerations](https://wicg.github.io/local-peer-to-peer/#security-and-privacy-considerations).

## Considered Alternatives

When a user wants to connect between two devices on the same local communication medium, for example to another device nearby—be it another device the user owns or that of a friend—the user has multiple ways to accomplish this task:

1. _A cloud service_. The web has many ways of connections to a third-party cloud service: HTTP, WebSocket or WebTransport. However, all of these methods require a round trip through the internet. This is inherently dependant on external resources, it consumes network bandwidth and can be slow or costly and has privacy implications in all but the strongest E2E encryption schemes.

2. _A local server_. Many modern Web security measures rely on the presence of naming, signaling and certificate authorities. Local use-cases where these authorities are not readily available have started lagging behind in user experience or are not supported altogether. A local solution involves knowing IPs, ports and accepting/ignoring a plethora of "Not secure" warnings to get going. This falls short of the user-friendliness that one can expect of—nowadays ubiquitous—cloud services.

3. _A WebRTC connection_. While WebRTC is a P2P protocol, it still requires a setup step usually referred to as 'signaling'. There is no good way to perform this step without relying on an existing connection between peers, commonly a cloud service is used.

None of these solutions to this seemingly common task provide a compelling user experience. When the devices share a local communication medium the user's expectation is the connection process should be as seamless as any physical interaction. With a variety of [local communication](https://wicg.github.io/local-peer-to-peer/#local-communication) technologies widely supported on today's devices we believe this user experience can be vastly improved.

This illustrates the gap for an optimized path for [local communication](https://wicg.github.io/local-peer-to-peer/#local-communication) by web applications.

![problem](./images/problem.svg)

Figure 5: Proposed Web Local Peer-to-Peer along with other existing options

4. The _Web Share and Web Share Target_ provide a minimal API to share text and files to a user-selected share target, including to another website, utilizing the sharing mechanism of the underlying operating system.

While the Web Share API partially satisfies the requirement R2 set forth above, the Web Share API by its design defines a minimal API surface that is likely not amenable to extensions required to support additional use cases and requirements outlined in this explainer. Notably, the Web Share API is a "push-based" API where content is pushed from one device to another device while the Local Peer-to-Peer API is catering to both the "push-based" as well as "pull-based" use cases as illustrated by "drop files here and share" and "import file nearby" concepts respectively. From the UX perspective, the Local Peer-to-Peer API allows for a more seamless in-web app experience in use cases where a system-provided share facility would disrupt the user flow.

Applications may still choose to use an internet-based connection, such as WebRTC, when no local link is available. To make that easy and to minimize conceptual weight for web developers, this API attempts to align with the established conventions and API semantics of other communication APIs such as WebTransport API, WebRTC, Fetch, and Presentation API, where applicable.

### Previous and related work

The Local Peer-to-Peer approach differs from earlier efforts by putting the user-agent in control during device discovery and mutual authentication. This approach allows exposing bare minimal information about the device to an origin. The list of discovered devices is never shared with the origin and no peer attributes are shared with the origin at all: only the user agent's user interface shows the peer's display name and nickname, to pick or accept a peer. No [communication medium topology](https://wicg.github.io/local-peer-to-peer/#communication-medium-topology) information such as IP addresses is ever shared with an origin.

#### Active

- **Precedents.** The following works guide the design of the Local Peer-to-Peer API: [Open Screen Network Protocol](https://www.w3.org/TR/openscreen-network/), [Presentation API](https://www.w3.org/TR/presentation-api/), [Remote Playback API](https://www.w3.org/TR/remote-playback/), [WebRTC](https://www.w3.org/TR/webrtc/), [ORTC (draft)](https://draft.ortc.org/) and [WebTransport](https://www.w3.org/TR/webtransport/).
- **P2P WebTransport.** The W3C WebTransport Working Group's [2026 charter](https://www.w3.org/2026/07/webtransport-wg-charter.html) says the group "is also considering incubating mechanisms for peer-to-peer capability", tracked in [w3c/webtransport#590](https://github.com/w3c/webtransport/issues/590). The WebTransport Community Group's [P2P WebTransport draft](https://w3c.github.io/p2p-webtransport/) defines a QUIC API for peer-to-peer connections. A WebTransport-based binding could align Local Peer-to-Peer with that work.
- **IETF SETTLE.** The [SETTLE](https://datatracker.ietf.org/wg/settle/about/) working group works on TLS access to local resources. See its [problem statement and requirements](https://datatracker.ietf.org/doc/draft-sweet-settle-requirements/) and [Public Key Hash for Local Domains](https://datatracker.ietf.org/doc/draft-wing-settle-public-key-hash/). Local HTTPS aims to meet these requirements, including the referee model.
- **"Expanding what HTTPS means".** Martin Thomson's [post](https://lowentropy.net/posts/local-https/) explores giving names such as `printer.local` a unique cryptographic identity. Local HTTPS takes a different route to the same goal: name-constrained trust anchors instead of extending the origin with the server's key.
- **Web Bluetooth.** [Web Bluetooth](https://webbluetoothcg.github.io/web-bluetooth/) gives pages access to the services of nearby Bluetooth Low Energy devices. The Local Peer-to-Peer API instead provides authenticated connections between peers; Bluetooth can still help with discovery or the trust ceremony.
- **Local Network Access.** [Local Network Access](https://wicg.github.io/local-network-access/) gates requests from public pages to the local network behind a permission. It solves reachability but not device identity. In Local HTTPS, a peer grant satisfies that permission for the granted device only.

#### Archived

- [TCP and UDP Socket API](https://www.w3.org/TR/tcp-udp-sockets/)
- [Discovery API](https://www.w3.org/TR/discovery-api/)
- [FlyWeb](https://flyweb.github.io/)

## References & Acknowledgements

Many thanks for valuable feedback and advice from:

- [Reilly Grant](https://github.com/reillyeon)
- [Sathish K Kuttan](https://github.com/sathishkuttan)
- Chia-hung S Kuo
- [Kyle Simpson](https://github.com/getify)
- [Drake42](https://github.com/Drake42)
- [Espen Klem](https://github.com/eklem)
- [Alex Bertram](https://github.com/akbertram)
- [Michiel De Backker](https://github.com/backkem)
