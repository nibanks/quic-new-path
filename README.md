# quic-new-path

This document describes an extension to the QUIC protocol to allow for one endpoint to request that a new network path be attempted to be used. The endpoint that originates the request supplies a public IP address and UDP port to connect to, and if the peer accepts the request will then (possibly using a completely new IP address and port of its own) attempt to use that new path.
