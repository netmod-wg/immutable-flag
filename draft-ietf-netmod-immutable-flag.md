---
title: "YANG Metadata Annotation for Immutable Flag"
abbrev: "immutable flag"
category: std
updates: 6241, 8040, 8526

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
    city: Jiangsu
    code: 210012
    country: China
    email: maqiufang1@huawei.com
 -
    fullname: Qin Wu
    organization: Huawei
    street: 101 Software Avenue, Yuhua District
    city: Jiangsu
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

   This document defines a way to formally document existing behavior,
   implemented by servers in production, on the immutability of some
   system-provided nodes, using a YANG metadata annotation called
   "immutable" to flag which nodes are immutable.

   Clients may use "immutable" annotations provided by the server, to
   know beforehand why certain otherwise valid configuration requests
   will cause the server to return an error.

   The immutable flag is descriptive, documenting existing behavior, not
   proscriptive, dictating server behavior.

--- middle

# Introduction

   This document defines a way to formally document as a YANG metadata
   annotation an existing model handling behavior that has been used by
   multiple standard organizations and vendors.  It is the aim to create
   one single standard solution for documenting non-modifiable system
   data declared as configuration, instead of the multiple existing
   vendor and organization specific solutions.  See {{implementations}} for
   existing implementations.

   YANG {{!RFC7950}} is a data modeling language used to model both state
   and configuration data, based on the "config" statement.  However,
   there exists some system configuration data that cannot be modified
   by the client (it is immutable), but still needs to be declared as
   "config true" to:

   * allow configuration of data nodes under immutable lists or containers;

   * place "when", "must" and "leafref" constraints between configuration
   and immutable data nodes;

   * ensure the existence of specific list entries that are provided and
   needed by the system, while additional list entries can be created,
   modified or deleted.

   If the server always rejects the client attempts to override some
   system-provided data because it internally thinks immutable, it should document
   it towards the clients in a machine-readable way rather than writing as
   plain text in the "description" statement.

   This document defines a way to formally document existing behavior,
   implemented by servers in production, on the immutability of some
   system-provided nodes, using a YANG metadata annotation {{!RFC7952}}
   called "immutable" to flag which nodes are immutable.

   This document does not apply to the server not having any immutable
   system configuration.  While in some cases immutability may be
   needed, it also has disadvantages, therefore it SHOULD be avoided
   wherever possible.

   The following is a list of already implemented and potential use
   cases:

   * UC1  Modeling of server capabilities
   * UC2  HW based auto-configuration
   * UC3  Predefined administrator roles
   * UC4  Declaring immutable system configuration from an LNE's perspective

   {{use-cases}} describes the use cases in detail.

## Updates to RFC 6241 and RFC 8526

   This document updates {{!RFC6241}} and {{!RFC8526}}. The NETCONF \<get\> and
   \<get-config\> operations defined in {{!RFC6241}}, and \<get-data\> operation
   defined in {{!RFC8526}} are augmented with an additional input parameter
   named "with-immutable", as specified in {{NETCONF-ext}}.

## Updates to RFC 8040

   This document updates {{Sections 4.8 and 9.1.1 of !RFC8040}} to add an
   additional input parameter named "with-immutable", as specified in {{RESTCONF-ext}}.

## Editorial Note (To be removed by RFC Editor)

  Note to the RFC Editor: This section is to be removed prior to publication.

  This document contains placeholder values that need to be replaced with finalized
  values at the time of publication.  This note summarizes all of the
  substitutions that are needed.  No other RFC Editor instructions are specified
  elsewhere in this document.

  Please apply the following replacements:

  * XXXX --> the assigned RFC number for this draft
  * 2024-06-04 --> the actual date of the publication of this document

# Conventions and Definitions

{::boilerplate bcp14-tagged}

   The following terms are defined in {{!RFC6241}}:

   * configuration data

   The following terms are defined in {{!RFC7950}}:

   * data node
   * leaf
   * leaf-list
   * container
   * list
   * anydata
   * anyxml
   * interior node
   * data tree

   The following terms are defined in {{!RFC8341}}:

   * access operation
   * write access

   This document defines the following term:

   immutable flag:
   : A read-only state value the server provides to describe
   immutability of the data, which is conveyed via a YANG metadata annotation
   called "immutable" with a boolean value.

# Applicability

   While immutable flag applies to all configuration nodes, its value "true"
   can only be used for system configuration.

   The immutable flag is also visible in read-only datastores like \<system\>
   (if implemented, see {{?I-D.ietf-netmod-system-config}}), \<intended\>
   and \<operational\> when a "with-immutable" parameter is carried ({{with-immutable}}),
   however this only serves as descriptive information about the
   instance node itself, but has no effect on the handling of the read-only
   datastore.

   Configuration data must have the same immutability in different
   writable datastores.  The immutability of data nodes is protocol and
   user independent.  The immutability and configured value of an
   existing node MUST only change via software upgrade, hardware
   resources change, or license change.

# "Immutable" Metadata Annotation

