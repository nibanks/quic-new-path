---
title: QUIC New Path Extension
abbrev: QUIC-NEW-PATH
docname: draft-banks-quic-new-path-00
category: exp
date: 2020

stand_alone: yes

ipr: trust200902
area: Transport
kw: Internet-Draft

coding: us-ascii
pi: [toc, sortrefs, symrefs, comments]

author:
  -
    ins: N. Banks
    name: Nick Banks
    org: Microsoft Corporation
    email: nibanks@microsoft.com
    country: U.S.A

--- abstract

This document describes an extension to the QUIC protocol to allow for one
endpoint to explicitly request that a new network path be used. The endpoint
that originates the request supplies one or more public IP addresses and UDP
ports to connect to, and if the peer accepts the request will then (possibly
using a completely new IP address and port of its own) attempt to use that new
path.

--- middle

# Introduction

The QUIC Transport Protocol {{!I-D.ietf-quic-transport}} supports a feature,
known commonly as migration, where the connection may use different network
paths other than the one originally used for the handshake.  From the server's
perspective, these new paths are implicitly created when packets for an active
QUIC connection are received on a new path.  This generally happens either if
something changes on path, resulting in a new IP address or UDP port from the
server's perspective (NAT rebinding) or if the client explicitly changes IP
address or UDP port.

This extension explicitly signals an endpoint's intent to create a new path,
without previously sending on said path.  If the peer agrees and is able to
create the new path, then it is immediately created and both sides probe to test
the viability of the path.

## Terms and Definitions

The keywords "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD",
"SHOULD NOT", "RECOMMENDED", "NOT RECOMMENDED", "MAY", and "OPTIONAL" in this
document are to be interpreted as described in BCP 14 {{!RFC2119}} {{!RFC8174}}
when, and only when, they appear in all capitals, as shown here.

## Applicable Scenarios for Use

TODO - Dynamic server migration
     - NAT/Firewall punch-through

# Specification

The new_path transport parameter is used for negotiating the use of the
extension is defined below.

## New Path Transport Parameter

The new_path transport parameter can be sent by both a client and server.  The
transport parameter is sent with an empty value by the server; an endpoint that
understands this transport parameter MUST treat the receipt of a non-empty value
as a connection error of type TRANSPORT_PARAMETER_ERROR.

Advertising the new_path transport parameter indicates that the
endpoint supports NEW_PATH_REQUEST and NEW_PATH_RESPONSE frames.  Both sides
must advertise support for the extension for it to be considered successfully
negotiated.

If successfully negotiated, the NEW_PATH_REQUEST and NEW_PATH_RESPONSE frames
may be used by either endpoint to request a new path be created.

## Requesting New Paths

Once the extension has been negotiated, an endpoint may request a new path any
time after normal migration would be allowed.  If an endpoint must treat the
receipt of a NEW_PATH_REQUEST frame before that as a connection error of type
PROTOCOL_VIOLATION.

An endpoint begins the new path request process by selecting one or more
addresses (and UDP ports) they wish to use for the new path.  These addresses,
along with a new request identifier (monotomically increasing number) are
packaged into a NEW_PATH_REQUEST frame and sent over the current path to the
peer.

TODO - How to select the addresses for the request?

When the peer received the request it must decide if it can/will accept the
request.  If it will not accept the request, it MUST reply with a
NEW_PATH_RESPONSE frame with the RequestAccepted bit set to zero.  If the peer
does accept the request it MUST reply with a NEW_PATH_RESPONSE frame with the
RequestAccepted bit set to one, and optionally, one or more addresses to use for
the new path.

TODO - When to (or not to) supply addresses in the response?
TODO - How to select the addresses for the response?

Once an endpoint has accepted a new path request or received a new path response
it should start probing all valid combination of local and peer IP addresses.

TODO - How to apply amplification protection logic?
TODO - What retransmission logic should be used for the probes?
TODO - Probes may be received by either endpoint on different addresses than
       those advertised, because of NATs and firewalls.

## NAT Traversal

TODO - Should this document describe the mechanisms for punching through NATs or
       should this document just describe the base mechanics?
TODO - Discussion of public IP address discovery in order to share that with the
       peer?

## NEW_PATH_REQUEST and NEW_PATH_RESPONSE frames

The Path Address structure represents a single IPv4 or IPV6 address (type
determined by length) and a UDP port. It is formated as shown in
{{path-address-format}}.

~~~
Path Address {
  UDP Port (16),
  IP Address Length (8),
  IP Address (..),
}
~~~
{: #fig-path-address title="Path Address format"}

NEW_PATH_REQUEST frames are formatted as shown in {{new-path-request-format}}.

~~~
NEW_PATH_REQUEST Frame {
  Type (i) = 0xA0,
  Request Id (i),
  Path Address Count (i),
  Path Address (..) ...,
}
~~~
{: #new-path-request-format title="NEW_PATH_REQUEST Frame Format"}

~~~
NEW_PATH_RESPONSE Frame {
  Type (i) = 0xA1,
  Request Id (i),
  RequestAccepted (1),
  Reserved (7),
  Path Address Count (i),
  Path Address (..) ...,
}
~~~
{: #new-path-response-format title="NEW_PATH_RESPONSE Frame Format"}

# Security Considerations

TODO - Even when supported, should the extension be disabled by default?
TODO - Could be used to bypass firewalls or monitors in previously unexpected ways.

# IANA Considerations

This document registers a new value in the QUIC Transport Parameter Registry:

   Value: TBD (using value 0x1234 in early deployments)

  Parameter Name: new_path

  Specification: Indicates the new-path extension is supported.

This document also registers a new value in the QUIC Frame Type registry:

  Value: TBD (using value 0xA0 in early deployments)

  Frame Name: NEW_PATH_REQUEST

  Specification: Request for a new path to be created.

This document also registers a new value in the QUIC Frame Type registry:

  Value: TBD (using value 0xA1 in early deployments)

  Frame Name: NEW_PATH_RESPONSE

  Specification: Response to a new path request.

--- back
