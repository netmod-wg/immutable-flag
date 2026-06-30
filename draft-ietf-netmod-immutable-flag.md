---
title: "YANG Metadata Annotation for Immutable Flag"
abbrev: "immutable flag"
category: std
updates: 8040, 8526

docname: draft-ietf-netmod-immutable-flag-latest
submissiontype: IETF
number:
date:
consensus: true
v: 3
area: "Operations and Management"
workgroup: "netmod"
keyword:
 - immutable flag
 - system configuration

author:
 -
    fullname: Qiufang Ma
    organization: Huawei
    role: editor
    street: 101 Software Avenue, Yuhua District
    city: Nanjing, Jiangsu
    code: 210012
    country: China
    email: maqiufang1@huawei.com
 -
    fullname: Qin Wu
    organization: Huawei
    street: 101 Software Avenue, Yuhua District
    city: Nanjing, Jiangsu
    code: 210012
    country: China
    email: bill.wu@huawei.com
 -
    fullname: Balazs Lengyel
    organization: Ericsson
    role: editor
    email: balazs.lengyel@ericsson.com
 -
    fullname: Hongwei Li
    organization: HPE
    email: flycoolman@gmail.com

normative:

informative:

  TR-531:
     title: UML to YANG Mapping Guidelines
     author:
       -
        organization: "ONF"
     target: https://opennetworking.org/wp-content/uploads/2018/08/TR-531_UML-YANG_Mapping_Gdls_v1.1-1-1.pdfhttps://opennetworking.org/wp-content/uploads/2018/08/TR-531_UML-YANG_Mapping_Gdls_v1.1-1-1.pdf
     date: July 2018

  TS28.623:
     title: "Telecommunication management; Generic Network Resource Model (NRM) Integration Reference Point (IRP); Solution Set (SS) definitions"
     author:
       -
        organization: "3GPP"
     target: https://www.3gpp.org/ftp/Specs/archive/28_series/28.623/28623-i02.zip
     date: false

  TS32.156:
     title: "Telecommunication management; Fixed Mobile Convergence (FMC) Model repertoire"
     author:
       -
        organization: "3GPP"
     target: https://www.3gpp.org/ftp/Specs/archive/32_series/32.156/32156-h10.zip
     date: false

  RFC8792:

--- abstract

   This document defines a mechanism to formally document an existing behavior,
   implemented by servers in production, on the immutability of some
   system-provided nodes, using a YANG metadata annotation called
   "immutable" to flag which nodes are immutable.

   Clients may use "immutable" annotations provided by the server, to
   know beforehand why certain otherwise valid configuration requests
   will cause the server to return an error.

   The immutable flag is descriptive, documenting an existing behavior, not
   prescriptive, dictating server behaviors.

   This document updates RFC 8040 and RFC 8526.

--- middle

# Introduction

   This document defines a YANG metadata annotation {{!RFC7952}} to formally document
   an existing model handling behavior that has been used by
   multiple standard organizations (e.g., 3GPP TS 32.156 {{TS32.156}}, 28.623 {{TS28.623}}, and ONF TR-531 {{TR-531}}) and vendors.

   YANG {{!RFC7950}} is a data modeling language used to model both state
   and configuration data, based on the "config" statement. However,
   there exists some system configuration data that cannot be modified
   by clients (that is, it is immutable), but still needs to be declared as
   "config true" to:

   * allow configuration of data nodes under immutable lists or containers;

   * place "when", "must" and "leafref" constraints between configuration
   and immutable nodes;

   * ensure the existence of specific list entries that are provided and
   needed by the system, while additional list entries can be created,
   modified or deleted.

   If a server always rejects a client's attempt to override some
   system-provided data because it internally thinks the data is immutable, it should document
   it towards the clients in a machine-readable way rather than writing as
   plain text in the "description" statement.

   This document defines an approach to formally document an existing behavior,
   implemented by servers in production, on the immutability of some
   system-provided data, using a YANG metadata annotation {{!RFC7952}}
   called "immutable" to flag which nodes are immutable. A server MUST NOT set the immutable flag to true for configuration data that is not system-provided. This document does not
   regulate server behaviors. That said, it is expected that a server will return
   an error with an error-tag value of "invalid-value" if a client attempts to
   modify an immutable node.

   A non-exhaustive list of already implemented and potential use cases is provided below:

   * UC1: Modeling of server capabilities ({{sec-uc1}})
   * UC2: Hardware based auto-configuration ({{sec-uc2}})
   * UC3: Predefined administrator roles ({{sec-uc3}})
   * UC4: Declaring immutable system configuration from the perspective of a Logical Network Element (LNE) ({{sec-uc4}})

