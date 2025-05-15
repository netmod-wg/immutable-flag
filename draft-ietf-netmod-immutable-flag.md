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
     target: https://wiki.opennetworking.org/download/attachments/376340494/Draft_TR-531_UML-YANG_Mapping_Gdls_v1.1.03.docx?version=5&modificationDate=1675432243513&api=v2
     date: February 2023

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

--- abstract

   This document defines a way to formally document an existing behavior,
   implemented by servers in production, on the immutability of some
   system-provided nodes, using a YANG metadata annotation called
   "immutable" to flag which nodes are immutable.

   Clients may use "immutable" annotations provided by the server, to
   know beforehand why certain otherwise valid configuration requests
   will cause the server to return an error.

   The immutable flag is descriptive, documenting an existing behavior, not
   proscriptive, dictating server behaviors.

   This document updates RFC 8040 and RFC 8526.

--- middle

# Introduction

   This document defines a YANG metadata annotation {{!RFC7952}} to formally document
   an existing model handling behavior that has been used by
   multiple standard organizations and vendors.  It is the aim to create
   one single standard solution for documenting non-modifiable system
   data declared as configuration, instead of the multiple existing
   vendor and organization specific solutions.

   YANG {{!RFC7950}} is a data modeling language used to model both state
   and configuration data, based on the "config" statement.  However,
   there exists some system configuration data that cannot be modified
   by the client (it is immutable), but still needs to be declared as
   "config true" to:

   * allow configuration of data nodes under immutable lists or containers;

   * place "when", "must" and "leafref" constraints between configuration
   and immutable nodes;

   * ensure the existence of specific list entries that are provided and
   needed by the system, while additional list entries can be created,
   modified or deleted.

   If the server always rejects a client's attempt to override some
   system-provided data because it internally thinks immutable, it should document
   it towards the clients in a machine-readable way rather than writing as
   plain text in the "description" statement.

   This document defines a way to formally document the existing behavior,
   implemented by servers in production, on the immutability of some
   system-provided nodes, using a YANG metadata annotation {{!RFC7952}}
   called "immutable" to flag which nodes are immutable. This document does not
   regulate server behavior. That said, it is expected that a server will return
   an error with an error-tag containing "invalid-value" when immutability
   is attempted to be violated.

   This document does not apply to the server not having any immutable
   system configuration.  While in some cases immutability may be
   needed, it also has disadvantages, therefore it SHOULD be avoided
   wherever possible.

   The following is a list of already implemented and potential use
   cases:

   * UC1  Modeling of server capabilities
   * UC2  Hardware based auto-configuration
   * UC3  Predefined administrator roles
   * UC4  Declaring immutable system configuration from the perspective of a logical network element (LNE)

   {{use-cases}} describes the use cases in detail.

## Updates to RFC 8040

  This document updates {{Sections 4.8 and 9.1.1 of !RFC8040}} to add an
  additional input parameter named "with-immutability", as specified in {{RESTCONF-ext}}.

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
  * YYYY --> the assigned RFC number for {{!I-D.ietf-netmod-system-config}}
  * 2025-05-15 --> the actual date of the publication of this document

# Conventions and Definitions

{::boilerplate bcp14-tagged}

   The document uses the following definition in {{!RFC6241}}:

   * configuration data

   The document uses the following definition in {{!RFC7950}}:

   * data node
   * leaf
   * leaf-list
   * container
   * list
   * anydata
   * anyxml
   * interior node
   * data tree

   The document uses the following definition in {{!RFC8341}}:

   * access operation

   The document uses the following definition in {{?I-D.ietf-netmod-system-config}}:

   * system configuration

   This document defines the following term:

   immutable flag:
   : A read-only state value the server provides to describe
   immutability of the configuration, which is conveyed via a YANG metadata annotation
   called "immutable" with a boolean value.

