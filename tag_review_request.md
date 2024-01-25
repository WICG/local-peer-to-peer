## Review Request

I'm requesting a TAG review of the Local Peer-to-Peer API.

- Explainer¹ (minimally containing user needs and example code): [url](https://github.com/WICG/local-peer-to-peer/blob/main/EXPLAINER.md)
- User research: [url](https://github.com/WICG/local-peer-to-peer/blob/main/EXPLAINER.md#use-cases).
- Security and Privacy self-review²: [url](https://github.com/WICG/local-peer-to-peer/blob/main/security-privacy-questionnaire.md)
- GitHub repo (if you prefer feedback filed there): [url](https://github.com/WICG/local-peer-to-peer)
- Primary contacts (and their relationship to the specification):

  - Anssi Kostiainen (@anssiko), [Intel Corporation](https://www.intel.com/)
  - Belem Zhang (@ibelem), [Intel Corporation](https://www.intel.com/)
  - Michiel De Backker (@backkem), [Twintag](https://twintag.com/)
  - Wei Wang (@wangw-1991), [Intel Corporation](https://www.intel.com/)

- Organization/project driving the design: []
- External status/issue trackers for this feature (publicly visible, e.g. Chrome Status):

Further details:

- [x] I have reviewed the TAG's [Web Platform Design Principles](https://www.w3.org/TR/design-principles/)
- The group where the incubation/design work on this is being done (or is intended to be done in the future): WICG
- The group where standardization of this work is intended to be done ("unknown" if not known): WICG
- Existing major pieces of multi-stakeholder review or discussion of this design: N/A
- Major unresolved issues with or opposition to this design: See Open Work below.
- This work is being funded by: N/A

You should also know that...

We'd prefer the TAG provide feedback as (please delete all but the desired option):

🐛 open issues in our GitHub repo for **each point of feedback**

**Open points**

- Security and privacy have been a major focus when designing this API. We're eager to hear about any concerns in this area so it can be addressed appropriately.
- We would be in favor of a unification effort that aligns the DataChannel and WebTransport APIs across all transports (such as LP2P, WebRTC and HTTP/3).
- There is a question if a stricter CORS variant is warranted for local HTTPS sites [Tracking issue](https://github.com/WICG/local-peer-to-peer/issues/34)
- DataChannel vs WebTransport: should we keep both? [Tracking issue](https://github.com/WICG/local-peer-to-peer/issues/29)
- Adding the appropriate teardown/shutdown logic & events is pending. This will be addressed by studying president set by specs such as [WebRTC](https://www.w3.org/TR/webrtc/) and [WebTransport](https://www.w3.org/TR/webtransport/).

**Implementation experiments**

To help inform the API design, we are conducting a series of experiments to evaluate the feasibility of the design:

- [go-lp2p](https://github.com/backkem/go-lp2p): an experimental API implementation in Go.
- There is a WIP implementation of the Open Screen Protocol in [Chromium](https://chromium.googlesource.com/openscreen/). It is being upgraded to using [QUICHE](https://github.com/google/quiche) QUIC implementation. We intent to build a POC on top in the future.