## Definition

   The immutable flag which is defined as the metadata annotation takes a boolean
   value, and it is returned as requested by the client using a "with-immutable"
   parameter ({{with-immutable}}). If the "immutable" metadata annotation for
   configuration is not specified, the default "immutable" value is the
   same as the immutability of its parent node in the data tree ({{interior}}).
   The immutable metadata annotation value for a top-level instance
   node is "false" if not specified.

   Note that "immutable" metadata annotation is used to annotate data node
   instances.  A list may have multiple instances in the data tree,
   servers can annotate some of the instances as immutable, while others as
   mutable.

   Servers MUST ignore any immutable metadata annotation sent from the client.

## "with-immutable" Parameter {#with-immutable}

   This section specifies the NETCONF and RESTCONF protocol extensions to support
   "with-immutable" parameter. The "immutable" metadata annotations are not returned
   in a response unless explicitly requested by the client using this parameter.

### NETCONF Extensions to Support "with-immutable" {#NETCONF-ext}

   This doument updates {{!RFC6241}} to augment the \<get-config\> and \<get\>
   operations with an additional parameter named "with-immutable". The
   \<get-data\> operation defined in {{!RFC8526}} is also updated to support
   this parameter. If present, this parameter requests that the server includes
   the "immutable" metadata annotations in its response.

   {{tree}} provides the tree structure {{?RFC8340}} of augmentations to NETCONF
   operations, as defined in the "ietf-immutable" module ({{module}}).

~~~~
module: ietf-immutable
  augment /ncds:get-data/ncds:input:
    +---w with-immutable?   empty {immutable}?
  augment /nc:get-config/nc:input:
    +---w with-immutable?   empty {immutable}?
  augment /nc:get/nc:input:
    +---w with-immutable?   empty {immutable}?
