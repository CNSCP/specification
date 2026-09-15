# CNS/CP: Connectivity Naming System and Connection Profiles

Authors:  
T. Considine  
A. Budiardjo  
December 2022

System interoperation is the loose coupling of different purpose-built
systems to achieve some purpose. The complexity and ever-developing
diversity of systems has traditionally led to closed integrations that
are neither dynamic, repeatable, or re-usable at scale. These closed
integrations typically require deep domain knowledge of each system by
the integrator.

A growing number of system interactions are across domains. 

Terms for such systems of interoperating systems in the Internet of Things include
Smart Buildings, Industrie 4.0, Digital Twins, Smart Enterprise, Smart
Cities, and Smart Energy. Too often, integrators reach directly to devices 
increasing both technical complexity and increasing integration burden. Large-scale 
integrations should encapsulate technical volatility. Connections to systems rather
than to the device enable the system to encapsulate the volatility and reduce the knowledge required.

In cybersecurity, external observation of
overall system performance and health creates service-oriented security,
particularly useful for detecting cyberthreats to cyberphysical systems.
Regulatory or research systems may use interaction with natural
aggregations of systems such as markets or crowds to understand complex
systems.

Other interactions enable imputing trustworthiness, that is, the fitness
of information or capability for a given purpose.

This specification assumes systems willing to supply a limited and
defined set of information to other systems, and other systems consuming
such information. In a client/server architecture, systems are typically
in a hierarchy, with all clients looking to a few servers for
information. Clients for a particular server have similar purposes and 
scopes. In system-of-system interactions, no such assumption holds. A system
providing information may provide the same set information to any number
of disparate systems. That same system may provide other information to
another set of systems for different purposes. The same system may
itself consume information from any number of other systems. For these
reasons we avoid the terms "server" and "client” and use instead
"provider" and "consumer".

This specification describes the Connection Profile, an intent-based
method to establish connections between systems. Each Connection Profile
defines a Provider Capability and a Consumer Capability. A system that
intends to provide information as described in a Connection Profile
publishes a Provider Capability. A system that intends to consume
information as described in a Connection Profile publishes a Consumer
Capability. Provider and Consumer capabilities from matching Profiles
define potential Connections.

This specification also defines the Connectivity Naming System (CNS).
The purpose of CNS is to create a universal public registry of
Connection Profiles. CNS enables system developers and integrators to
share and re-use their Connection Profiles. While some standard
Connection Profiles may arise over time, this specification does not
define any.

This specification also describes a Connection Broker. The participating
systems publish their intents to a Connection Broker. The Broker finds
matching Capabilities to create Connections. A Connection Broker may act
as a security realm, as systems sharing a Connection Broker establish
Connections.

This specification makes no assumptions about whether Connection Brokers
are stand-alone systems or federated into a larger system, nor how
participants in a federated system handle security between members of
that federation. As in directory services, such decisions are dependent
on the business context of the Connection Broker. An open source (Apache
2.0 license) simple Connection Broker is in development.

This specification does not define or require a specific topology of
systems. While a Connection Profile is typically directional, the
resulting set of Connections may not be. A system may provide multiple
Provider Capabilities as well as multiple Consumer Capabilities. Many
systems may interact using the same Capability. True peers may interact
through reciprocal paired Connection Profiles, each Node offering
reciprocal Capabilities, and each node consuming from the other.

This document is independent of any service or protocol. This document
defines interfaces for each of the systems described through examples
conveyed in JSON; these examples are for clarity but do not mandate a
specific serialization or binding for messages.

# Contents

