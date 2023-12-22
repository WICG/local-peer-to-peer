# Local Peer-to-Peer API Explained

The Local Peer-to-Peer API aims to give browsers the means to communicate directly, without the aid of a server in the middle. It is designed to enable this communication within the confines of a local communication medium such as the Local Area Network.

The API aims to strike a balance between creating a powerful new building block for developers and providing a seamless, secure and privacy preserving experience for the user. As an example: while the API doesn’t provide raw socket access, it does aim to give developers the flexibility to innovate on top by providing a persistent, two-way communication channel with little overhead.

See also:

- [Specification](https://WICG.github.io/local-peer-to-peer/), the formal draft spec.

## Motivation

As a blast from the past:

> Tim Berners-Lee's [vision for the World Wide Web](https://www.w3.org/People/Berners-Lee/1996/ppf.html) was close to a P2P network [...] The early Internet was more open than the present day, where _two machines connected to the Internet could send packets to each other_ without firewalls and other security measures. ([Wikipedia](https://en.wikipedia.org/wiki/Peer-to-peer#Historical_development))

This proposal sets to make a part of this Tim's vision a reality while adhering to the modern security and privacy requirements expected of modern web capabilities.

## Problem Description

When a user wants to connect between two devices on the same network, for example to another device nearby—be it another device the user owns or that of a friend—the user has multiple ways to accomplish this task:

1. _A cloud service_. The web has many ways of connections to a third-party cloud service: HTTP, WebSocket or WebTransport. However, all of these methods require a round trip through the internet. This is inherently dependant on external resources, it consumes network bandwidth and can be slow or costly and has privacy implications in all but the strongest E2E encryption schemes.

2. _A local server_. Many modern Web security measures rely on the presence of naming, signaling and certificate authorities. Local use-cases where these authorities are not readily available have started lagging behind in user experience or are not supported altogether. A local solution involves knowing IPs, ports and accepting/ignoring a plethora of "Not secure" warnings to get going. This falls short of the user-friendliness that one can expect of—nowadays ubiquitous—cloud services.

3. _A WebRTC connection_. While WebRTC is a P2P protocol, it still requires a setup step usually referred to as 'signaling'. There is no good way to perform this step without relying on an existing connection between peers, commonly a cloud service is used.

None of these solutions to this seemingly common task provide a compelling user experience. When the devices are physically nearby the user's expectation is the connection process should be as seamless as any physical interaction. With close-range communication technologies widely supported on today's devices we believe this user experience can be vastly improved.

We need an optimized network path to use a local network connected by the devices for web applications.

![problem](./images/problem.svg)

Figure 1: Proposed Web Local Peer-to-Peer along with other existing options

## Usage example

### Establishing a connection

```js
// Peer A
const receiver = new LP2PReceiver({
  nickname: "Peer A",
});

receiver.onconnection = (e) => {
  const conn = e.connection;
  console.log("Receiver: Got a connection!");
};
```

```js
// Peer B
const request = new LP2PRequest({
  nickname: "Peer B",
});

const conn = await request.start();
console.log("Requester: Got a connection!");
```

### Basic message exchange

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
  console.log(`Receiver: Received message: ${e.message}`);
};
```

### Dedicated WebTransport

```js
// Peer A
const transport = new LP2PQuicTransport(request);