~~~~
{: #tree title="Augmentations to NETCONF Operations" artwork-align="center}

   Servers' support for accepting "with-immutable" parameter and returning "immutable"
   annotations is identified with the feature "immutable".

### RESTCONF Extensions to Support "with-immutable" {#RESTCONF-ext}

   This document extends sections 4.8 and 9.1.1 of {{!RFC8040}} to add query
   parameter named "with-immutable" to the GET operation. If present, this parameter
   requests that the server includes the "immutable" metadata annotations in its
   response. This parameter is only allowed with no values carried. If it has
   any unexpected value, then a "404 Bad Request" status-line is returned.

   To enable a RESTCONF client to discover if the "with-immutable" query parameter
   is supported by the server, the following capability URI is defined:

~~~~
    urn:ietf:params:restconf:capability:with-immutable:1.0
~~~~

# Use of Immutable Flag for Different Statements

   This section defines what the immutable flag means to the client for
   each instance of YANG data node statement.

   Throughout this section, the word "change" refers to create, update, and delete.

## The "leaf" Statement

   When a leaf node instance is immutable, its value cannot change.

## The "leaf-list" Statement

   When a leaf-list node instance is immutable, its value cannot change.

   The immutable annotation attached to the individual leaf-list instance
   provides immutability with respect to the instance itself. If leaf-list
   inherits immutability from an ancestor (e.g., container), it is identical
   to each individual leaf-list entry being annotated without any bearing on the
   entry ordering and addition of new entries.

## The "container" Statement

   When a container node instance is immutable, it cannot change, unless
   the immutability of its descendant node is toggled.

   By default, as with all interior nodes, immutability is recursively
   applied to descendants ({{interior}}).

## The "list" Statement

   When a list node instance is immutable, it cannot change, unless the
   immutability of its descendant node is toggled.

   By default, as with all interior nodes, immutability is recursively
   applied to descendants ({{interior}}).

   The immutable annotation attached to the individual list instance provides
   immutability with respect to the instance itself. If list inherits immutability
   from an ancestor (e.g., container), it is identical to each individual list
   entry being annotated without any bearing on the entry ordering and addition
   of new entries.

## The "anydata" Statement

   When an anydata node instance is immutable, it cannot change. Additionally,
   as with all interior nodes, immutability is recursively applied to
   descendants ({{interior}}).

## The "anyxml" Statement

   When an "anyxml" node instance is immutable, it cannot change. Additionally,
   as with all interior nodes, immutability is recursively applied to
   descendants ({{interior}}).

# Immutability of Interior Nodes {#interior}

   Immutability is a conceptual operational state value that is
   recursively applied to descendants, which may reset the immutability
   state as needed, thereby affecting their descendants.  There is no limit
   to the number of times the immutability state may change in a data tree.

   If the "immutable" metadata annotation for returned child nodes are omitted,
   it has the same immutability as its parent node. The immutability of top
   hierarchy of returned nodes is false by default. Servers may suppress the
   annotation if it is inherited from its parent node or uses the default value
   as the top-level node, but are not precluded from returning the annotation
   on every single element.

   For example, given the following application configuration XML snippets:

~~~~
<applications im:immutable="false">
<application im:immutable="true">
  <name>ssh</name>
  <protocol>tcp</protocol>
  <port-number im:immutable="false">22</port-number>
</application>
<application im:immutable="false">
  <name>my-ssh</name>
  <protocol>tcp</protocol>
  <port-number>10022</port-number>
</application>
</applications>
~~~~

   In this example, there are two "application" list entries inside "applications"
   container node. The "immutable" metadata attribute for applications container
   instance is "false", which is also its default value as the top-level element,
   and thus can be omitted. The "application" list entry named "ssh" is immutable
   with the immutability of its child node "port-number" being explicitly toggled.
   The other child nodes inheriting immutability from their parent node thus are
   also immutable. The "immutable" metadata attribute for application list entry
   named "my-ssh" is "false", which is also its inherited value from its parent
   node, and thus can be omitted.

# System Configuration Datastore Interactions

   Immutable configuration can only be created, updated and deleted by the server,
   thus it is present in \<system\>, if implemented. That said, the existence of
   immutable configuration is independent of whether \<system\> is implemented or
   not. Not all system configuration data is immutable. Immutable configuration
   does not appear in \<running\> unless it is explicitly provided by the client
   or copied by the server via "resolve-system" parameter defined in
   {{?I-D.ietf-netmod-system-config}}.

   A client may create/delete immutable nodes with same values as found
   in \<system\> (if implemented) in read-write configuration datastore (e.g.,
   \<candidate\>, \<running\>), which merely mean making immutable nodes
   visible/invisible in the datastore.


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
<CODE BEGINS> file "ietf-immutable@2024-06-04.yang"
{::include ietf-immutable.yang}
<CODE ENDS>
~~~~

# Security Considerations

   The YANG module specified in this document defines a metadata annotation,
   it also extends the base operations of the NETCONF protocol in {{!RFC6241}}
   and {{!RFC8526}}.

   The Network Configuration Access Control Model (NACM) {{!RFC8341}}
   provides the means to restrict access for particular NETCONF or
   RESTCONF users to a preconfigured subset of all available NETCONF or
   RESTCONF protocol operations and content. Since immutable flag is tied
   to applied configuration values, it is only accessible to clients that
   have the permissions to read the applied configuration values.

   The security considerations for the Defining and Using Metadata with
   YANG (see {{Section 9 of !RFC7952}}) apply to the metadata annotation
   defined in this document.

   The security considerations for the NETCONF protocol operations (see
   {{Section 9 of !RFC6241}} and {{Section 6 of !RFC8526}}) still apply to
   the operations extended in this document.

# IANA Considerations

## The "IETF XML" Registry

   This document registers one XML namespace URN in the 'IETF XML registry',
   following the format defined in {{!RFC3688}}.

~~~~
        URI: urn:ietf:params:xml:ns:yang:ietf-immutable
        Registrant Contact: The IESG.
        XML: N/A, the requested URIs are XML namespaces.
~~~~

## The "YANG Module Names" Registry

This document registers one module name in the 'YANG Module Names'
registry, defined in {{!RFC6020}}.

~~~~
        name: ietf-immutable
        prefix: im
        namespace: urn:ietf:params:xml:ns:yang:ietf-immutable
        RFC: XXXX
~~~~

## RESTCONF Capability URN Registry

This document defines the following capability identifier URNs in the
"RESTCONF Capability URNs" registry defined in {{!RFC8040}}:

~~~~
Index           Capability Identifier
----------------------------------------------------------------------
:with-immutable urn:ietf:params:restconf:capability:with-immutable:1.0
~~~~

--- back

# Detailed Use Cases {#use-cases}

## UC1 - Modeling of server capabilities

   System capabilities might be represented as system-defined data nodes in
   the model.  Configurable data nodes might need constraints specified as
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
   the "immutable" annotation  making it unchangeable.  After this the
   'interface-timer' shall be defined as a leaf-ref pointing at the
   'supported-timer-values'.

## UC2 - HW based auto-configuration - Interface Example

   {{?RFC8343}} defines a YANG data model for the management of network
   interfaces.  When a system-controlled interface is physically present,
   the system creates an interface entry with valid name and type
   values in \<system\> (if exists, see {{?I-D.ietf-netmod-system-config}}).

   The system-generated type value is dependent on and represents the HW
   present, and as a consequence cannot be changed by the client.  If a
   client tries to set the type of an interface to a value that can
   never be used by the system, the request will be rejected by the
   server.  The data is modelled as "config true" and should be annotated
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
   possible that clients can define a new user/group and grant it
   particular privileges, but the predefined administrator account and
   its granted access cannot be modified.

## UC4 - Declaring immutable system configuration from an LNE's perspective

   An LNE (logical network element) is an independently managed virtual
   network device made up of resources allocated to it from its host or
   parent network device {{?RFC8530}}.  The host device may allocate some
   resources to an LNE, which from an LNE's perspective is provided by
   the system and may not be modifiable.

   For example, a host may allocate an interface to an LNE with a valid
   MTU value as its management interface, so that the allocated
   interface should then be accessible as the LNE-specific instance of
   the interface model.  The assigned MTU value is system-created and
   immutable from the context of the LNE.

# Existing Implementations {#implementations}

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