## Updates to RFC 8040

  This document updates {{Sections 4.8 and 9.1.1 of !RFC8040}} to add an
  additional query parameter named "with-immutability", as specified in {{RESTCONF-ext}}.

## Updates to RFC 8526

   This document updates {{Section 3.1.1 of !RFC8526}} to add an additional input parameter
   named "with-immutability" for the \<get-data\> operation, as specified in {{NETCONF-ext}}.


## Editorial Note (To be removed by RFC Editor)

  Note to the RFC Editor: This section is to be removed prior to publication.

  This document contains placeholder values that need to be replaced with finalized
  values at the time of publication.  This note summarizes all of the
  substitutions that are needed.  No other RFC Editor instructions are specified
  elsewhere in this document.

  Please apply the following replacements:

  * XXXX --> the assigned RFC number for this draft
  * YYYY --> RFC number assigned to {{!I-D.ietf-netmod-system-config}}
  * 2026-05-26 --> the actual date of the publication of this document

# Conventions and Definitions

{::boilerplate bcp14-tagged}

   The document uses the following term defined in {{!RFC6241}}:

   * configuration data

   The document uses the following terms defined  in {{!RFC7950}}:

   * data node
   * leaf
   * leaf-list
   * container
   * list
   * anydata
   * anyxml
   * interior node
   * data tree

   The document uses the following term defined in {{!RFC8341}}:

   * access operation

   The document uses the following term defined in {{?I-D.ietf-netmod-system-config}}:

   * system configuration

   This document defines the following term:

   immutable flag:
   : A read-only server-provided metadata value that describes the
   immutability of the configuration, which is conveyed via a YANG metadata annotation
   called "immutable" with a boolean value.

# Applicability

   While the immutable flag applies to all configuration nodes, its value MUST NOT be set to true for configuration data that is not system configuration.

   The immutable flag is only visible in read-only datastores (i.e., \<system\>
   {{?I-D.ietf-netmod-system-config}}, \<intended\>, and \<operational\>)
   when a "with-immutability" parameter is carried ({{with-immutability}}).
   However, this only serves as descriptive information about the
   instance node itself, but has no effect on the handling of the read-only
   datastore. If the immutable flag is requested to be returned for an invalid
   datastore (i.e., any datastore other than \<system\>, \<intended\>, or \<operational\>), then the server MUST return an error response with the error-tag value
   "unknown-element".

   Configuration data has the same immutability if it appears in different datastores.
   The immutability of configuration data is protocol and
   user independent.

# "Immutable" Metadata Annotation

## Definition {#immutable-def}

   The immutable flag which is defined as the metadata annotation takes a boolean
   value, and it is returned as requested by the client using a "with-immutability"
   parameter ({{with-immutability}}). If the "immutable" metadata annotation for
   a configuration node is not specified, the default "immutable" value is the
   same as the value of its parent node in the data tree ({{interior}}).
   The "immutable" metadata annotation value for a top-level instance
   node is "false" if not specified.

   A node annotated as immutable indicates that the server does not allow it to be changed by configuring
   a different value in read-write configuration datastores (e.g., \<running\>),
   or deleted from the intended datastore, which is the merged result of \<running\> and \<system\> as defined in {{Section 4 of ?I-D.ietf-netmod-system-config}}. The node MAY be explicitly configured by a client in \<running\> with the
   same value and that configuration in \<running\> may subsequently be removed,
   but neither of these edits will change the configuration in \<intended\> (if implemented) on the device.

   Note that "immutable" metadata annotations are used to annotate data node
   instances.  A list may have multiple instances in the data tree,
   servers may annotate some of the instances as immutable, while others as
   mutable.

   Servers MUST ignore any immutable annotations sent from the client.

