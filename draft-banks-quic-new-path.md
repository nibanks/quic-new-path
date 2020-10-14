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
that originates the request supplies a public IP address and UDP port to connect
to, and if the peer accepts the request will then (possibly using a completely
new IP address and port of its own) attempt to use that new path.

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

TODO

# Specification

TODO

# Security Considerations

TODO

# IANA Considerations

This document registers a new value in the QUIC Transport Parameter
Registry:

Value: TBD (using value ? in early deployments)

Parameter Name: new_path

Specification: Indicates the new-path extension is being negotiated.

--- back