await transport.ready;
```

```js
// Peer B
receiver.ontransport = async (e) => {
  const transport = e.transport;

  await transport.ready;
};
```

Refer to the [WebTransport examples](https://www.w3.org/TR/webtransport/#examples) for usage of a WebTransport object.

## Use Cases

### UC1: Single User Multiple Devices

- Collaboration tools that work during an internet outage or emergency situations
- Seamlessly connecting to your NAS, your home security system, your robotic assistant doing the dishes.
- Send and receive files instantly, including photos or videos, between mobile phone, tablet, and personal computer without using mobile data or internet connection
- Add the "Import file nearby" and “Export to nearby” buttons in web version of Figma on desktop to access images from mobile devices"
- Run a game in web app on the smart TV, use mobile phone as the game controller via this local peer-to-peer API to send control messages
- Open files in "Nearby" tab in "Open a file" dialog of Google doc
- Video editing web app that allows users to ingest footage directly from their phone.
- Seamlessly sync private keys and other identity credentials across personal devices, securely transfer one-time pads to encrypt and decrypt messages

![Web Drop](./images/drop.svg)

Figure 2: Web Drop, an In-App Sharing feature based on Local Peer-to-Peer API compare with cloud-client solution

![F](./images/n1.svg)

Figure 3: Import file from nearby devices in web based Figma app

![G](./images/n2.svg)

Figure 4: Open a file from nearby devices in Google Doc

![Game 1](./images/n3.svg)

Figure 5: Play web game cross smart TV and mobile phone

### UC2: Multiple Users and Devices

- In-App Sharing, quickly share group photos or videos with friends without relying on cloud services
- Run a 2 players web game on two mobile phones, synching messages between two players instantly
- Ephemeral groups support: Share files to a group with a single “push” vs. sending to each friend one at a time
- An app that allows humanitarian field workers in remote areas with no connectivity to gather, synchronize, review, and edit data offline for several days, the data can then be synchronized with the central server when internet connection becomes available
- A non-profit educational organization is concerned about the large file download size required for on-device speech recognition in the browser. This is problematic in low bandwidth, high latency environments such as classrooms, particularly in emerging countries. Their goal is for one student to download the file first and then easily share/distribute it with classmates without low speed internet connections any more to save time and cost

![Game 2](./images/n4.svg)

Figure 6: Play a web game across two nearby devices with 2 players

### Requirements

The following are the high-level requirements derived from the use cases:

- R1: Discover nearby device(s)
- R2: Advertise yourself to nearby device(s)
- R3: Establish a bi-directional communication channel between two nearby devices
- R4: User consent and delegation per web origin.

### Prerequisites

What is a prerequisite for all these use cases is that the participating devices are physically nearby to each other and as such able to establish a direct connection using either a wireless connectivity technology such as Wi-Fi Direct, Wi-Fi via access point, or a wired computer networking technology such as Ethernet. This connection technology and its details are abstracted out by both the Web API exposed to web developers as well as the UI/UX visible to the user.

In summary, the following are the prerequisites:

- The participating devices are physically nearby (the definition of "nearby" is an implementation detail)
- The participating devices are able to establish a direct connection using some connection technology (the supported technologies may vary depending on hardware and OS capabilities and remain an implementation detail)

## User Interface Considerations

ℹ️ This section is informative.

This section presents examples of user interface concepts. Implementers are expected to come up with their own shapes and forms for the user interface elements that fit the conventions of the underlying platform, form factor and the browser.

![Shapes](./images/shape.svg)

## User Interaction Considerations

ℹ️ This section is informative.

This section represents concepts of how a user could discover, connect and share files from one device to the other device nearby.

![User flow](./images/userflow.svg)

## Goals

Build a generic local peer-to-peer API and provide an arbitrary bidirectional channel on the web for devices under short-range communication environment.

The API will abstract over peer-to-peer technology and provide a high-level interface for two instances of a web app running on peer devices to discover and connect to each other.

The Local Peer-to-Peer API will cover the following main parts:

- Methods to discover, request, and connect to peers
- Listeners to notify if these method calls succeed or fail
- Listeners to notify if the connection is received or its status is updated
- Means to send and receive data after connection to a peer device has been established

The API is designed to be backed by an authenticated, streams-based transport. As a commitment to an open standards-based implementation path, this specification describes how the API can be implemented on top of the [Open Screen Protocol](https://w3c.github.io/openscreenprotocol/). While not described here, the API is expected to be implementable on top of other transports when technically feasible.

## Non-goals

- Providing direct access to TCP/UDP sockets.

## Security and Privacy

The Local Peer-to-Peer API can minimize security and privacy risks associated with cloud services. Web app users can choose to limit the use to offline mode only (completely disconnect the Internet) which improves the security further and avoids the information leak to cloud.

Restrictions will be evaluated in accordance with security models. Among others, restrictions may include:

- Web browser restricted access
- [Secure context](https://w3c.github.io/webappsec-secure-contexts/) required
- Least privilege principle, permission granted one device to one site
- Pairing individual device requires at least a user action
- User informed when the device is connected
- Disconnect automatically after a period of inactivity (implementation-defined e.g. 10 minutes) with an extension opportunity with a user's consent
- Authorization on a per-session basis: Colleagues, friends, family members or the user themselves can authorize the “content pull request” on the device that can allow pulls for one session (e.g. 10 minutes)
- We are investigating whether this API should be restricted to PWA only

## Considered Alternatives

The Web Share and Web Share Target provide a minimal API to share text and files to a user-selected share target, including to another website, utilizing the sharing mechanism of the underlying operating system.

While the Web Share API partially satisfies the requirement R2 set forth above, the Web Share API by its design defines a minimal API surface that is likely not amenable to extensions required to support additional use cases and requirements outlined in this explainer. Notably, the Web Share API is a "push-based" API where content is pushed from one device to another device while the Local Peer-to-Peer API is catering to both the "push-based" as well as "pull-based" use cases as illustrated by "drop files here and share" (Figure 2) and "import file nearby" (Figure 3) concepts respectively. From the UX perspective, The Local Peer-to-Peer API allows for a more seamless in-web app experience in use cases where a system-provided share facility would disrupt the user flow.

Certain use cases can benefit from an internet-based P2P fallback if local communications is not available. To minimize conceptual weight for web developers, this API attempts to align with the established conventions and API semantics of other communication APIs such as WebTransport API, WebRTC, Fetch, and Presentation API, where applicable.

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
