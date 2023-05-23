# Authors
Anssi Kostiainen (Intel)
Belem Zhang (Intel)

# Local Peer-to-Peer API

View proposals in the [Local Peer-to-Peer API Explainer](EXPLAINER.md).

## Problem

When transfer files to other devices or share files to friends or colleagues on the web in the same LAN subnet, usually the two following approaches have their limitations.

1. The centralized service, member fee, long round trip through internet, data wastage, and privacy concern are the drawbacks to share files by using cloud storage service.

2. Send the file directly through the WebRTC peer to peer on the web requires internet connection to communicate with WebRTC signaling server which establishes and manages the connections between devices.

We need an optimized network path to use a local network connected by the devices for web applications.

## Introduction

The Local Peer-to-Peer API enables supported mobile and desktop devices to transfer messages or files through close-range wireless communication on the web.

[Read the complete Explainer](EXPLAINER.md).

## Feedback

We welcome feedback in this thread, but encourage you to file bugs against the [Explainer](EXPLAINER.md).
