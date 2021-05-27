ANI Support for network automation example
------------------------------------------

The ANI is designed to be the enabler of distributed autonomic
behavior. That can start today with very pragmatic incremental
in-network automation, developed even by operators as simple 
scripts in the scripting language that can run locally on
routers. Tcl and python are popular choices.

Consider an existing network where basic services are already running,
e.g. IPv4 and/or IPv6 addressing, routing. A software upgrade to
the routers that adds support for ANI could introduce it, without
impacting any of the pre-existing configuration and services.

One of the services desirable is protocol security, for example in
routing protocols such as OSPF, ISIS, BGP or PIM, but also for many others,
and most protocols have their own security mechanism and/or keying material
requirements.

But security is often not configured because there is no automated
key management including key rollover and revocation. Without good
automation of key management, networks either forego to enable protocol
security, or operators set up a single network wide password that is
never changed afterwards.

With the ANI, automation of such functionality becomes much simpler,
by using the ANIs "GeneRic Autonomic Signaling Protocol" (GRASP).

GRASP is a simple protocol built on CBOR, which is a compact binary
derivative of JSON, which is the standard for the data structure model introduced
by Javascript, and easily used with libraries in other programming languages
including Python or Tcl.

GRASP implements a core set of interactions between peers. Unicast:
REQUEST, NEGOTIATE, CONFIRM, SYNCHRONIZE, Multicast: DISCOVER, FLOOD. 
GRASP message payloads are defined by the application as CBOR data structures,
GRASP runs inside the ACP, therefore requiring no configuration at all once
ANI is running.

With this in mind, a simple python or tcl script that integrates a GRASP API
could easily be written to auto-configure routing protocol security:

-   DISCOVER ANI neighbors on links that use the same routing protocol.
-   Generate random key.
-   NEGOTIATE key with neighbor
-   Configure routing protocol key locally on the router
-   Periodically wake up, re-NEGOTIATE and configure new key including
    when neighbor a neighbor has disappeared from the ACP.

Some protocols may not even have security included in the protocol itself, for example PIM.
Instead, protocols packets need to be secured via IPsec security associations (SA).
For those protocols, the above script would then auto-configure the IPsec SA
instead of an in-protocol key parameter.

In summary, GRASP with ANI solves all the recurring core problems of
in-network automation between routers:

-   Q: How do i communicate with a peer (link-local or across other routers)
    without having any configured IP connectivity ?
-   A: ACP provides connectivity automatically.
-   Q: How do i discover what peers with what type of service inerests there are
    (especially when not link-local) ?
-   A: GRASP does this.
-   Q: How do i trust these peers ?
-   A: This trust comes from the ANI certificate used for the ACP.
-   Q: How do i avoid re-inventing a new protocol to coordinate with my peers ?
-   A: This is what GRASP does.

Securing existing deployed protocols is of course just one example.
Many or all the benefits equally apply to any other in-network automation
that needs to solve any of these issues first. Establishing/adjusting QoS and other services policies,
auto-configuring decentralized protocol instances, monitoring, error troubleshooting/isolation, even auto-configuring the most basic user network configuration, such as basic IP/IPv6 addressing, as described in ANI reference example use case RFC8992.