- [CNS/CP: Connectivity Naming System and Connection Profiles](#cnscp-connectivity-naming-system-and-connection-profiles)
- [Contents](#contents)
- [1. Introduction](#1-introduction)
- [2. The Connection Profile](#2-the-connection-profile)
  - [2.1. Unique Reference for each Connection Profile](#21-unique-reference-for-each-connection-profile)
    - [2.1.1. The Connection Profile Namespace](#211-the-connection-profile-namespace)
    - [2.1.2. Naming a Connection Profile](#212-naming-a-connection-profile)
    - [2.1.3. Versions of a Connection Profile](#213-versions-of-a-connection-profile)
  - [2.2. Status of a Connection Profile](#22-status-of-a-connection-profile)
  - [2.3. Connection Profile Header](#23-connection-profile-header)
  - [2.4. Connection Profile Properties](#24-connection-profile-properties)
    - [2.4.1. Example of a Connection Profile definition](#241-example-of-a-connection-profile-definition)
  - [2.5. Registering Connection Profiles](#25-registering-connection-profiles)
- [3. Publishing Connection Profile Capabilities](#3-publishing-connection-profile-capabilities)
  - [3.1. The Node ID](#31-the-node-id)
  - [3.2. The Context](#32-the-context)
  - [3.3. Publishing a Provider Capability to a Connection Broker](#33-publishing-a-provider-capability-to-a-connection-broker)
- [3.4 Publishing a Consumer Capability to a Connection Broker](#34-publishing-a-consumer-capability-to-a-connection-broker)
- [4. The Connection Broker](#4-the-connection-broker)
  - [4.1. Relationship between Provider, Consumer, and Broker](#41-relationship-between-provider-consumer-and-broker)
  - [4.2. Receiving Submissions for Publication](#42-receiving-submissions-for-publication)
- [5. Creating Connections](#5-creating-connections)
  - [5.1. Connection Life-cycle](#51-connection-life-cycle)
    - [5.1.1. Connection Status](#511-connection-status)
    - [5.1.2. Discussion of Status](#512-discussion-of-status)
  - [5.2. Removing Published Capabilities.](#52-removing-published-capabilities)
  - [5.3. Monitoring Connections](#53-monitoring-connections)
- [6. Connection Security and Conection Broker Federation](#6-connection-security-and-conection-broker-federation)
# 1. Introduction

As more and more systems become embedded in and exposed through the
Internet, there is a growing interest in interoperations between
systems, referred to a Systems of Systems. A discussion of Systems of
Systems must begin with a definition of a System.

A system is a group of interacting or interrelated elements that act
according to a set of rules to form a unified whole. A system has
operational independence, i.e., it is independent and it achieves its
purposes by itself. A system has managerial independence, i.e., it is
managed in large part for its own purposes. A system encapsulates
complexity, i.e., it embodies knowledge and practices in its technology
and code that are beyond what is required to operate a system. A system
maintains integrity, i.e., its protects its inner workings and details
from unauthorized or unplanned observation or manipulation. Systems are
typified by central control, global visibility, hierarchical structures,
and coordinated activities.

A System of Systems is built on the interconnection between otherwise
independent systems, to create capabilities and properties that do not
reside in those independent systems (Nodes). The Nodes are likely to be
physically and functionally heterogeneous. The Nodes may operate in
different technological domains. Nodes may be geographically dispersed.
Different regulatory frameworks may govern each node. Nodes may have
different lifespans and different upgrade schedules so that tight
integration of Nodes would create an on-going requirement for complex
technical maintenance.

This document describes a method for defining and documenting persistent
connections between systems to enable Systems of Systems.

A growing number of regulatory agencies and governance processes rely on
up-to-date access access to standard information. Examples include
central bank tracking of economic indicators, public health tracking,
and smart cities. Certification and testing organizations want
connections from their records to widely disbursed individual systems,
and for reliability and safety data to be collected from those systems
to augment the information gathered during testing. Owners and operators
of these systems would like access to both. 

The Digital Twin Consortium has described using connections to expose
trust vectors to enable machine-to-machine (M2M) to assess the
trustworthiness of a connection for particular purpose
([*_https://www.digitaltwinconsortium.org/assuring-trustworthiness-in-dynamic-systems-using-digital-twins-and-trust-vectors-form/_*](https://www.digitaltwinconsortium.org/assuring-trustworthiness-in-dynamic-systems-using-digital-twins-and-trust-vectors-form/));
similar data from a manufacturing system may be trustworthy for
inventory purposes, but not trustworthy enough to support a life/safety
application. 

This document describes a method to define and document the regular
conveyance of data between diverse privately held systems to mandated
processes and within regulated environments..

There is a growing interest in Digital Twins, and in the Internet of
Things.

Digital Twinning names a wide-ranging set of practices to aggregate data
derived from a system, to construct a model of a system, or to simulate
a system. Digital twins are finding wide use in multi-actor and
cloud-native applications, wherein remote system telemetry may be
compared to the output of its simulated twin for continuous
commissioning of systems and for validation of the simulation. If the
simulated twin is accelerated in time, then the twin can be a component
of multiple simulations to discover a preferred operating model. A
system and its twin can each receive the same messages and inputs, and
the results can generate parallel sets of telemetry and messages as
outputs for comparison. The aim is to develop various rich networked
applications and realize efficient and cost-effective data-driven
process management.

The Internet of Things (IoT) denotes the interconnection of highly
heterogeneous networked entities and networks. The networked Things
named as components of the Internet of Things are smart devices and
systems that understand and react to their environment. The complexity
of a thing may range from a simple network-addressable sensor to a
purpose-built autonomous system.

The systems that comprise the IoT are typically diverse and often
purpose-built. Inconsistent technical implementations and the
heterogeneity of vendor and industrial sector technologies make it
difficult to establish consistent connectivity. Even telemetry from an
IoT system may be drawn directly from a sensor, or computed based on
several sensors, or even imputed to meet the needs of the consuming
system. cover gaps in sensor reading. More abstract information, such as
building occupancy, or automated maintenance parts ordering may require
detailed internal knowledge of a system. The internal process may be
complex, but the external telemetry requirement may be simple.

The Internet Research Task Force (IRTF) describes the Internet of Things
(IoT) in RFC 8576. “Internet of Things (IoT) Security: State of the Art
and Challenges (https://datatracker.ietf.org/doc/html/rfc8576). Systems
of Digital Twins are described in “Digital Twin Network: Concepts and
Reference Architecture”
(https://www.ietf.org/id/draft-irtf-nmrg-network-digital-twin-arch-02.html).

The IRTF Digital Twin Reference Architecture names and defines three
layers: Application Layer, Digital Twin Layer, and Physical Network
Layer. This document describes a machine-readable catalog of the Digital
Twin layer. CNS/CP catalogs expose components and functionalities and
define standardized and unified interfaces.

System of Systems interactions synchronize all or a subset of the data
related to involved component systems in real time, which inevitably
expands the attack surface, and increases the risk of information
leakage. This specification supports the development of more secure data
mechanisms and data protection methods, even atop legacy systems and
protocols by enabling the use of defined, re-usable Nodes. A providing
system may be configured to share simple data that has in fact been
computed by selecting one internal point source among many, or averaging
several such points, or even inferring missing data. Information sharing
between complex operational systems should access the system, and not
through direct access to internal sensors or components that may be on
protected networks. 

This specification defines a means to describe complementary roles by
which two nodes may interact, known as a Connection Profile. For clarity
of exposition, we designate one role to have a “Provider Capability" and
the other a "Consumer Capability". These terms are limited to a
particular published interaction and do not limit the autonomy of either
node.

We name the relationship between two systems using the same Connection
Profile, one as a Provider Capability and the other as a Consumer as
having a “Connection”.

A system acting as a Provider in one interaction may act as a Consumer
in another. It is important for both the Consumer and the Provider to
use defined and constrained interactions with such systems so that:

  - Interactions do not harm or impair the functioning of remote
    systems.
  - Would-be consumers of information (“Consumers”) can find appropriate
    sources of information or service without needing to understand the
    inner workings of the systems providing the information or service
    ("Providers").
  - Complementary nodes can negotiate protocols and specifications
    needed for interoperation.
  - Connections to Providers are discoverable and can be re-used by
    different Consumers to accomplish a variety of ends.
  - The work performed to expose a Provider Capability can be re-used
    over the life of a system.

A Node may chain Connections together. The sole purpose of a particular
Node may be to process other Connections. A Node may provide translation
of message or of binding and expose that using a different Profile. A
Consumer Node may process information from several Provider Nodes and
publish a Provider Node representing a single summary or consensus. Such
nodes would register as Consumer Nodes to consume data from Providers
and Provider Nodes, to provide services to other Consumers.

The specification also describes the Connection Broker.

In telecommunications and cloud computing architectures, there is a
distinction made between the control plane and the data plane. The
control plane manages data provisioning and may orchestrate data
services across data storage pools but does not access the data
directly. The data plane manages data layout, storage devices, and
read/write to data.

By analogy, a Connection Broker documents a specific control plane
defining each Node using Connection Profiles. The data plane is the
connections between Nodes, which may either pass information through the
Connection Broker or out of band using a different mechanism.

Throughout this document, we use examples drawn from Connections between
systems that operate buildings and enterprise systems. The value of
documenting such Connection Capabilities is high because systems in
buildings and infrastructure often remain in production for decades
rather than the years typical for enterprise systems. The examples are
selected for simplicity and ease of understanding. No part of this
specification is intended to limit the use of Connection Profiles to
this one class of Connections.

# 2. The Connection Profile

A Connection Profile contains specific information on how a type of
Consumer will connect with a type of Provider. Each Connection Profile
defines a Provider Capability and a Consumer Capability. A Connection
Profile defines a template/model used when creating specific
Connections. In other words, a Connection Profile describes the metadata
required to establish a Connection to enable interoperation between
systems.

The owner (see Connection Naming, below) of a Connection Profile
registers that Profile in the Connection Profile Registry, described
below. A system developer or integrator can reference the Connection
Profile to publish that a specific system supports Provider or a
Consumer Capability to a Connection Broker. A Connection Broker can
reference the Connection Profile Registry to validate the publication of
each Node. The Connection Broker uses this published information to
create Connections between a Consumer Node and a Provider Node to enable
the specific purpose defined in the Connection Profile.

Each Connection Profile has a unique name and a version number. For
example, consider a Connection Profile to connect the Capabilities
“dataserver” defined by the Example company. It is referenced by the
URN CP:example.dataserver:1. Connection Profile Names are discussed
below.

Each Connection Profile includes a Header section describing the origins
of the Profile and its purpose. A Connection Profile also itemizes the
name/value pairs for a Provider and for a Consumer using that profile.

IMPORTANT: The terms “Provider” and "Consumer" are for convenience only,
identifying complementary roles in a Connection, and indicating the
information that must be part of Publishing each Capability. Any
data/information flow between the two ends can be in any direction as
required and specified by the Connection Profile.

## 2.1. Unique Reference for each Connection Profile

The Connection Profile Registry maintains a persistent distributed
registry for Connection Profiles. A Connection Profile can be uniquely
referenced as cp:namespace.sub1.sub2:version.

### 2.1.1. The Connection Profile Namespace

In a manner similar to Internet domain names, an organization registers
for rights to a Namespace. This organization “owns” all Connection
Profiles in that namespace. The Namespace owner defines new Connection
Profiles and registers them in the Connection Profile Registry.

### 2.1.2. Naming a Connection Profile

The owner of a Namespace may create subordinate parts of a Connection
Profile name (sub1 and sub2 in the example cp:namespace.sub1.sub2),
using any system that makes sense to the Namespace owner.

### 2.1.3. Versions of a Connection Profile

Each new version of a Connection Profile defines Capabilities for a
Publisher and a Consumer. To create a connection, the Connection Profile, 
the Version, and the Context MUST match. See discussion of the use of the Context below.

A Node may Publish its Capability of acting as a Provider for multiple
versions of a Connection Profile by publishing a Publisher Capability
for each. A Node may Publish its Capability of acting as a Consumer for
multiple versions of a Connection Profile by publishing a Consumer
Capability for each.

Publisher Capability v3 only connects with Consumer Capability v3. A
Developer that wishes to add additional Properties must declare a new
Version number.

## 2.2. Status of a Connection Profile

Each new Name and Version is registered with a status of “Testing”. The
distributed Connection Registry should not accept remote inquiries about
a Connection Profile with the Status “Testing”. This is so a system or a
Connection Broker owned by the Namespace owner can test the New profile
without risk of creating one-offs around the world.

When the developer is ready to release a Version, the status is set to
"Active". A developer SHALL NOT change any properties of a Connection
Profile Version after it is “Active”.

Active Connection Profiles MAY be referenced from Connection Registry
services external to the Namespace owner and referenced when publishing
Nodes in a Connection Broker. Changing the properties once the status is
“Active” could invalidate existing Connections and break existing
publications.

In general, once a Connection Profile is “Active” it persists forever.
Defining additional versions creates new capabilities, but does not
invalidate any existing Connection. However, if the Developer sets the
Status of a profile to “Deprecated”, no new Connections will be
automatically created for that Version.

## 2.3. Connection Profile Header

The Connection Profile Header contains metadata about the Profile,
including the owner’s name, Profile title, description, a URL link to
additional specifications for the use of the Profile, and other
information necessary to inform developers and integrators on how to use
the Profile.

The Connection Profile Header metainformation properties are as below.
All fields are mandatory.

|             |                                                                                                                                                                                                  |
| ----------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Attribute   | Description                                                                                                                                                                                      |
| Name        | The name of the Profile. Must match that published in the Registry                                                                                                                               |
| Version     | Every profile includes a version number. Publishing v3 does not invalidate the Nodes currently published with v2. The Version should be considered as a mandatory component of the Profile name. |
| Pub Date    | Date the Name/Version was set to Active                                                                                                                                                          |
| Status      | Testing: Profile is in development Active: Profile development is complete.                                                                                                                      |
| Owner       | Usually business name of the owner/developer                                                                                                                                                     |
| Title       | Title for the Profile                                                                                                                                                                            |
| Provider    | Title for the Provider Capability based on this Profile                                                                                                                                          |
| Consumer    | Title for the Consumer Capability based on this Profile                                                                                                                                          |
| Description | General narrative describing the use, purpose and perhaps history of this Connection Profile                                                                                                     |
| Website     | URL of the web site for the Owner or Developer for this system or equipment. May be a link to documentation about using this Profile.                                                            |

## 2.4. Connection Profile Properties

The Connection Profile Properties defines the information required to
publish a Provider or Consumer Capability.

This Connection Profile consists of any number of named Properties. For
each Property, the Profile specifies the Role, either Provider or
Consumer that provides the value of the property. Each Property has a
unique name within the Profile and Role. Each Property is designated as
Required (mandatory = “yes”) or Optional (mandatory = “no”).

A Provider Capability is defined as the set of all Properties whose
source is “provider”.

A Consumer Capability is defined as the set of all Properties whose
source is “consumer”.

### 2.4.1. Example of a Connection Profile definition

Example Profile definition cp:xyz.ics:2 showing Header and Properties.
All Header elements are mandatory. A publication of a Capability as
Provider or as a Consumer MUST provide each Required Property and MAY
provide any Optional Properties.

```json
  Sample Profile Registration{
    "Header" : {
      "Name" :      "xyz.ics",
      "Version" :   "2",
      "Pub Date" :  "2020-03-04", 
      "Status" :    "Active",
      "Owner" :     "XYZ Systems, Inc.", 
      "Title" :     "XYZ Control & Automation System",
      "Producer" :  "XYZ ICS App Server",
      "Consumer" :  "Visualization Application",
      "Desc" :      "Profile for control system for smart buildings. It
                     serves visualization data for dashboards and other UI
                     needs for all versions of XYZ’s ICS automation system
                     since 2003.",
      "Website" :   "www.example.com/sys/ics.html"
    },
    "Property" : {
      "Name" :       "uri",
      "Source" :     "provider",
      "Mandatory" :  "yes",
      "Description" :"Property for URI of the endpoint XYZ ICS server",
      "Sample" :     "http://10.0.5.123/dev/sys/api.html?format=json"
    },
    "Property" : {
      "Name" :        "prot",
      "Source" :      "provider",
      "Mandatory" :   "no",
      "Desc" :        "Available protocols from this server. Multiple ok.",
      "Sample" :      "bacnet, json (default), xml, xyzsys, etc."
    },
    "Property" : {
      "Name" :        "client-geo",
      "Source" :      "consumer",        
      "Mandatory" :   "yes",
      "Desc" :        "Latitude & longitude of the location of XYZ device.",
      "Sample" :      "‎37.751, ‎-97.822",
    },
    "Property" : {
      "Name" :        "cost",
      "Source" :      "provider",        
      "Mandatory" :   "no",
      "Desc" :        "The monthly service cost to provide information.",
      "Sample" :      "USD 2.50 (default $0)"
    }
  }

```

## 2.5. Registering Connection Profiles

Every Connection Profile is registered with the Connection Profile
Registry. This Registry is intended to be universal, distributed, and
accessed locally by reference to a local Registry. Any mechanisms for
retrieving remote or cached information by the local Registry are out of
scope.

Because Capabilities may be Published in any number of private
Connection Brokers, there is no way to determine if a registered
Connection Profile currently in use. For this reason, Connection
Profiles MAY NOT be deleted after the Connection Profile is moved out of
Testing status.

# 3. Publishing Connection Profile Capabilities

In CNS/CP, peer systems establish Connection through virtual endpoints
or Nodes. A Node is an endpoint that has been published to a Connection
Broker with a specific Connection Profile, Context, and Role. A Node may publish
many Connection Capabilities, or even publish multiple instances of the
same Connection Capability for different Contexts. See below for a
discussion of Context. A discussion of the Connection Broker is below.

## 3.1. The Node ID

The Node ID is at its simplest, a unique identifier for an end-point. It
may be a URI, or a URL, or a Decentralized Identifier (DID)
([*https://www.w3.org/TR/did-core/*](https://www.w3.org/TR/did-core/))
any other identity that makes sense to the Broker.

Some Connection Brokers MAY "own" the Identity, and assign them by some
internal process and through a Node registration process. Other Nodes
may come with an Identity already set. This document does not specify
how Node IDs are determined.

Some Connection Brokers MAY refuse to register a Consumer or a Provider
Capability unless the Node is already known to the Broker. Registering a
Node may involve cybersecurity concerns that are out of scope for this
document.

A Connection Broker MAY reject a submission from an unknown Node.

## 3.2. The Context

The Context is a string that distinguishes one published Capability from 
the same Connection Profile and type. 

As a simple example, consider a Connection Profile that enables access to 
the current room temperature. A Node represents a building’s environmental 
control system. That Node uses the Provider Capability to publish the room temperature of each 
conference room in the building. The node distinguishes these Capabilities 
by using the Contexts "ConfRm101", "ConfRm103", "ConfRm201", "ConfRm301", 
"ConfRm602", and ConfRmExec.

A system able to use the Consumer Capability of the same Connection Profile 
could request a access to the room temperatures of the ground floor conference 
room ("ConfRm101") and of the executive conference room ("ConfRmExec) could 
publish two Consumer capabilities with the Contexts "ConfRm101" and "ConfRmExec".
The Broker would discover the two matches and create two Connections. The consuming 
system would be able to use these two Connections to read each room's temperature. 

There are many existing semantic standards for Room Number, and this specification
has no preference for one versus another.

A Context MAY be a UID with no 
human-accessible reference. Its use is similar in some ways to the use of the
"topic" in MQTT
([*https*<span id="anchor"></span>*://docs.oasis-open.org/mqtt/mqtt/v5.0/mqtt-v5.0.html*](https://docs.oasis-open.org/mqtt/mqtt/v5.0/mqtt-v5.0.html)).
There are no specific rules for creating Context strings in this
specification, although specific Connection Profiles or industries may
create them.

While Context is a mandatory component of Capability publishing, the 
Context MAY be a null or emptry string. In this case, the Broker treats it 
as a wildcard, able to match every published room temperature Provider. In
this case, the Broker would return six Connections, one for each published 
Provider Capability  

The Use of the Context is discussed below under the Connection Broker.

## 3.3. Publishing a Provider Capability to a Connection Broker

To publish that a Node is capable of acting as a Provider for a specific
Connection Profile, the Capability must be submitted to the Connection
Broker. The submission of a Provider Capability includes the Node ID,
the Context, Connection Profile and Role ("provider"), and all
Properties required for the Provider Capability as declared in the
Connection Profile.

Example of submitting a Provider Capability for publication.

```json
Provider Initial Publication {
   "nodeID" : "did:example:123456789abcdefghijk",
   "context" : "context-string",
   "profiles" : {
      "cp" : "test.abc:1",
      "role" : "provider",
      "properties": {
         "foo1" : "value for foo1",
         "foo2" : "value for foo2"
         }
      }
   }
```

The Provider registration above, the test.abc profile, version 1, is
identified as cp:test.abc:1/p

The Connection Broker can reference the Connection Profile Registry to
find the named Connection Profile and reject any submission that does
not conform to the definition.

# 3.4 Publishing a Consumer Capability to a Connection Broker

To publish that a Node is capable of acting as a Consumer for a specific
Connection Profile, the Capability must be submitted to the Connection
Broker. The submission of a Consumer Capability includes the Node ID,
the Context, Connection Profile and Role ("Consumer"), and all
Properties required for the Consumer Role as declared in the Connection
Profile.

Example of submitting a Consumer Capability for publication

```json
    Consumer Initial Publication { 
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

The Consumer Capability above, the test.abc profile, version 2, can be
identified as cp:test.abc:2/c

# 4. The Connection Broker

Connection Brokers are systems that create Connections between
complementary Consumer and Provider Capabilities. Examples of Brokers
are digital twin systems, supervisory systems, marketplaces, etc. A
Broker enforces the requirements established by the Connection Profile
during Publication. After Publication, the Broker matches Provider
Capabilities and Consumer Capabilities that are based on the same
Connection Profile.

No part of this specification is intended to limit the potential
functions and services provided by a Connection Broker. Profile
referencing and propagation is also undefined. A characteristic of the
Internet of Things is that there will be legacy systems for some time,
likely decades. The narrative presumes automation, but works the same if
the Connection Broker supports only human intervention.

## 4.1. Relationship between Provider, Consumer, and Broker

The publishing process for Providers and Consumers is similar. Each Node
needs to authenticate itself to the Broker. If the Connection Broker
assigns a Node ID, the Node must use this ID in submissions for
publication. The difference between the Provider and Consumer
publication is solely in the Role chosen and the Properties it needs to
provide, as specified in the Connection Profile.

A Node must provide information as specified for its chosen role in the
Connection Profile, and to associate that role with its Node ID.

NOTE: A Node can simultaneously be a published as a Provider or a
Consumer for different Connection Profiles or for multiple instances of
the same Capability. If a Node has published Provider Capabilities for
multiple versions of a Connection Profile for a Context, and another
Node has published Consumer Capabilities for multiple versions of the
same Connection Profile for a matching Context, the Connection Broker
will create only a single Connection based on the highest shared version
published.

## 4.2. Receiving Submissions for Publication

The Connection Broker receives submission of Consumer Capabilities and
Provider Capabilities for publication. The Connection Broker retrieves
the Connection Profile from the Connection Profile Registry and
validates the information submitted for conformance with the Connection
Profile.

The Connection Broker can reject any submission if:

  - If the Node has not been registered with the Connection Broker and
    that Broker is configured to reject unknown Nodes.
  - The Connection Profile is unknown.
  - The Connection Profile has been marked as "Deprecated"
  - The Submission does not conform to the requirements defined in the
    Connection Profile.

(TODO: error codes, including error codes for reason for
non-conformance)

(TODO: proper handling of duplicate submissions. A Duplicate has the
Same NodeID, the same Connection Profile, the same Role, and the same
Context. Are they treated as updates? Under the principle of accept
much, but be strict in what you produce)

If the Broker accepts the submission, then the Provider Capability or
Consumer Capability is now "Published".

# 5. Creating Connections

Upon Publication of a Provider Capability or a Consumer Capability, the
Broker examines all registered Nodes for a complementary Capability.

A Consumer Capability and a Provider Capability are complementary if
they are based on the same Connection Profile and have a matching
Contexts. A Null Context on a Provider Capability matches to all
Consumer Capabilities based on the same Connection Profile no matter the
Context. A Null Context on a Consumer Capability matches to all Provider
Capabilities based on the same Connection Profile no matter the Context.

The Connection Broker creates potential Connections for all Matches, and
assigns those Connections the status "New". The Broker then notifies
each Node participating in the Connection of the creation of the new
Connection.

Example announcement of a new Connection
```json
Connection Created {
  "providerID" :  "did:example:12345678abcdefghijk",
  "consumerID" :  "did:example:abcdefghijk123456789",
  "connectionID" :"NvdmaNDNLPFR9d9dB",
  "cp" :          "test.abc:2",
  "context" :     "room342",
  "properties": {
    "far1" :        "value for far1",
    "far2" :         "value for far2"
    }
  }
```

Note that each connection announcement has a reciprocal announcement,
the Provider Capability is informed of the Consumer Capability and the
Consumer Capability is informed of the Provider Capability, each with
their respective Properties.

The components of the message are:

  - ProviderID: The Node with a published Provider Capability that is
    part of the Connection.
  - ConsumerID: The Node with a published Consumer Capability that is
    part of the Connection.
  - ConnectionID: Identifier created by the Connection Broker for this
    Connection
  - CP: the Connection Profile including version number
  - Context: The Context that was matched.

If Consumer and Provider have matching Contexts, this is the Context
that matched. If one side has a wild card (null string) context and the
other has one or more non-empty contexts, then this is the Context from
the Node Registration with a specific context.

Note: If one side has an empty context and the other side has many
contexts, and all else matches, there will be many Connections
announced, one for each Context. Example: An empty context on a
RoomTemperature Consumer Capability may match 25 published Provider
Capabilities for the RoomTemperature Connection Profile each with a
different Context ("room number"). The Connection Broker would announce
the creation of 25 new Connections to each participating Node, each with
its own context.

## 5.1. Connection Life-cycle

Note: This entire section (5.4.x) deals with system forensics, and error
logging, and state changes. This section is still under development and
use case examination.

The Connection life-cycle is reflected as a status maintained by the
Connection Broker for each Connection. This life-cycle is designed to
support system maintenance and replacement.

### 5.1.1. Connection Status

During the life of a Connection, the Connection Broker maintains a
Connection status that determines how that Connection may be used by
participating Nodes.

|            |                                                                                                                                                          |
| ---------- | -------------------------------------------------------------------------------------------------------------------------------------------------------- |
| New        | The potential Connection has been discovered by the Connection Broker but has not yet been used. A Connection can remain in the New status indefinitely. |
| Active     | A Connection is being used by a Consumer. This specification makes no rules for when the Consumer notifies the Broker.                                   |
| Deprecated | The Connection Profile has been deprecated and users of the Connection should plan to migrate to using a currently supported Connection Profile          |
| Inactive   | A Connection is no longer being used by a Consumer. This specification makes no rules for when the Consumer notifies the Broker.                         |
| Canceled   | The Provider has deregistered the Capability and no longer supports this Connection.                                                                     |

### 5.1.2. Discussion of Status

A Connection that has a status of New has never been in use, and MAY be
purged from a system as soon as a Capability is removed. A Connection
Broker MAY delete a Connection that has the Status New if either end of
the Connection is removed from publication.

When a Provider removes a publication, the Connection is no longer
active. The next action depends on the reason for Cancellation. For
example, the reason for removing Provider capabilities may be to replace
or upgrade a System. The new system may not support any of the old
Provider Capabilities. Those Connections may be in use across an
enterprise for purposes not known to the system upgrade team. The
Cancelled Connections provide documentation on what was lost during the
replacement. These can be reviewed to assist an integrator at
re-creating lost system interoperation.

## 5.2. Removing Published Capabilities.

TODO: Delete Node / Delete Provider Capability / Delete Consumer
Capability / Suspend?

## 5.3. Monitoring Connections

Advanced Connection Brokers may be able to maintain heart-beats, to
count packets, and to route connections. Such Brokers would be able to
detect status changes of Connections automatically. While this
specification foresees such systems, the details of such operation are
beyond the scope of this document.

# 6. Connection Security and Conection Broker Federation

Each Connection Broker is inherently a cybersecurity realm, as well as a
privacy realm. A Node can only publish a Capability to a Broker that 
accepts its identity. A Broker only discovers matching capabilities between
Nodes that have published Capabilities to that Broker. In effect, a Connection
Broker can act as a message firewall and proxy, a means to exchange information from
internal private systems on internal non-routable networks with external
systems.

An on-premises Connection Broker may federate with an off-premise Connection 
Broker in accord with rules established by that federate. Federations MAY be 
designed that enable transitive connections across multiple Brokers.

As Connection Brokers evolve, they will take advantage of different
forms of centralized and decentralized identity. There will be
addressing needs to query "all Providers published in \*that\* Broker".
The authors of this specification encourage development in this area even 
as they are undefined.
