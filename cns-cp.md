# CNS/CP: Connectivity Naming System and Connection Profiles

Authors:  
T. Considine  
A Budiardjo  
June 2022

This document describes the Connectivity Naming System and Connection Profiles for exchanging information to support creating Connections between independent systems to compose systems of systems.

A Connection Profile defines how a node can expose a Capability, and how another node can consume that Capability. Each exposed Capability can be considered as a service with a single responsibility, and each consuming Capability as a means to interact with that service. This Capability might define a decomposed business capability, or a microservice, or a single verb, or a single use case. The relationship between the exposed Capability and the consuming Capability defines a Connection, part of a relationship whereby the Node may request information from the service, or request a state change in the system behind that service. 

System interoperation is the loose coupling of different systems to achieve some purpose. The complexity and ever-developing diversity of systems has traditionally led to closed integrations that are neither dynamic, repeatable or re-usable at scale. A growing number of system interactions are across domains; some examples of such interoperating systems are referred to as Smart Buildings, Industrie 40, Digital Twins, Internet of Things, Smart Enterprise, Smart Cities, and Smart Energy. 

Connection Profiles as described herein are a mechanism to define and publish common interactions between systems. A Connection Profile defines a Capability which consists of two roles. Each Connection Profile has a unique name, metadata about its purpose and its creator. Each Connection Profile describes how a node representing a system exposes a Capability, and how another node can consume or invoke that Capability. For each role, the Connection Profile itemizes the interfaces and protocols that are required to create a Connection. 

Complementary Roles, that is complementary published capabilities based on the same Connection Profile, define Connections. A Connection identifies the discovered ability of two nodes interact through a Capability. This document describes how to specify a Connection Profile. Systems may use Connections to create relationships, that is, communicate using one or more Connections.

No specific topology is defined by the CNS/CP framework. While the specificity needed to define a single Connection Profile is typically directional, CP/CNS does not suggest a particular topology. A Node may publish multiple capabilities and an ability to consume many capabilities. Many nodes may interact with the same service. True peers may interact through reciprocal paired Connection Profiles, each Node offering the service, and each node consuming from the other. 

The document also describes the Connectivity Naming System (CNS). Two functions support CNS: a universal public Connection Profile Registry and a private local Connection Broker. The Connection Profile Registry enables system developers and integrators to share and re-use their Connection Profiles. The Connection Broker publishes specific systems and the Connection Profiles they support. The Connection Broker discovers Complementary Nodes to create Connections.
The author anticipates open-source Connection Brokers offering the minimum functionality of node matching.

This document is independent of any particular service or protocol. This document defines interfaces for each of the systems described through examples conveyed in JSON; these examples are for clarity but do not mandate a specific serialization or binding for messages.

# Contents