# Applicability

   While immutable flag applies to all configuration nodes, its value "true"
   can only be used for system configuration.

   The immutable flag is only visible in read-only datastores (i.e., \<system\>
   {{?I-D.ietf-netmod-system-config}}, \<intended\>, and \<operational\>)
   when a "with-immutability" parameter is carried ({{with-immutability}}),
   however this only serves as descriptive information about the
   instance node itself, but has no effect on the handling of the read-only
   datastore. If the immutable flag is requested to be returned for an invalid
   datastore, then the server MUST return an \<rpc-error\> element with an \<error-tag\>
   value of "invalid-value".

   An instance has the same immutability if it appears in different datastores,
   the immutability of configuration data is also protocol and
   user independent. The immutability of any configuration data, and the value
   of any immutable configured data node, MUST only change via software
   upgrade, hardware resources change, or license change.

# "Immutable" Metadata Annotation

## Definition {#immutable-def}

   The immutable flag which is defined as the metadata annotation takes a boolean
   value, and it is returned as requested by the client using a "with-immutability"
   parameter ({{with-immutability}}). If the "immutable" metadata annotation for
   a configuration node is not specified, the default "immutable" value is the
   same as the value of its parent node in the data tree ({{interior}}).
   The immutable metadata annotation value for a top-level instance
   node is "false" if not specified.

   A node that is annotated as immutable cannot be changed via configuring
   a different value in read-write configuration datastores (e.g., \<running\>),
   nor is there any way to delete the node from the combined configuration (as described in {{?I-D.ietf-netmod-system-config}}). The node MAY be explicitly configured by a client in \<running\> with the
   same value and that configuration in \<running\> may subsequently be removed again,
   but neither of these edits will change the configuration in \<intended\> of the device.

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

   This doument updates {{!RFC8526}} to augment the \<get-data\>
   operation with an additional parameter named "with-immutability".
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


### RESTCONF Extensions to Support "with-immutability" {#RESTCONF-ext}

   This document extends {{Sections 4.8 and 9.1.1 of !RFC8040}} to add a query
   parameter named "with-immutability" to the GET operation. If present, this parameter
   requests that the server includes the "immutable" metadata annotations in its
   response. This parameter is only allowed with no values carried. If it has
   any unexpected value, then a "404 Bad Request" status-line is returned.

   To enable a RESTCONF client to discover if the "with-immutability" query parameter
   is supported by the server, the following capability URI is defined:

~~~~
    urn:ietf:params:restconf:capability:with-immutability:1.0
~~~~

# Use of Immutable Flag for Different Statements

   This section defines what the immutable flag means to the client for
   each instance of YANG data node statement.

## The "leaf" Statement

   When a leaf node instance is immutable, it cannot be configured with a
   different value in read-write configuration datastores (e.g., \<running\>) or
   removed from \<intended\> (if implemented). Though it can be created/deleted
   in read-write configuration datastores (see Sections {{<immutable-def}} and {{<system-interact}}).

## The "leaf-list" Statement

  When a leaf-list entry is immutable, it cannot be configured with a
  different value in read-write configuration datastore (e.g., \<running\>) or
  removed from \<intended\> (if implemented). Though it can be created/deleted
  in read-write configuration datastores (see Sections {{<immutable-def}} and {{<system-interact}}).

   The immutable annotation attached to the individual leaf-list entry
   provides immutability with respect to the entry itself. As per the restrictions in {{!RFC7952}},
   annotations cannot be attached to an entire leaf-list instance and only
   to individual leaf-list entries, which implies a leaf-list as a whole
   can only inherit immutability from a parent node (e.g., container).

   If a leaf-list as a whole is immutable, any leaf-list entries cannot be added,
   modified, or reordered (if it is ordered-by user).

   Refer to {{imm-leaf-list}} for an example of immutability of leaf-lists.

## The "container" Statement

   When a container node instance is immutable, it cannot be removed from \<intended\> (if implemented).
   Though it can be created/deleted in read-write configuration datastores
   (see Sections {{<immutable-def}} and {{<system-interact}}).

   Descendant nodes of the container recursively inherit the immutability of the container, unless
   the immutability is overridden by an "immutable" annotation on a descendant node.

   By default, as with all interior nodes, immutability is recursively
   applied to descendants ({{interior}}).