## "with-immutability" Parameter {#with-immutability}

   This section specifies the NETCONF {{!RFC6241}} {{!RFC8526}} and RESTCONF {{!RFC8040}}
   protocol extensions to support the "with-immutability" parameter.
   The "immutable" metadata annotations are not returned in a response unless
   explicitly requested by the client using this parameter.

### NETCONF Extensions to Support "with-immutability" {#NETCONF-ext}

   This document updates {{!RFC8526}} to augment the \<get-data\>
   operation with an additional parameter named "with-immutability" when interacting with read-only datastores.
   If present, this parameter requests that the server includes
   the "immutable" metadata annotations in its response.

   {{tree}} provides the tree structure {{?RFC8340}} of augmentations to NETCONF
   operations, as defined in the "ietf-immutable-annotation" module ({{module}}).

~~~~
module: ietf-immutable-annotation
  augment /ncds:get-data/ncds:input:
    +---w with-immutability?   empty
~~~~
{: #tree title="Augmentations to NETCONF Operations" artwork-align="center}

To discover if the "with-immutability" parameter is supported by a server,
a NETCONF client can query if the server implements "ietf-immutable-annotation" module ({{module}}) by reading the YANG library information from the operational state datastore, as per {{!RFC8526}}.

Refer to {{NETCONF-example}} for an example of a NETCONF operation with "with-immutability" input parameter.

### RESTCONF Extensions to Support "with-immutability" {#RESTCONF-ext}

   This document extends {{Sections 4.8 and 9.1.1 of !RFC8040}} to add a query
   parameter named "with-immutability" to the GET operation. If present, this parameter
   requests that the server includes the "immutable" metadata annotations in its
   response. This parameter is only allowed with no values carried when interacting with read-only datastores.
   If it has any unexpected value, then a "400 Bad Request" status-line MUST be returned. The error-tag value "invalid-value" is used in this case.
   RESTCONF protocol operations for the datastore resources are defined in {{!RFC8527}}.

   To enable a RESTCONF client to discover if the "with-immutability" query parameter
   is supported by a server, the following capability URI is defined:

~~~~
    urn:ietf:params:restconf:capability:with-immutability:1.0
~~~~

If the "with-immutability" query parameter URI is listed in the "capability"
leaf-list defined in {{Section 9.3 of !RFC8040}}, then the server supports the
"with-immutability" query parameter.

Refer to {{RESTCONF-example}} for an example of a RESTCONF operation with "with-immutability" query parameter.


# Use of Immutable Flag for Different Statements

   This section defines what the immutable flag means to the client for
   each instance of YANG data node statements.

## The "leaf" Statement

   When a leaf node instance is immutable, it cannot be configured with a
   different value in read-write configuration datastores (e.g., \<running\>) or
   removed from \<intended\> (if implemented). Though it can be created/deleted
   in read-write configuration datastores (see Sections {{<immutable-def}} and {{<system-interact}}).

## The "leaf-list" Statement

  When a leaf-list entry is immutable, it cannot be
  removed from \<intended\> (if implemented). Though it can be created/deleted
  in read-write configuration datastores (see Sections {{<immutable-def}} and {{<system-interact}}).

   The immutable annotation attached to an individual leaf-list entry
   provides immutability with respect to the entry itself. As per the restrictions in {{!RFC7952}},
   annotations cannot be attached to an entire leaf-list instance and only
   to individual leaf-list entries, which implies a leaf-list as a whole
   can only inherit immutability from a parent node (e.g., container).

   If a leaf-list as a whole is immutable via inheritance from a parent node, any leaf-list entries cannot be added,
   modified, or reordered (if it is ordered-by user).

   Refer to {{imm-leaf-list}} for an example of immutability of leaf-lists.

## The "container" Statement

   When a container node instance is immutable, it cannot be removed from \<intended\> (if implemented).
   Though it can be created/deleted in read-write configuration datastores
   (see Sections {{<immutable-def}} and {{<system-interact}}).

   Descendant nodes of a container recursively inherit the immutability of the container, unless
   the immutability is overridden by an "immutable" annotation on a descendant node ({{interior}}).

## The "list" Statement

  When a list entry is immutable, it cannot be removed from \<intended\> (if implemented).
  Though it can be created/deleted in read-write configuration datastores
  (see Sections {{<immutable-def}} and {{<system-interact}}).

  Descendant nodes of a list entry recursively inherit the immutability of the list entry, unless
  the immutability is overridden by an "immutable" annotation on a descendant node ({{interior}}).

   The immutable annotation attached to an individual list entry provides
   immutability with respect to the entry itself. As per the restrictions in {{!RFC7952}},
   annotations cannot be attached to an entire list instance and only
   to individual list entries, which implies a list as a whole
   can only inherit immutability from a parent node (e.g., container).

   If a list as a whole is immutable via inheritance from a parent node, any list entries cannot be added, removed, or reordered (if it is ordered-by user).
   Each list entry inherits the immutability of the list by default, unless the immutability is
   overridden by an "immutable" annotation on a list entry.

  Refer to {{imm-list}} for an example of immutability of lists.

## The "anydata" Statement

   When an "anydata" node instance is immutable, it cannot be removed from \<intended\> (if implemented).
   Though it can be created/deleted in read-write configuration datastores
   (see Sections {{<immutable-def}} and {{<system-interact}}).

   Additionally, as with all interior nodes, immutability is recursively applied to
   descendants ({{interior}}).

## The "anyxml" Statement

   When an "anyxml" node instance is immutable, it cannot be removed from \<intended\> (if implemented).
   Though it can be created/deleted in read-write configuration datastores
   (see Sections {{<immutable-def}} and {{<system-interact}}).

   Additionally, as with all interior nodes, immutability is recursively applied to
   descendants ({{interior}}).

# Immutability of Interior Nodes {#interior}

   Immutability is a property of a configuration data node instance, conveyed as metadata {{!RFC7952}}. It is
   recursively applied to descendants, which may reset the immutability
   state as needed, thereby affecting their descendants.  There is no limit
   to the number of times the immutability state may change in a data tree.

   If the "immutable" metadata annotation for a returned child node is omitted,
   it has the same immutability as its parent node. For each top-level returned node, the default "immutable" annotation value is false unless explicitly annotated. Servers may suppress the
   annotation if it is inherited from its parent node or uses the default value
   as the top-level node, but are not precluded from returning the annotation
   on every single element.

   Refer to {{inherit}} for an example of how immutability is recursively
   inherited or explicitly reset by descendants.

# System Configuration Datastore Interactions {#system-interact}

   Immutable configuration can only be created, updated and deleted by the server,
   and it is present in \<system\>, if implemented. That said, the existence of
   immutable configuration is independent of whether \<system\> is implemented or
   not. For example, a server that does not support \<system\> may have predefined hardware-specific configuration in use that cannot be overridden or removed by clients.
   Not all system configuration data is immutable. Immutable configuration
   does not appear in \<running\> unless it is explicitly configured.

   As specified in {{immutable-def}}, a client MAY create/delete immutable nodes
   with same values as defined by server in read-write configuration datastore (e.g.,
   \<candidate\>, \<running\>), which merely makes immutable nodes
   visible/invisible in the datastore.


# NACM Interactions

   The server rejects an operation request due to immutability when it
   tries to perform the operation on the request data. Any immutability checking
   MUST be performed after
   access control decisions, if the Network Configuration Access
   Control Model (NACM) {{!RFC8341}} is implemented on a server. For
   example, if an operation requests to override an immutable
   configuration node, but the server checks the user is not authorized
   to perform the requested access operation on the request data, the
   request is rejected with an "access-denied" error.

# YANG Module {#module}

   This module imports definitions from {{!RFC7952}}, {{!RFC8342}}, {{!RFC8526}}, and {{!I-D.ietf-netmod-system-config}}.

~~~~
<CODE BEGINS> file "ietf-immutable-annotation@2026-05-26.yang"
{::include ietf-immutable-annotation.yang}
<CODE ENDS>
~~~~

# IANA Considerations

## The "IETF XML" Registry

IANA is requested to register the following URI in the "ns"
registry within the "IETF XML Registry" group {{!RFC3688}}:

~~~~
URI: urn:ietf:params:xml:ns:yang:ietf-immutable-annotation
Registrant Contact: The IESG
XML: N/A; the requested URI is an XML namespace.
~~~~

## The "YANG Module Names" Registry

IANA is requested to register the following YANG module in the "YANG
Module Names" registry {{!RFC6020}} within the "YANG Parameters"
registry group.

~~~~
Name: ietf-immutable-annotation
Maintained by IANA?  N
Namespace: urn:ietf:params:xml:ns:yang:ietf-immutable-annotation
Prefix: imma
Reference: RFC XXXX
~~~~

## RESTCONF Capability URN Registry

IANA is requested to register the following capability identifier URNs in the
"RESTCONF Capability URNs" registry defined in {{!RFC8040}}:

Index:
: :with-immutability

Capability Identifier:
: urn:ietf:params:restconf:capability:with-immutability:1.0

# Operational Considerations

This document specifies a mechanism for clients to discover immutable system configuration before attempting modifications. Clients can leverage this information to avoid sending edit requests that would otherwise fail due to immutable nodes.

The mechanism defined in this document is backward compatible with existing implementations. A legacy client unware of this mechanism will not include the "with-immutability" query parameter in its retrieval requests. Consequently, servers will process the request normally without returning any "immutable" metadata annotations. Conversely, a client explicitly requesting the immutable flag from a legacy server may either receive an error response for unsupported query parameter or have the parameter ignored, depending on the server's implementation.


# Security Considerations

   This section is modeled after the template described in {{Section 3.7.1 of ?RFC9907}}.

   The "ietf-immutable-annotation" YANG module defines a data model that is
   designed to be accessed via YANG-based management protocols, such as the Network Configuration Protocol (NETCONF) {{!RFC6241}} and RESTCONF {{!RFC8040}}. These YANG-based management protocols (1) have to
   use a secure transport layer (e.g., Secure Shell (SSH) {{?RFC4252}}, TLS {{?I-D.ietf-tls-rfc8446bis}}, and QUIC {{?RFC9000}}) and (2) have to use mutual authentication.

   The Network Configuration Access Control Model (NACM) {{!RFC8341}}
   provides the means to restrict access for particular NETCONF or
   RESTCONF users to a preconfigured subset of all available NETCONF or
   RESTCONF protocol operations and content.

   The YANG module specified in this document defines a metadata annotation,
   it also extends the RPC operations of the NETCONF protocol in {{!RFC6241}}
   and {{!RFC8526}}.

   The "immutable" metadata annotation exposes the immutability of configuration data,
   which may provide hints for attackers to find vulnerabilities in the network,
   e.g., to leverage the immutability of some configuration to better craft an attack.
   Since immutable annotations are attached to the instances of configuration data nodes,
   it is only accessible to clients that have the permissions to read the annotated configuration nodes.

   The security considerations for the NETCONF protocol operations (see
   {{Section 9 of !RFC6241}} and {{Section 6 of !RFC8526}}) also apply to
   the operations extended in this document.

--- back

# Sample Use Cases {#use-cases}

## UC1: Modeling of Server Capabilities {#sec-uc1}

   System capabilities might be represented as immutable configuration.
   Configurable data nodes might need constraints specified using
   "when", "must", or "path" statements to ensure that configuration is set
   according to the system's capabilities. For example, configurable timer (e.g., for an "interface-timer" or a "bfd-interval-timer") values are dependent on the underlying system timer resource limits.

   * A timer might only support the values 1, 5, and 8 seconds, determined by the system capabilities. This is defined in the leaf-list 'supported-timer'.

   * When the configurable 'interface-timer' leaf is set by a client, it should be ensured that one of the supported values is used.  The natural solution would be to make the "interface-timer" a leafref pointing at the "supported-timer".

   However, this is not possible as "supported-timer" must be
   read-only thus "config false" while 'interface-timer' must be writable
   thus "config true".  According to the rules of YANG it is not allowed
   to put a constraint between "config true" and "config false" data nodes ({{Section 9.9 of ?RFC7950}}).

   A solution is that the 'supported-timer' data node in the YANG
   Model shall be defined as "config true" and shall also be marked with
   the "immutable" annotation making it unchangeable. After this the
   'interface-timer' shall be defined as a leafref pointing at the
   'supported-timer-values'.

## UC2: Hardware-based Auto-configuration - Interface Example {#sec-uc2}

   {{?RFC8343}} defines a YANG data model for the management of network
   interfaces.  When a system-controlled interface is physically present,
   the system creates an interface entry with valid name and type
   values in \<system\> (if exists, see {{?I-D.ietf-netmod-system-config}}).

   The system-generated type value is dependent on and represents the hardware
   present, and as a consequence cannot be changed by clients.  If a
   client tries to set the type of an interface to a value that can
   never be used by the system, the request will be rejected by the
   server.  The data is modeled as "config true" and thus should be annotated
   as immutable.

   An alternative would be to model the list and these leafs
   as "config false", but that does not work because:

  * The list cannot be marked as "config false", because it needs to contain
  configurable child nodes, e.g., IP address or enabled;

  * The key leaf (name) cannot be marked as "config false" as the list
  itself is "config true";

  * The type cannot be marked "config false", because we may need to
  reference the type to make different configuration nodes
  conditionally available.

## UC3: Predefined Administrator Roles {#sec-uc3}

   User and group management is fundamental for setting up access
   control rules (see {{Section 2.5 of !RFC8341}}).

   A device may provide a predefined user account (e.g., a system
   administrator that is always available and has full privileges) for
   initial system set up and management of other users/groups.  It is
   possible that a new user/group can be defined granted particular privileges,
   but the predefined administrator account and its granted access are immutable.

## UC4: Declaring Immutable System Configuration from the Perspective of a Logical Network Element (LNE) {#sec-uc4}

   An LNE, as described in {{?RFC8530}}, is an independently managed virtual
   network device made up of resources allocated to it from its host or
   parent network device.  The host device may allocate some
   resources to an LNE, which from an LNE's perspective is provided by
   the system and may not be modifiable.

   For example, a host may allocate an interface to an LNE with a valid
   MTU value as its management interface, so that the allocated
   interface should then be accessible as the LNE-specific instance of
   the interface model.  The assigned MTU value is system-created and
   immutable from the context of the LNE.

# Examples of Server's Immutable Behavior

  This section provides some examples to illustrate the server's behavior with
  immutable flag. These examples are not intended as recommendations for
  real-world deployments. Long lines within examples are handled in accordance with the line-wrapping specified in {{RFC8792}}.

  The following fictional module is used throughout this section:

~~~~
{::include example-user-group.yang}
~~~~

## NETCONF Example to Retrieve Immutable Configuration {#NETCONF-example}

   {{NETCONF-with-immutability}} illustrates a NETCONF request example to retrieve "user-groups"
   configuration in \<system\> with "with-immutability" parameter and the response that a server might return if it supports this query parameter. For illustrative clarity, some annotations that could otherwise be omitted are shown explicitly in the response.

~~~~
{::include-fold NETCONF-example.xml}
~~~~
{: #NETCONF-with-immutability title="A NETCONF Example to Retrieve Immutable Configuration"}


## RESTCONF Example to Retrieve Immutable Configuration {#RESTCONF-example}

  {{RESTCONF-with-immutability}} illustrates a RESTCONF request example to retrieve "user-groups"
  configuration in \<system\> with "with-immutability" query parameter and the response a server might return if it supports this query parameter. The JSON representation of the metadata annotations in the response follows the encoding specified in {{Section 5.2 of ?RFC7952}}. For illustrative clarity, some annotations that could otherwise be omitted are shown explicitly in the response.

~~~~
{::include-fold RESTCONF-example.json}
~~~~
{: #RESTCONF-with-immutability title="A RESTCONF Example to Retrieve Immutable Configuration"}


## The Inheritance of Immutability {#inherit}

In the example in {{NETCONF-with-immutability}} and {{RESTCONF-with-immutability}}, there are two "group" list entries inside "user-groups"
container node. The "immutable" metadata attribute for "user-groups" container
instance is "false", which is also its default value as the top-level element,
and thus can be omitted. The "administrator" list entry is immutable
with the immutability of its descendant nodes "description" and "user" list entry of "ex-username-2" being explicitly toggled.
Other descendant nodes (e.g., "access-level", "ex-username-1" user list entry, and "tag" leaf-list) inside "administrator" list entry inherit the immutability of the list entry, thus are also immutable.

The "immutable" metadata attribute
for "power-users" list entry is "false", which is also the same
value as its parent node (i.e., the "user-groups" container), and thus can be omitted.
Descendant nodes (e.g., "description", "access-level", "user" list, and "tag" leaf-list) inside "power-users" group inherit the immutability of the list entry, thus are also mutable.

## Immutability of the list {#imm-list}

 In the example in {{NETCONF-with-immutability}} and {{RESTCONF-with-immutability}}, the "group" list as a whole inherits immutability from the
 container "user-groups", which is mutable. One of the list entry named "administrator" is immutable,
 and the other entry named "power-user" is mutable. The client is able to copy the entire "user-groups"
 container in \<running\>, add new "group" entries, modify the values of descendant nodes of "power-users" list entry,
 but the values of descendant nodes of "administrator" list entry cannot be overridden with different values except
 for the "description" and "ex-username-2" user list entry nodes, which is explicitly reset to be mutable.
 The client may also subsequently delete any copied "group" entries or the entire
 "user-groups" container, which will not prevent the deleted data being present in \<intended\> (if implemented) assuming it
 is still contained in \<system\>.

 The "user" list inside the "administrator" group list entry as a whole inherits immutability from the
 list entry, which is immutable. Thus the client cannot add new user entries inside "administrator" group.
 As one of the user entry named "ex-username-1" is immutable through inheritance,
 and the other "ex-username-2" user entry is explicitly set to be mutable. The client cannot
 modify the "password" parameter, or add a "full-name" value for user "ex-username-1".
 but is allowed to update (e.g., modify the "password" value, or add a "full-name" value)
 the list entry for user "ex-username-2". The client may copy or subsequently delete any of the two list entries in \<running\>,
 but there is no way to delete the nodes from \<intended\> (if implemented).

## Immutability of the leaf-list {#imm-leaf-list}

In the example in {{NETCONF-with-immutability}} and {{RESTCONF-with-immutability}}, the user-ordered "tag" leaf-list node inside the "administrator" group entry as a whole inherits immutability from the list entry, which is immutable. Thus the client cannot add, modify, or reorder
entries, the client may copy or subsequently delete any of the two leaf-list entries in \<running\>,
but there is no way to delete the nodes from \<intended\> if those entries appear in \<system\>.

The leaf-list node instance inside the "power-users" group entry as a whole inherits
immutability from the list entry, which is mutable. Thus the client can add or reorder
entries, the client may copy or subsequently delete any of the two leaf-list entries in \<running\>,
but there is no way to delete the nodes from \<intended\> if those entries appear in \<system\>.

## Error Responses to Clients Overriding Immutable Configuration

This section provides examples of a client's attempts to override immutable configuration and error responses that the server might return. Separate examples are provided for NETCONF and RESTCONF protocols, in {{NETCONF-error}} and {{RESTCONF-error}} respectively.

~~~~
{::include-fold error.xml}
~~~~
{: #NETCONF-error title="A NETCONF Example to Override Immutable Configuration with Error Response"}


~~~~
{::include-fold restconf-error.txt}
~~~~
{: #RESTCONF-error title="A RESTCONF Example to Override Immutable Configuration with Error Response"}

# Existing Implementations {#implementations}

   Note to the RFC Editor: Please remove this section prior to publication.

   There are already a number of full or partial implementations of
   immutability:

   * 3GPP TS 32.156 {{TS32.156}} and 28.623 {{TS28.623}}: Requirements
   and a partial solution

   * ITU-T using ONF TR-531 {{TR-531}} concept on information model level but
   no YANG representation.

   * Ericsson: requirements and solution

   * YumaPro: requirements and solution

   * Nokia: partial requirements and solution

   * Huawei: partial requirements and solution

   * Cisco using the concept at least in some YANG modules

   * Junos OS provides a hidden and immutable configuration group
   called junos-defaults


# Acknowledgments
{:numbered="false"}

   Thanks to Kent Watsen, Jan Lindblad, Jason Sterne, Robert Wilton, Andy Bierman,
   Juergen Schoenwaelder, Reshad Rahman, Anthony Somerset, Lou Berger, Joe Clarke,
   and Scott Mansfield for reviewing, and providing important inputs to this document.

   Thanks to Per Andersson for the YANGDOCTORS review.

   Thanks to Mahesh Jethanandani for the AD review.