[1. Introduction](#introduction)

[2. The Connection Profile](#the-connection-profile)

[2.1. Unique Reference for each Connection Profile](#unique-reference-for-each-connection-profile)

[2.1.1. The Connection Profile Namespace](#the-connection-profile-namespace)

[2.1.2. Naming a Connection Profile](#naming-a-connection-profile)

[2.1.3. Versions of a Connection Profile](#versions-of-a-connection-profile)

[2.2. Status of a Connection Profile](#status-of-a-connection-profile)

[2.3. Connection Profile Header](#connection-profile-header)

[2.4. Connection Profile Properties](#connection-profile-properties)

[2.5. Example of Connection Profile definition](#example-of-connection-profile-definition)

[2.6. Registering Connection Profiles](#registering-connection-profiles)

[3. Publishing Connection Capabilities](#publishing-connection-capabilities)

[3.1. The Node ID](#the-node-id)

[3.2. The Context](#the-context)

[3.3. Publishing a Server Capability to a Connection Broker](#publishing-a-server-capability-to-a-connection-broker)

[3.4. Publishing a Client Capability to a Connection Broker](#publishing-a-client-capability-to-a-connection-broker)

[4. The Connection Broker](#the-connection-broker)

[4.1. Relationship between Client, Server, and Broker](#relationship-between-client-server-and-broker)

[4.2. Receiving Submissions for Publication](#receiving-submissions-for-publication)

[4.3. Creating Connections](#creating-connections)

[4.4. Connection Life-cycle](#connection-life-cycle)

[4.4.1. Connection Status](#connection-status)

[4.4.2. Discussion of Status](#discussion-of-status)

[4.5. Removing Published Capabilities.](#removing-published-capabilities)

[4.6. Monitoring Connections](#monitoring-connections)

[5. Connection Broker Federation](#connection-broker-federation)

# Introduction

The Internet Research Task Force (IRTF) describes the Internet of Things (IoT) in RFC 8576. “Internet of Things (IoT) Security: State of the Art and Challenges” https://datatracker.ietf.org/doc/html/rfc8576). Systems of Digital Twins are described in “Digital Twin Network: Concepts and Reference Architecture” (https://www.ietf.org/id/draft-irtf-nmrg-network-digital-twin-arch-00.html). This specification relies on each of these documents, and describes a mechanism to support simple, reusable, and scalable interoperation between systems such as those described in those documents.

In quick summary, the Internet of Things (IoT) denotes the interconnection of highly heterogeneous networked entities and networks. The networked things named as components of the Internet of Things are smart devices that understand and react to the environment they reside in. The complexity of a thing may range from a simple network-addressable sensor to a purpose-built autonomous system.

It is important for both the client and server to use defined and constrained interactions with such systems so that:

-   Interactions do not harm or impair the functioning of remote systems (“Servers”).
-   Would-be consumers of information (“Clients”) can find appropriate sources of information without needing to understand the inner workings of the Server.
-   Clients and Servers can negotiate protocols and specifications needed for interoperation.
-   Connections to Servers are discoverable and can be re-used by different Clients to accomplish a variety of ends.
-   The work performed to expose a server connection can be re-used over the life of a networked system. Systems in buildings and infrastructure often remain in production for decades rather than the years typical for many enterprise systems.

Digital Twinning names a wide-ranging set of practices from the simple collection of a few data points to maintaining a complete abstract model of a remote system. When applied to manufacturing and to logistics processes, this is sometimes referred to as Industry 4.0. The aim is to develop various rich networked applications and realize efficient and cost-effective data-driven process management.

The purposes of Digital Twin Networks include:

-   Data acquisition and processing
-   High-fidelity modeling
-   Real-time, two-way connection between the virtual and the real twins
-   Unified development platform and tools
-   Environmental coupling technologies

The systems that comprise the IoT and Digital Twins are typically diverse and often purpose-built. Inconsistent technical implementations and the heterogeneity of vendor and industrial sector technologies make it difficult to establish consistent connectivity.

Component systems often rely on internal protocols that are poorly suited to transport across the Internet. They usually lack the cybersecurity needed to support direct exposure to the internet. There are many kinds of network elements and topologies used. As a minimum, the complexity of discovering and using interactions must be reduced and documented.

The IRTF Digital Twin Reference Architecture names and defines three layers: Application Layer, Digital Twin Layer, and Physical Network Layer. CNS/CP specifies a machine-readable catalog of the Digital Twin layer. CNS/CP catalogs expose components and functionalities and define standardized and unified interfaces.

Digital twin networks synchronize all or a subset of the data related to involved component systems in real time, which inevitably expands the attack surface, and increases the risk of information leakage. CNS/CP supports the development of more secure data mechanisms and data protection methods, even atop legacy protocols by enabling the use of defined, re-usable Nodes.

A Node can chain Connections together. The purpose of a particular Node may be to process other Connections. A Node may provide translation of message or of binding and expose that using a different Profile. A Client Node may process information from several Server Nodes and publish a Server Node representing a single summary or consensus. Such nodes would register as Client Nodes to consume data from Servers and Server Nodes, to provide services to other Clients.

In telecommunications and cloud computing architectures, there is a distinction made between the control plane and the data plane. The control plane manages data provisioning and may orchestrate data services across data storage pools but does not access the data directly. The data plane manages data layout, storage devices, and read/write to data.

By analogy, a Connection Broker documents a specific control plane defining each Node using Connection Profiles. The data plane is the connections between Nodes, which may either pass around or through the Connection Broker.

# The Connection Profile

A Connection Profile contains specific information on how a type of Client will connect with a type of Server. Each Connection Profile defines a Server Capability and a Client Capability. A Connection Profile defines a template/model used when creating specific Connections. In other words, a Connection Profile describes the metadata required to establish a Connection to enable interoperation between systems.

The owner (see Connection Naming, below) of a Connection Profile registers that Profile in the Connection Profile Registry, described below. A system developer or integrator can reference the Connection Profile to publish that a specific system supports Server or a Client Capability to a Connection Broker. A Connection Broker can reference the Connection Profile Registry to validate the publication of each Node. The Connection Broker uses this published information to create Connections between a Client Node and a Server Node to enable the specific purpose defined in the Connection Profile.

Each Connection Profile has a unique name and a version number. For example, consider a Connection Profile to connect the Capabilities “dataserver” defined by the Example company. It is referenced by the URN CP:example.dataserver:1. Connection Profile Names are discussed below.

Each Connection Profile includes a Header section describing the origins of the Profile and its purpose. A Connection Profile also itemizes the name/value pairs for a Server and for a Client using that profile.

## Unique Reference for each Connection Profile

The Connection Profile Registry maintains a persistent distributed registry for Connection Profiles. A Connection Profile can be uniquely referenced as cp:namespace.sub1.sub2:version.

### The Connection Profile Namespace

In a manner similar to Internet domain names, an organization registers for rights to a Namespace. This organization “owns” all Connection Profiles in that namespace. The Namespace owner defines new Connection Profiles and registers them in the Connection Profile Registry.

### Naming a Connection Profile

The owner of a Namespace may create subordinate parts of a Connection Profile name (sub1 and sub2 in the example cp:namespace.sub1.sub2), using any system that makes sense to the Namespace owner.

### Versions of a Connection Profile

Each new version of a Connection Profile defines Capabilities for a Server and a Client. To create a connection, the Namespace, all subordinate parts, the Version, and the Context MUST match. See rules about matching Context below.

A Node may Publish its Capability of acting as a Server for multiple versions of a Connection Profile by publishing a Server Capability for each. A Node may Publish its Capability of acting as a Client for multiple versions of a Connection Profile by publishing a Client Capability for each.

Server Capability v3 only connects with Client Capability v3. A Developer that wishes to add additional Properties must declare a new Version number.

## Status of a Connection Profile

Each new Name and Version is registered with a status of “Testing”. The distributed Connection Registry should not accept remote inquiries about a Connection Profile with the Status “Testing”. This is so a system or a Connection Broker owned by the Namespace owner can test the New profile without risk of creating one-offs around the world.

When the developer is ready to release a Version, the status is set to "Active". A developer SHALL NOT change any properties of a Connection Profile Version after it is “Active”.

Active Connection Profiles MAY be referenced from Connection Registry services external to the Namespace owner and referenced when publishing Nodes in a Connection Broker. Changing the properties once the status is “Active” could invalidate existing Connections and break existing publications.

In general, once a Connection Profile is “Active” it works forever. Defining additional versions creates new capabilities, but does not invalidate any existing Connection. However, if the Developer sets the Status of a profile to “Deprecated”, no knew Connections will be automatically created for that Version.

## Connection Profile Header

The Connection Profile Header contains metadata about the Profile, including the owner’s name, Profile title, description, a URL link to additional specifications for the use of the Profile, and other information necessary to inform developers and integrators on how to use the Profile.

The Connection Profile Header metainformation properties are as below. All fields are mandatory.

| Attribute   | Description                                                                                                                                                                                      |
|-------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Name        | The name of the Profile. Must match that published in the Registry                                                                                                                               |
| Version     | Every profile includes a version number. Publishing v3 does not invalidate the Nodes currently published with v2. The Version should be considered as a mandatory component of the Profile name. |
| Pub Date    | Date the Name/Version was set to Active                                                                                                                                                          |
| Status      | Testing: Profile is in development Active: Profile development is complete.                                                                                                                      |
| Owner       | Usually business name of the owner/developer                                                                                                                                                     |
| Title       | Title for the Profile                                                                                                                                                                            |
| Server      | Title for the Server Capability based on this Profile                                                                                                                                            |
| Client      | Title for the Client Capability based on this Profile                                                                                                                                            |
| Description | General narrative describing the use, purpose and perhaps history of this Connection Profile                                                                                                     |
| Website     | url of the web site for the Owner or Developer for this system or equipment. May be a link to documentation about using this Profile.                                                            |

## Connection Profile Properties

The Connection Profile Properties defines the information required to create an instance of the Profile, known as a Connection. This information consists of any number of named Properties. For each Property, the Profile specifies the Role, either Server or Client that provides the value of the property. Each Property has a unique name within the Profile and Role. Each Property is either mandatory or optional.

The set of all properties marked as “Server” are the Server properties that define a Server Capability

The set of all properties marked as “Client” are the Client properties that define a Client Capability

## Example of Connection Profile definition

Example Profile definition cp:xyz.ics:2 showing header and properties.

```
 Header
    Name        xyz.ics
    Version     2
    Pub Date    2020-03-04 
    Status      Active
    Owner       XYZ Systems, Inc.
    Title       XYZ Control & Automation System
    Server     XYZ ICS App Server
    Client     Visualization Application
    Description Profile for control system for smart buildings. It
                serves visualization data for dashboards and other UI
                needs for all versions of XYZ’s ICS automation system
                since 2003.
    Website     www.example.com/sys/ics.html
Property        uri
    Source      server
    Mandatory   yes
    Description Property for URI of the endpoint XYZ ICS server
    Sample      http://10.0.5.123/dev/sys/api.html?format=json
Property        prot
    Source      server
    Mandatory   no
    Desc        Available protocols from this server. Multiple ok.
    Sample      bacnet, json (default), xml, xyzsys, etc.
Property        client-geo
    Source      client        
    Mandatory   yes
    Desc        Latitude & longitude of the location of XYZ device.
    Sample      ‎37.751, ‎-97.822
Property        cost
    Source      server        
    Mandatory   no
    Desc        The monthly service cost to provide information.
    Sample      USD 2.50 (default $0)
```

## Registering Connection Profiles

Every Connection Profile is registered with the Connection Profile Registry. This registry is intended to be universal, distributed, and accessed locally by reference a local Registry. Any mechanisms for retrieving remote or cached information by the local Registry are out of scope.

Connection Profiles MAY NOT be deleted after the Connection Profile is moved out of Testing status.

# Publishing Connection Capabilities

In CNS/CP, peer systems establish Connection through virtual endpoints or Nodes. A Node is an endpoint that has been published to a Connection Broker with a specific Connection Profile and Role. A Node may publish many Connection Capabilities, or even publish multiple instances of the same Connection Capability for different Contexts. See below for a discussion of Context. A discussion of the Connection Broker is below.

IMPORTANT: The terms "Client" and "Server" are for convenience only, identifying complementary roles in a Connection, and indicating the information that must be part of Publishing each Capability. Any data/information flow between the two ends can be in any direction as required and specified by the Connection Profile.

## The Node ID

The Node ID is at its simplest, a unique identifier for an end-point. It may be a URI, or a URL, or a Decentralized Identifier (DID) (https://www.w3.org/TR/did-core/)or any other identity that makes sense to the Broker.

Some Connection Brokers MAY "own" the Identity, and assign them by some internal process and through a Node registration process. Other Nodes may come with an Identity already set. This document does not specify how Node IDs are determined.

Some Connection Brokers MAY refuse to register a Client or a Server Capability unless the Node is already known to the Broker. Registering a Node may involve cybersecurity concerns that are out of scope for this document.

A Connection Broker MAY reject a submission from an unknown Node.

## The Context

The Context is a string that guides the creation of Connections by the Connection Broker. Its use is similar in some ways to the use of the "topic" in MQTT (https://docs.oasis-open.org/mqtt/mqtt/v5.0/mqtt-v5.0.html). There are no specific rules for creating Context strings in this specification, although specific Connection Profiles or industries may create them.

As a simple example, consider a Connection Profile that enables access to the current room temperature. A Node represents a building’s environmental control system. That Node MAY publish 25 Server Capabilities using the Room Temperature Connection Profile. Using the Context, each Server Capability is distinguished by room. There are many existing semantic standards for Room Number, and this specification has no preference for one versus another.

The Context is mandatory, although it may be submitted as a null string, i.e., "". When used for Context matching, a null string is treated as a wildcard, matching all Contexts. A Connection Broker would create Connections to all rooms for a Client Node that published a null Context. A Connection Broker would create a Connection to any rooms for a Server Node that published a null context.

The Use of the Context is discussed below under the Connection Broker.

## Publishing a Server Capability to a Connection Broker

To publish that a Node is capable of acting as a Server for a specific Connection Profile, the Capability must be submitted to the Connection Broker. The submission of a Server Capability includes the Node ID, the Context, Connection Profile and Role ("server"), and all Properties required for the Server Capability as declared in the Connection Profile.

Example of submitting a Server Capability for publication.

```json
Server Initial Publication {
   "nodeID" : "did:example:123456789abcdefghijk",
   "context" : "context-string",
   "profiles" : {
      "cp" : "test.abc:1",
      "role" : "server",
      "properties": {
         "foo1" : "value for foo1",
         "foo2" : "value for foo2"
         }
      }
   }
```

The server registration above, the test.abc profile, version 1, is identified as cp:test.abc:1/s

The Connection Broker can reference the Connection Profile Registry to find the named Connection Profile and reject any submission that does not meet the specification.

## Publishing a Client Capability to a Connection Broker

To publish that a Node is capable of acting as a Client for a specific Connection Profile, the Capability must be submitted to the Connection Broker. The submission of a Client Capability includes the Node ID, the Context, Connection Profile and Role ("client"), and all Properties required for the Client Role as declared in the Connection Profile.

Example of submitting a Client Capability for publication

```json
    Client Initial Publication { 
        "nodeID" : "did:example:123456789abcdefghijk",
        "context" : "context-string",
        "profiles" : {
            "cp" : "test.abc:2",
            "role" : "client",
            "properties": {
                "foo3" : "value for foo3",
                "foo4" : "value for foo4"
                }
            }
        }
```

The Client Capability above, the test.abc profile, version 2, can be identified as cp:test.abc:2/c

# The Connection Broker

Connection Brokers are systems that create Connections between complementary Client and Server Capabilities. Examples of Brokers are digital twin systems, supervisory systems, marketplaces, etc. A Broker enforces the requirements established by the Connection Profile during Publication. After Publication, the Broker matches Server Capabilities and Client Capabilities that are based on the same Connection Profile.

No part of this specification is intended to limit the potential functions and services provided by a Connection Broker. Profile referencing and propagation is also undefined. A characteristic of the Internet of Things is that there will be legacy systems for some time, likely decades. The narrative presumes automation, but works the same if the Connection Broker supports only human intervention.

## Relationship between Client, Server, and Broker

The publishing process for Clients and Servers is similar. Each Node needs to authenticate itself to the Broker. If the Connection Broker assigns a Node ID, the Node must use this ID in submissions for publication. The difference between the Client/Server registrations is solely in the Role chosen and the Properties it needs to provide, as specified in the Profile.

A Node must provide information as specified for its chosen role in the Connection Profile, and to associate that role with its Node ID.

NOTE: A Node can simultaneously be a published Client and Server to different Connection Profiles and multiple instances of the same Profile. If a Node has published Server Capabilities for multiple versions of a Connection Profile for a Context, and another Node has published Client Capabilities for multiple versions of the same Connection Profile for a matching Context, the Connection Broker will create only a single Connection based on the highest shared version published.

IMPORTANT: The terms "Client" and "Server" are for convenience only, identifying complementary roles in a Connection, and indicating the information that must be provided for registration using a specific Profile. Any data/information flow between the two ends can be in any direction as required and specified by the Connection Profile.

## Receiving Submissions for Publication

The Connection Broker receives submission of Client Capabilities and Server Capabilities for publication. The Connection Broker retrieves the Connection Profile from the Connection Profile Registry and validates the information submitted for conformance with the Connection Profile.

The Connection Broker can reject any submission if:

-   If the Node has not been registered with the Connection Broker and that Broker is configured to reject unknown Nodes.
-   The Connection Profile is unknown.
-   The Connection Profile has been marked as "Deprecated"
-   The Submission does not conform to the requirements defined in the Connection Profile.

(TODO: error codes, including error codes for reason for non-conformance)

(TODO: proper handling of duplicate submissions. A Duplicate has the Same NodeID, the same Connection Profile, the same Role, and the same Context. Are they treated as updates? Under the principle of accept much, but be strict in what you produce)

If the Broker accepts the submission, then the Server Capability or Client Capability is now "published".

## Creating Connections

Upon publication of a Server Capability or a Client Capability, the Broker examines all existing published Nodes for a complementary Capability.

A Client Capability and a Server Capability match, if they are based on the same Connection Profile, have complementary Capabiltiies, and have a matching Context. A Null Context on a Server Capability matches to all Client Capabilities based on the same Connection Profile no matter the Context. A Null Context on a Client Capability matches to all Server Capabilities based on the same Connection Profile no matter the Context.

If there are multiple matches

The Connection Broker creates Connections for all Matches, and assigns those Connections as "New". The Broker then notifies each Node participating in the Connection of the creation of the new Connection.

Example announcement of a new Connection

```json
Connection Created {
    "serverID" : "did:example:12345678abcdefghijk",
    "clientID" : "did:example:abcdefghijk123456789",
    "connectionID" : "NvdmaNDNLPFR9d9dB",
    "cp" : "test.abc:2",
    "context" : "room342",
    "properties": {
        "far1" : "value for far1",
        "far2" : "value for far2"
        }
    }
```

Note that each connection announcement has a reciprocal announcement, the Server Capability is informed of the Client Capability and the Client Capability is informed of the Server Capability, each with their respective Properties.

The components of the message are:

ServerID: The Node with a published Server Role that is part of the Connection.

ClientID: The Node with a published Client Role that is part of the Connection.

ConnectionID: Identifier created by the Connection Broker for this Connection

CP: the Connection Profile including version number

Context: The Context that was matched. If Client and Server have matching Contexts, this is the Context that matched. If one side has a wild card (null string) context and the other has one or more non-empty contexts, then this is the Context from the Node Registration with a specific context.

Note: If one side has an empty context and the other side has many contexts, and all else matches, there will be many Connections announced, one for each Context. Example: An empty context on a RoomTemperature Client Capability may match 25 published Server Capabilities for the RoomTemperature Connection Profile each with a different Context ("room number"). The Connection Broker would announce the creation of 25 Connections to each participating Node, each with its own context.

## Connection Life-cycle

Note: This entire section (5.4.x) deals with system forensics, and error logging, and state changes. This section is still under development and use case examination.

The Connection life-cycle is reflected as a status maintained by the Connection Broker for each Connection. This life-cycle is designed to support system maintenance and replacement.

### Connection Status

New: The Connection has been discovered by the Connection Broker but has not yet been used. A Connection can remain in the New status indefinitely.

Active: A Connection is being used by a Client. This specification makes no rules for when the Client notifies the Broker.

Deprecated: The Connection Profile has been deprecated and users of the Connection should plan to migrate to using a currently supported Connection Profile

Inactive: A Connection is no longer being used by a Client. This specification makes no rules for when the Client notifies the Broker.

Canceled: The Server has deregistered the capability and no longer supports this Connection.

### Discussion of Status

A Connection that has a status of New has never been in use, and MAY be purged from a system as soon as a Capability is removed. A Connection Broker MAY delete a Connection that has the Status New if either end of the Connection is removed from publication.

When a Server removes a publication, the Connection is no longer active. The next action depends on the reason for Cancellation. For example, the reason for removing Server capabilities may be to replace the Environmental Control System for a building with one from a different manufacturer during a renovation. The new system may not support any of the old Server Capabilities. Those Connections may be in use across an enterprise for purposes not known to the construction and maintenance team. The Cancelled Connections provide documentation on what was lost during the replacement. These can be reviewed to assist an integrator at re-creating lost system interoperation.

## Removing published Capabilities.

TODO: Delete Node / Delete Server Capability / Delete Client Capability / Suspend?

## Monitoring Connections

Advanced Connection Brokers may be able to maintain heart-beats, to count packets, and to route connections. Such Brokers would be able to detect status changes of Connections automatically. While this specification foresees such systems, the details of such operation are beyond the scope of this document.

# Connection Broker Federation

Each Connection Broker is inherently a cybersecurity realm, as well as a privacy realm. A Connection Broker is also potentially effectively a message firewall and proxy, a means to exchange information from internal private systems on internal non-routable networks with external systems.

An on-premises Connection Broker may share some of its information with one off-premise Broker, and all with another.

As Connection Brokers evolve, they will take advantage of different forms of centralized and decentralized identity. There will be addressing needs to query "all Servers published in \*that\* Broker". The authors of this specification encourage development in this area as they are undefined.