## The "list" Statement

  When a list entry is immutable, it cannot be removed from \<intended\> (if implemented).
  Though it can be created/deleted in read-write configuration datastores
  (see Sections {{<immutable-def}} and {{<system-interact}}).

  Descendant nodes of the list entry recursively inherit the immutability of the list entry, unless
  the immutability is overridden by an "immutable" annotation on a descendant node.

   By default, as with all interior nodes, immutability is recursively
   applied to descendants ({{interior}}).

   The immutable annotation attached to the individual list entry provides
   immutability with respect to the entry itself. As per the restrictions in {{!RFC7952}},
   annotations cannot be attached to an entire list instance and only
   to individual list entries, which implies a list as a whole
   can only inherit immutability from a parent node (e.g., container).

   If a list as a whole is immutable, any list entries cannot be added, removed, or reordered (if it is ordered-by user).
   Each list entry inherits the immutability of the list by default, unless the immutability is
   overridden by an "immutable" annotation on a list entry.

  Refer to {{imm-leaf-list}} for an example of immutability of lists.

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

   Immutability is a conceptual operational state value that is
   recursively applied to descendants, which may reset the immutability
   state as needed, thereby affecting their descendants.  There is no limit
   to the number of times the immutability state may change in a data tree.

   If the "immutable" metadata annotation for returned child node is omitted,
   it has the same immutability as its parent node. The immutability of top
   hierarchy of returned nodes is false by default. Servers may suppress the
   annotation if it is inherited from its parent node or uses the default value
   as the top-level node, but are not precluded from returning the annotation
   on every single element.

   Refer to {{inherit}} for an example of how immutability is recursively
   inherited or explicitly reset by descendants.

# System Configuration Datastore Interactions {#system-interact}

   Immutable configuration can only be created, updated and deleted by the server,
   and it is present in \<system\>, if implemented. That said, the existence of
   immutable configuration is independent of whether \<system\> is implemented or
   not. Not all system configuration data is immutable. Immutable configuration
   does not appear in \<running\> unless it is explicitly configured.

   A node that is annotated as immutable in \<system\> (if implemented) cannot be changed via
   configuring a different value in \<running\>, nor is there any way to delete
   the node from the combined configuration in \<intended\> (as described in {{?I-D.ietf-netmod-system-config}}).
   A client MAY create/delete immutable nodes with same values as found
   in \<system\> (if implemented) in read-write configuration datastore (e.g.,
   \<candidate\>, \<running\>), which merely mean making immutable nodes
   visible/invisible in the datastore. Neither of these edits will change the
   \<intended\> configuration of the device.


# NACM Interactions

   The server rejects an operation request due to immutability when it
   tries to perform the operation on the request data.  It happens after
   any access control processing, if the Network Configuration Access
   Control Model (NACM) {{!RFC8341}} is implemented on a server.  For
   example, if an operation requests to override an immutable
   configuration data, but the server checks the user is not authorized
   to perform the requested access operation on the request data, the
   request is rejected with an "access-denied" error.

# YANG Module {#module}

   This module imports definitions from {{!RFC6241}} and {{!RFC8526}}.

~~~~
<CODE BEGINS> file "ietf-immutable-annotation@2025-05-15.yang"
{::include ietf-immutable-annotation.yang}
<CODE ENDS>
~~~~

