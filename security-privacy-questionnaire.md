# [Self-Review Questionnaire: Security and Privacy](https://w3ctag.github.io/security-questionnaire/)

1.  What information does this feature expose,
    and for what purposes?
    - A: [Addressed in spec doc](https://wicg.github.io/local-peer-to-peer/#security-pii)
2.  Do features in your specification expose the minimum amount of information
    necessary to implement the intended functionality?
    - A: The proposed design puts the User Agent in control of peer management. This approach was designed specifically to limit exposing information as much as possible.
3.  Do the features in your specification expose personal information,
    personally-identifiable information (PII), or information derived from
    either?
    - A: [Addressed in spec doc](https://wicg.github.io/local-peer-to-peer/#security-pii)
4.  How do the features in your specification deal with sensitive information?
    - A: [Addressed in spec doc](https://wicg.github.io/local-peer-to-peer/#security-pii)
5.  Do the features in your specification introduce state
    that persists across browsing sessions?
    - A: [Addressed in spec doc](https://wicg.github.io/local-peer-to-peer/#security-persistent-state)
6.  Do the features in your specification expose information about the
    underlying platform to origins?
    - A: By design no such information should be exposed.
7.  Does this specification allow an origin to send data to the underlying
    platform?
    - A: The specification is meant to be implementable using the Open Screen Protocol, a cross platform protocol.
8.  Do features in this specification enable access to device sensors?
    - A: The specification doesn't allow direct access to device sensors.
9.  Do features in this specification enable new script execution/loading
    mechanisms?
    - A: No
10. Do features in this specification allow an origin to access other devices?
    - A: [Addressed in spec doc](https://wicg.github.io/local-peer-to-peer/#security-device-access)
11. Do features in this specification allow an origin some measure of control over
    a user agent's native UI?
    - A: [Addressed in spec doc](https://wicg.github.io/local-peer-to-peer/#security-ui)
12. What temporary identifiers do the features in this specification create or
    expose to the web?
    - A: [Addressed in spec doc](https://wicg.github.io/local-peer-to-peer/#security-pii)
13. How does this specification distinguish between behavior in first-party and
    third-party contexts?
    - A: A [Permission Policy Integration](https://wicg.github.io/local-peer-to-peer/#permission-policy-integration) has been defined.
14. How do the features in this specification work in the context of a browser’s
    Private Browsing or Incognito mode?
    - A: The [specification](https://www.w3.org/TR/openscreenprotocol/#private-browsing-mode) of the OpenScreen protocol are to be followed in this area.
15. Does this specification have both "Security Considerations" and "Privacy
    Considerations" sections?
    - A: Both Security and Privacy concerns have been considered.
16. Do features in your specification enable origins to downgrade default
    security protections?
    - A: [Addressed in spec doc](https://wicg.github.io/local-peer-to-peer/#security-same-origin)
17. What happens when a document that uses your feature is kept alive in BFCache
    (instead of getting destroyed) after navigation, and potentially gets reused
    on future navigations back to the document?
    - A: This is an open point. It will be addressed by studying president set by specs such as [WebRTC](https://www.w3.org/TR/webrtc/) and [WebTransport](https://www.w3.org/TR/webtransport/).
18. What happens when a document that uses your feature gets disconnected?
    - A: This is handled by the appropriate teardown logic & events.
19. What should this questionnaire have asked?
    - A: No further comments. This specification purposefully makes an effort to stay within established security concepts. It exposes less information, such as IP information, about the peers involved than WebRTC.
