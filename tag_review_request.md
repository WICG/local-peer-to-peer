## Review Request

I'm requesting a TAG review of the Local Peer-to-Peer API.

- Explainer¹ (minimally containing user needs and example code): [explainer](https://github.com/WICG/local-peer-to-peer/blob/main/EXPLAINER.md)
- User research: see [use cases](https://github.com/WICG/local-peer-to-peer/blob/main/EXPLAINER.md#use-cases) and [references](https://github.com/WICG/local-peer-to-peer/blob/main/EXPLAINER.md#references) with supporting user discussion and open-source projects
- Security and Privacy self-review²: [self-review](https://github.com/WICG/local-peer-to-peer/blob/main/security-privacy-questionnaire.md)
- GitHub repo (if you prefer feedback filed there): [WICG/local-peer-to-peer](https://github.com/WICG/local-peer-to-peer)
- Primary contacts (and their relationship to the specification):

  - Anssi Kostiainen (@anssiko), [Intel Corporation](https://www.intel.com/)
  - Belem Zhang (@ibelem), [Intel Corporation](https://www.intel.com/)
  - Michiel De Backker (@backkem), [Twintag](https://twintag.com/)
  - Wei Wang (@wangw-1991), [Intel Corporation](https://www.intel.com/)

- Organization/project driving the design: Intel, individual contributors
- External status/issue trackers for this feature (publicly visible, e.g. Chrome Status):

Further details:

- [x] I have reviewed the TAG's [Web Platform Design Principles](https://www.w3.org/TR/design-principles/)
- The group where the incubation/design work on this is being done (or is intended to be done in the future): WICG
- The group where standardization of this work is intended to be done ("unknown" if not known): currently unknown, possibly Second Screen WG due to the Open Screen Protocol foundations
- Existing major pieces of multi-stakeholder review or discussion of this design: https://github.com/WICG/proposals/issues/103 received encouraging feedback from multiple stakeholders and motivated this further work
- Major unresolved issues with or opposition to this design: no opposition per se, major unresolved issues noted as ℹ️ open points below
- This work is being funded by: Intel

You should also know that...

**The following design considerations would especially welcome TAG's feedback:**

- The Local Peer-to-Peer API aims to give browsers the means to communicate directly, without the aid of a server in the middle. It is designed to enable this communication within the confines of a [local communication medium](https://wicg.github.io/local-peer-to-peer/#local-communication-medium), a purposefully broad term defined for the purpose of this proposal.
  - ℹ️ We are seeking feedback on the local communication terminology and level of abstraction this specification establishes. Is this level of abstraction desirable? [Early feedback](https://github.com/WICG/proposals/issues/103#issuecomment-1680472714) suggests web developers prefer to work with an API that abstracts out details of the underlying communication technologies.

- For improved developer ergonomics, APIs are provides for both [simple message exchange](https://github.com/WICG/local-peer-to-peer/blob/main/EXPLAINER.md#simple-message-exchange) and [advanced data exchange](https://github.com/WICG/local-peer-to-peer/blob/main/EXPLAINER.md#advanced-data-exchange) use cases. Also [shorthand APIs](https://github.com/WICG/local-peer-to-peer/blob/main/EXPLAINER.md#shorthand-apis) are under consideration and will be develop further subject to feedback.
  - ℹ️ We would be in favor of a unification effort that aligns the DataChannel and WebTransport APIs across all transports (such as LP2P, WebRTC and HTTP/3).
  - ℹ️ DataChannel vs WebTransport: should we keep both? [Tracking issue](https://github.com/WICG/local-peer-to-peer/issues/29)
  - ℹ️ Adding the appropriate teardown/shutdown logic & events is pending. This will be addressed by studying precedent set by specs such as [WebRTC](https://www.w3.org/TR/webrtc/) and [WebTransport](https://www.w3.org/TR/webtransport/).

- [Local HTTPS](https://github.com/WICG/local-peer-to-peer/blob/main/EXPLAINER.md#local-https) is proposed to improve local use of HTTPS. This feature is illustrated and discussed in https://github.com/WICG/local-peer-to-peer/issues/34 and has real-world demand from e.g. an established media player software vendor https://github.com/WICG/local-peer-to-peer/issues/39
  - ℹ️ There is a question if a stricter CORS variant is warranted for local HTTPS sites [Tracking issue](https://github.com/WICG/local-peer-to-peer/issues/34)

- This specification purposefully makes an effort to stay within established security concepts. It exposes less information, such as IP information, about the peers involved than WebRTC, see [Security and Privacy self-review](https://github.com/WICG/local-peer-to-peer/blob/main/security-privacy-questionnaire.md).
  - ℹ️ Security and privacy have been a major focus when designing this API. We're eager to hear about any concerns in this area so it can be addressed appropriately.

**Implementation experiments**

To help inform the API design, we are conducting a series of experiments to evaluate the feasibility of the design:

- [go-lp2p](https://github.com/backkem/go-lp2p): an experimental API implementation in Go.
- There is a WIP implementation of the Open Screen Protocol in [Chromium](https://chromium.googlesource.com/openscreen/). It is being upgraded to using [QUICHE](https://github.com/google/quiche) QUIC implementation. We intent to build a POC on top in the future.

We'd prefer the TAG provide feedback as (please delete all but the desired option):

🐛 open issues in our GitHub repo for **each point of feedback**