# Security Considerations

   This section is modeled after the template described in {{Section 3.7 of ?I-D.ietf-netmod-rfc8407bis}}.

   The "ietf-immutable-annotation" YANG module defines a data model that is
   designed to be accessed via YANG-based management protocols, such
   as NETCONF {{!RFC6241}} or RESTCONF {{!RFC8040}}. These protocols have to
   use a secure transport layer (e.g., SSH {{?RFC4252}}, TLS {{?RFC8446}}, and
   QUIC {{?RFC9000}}) and have to use mutual authentication.

   The Network Configuration Access Control Model (NACM) {{!RFC8341}}
   provides the means to restrict access for particular NETCONF or
   RESTCONF users to a preconfigured subset of all available NETCONF or
   RESTCONF protocol operations and content.

   The YANG module specified in this document defines a metadata annotation,
   it also extends the RPC operations of the NETCONF protocol in {{!RFC6241}}
   and {{!RFC8526}}.

   The immutable metadata annotation exposes the immutability of configuration data,
   which may provide hints for attackers to find vulnerabilities in the network,
   e.g., to leverage the immutability of some configuration to better craft an attack.
   Since immutable annotations are attached to the instances of configuration data nodes,
   it is only accessible to clients that have the permissions to read the annotated configuration nodes.

   The security considerations for the NETCONF protocol operations (see
   {{Section 9 of !RFC6241}} and {{Section 6 of !RFC8526}}) also apply to
   the operations extended in this document.

# IANA Considerations

## The "IETF XML" Registry

   This document registers one XML namespace URN in the 'IETF XML registry',
   following the format defined in {{!RFC3688}}.

~~~~
        URI: urn:ietf:params:xml:ns:yang:ietf-immutable-annotation
        Registrant Contact: The IESG.
        XML: N/A, the requested URIs are XML namespaces.
~~~~

## The "YANG Module Names" Registry

This document registers one module name in the 'YANG Module Names'
registry, defined in {{!RFC6020}}.

~~~~
        name: ietf-immutable-annotation
        prefix: imma
        namespace: urn:ietf:params:xml:ns:yang:ietf-immutable-annotation
        RFC: XXXX
~~~~

## RESTCONF Capability URN Registry

This document defines the following capability identifier URNs in the
"RESTCONF Capability URNs" registry defined in {{!RFC8040}}:

~~~~
Index
Capability Identifier
---------------------

:with-immutability
urn:ietf:params:restconf:capability:with-immutability:1.0
~~~~

--- back

# Detailed Use Cases {#use-cases}

## UC1 - Modeling of server capabilities

   System capabilities might be represented as immutable configuration.
   Configurable data nodes might need constraints specified as
   "when", "must" or "path" statements to ensure that configuration is set
   according to the system's capabilities. For example,

   * A timer can support the values 1,5,8 seconds. This is defined in the
   leaf-list 'supported-timer-values'.

   * When the configurable 'interface-timer' leaf is set, it should be ensured
   that one of the supported values is used.  The natural solution would be to
   make the 'interface-timer' a leaf-ref pointing at the 'supported-timer-values'.

   However, this is not possible as 'supported-timer-values' must be
   read-only thus config=false while 'interface-timer' must be writable
   thus config=true.  According to the rules of YANG it is not allowed
   to put a constraint between config true and false data nodes.

   The solution is that the supported-timer-values data node in the YANG
   Model shall be defined as "config true" and shall also be marked with
   the "immutable" annotation making it unchangeable. After this the
   'interface-timer' shall be defined as a leaf-ref pointing at the
   'supported-timer-values'.

## UC2 - Hardware based auto-configuration - Interface Example

   {{?RFC8343}} defines a YANG data model for the management of network
   interfaces.  When a system-controlled interface is physically present,
   the system creates an interface entry with valid name and type
   values in \<system\> (if exists, see {{?I-D.ietf-netmod-system-config}}).

   The system-generated type value is dependent on and represents the hardware
   present, and as a consequence cannot be changed by the client.  If a
   client tries to set the type of an interface to a value that can
   never be used by the system, the request will be rejected by the
   server.  The data is modeled as "config true" and thus should be annotated
   as immutable.

   Seemingly an alternative would be to model the list and these leaves
   as "config false", but that does not work because:

  * The list cannot be marked as "config false", because it needs to contain
  configurable child nodes, e.g., ip-address or enabled;

  * The key leaf (name) cannot be marked as "config false" as the list
  itself is config true;

  * The type cannot be marked "config false", because we MAY need to
  reference the type to make different configuration nodes
  conditionally available.

## UC3 - Predefined Administrator Roles

   User and group management is fundamental for setting up access
   control rules (see {{Section 2.5 of !RFC8341}}).

   A device may provide a predefined user account (e.g., a system
   administrator that is always available and has full privileges) for
   initial system set up and management of other users/groups.  It is
   possible that a new user/group can be defined granted particular privileges,
   but the predefined administrator account and its granted access are immutable.

## UC4 - Declaring immutable system configuration from the perspective of a logical network element (LNE)

   A logical network element (LNE), as described in {{?RFC8530}}, is an independently managed virtual
   network device made up of resources allocated to it from its host or
   parent network device.  The host device may allocate some
   resources to an LNE, which from an LNE's perspective is provided by
   the system and may not be modifiable.

   For example, a host may allocate an interface to an LNE with a valid
   MTU value as its management interface, so that the allocated
   interface should then be accessible as the LNE-specific instance of
   the interface model.  The assigned MTU value is system-created and
   immutable from the context of the LNE.

# Example of Server Immutable behavior

  This section provides some examples to illustrate the server's behavior with
  immutable flag. The following fictional module is used throughout this section:

~~~~
{::include example-user-group.yang}
~~~~

{{example}} shows an example of "user-groups" configuration in \<system\> a
server might return, XML snippets are used only for illustration purposes.

~~~~
{::include-fold example-urp.xml}
~~~~
{: #example title="An Example of System-defined 'user-groups' Configuration"}

## The Inheritance of Immutability {#inherit}

In the above example in {{example}}, there are two "user-group" list entries inside "user-groups"
container node. The "immutable" metadata attribute for "user-groups" container
instance is "false", which is also its default value as the top-level element,
and thus can be omitted. The "administrator" list entry is immutable
with the immutability of its descendant nodes "description" and "user" list entry of "Bob" being explicitly toggled.
Other descendant nodes inside "administrator" list entry inherit the immutability of the list entry thus are also immutable.

The "immutable" metadata attribute
for "power-users" list entry is "false", which is also the same
value as its parent node (i.e., the "user-groups" container), and thus can be omitted.
Other descendant nodes inside "power-users" user-group inherit the immutability of the list entry thus are also mutable.

## Immutability of the list {#imm-list}

 In the example of {{example}}, the "user-group" list as a whole inherits immutability from the
 container "user-groups", which is mutable. One of the list entry named "administrator" is immutable,
 and the other entry named "power-user" is mutable. The client is able to copy the entire "user-groups"
 container in \<running\>, add new user-group entries, modify the values of descendant nodes of "power-users" list entry,
 but the values of descendant nodes of "administrator" list entry cannot be overridden with different values expect
 for the "description" and "Bob" user list entry nodes, which is explicitly reset to be mutable. The client
 may also subsequently delete any copied "user-group" entries or the entire
 "user-groups" container, but this will not prevent the
 configuration as shown in {{example}} being present in \<intended\> (if implemented).

 The "user" list inside the "administrator" user-group list entry as a whole inherits immutability from the
 list entry, which is immutable. Thus the client cannot add new user entries inside "administrator" user-group.
 As one of the user entry named "Andy" is immutable through inheritance,
 and the other "Bob" user entry is explicitly set to be mutable. The client cannot
 modify the "password" parameter, or add a "full-name" value for user "Andy".
 but is allowed to update (e.g., modify the "password" value, or add a "full-name" value)
 the list entry for user "Bob". The client may copy or subsequently delete any of the two list entries in \<running\>,
 but there is no way to delete the nodes from \<intended\> (if implemented).

## Immutability of the leaf-list {#imm-leaf-list}

The user-ordered "tag" leaf-list node inside the "administrator" user-group entry as a whole inherits
immutability from the list entry, which is immutable. Thus the client cannot add, modify, or reorder
entries, the client may copy or subsequently delete any of the two leaf-list entries in \<running\>,
but there is no way to delete the nodes from \<intended\>.

The leaf-list node instance inside the "power-users" user-group entry as a whole inherits
immutability from the list entry, which is mutable. Thus the client can add or reorder
entries, the client may copy or subsequently delete any of the two leaf-list entries in \<running\>,
but there is no way to delete the nodes from \<intended\>.

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
