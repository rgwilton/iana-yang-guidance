---
title: "Guidance for Managing YANG Modules in RFCs and IANA Registries"
abbrev: "IANA & RFC Editor YANG Guidance"
category: info

docname: draft-ietf-netmod-iana-yang-guidance-latest
submissiontype: IETF  # also: "independent", "editorial", "IAB", or "IRTF"
number:
date:
consensus: true
v: 3
area: "OPS"
workgroup: "NETMOD"
keyword:
 - IANA
 - RFC Editor
 - YANG
 - Versioning
venue:
  group: "Network Modelling"
  type: "Working Group"
  mail: "netmod@ietf.org"
  arch: "https://mailarchive.ietf.org/arch/browse/netmod/"
  github: "rgwilton/iana-yang-guidance"
  latest: "https://rgwilton.github.io/iana-yang-guidance/draft-ietf-iana-yang-guidance.html"

author:
 -
    fullname: "Robert Wilton"
    organization: Cisco
    email: "rwilton@cisco.com"

normative:
  RFC6020:
  RFC7950:
  RFC8340:
  I-D.ietf-netmod-yang-module-versioning:
  I-D.ietf-netmod-yang-semver:
  RFC9907:
  I-D.ietf-netmod-yang-module-filename:
  IANA-YANG-PARAMETERS:
    title: "YANG Parameters"
    target: "https://www.iana.org/assignments/yang-parameters/yang-parameters.xhtml"
    author:
      - org: "IANA"

informative:
  RFC8526:
  RFC8791:
  I-D.ietf-netmod-yang-schema-comparison:
  I-D.mahesh-veloce-yang:
  iana-iftype-registry:
    title: "Interface Types (ifType) Registry"
    target: "https://www.iana.org/assignments/smi-numbers"
    author:
      - org: "IANA"
  iana-afnum-registry:
    title: "Address Family Numbers"
    target: "https://www.iana.org/assignments/address-family-numbers"
    author:
      - org: "IANA"
  iana-safi-registry:
    title: "SAFI Parameters"
    target: "https://www.iana.org/assignments/safi-parameters"
    author:
      - org: "IANA"
  iana-bgp-parameters:
    title: "BGP Parameters"
    target: "https://www.iana.org/assignments/bgp-parameters"
    author:
      - org: "IANA"

...

--- abstract

This document provides guidance to the RFC Editor and IANA on managing YANG modules in RFCs and IANA registries, ensuring consistent application of YANG Semantic Versioning rules.

--- middle

# For Reviewers of this document

**RFC Editor - please delete this section before publication**

This draft should be carefully reviewed by:

- IANA and RFC Editor to check that they agree with the workflows
- OPS ADs & IESG (if needed) that they agree that the IETF should delay publishing YANG modules in approved internet drafts until after the RFC Editor has had the opportunity to review and amend the text.
- YANG Doctors and NETMOD to ensure that they are happy with the requirements being placed upon them.

# Introduction

YANG {{RFC6020}} {{RFC7950}} modules are used to model network management data, protocol RPCs {{RFC8526}}, and even abstract data structures {{RFC8791}}. The IETF publishes YANG modules as part of RFCs, and the Internet Assigned Numbers Authority (IANA) maintains YANG modules that are derived from IANA registries (a.k.a. IANA-maintained YANG modules {{RFC9907}}). Both processes require careful attention to module versioning and the timing of publication to ensure that implementations can correctly assess module version compatibility when modules are updated.

This document provides informational guidance to both the RFC Editor and IANA for managing YANG modules in two distinct scenarios:

1. **Managing YANG Modules in RFCs**: When documents containing normative YANG modules are approved by the IESG and processed for publication as RFCs, both the RFC Editor and IANA have responsibilities to ensure that modules are correctly versioned and published.

2. **Managing IANA-Maintained YANG Modules**: When IANA registries are updated, any YANG modules derived from those registries must be updated accordingly with proper versioning.

This document describes recommended practices and procedures that reflect current consensus within the NETMOD working group and the IETF operations and management community. While following this guidance will help ensure consistent and correct handling of YANG modules, specific situations may require consultation with the YANG Doctors (as described in {{sec-additional-guidance}}).

> **Note:** In addition to the guidance detailed in this document, there is a broader, ongoing discussion within the IETF community around the processes and responsibilities for managing YANG modules in RFCs. For further information and the latest proposals, see {{I-D.mahesh-veloce-yang}}. The recommendations and operational practices described here may be revised in the future to reflect outcomes from that work.

The procedures and classifications in this document are drawn from text and general guidance on the following IETF specifications:

- {{RFC9907}} - Provides general guidelines for IETF YANG module authors. It also includes guidance for IANA.
- {{I-D.ietf-netmod-yang-module-versioning}} - Defines updated YANG module revision handling, including rules for backwards-compatible and non-backwards-compatible changes.
- {{I-D.ietf-netmod-yang-semver}} - Defines YANG Semantic Versioning (YANG Semver) for YANG modules.
- {{I-D.ietf-netmod-yang-module-filename}} - Defines filename conventions for YANG modules versioned using YANG Semver.


# Conventions and Definitions {#sec-conventions}

{::boilerplate bcp14-tagged}

This document uses the following terminology from {{I-D.ietf-netmod-yang-module-versioning}}:

**Backwards-Compatible (BC) Change**
: A change to a YANG module that conforms to the backwards-compatible update rules defined in {{Section 3.1.1 of I-D.ietf-netmod-yang-module-versioning}}. BC changes require incrementing the MINOR version number.

**Non-Backwards-Compatible (NBC) Change**
: A change to a YANG module that does not conform to the backwards-compatible update rules defined in {{Section 3.1.2 of I-D.ietf-netmod-yang-module-versioning}}. NBC changes require incrementing the MAJOR version number and adding the `rev:non-backwards-compatible` extension statement within the `revision` statement in the YANG module.


This document uses the following terminology from {{I-D.ietf-netmod-yang-semver}}:

**YANG Semver**
: YANG Semantic Versioning - a version identifier in the format *MAJOR.MINOR.PATCH_COMPAT* that indicates the compatibility level of a YANG module, as defined in {{I-D.ietf-netmod-yang-semver}}.

**Editorial Change**
: A change to a YANG module that does not affect the semantic meaning or functionality of the module. Editorial changes only require incrementing the PATCH version number, as described in section 4.4 of {{I-D.ietf-netmod-yang-semver}}.

In addition, this document uses this terms from {{RFC9907}}:

**IANA-maintained module**
: A YANG module that is maintained by IANA and has an IANA registry associated with it (e.g., "iana-tunnel-type" {{?RFC8675}} or "iana-pseudowire-types" {{?RFC9291}}).
: Once an IANA-maintained YANG module is initialized, new values are not directly added to the module. These values are instead added to the companion registry, a new version of the IANA-maintained is generated based on the changes made to the registry.

**IETF module**
: A YANG module that is published by the IETF and that is not maintained by IANA.

# Background on YANG Versioning {#sec-background}

## YANG Semantic Versioning

YANG Semantic Versioning (YANG Semver) {{I-D.ietf-netmod-yang-semver}} uses a version identifier in the format MAJOR.MINOR.PATCH (with an optional _COMPAT suffix for branched development):

- **MAJOR** version increments indicate non-backwards-compatible (NBC) changes, with *MINOR* and *PATCH* fields reset to 0.
- **MINOR** version increments indicate backwards-compatible (BC) additions, with the *PATCH* field reset to 0.
- **PATCH** version increments indicate editorial or documentation-only changes.
- **_COMPAT** is used for branched development trees and is not applicable to normative modules published by the IETF or IANA-maintained modules.

If an update to a YANG module contains a mix of changes, then the version number is updated as per the most impactful change.  For example, if a change included both backwards-compatible feature additions and editorial changes then the *MINOR* version field is incremented and the *PATCH* version field is set to 0, e.g., as per the second example below.  If in doubt as to which category a particular change fits into, it is always better to err on the side of caution and choose the more significant version change.

For example, if a published IETF YANG module is at version *1.2.3*:

- An editorial only change would update it to *1.2.4*
- A backwards-compatible addition would update it to *1.3.0*
- A non-backwards-compatible change would update it to *2.0.0*.

Pre-release versions (versions with MAJOR = 0, e.g., "0.2.0", or with a pre-release suffix, e.g., "1.3.0-04") indicate modules that have not completed the IETF standardization process and whose revision content is subject to change in non-backwards-compatible ways without corresponding changes to the major version number.  Published IETF and IANA-maintained YANG modules SHOULD always be at version "1.0.0" or later, and SHOULD never include a pre-release suffix.  The initial published version SHOULD be "1.0.0".

## Backwards Compatibility Rules

The rules that determine whether a change to a YANG module is backwards-compatible or non-backwards-compatible are defined in {{Section 3.1 of I-D.ietf-netmod-yang-module-versioning}}. These rules refine and extend the update rules specified in {{Section 11 of RFC7950}}.

{{Section 3.1.1 of I-D.ietf-netmod-yang-module-versioning}} defines backwards-compatible changes; examples include:

- Adding new schema nodes (e.g., new enum values, identities, leafs, containers)
- Adding or updating "description" and "reference" statements (provided the semantic meaning is unchanged)
- Changing the status of a schema node from "current" to "deprecated" (e.g., by adding a `status deprecated;` statement)

{{Section 3.1.2 of I-D.ietf-netmod-yang-module-versioning}} defines non-backwards-compatible changes; examples include:

- Removing schema nodes (unless they already have status "obsolete")
- Changing the status of a schema node from "current" or "deprecated" to "obsolete"
- Renaming schema nodes or changing their identifiers
- Changing data types in ways that alter syntax or semantics
- Changing numeric values assigned to enumerations
- Modifying "description" statements in ways that change semantic meaning or behavior

In addition,  {{Section 4.4 of I-D.ietf-netmod-yang-semver}} defines editorial changes as the subset of backwards-compatible changes that have no impact on the semantics or syntax of a YANG module, such as:

- Corrections to comments, descriptions, or references that do not change the semantic meaning
- Formatting improvements such as whitespace or indentation changes
- Updates to contact information or copyright statements

## The rev:non-backwards-compatible Extension

The YANG module versioning framework {{I-D.ietf-netmod-yang-module-versioning}} defines the "rev:non-backwards-compatible" extension statement. This extension MUST be added as a substatement of a revision statement whenever that revision contains non-backwards-compatible changes relative to the previous revision.

The following example illustrates this extension in use. In the example, an identity 'foo' was added in version 1.3.0, but was subsequently renamed to 'bar' in version 2.0.0. Since renaming is a non-backwards-compatible change, the major version number is incremented and the `rev:non-backwards-compatible` extension is included in the revision statement in version 2.0.0 of the YANG module:

~~~~ yang
  revision 2025-11-15 {
    ysv:version "2.0.0";
    rev:non-backwards-compatible;
    description
      "Renamed identity 'foo' to 'bar'.";
  }
  revision 2025-06-01 {
    ysv:version "1.3.0";
    description
      "Added identity 'foo'.";
  }
  ...
~~~~
{: align="right" title="Revision history example from a YANG module"}

## Module Immutability

A fundamental principle of YANG module versioning is that once a module revision is published with a specific revision date and version number, its content is immutable (much like an RFC is). The published content of that revision MUST NOT change. Any change to the module content requires publishing a new revision with a new revision date and an updated YANG Semver.

This immutability principle has important implications:

- Normative YANG modules in Internet-Drafts MUST use pre-release versions (e.g., 0.1.0 or 2.0.0-draft-name) to indicate that the content may still change.
- Once a document containing normative YANG modules is approved by the IESG and has been processed by the RFC Editor, then each normative YANG module version MUST be updated to the correct release version (e.g., 1.0.0 or 2.0.0) before publication in an RFC or before being made available in the IANA YANG Module Names registry {{IANA-YANG-PARAMETERS}}.
- IANA-maintained YANG modules MUST publish a new YANG module revision any time IANA registry changes require YANG module updates.

# YANG Modules in Documents Being Published as RFCs {#sec-rfc-workflow}

This section describes the workflow and responsibilities for managing YANG modules in documents that have been approved by the IESG and are being processed for publication as RFCs. Both the RFC Editor and IANA have roles in this process.

## Core Requirements

All new normative YANG modules published by the RFC Editor or maintained by IANA MUST meet the following requirements:

1. **YANG Semver Version**: Every normative YANG module MUST include a semantic version number using the `ysv:version` statement in its most recent revision. The version MUST be correct relative to any previous version of the same module published either by the RFC editor or on the IANA website.

2. **NBC Extension for NBC Changes**: If the normative YANG module contains non-backwards-compatible changes relative to the previously published version, the revision statement MUST include the `rev:non-backwards-compatible` extension.

   In current tooling, general enforcement of this rule is performed by update comparison checks such as the `--check-update-from` option to `pyang`. Please also note, the `pyang` checks provided by the `--ietf` option only provide a narrower validation based on revision-to-revision YANG Semver major-version changes.

3. **Revision Immutability**: A published YANG module with a specific revision date and version number is immutable. Its content MUST NOT change without also changing the revision date and version number. For this reason, normative YANG modules in Internet-Drafts use pre-release versions (e.g., versions with MAJOR = 0 such as 0.1.0, or versions with a pre-release suffix such as 2.0.0-05, where the -05 is the Internet Draft number where the YANG module was updated) to indicate that content may still change before final publication.

4. **RFC Code Markers**: Normative YANG modules in RFCs MUST be properly marked with `<CODE BEGINS>` and `<CODE ENDS>` markers (or equivalent in the source format) to enable automated extraction per {{Section 3.2 of RFC9907}}. The markers MUST include the filename following the conventions in {{I-D.ietf-netmod-yang-module-filename}}.

## Workflow Steps

The following steps describe the coordinated process between the RFC Editor and IANA for handling YANG modules during RFC publication:

### Step 1: IESG Approval with Pre-Release Version

When a document is approved by the IESG, any normative YANG modules it contains typically have pre-release version numbers (e.g., 0.4.0, 1.1.0-03, or 2.0.0-07). These pre-release versions indicate that the module content may still be subject to editorial changes during RFC Editor processing.

### Step 2: RFC Editor Processing

During RFC Editor processing, the RFC Editor may make editorial changes to the YANG module, such as:

- Improving description text for clarity without changing semantic meaning
- Updating references to use final RFC numbers instead of draft names
- Correcting typographical errors
- Standardizing formatting and style

These editorial changes are appropriate and expected. Consistent with the editing practices when preparing edited RFCs, the RFC Editor SHOULD:

- Coordinate with document authors regarding any substantive changes.
- Ensure that only editorial changes (as defined in {{sec-background}}) are made without author consultation.
- For normative YANG modules that have previously been published, e.g., updated YANG modules in -bis documents:
  - If more significant changes are needed that might be backwards-compatible or non-backwards-compatible, consult with the authors to determine the correct version number and whether the `rev:non-backwards-compatible` extension is required.
- Ensure that the final module is correctly formatted (e.g., by running {{pyang-formatting}}).


### Step 3: Finalizing the Module Version

Before publication, each normative YANG module version MUST be updated from the pre-release version to a release version. The RFC Editor, in coordination with the document authors:

- Updates the revision date to reflect the date of the final revision for each normative YANG module.
- Updates the version to remove pre-release indicators for each normative YANG module (e.g., 0.1.0 → 1.0.0, or 1.1.0-\<draft-num\> → 1.1.0).
- For normative YANG modules that have previously been published, e.g., updated YANG modules in -bis documents:
  - Uses `pyang` ({{pyang-next-version}}) to compare the candidate module against the previously published version and obtain a recommended next YANG Semver, subject to the tool limitations described in {{pyang-next-version}} and {{tool-limitations}}.
 Tooling is not infallible, so if the suggested version from the tooling is unexpected then please reach out for additional guidance, as per {{sec-additional-guidance}}.
  - Checks, and if necessary adds, the `rev:non-backwards-compatible` extension if NBC changes have occurred since the previous publication.

### Step 4: Validate the Module

Normative YANG modules are expected to be provided to the RFC Editor for publication already passing validation (`pyang` and `yanglint`).  However, it is possible that mistakes could be introduced when editing a YANG module so validation should be re-run to ensure that IETF does not publish invalid YANG modules.

After all updates are completed, or as updates are made, and after any formatting, then validation tools MUST be run over the resultant module to ensure that there are no warnings or errors. `pyang` validation ({{pyang-validation}}) MUST be performed, and it is RECOMMENDED that `yanglint` ({{yang-lint-validation}}) validation is also performed.

If the tools return any warnings or errors then the authors should help fix them, potentially seeking additional guidance if required, as per {{sec-additional-guidance}}.

If further changes are made, then for previously published modules, the step 3 versioning check MUST be re-run to ensure that the module version is still correct.

### Step 5: IANA Delay of Publication

IANA SHOULD delay publishing a normative YANG module to the IANA YANG Parameters registry until the RFC Editor has completed editing the module. This coordination ensures that:

- The IANA-published version matches the RFC-published version exactly
- No discrepancies exist between the two authoritative sources
- The module reference to the RFC (if present) is correct

### Step 6: Coordinated Publication

Once the RFC Editor has finalized the module:

- The RFC is published with the final module content
- IANA publishes the module to the IANA YANG Parameters registry at approximately the same time
- The module filename follows the conventions in {{I-D.ietf-netmod-yang-module-filename}}
- IANA registers the module in the "YANG Module Names" registry if it is not already registered

# IANA-Maintained YANG Modules {#sec-iana-modules}

This section describes the process for IANA to update and publish YANG modules that are maintained by IANA and derived from IANA registries.

## Overview

Some IANA registries have corresponding YANG modules that represent registry contents in a machine-readable format, and that are published at {{IANA-YANG-PARAMETERS}}.  Examples include:

- **iana-if-type.yang** - derived from the Interface Types (ifType) registry {{iana-iftype-registry}}
- **iana-routing-types.yang** - derived from Address Family Numbers {{iana-afnum-registry}} and SAFI Parameters {{iana-safi-registry}} registries
- **iana-bgp-types.yang** - derived from BGP Parameters registries {{iana-bgp-parameters}}

When these registries are updated, the corresponding YANG modules MUST be updated accordingly by IANA, following the same versioning rules described in {{sec-background}}.

## Characteristics of IANA-Maintained Modules

IANA-maintained YANG modules typically:

- Have names starting with "iana-"
- Contain primarily enumeration typedefs or identity definitions that are derived from registry entries
- Are updated more frequently than IETF-defined modules
- Follow a linear version history without branching
- Have a much simpler structure than general-purpose YANG modules, which simplifies classification of changes

Because IANA-maintained YANG modules are always expected to follow a linear version history without branching, the *_COMPAT* modifier defined in {{I-D.ietf-netmod-yang-semver}} is not needed or used for these modules. The *_COMPAT* modifier is only required for non-linear branched histories of YANG module versions. Therefore, only the *MAJOR.MINOR.PATCH* elements of YANG Semver need be considered for IANA-maintained modules.

## Rules Applicable to IANA-Maintained Modules

IANA-maintained YANG modules typically contain only enumerations (enum) and identity definitions, as they represent simple registry mappings. Hence, the most relevant compatibility rules for these modules are:

**Editorial Changes:**

- Clarifying "description" statements without changing meaning
- Adding or updating "reference" statements
- Fixing typographical errors in description text
- Updating contact information
- Formatting improvements

**Backwards-Compatible Changes:**

- Adding a new enum value or identity
- Changing status from "current" to "deprecated"
- Removing schema nodes that already have status "obsolete"

**Non-Backwards-Compatible Changes:**

- Removing an enum value or identity (unless status is "obsolete")
- Changing status from "current" or "deprecated" to "obsolete"
- Renaming an enum or identity
- Changing the numeric value assigned to an enum
- Modifying "description" statements in a way that changes the semantic meaning

**Important**: If multiple updates to the registry are made at the same time resulting in a single update to the IANA maintained YANG module then the new module version number is decided by the impact of the most significant change.

### Special YANG Considerations for Deprecated Registry Entries

IANA registries and YANG modules use the term *deprecated* differently:

- In IANA registries, deprecated generally means the value SHOULD NOT be used for new deployments.

- In YANG modules, `status deprecated` means the definition is still supported (including for new deployments) but it is expected to be obsoleted (or removed) in a future module version.

To avoid confusion, when an IANA registry entry is marked deprecated, the corresponding enum or identity description SHOULD include indicate that the base IANA registry entry is deprecated and therefore the entry SHOULD NOT be used. I.e., the following sentence SHOULD be added to the end of the enum/identity description: ```This value is deprecated in the base IANA registry which means that its use is NOT RECOMMENDED.```

## Process for Updating IANA-Maintained YANG Modules

When a change is made to an IANA registry that has a corresponding YANG module, it is recommended that IANA update the module following these steps:

### Step 1: Follow RFC-Defined Rules

First, consult the RFC that defines the IANA registry and its associated YANG module. That RFC may specify:

- Specific rules for how registry entries map to YANG constructs
- Guidance on when and how to update the module
- Contact information for expert reviewers
- Special considerations for the particular registry

Always follow the specific guidance in the RFC that created the registry and module.

### Step 2: Identify the Registry Change

Determine exactly what changed in the registry:

- Was a new entry added?
- Was an existing entry modified (description, reference, status)?
- Was an entry deprecated or obsoleted?
- Was an entry removed?
- Were multiple changes made simultaneously?

### Step 3: Apply Equivalent Changes to the YANG Module

Update the YANG module to reflect the registry changes. For IANA-maintained modules, this typically involves:

- Adding a new enum value or identity for new registry entries
- Updating description or reference statements for modified entries
- Changing status statements for deprecated or obsoleted entries
- Removing entries only if they are obsolete or if the defining RFC specifies removal
- Add a revision statement (using the current date) describing the change, and a reference, if appropriate.
- *(Optional) include a version statement with the anticipated new version and a `rev:non-backwards-compatible` statement if it is a non-backwards-compatible change.*
- *(Optional) Use tooling to format the YANG module, as described in {{pyang-formatting}}.*

### Step 4: Use Pyang Tooling to Check/Recommend Next Version

Use the tools described in {{pyang-next-version}} to recommend or check (if provided in step 3) the next module version.  Be aware of the tooling limitations, as per {{tool-limitations}}, and sanity check that the version recommended by the tooling is what is expected based on the changes.

- Add or update the version statement with the correct version
- Add `rev:non-backwards-compatible` extension if NBC changes have occurred

### Step 5: Validate the Module

Use validation tools, as per {{pyang-validation}}, to ensure the updated module is syntactically correct.  Since these modules are simple, just checking with the `pyang` tool is sufficient but `yanglint`({{yang-lint-validation}}) may be used as an alternative.

### Step 6: Seek additional help if Needed

In most cases, the classification will be straightforward. However, if any of the following apply, IANA should seek additional guidance as described in {{sec-additional-guidance}}:

- The change classification is unclear
- The tool output is unexpected or contradictory
- Description changes, where it is not obvious if they change semantic meaning
- Any situation not covered by the guidance above, or examples in {{appendix-scenarios}}

### Step 7: Publish the Updated Module

Once the module is validated and the version is confirmed:

- Publish the updated module to the IANA website
- Publish the module using two URLs, one using the version and one using the revision date: `<module-name>#<version>.yang` and `<module-name>@<revision-date>.yang`, as per {{I-D.ietf-netmod-yang-module-filename}}.
- Update any relevant registries or indexes
- Ensure the new version is discoverable and accessible

## Examples

Detailed examples of common scenarios (adding entries, updating references, deprecating entries, etc.) are given in {{appendix-scenarios}}.


# Seeking Additional Guidance {#sec-additional-guidance}

## When to Seek Guidance

The RFC Editor and IANA should contact the YANG Doctors in the following situations:

1. **Classification Uncertainty** - When it's unclear whether a change is NBC, BC, or Editorial
1. **Tool Disagreement** - When validation tools give unexpected or contradictory results
1. **Description Changes** - When it is unclear whether a description update alters semantic meaning
1. **Unusual Situations** - Any scenario not clearly covered in this document
1. **Registry Restructuring** - Major changes to how a registry is organized
1. **RFC Editor Processing** - When RFC Editor changes may go beyond editorial scope

## How to Seek Guidance

Email the YANG Doctors mailing list and the Operations and Management Area Directors (OPS ADs):

- **Email**: yang-doctors@ietf.org & ops-ads@ietf.org
- **Purpose**: Technical review and guidance on YANG module versioning.
- **Response Time**: Typically 1-2 weeks

When emailing, please include:

- Description of the change or situation
- The affected YANG module and relevant excerpts
- Your proposed classification and version change
- Specific questions or concerns
- Any relevant tool output
- An indication if an urgent reply is required.

The expectation is that the YANG Doctors should reply to the request within the time frame given above, but if a reply isn't forthcoming then please escalate via the OPS ADs.

## Example Request

~~~~ text
<BEGIN TEMPLATE TEXT>

Subject: YANG Versioning Question - iana-if-type Update

Dear YANG Doctors,

I need guidance on classifying a change to the iana-if-type module.

Change Description:
The Interface Types registry has updated the description for
interface type 6 (ethernet) to clarify that it includes both
10BASE-T and 100BASE-T variants.

Proposed YANG Change:
Update the description statement for the "ethernet" enum to
include the clarification.

Question:
Should this be classified as Editorial (PATCH increment) since
it's clarifying existing behavior, or as BC (MINOR increment)
because it's adding new information?

The old description said: "Ethernet interface"
The new description says: "Ethernet interface, including
10BASE-T and 100BASE-T variants"

Current module version: 1.5.0
Proposed version: 1.5.1 (if Editorial) or 1.6.0 (if BC)

Thank you for your guidance.

<END TEMPLATE TEXT>
~~~~

# Operational Considerations {#sec-operational}

This entire document provides operational guidance for the RFC Editor and IANA on how to process and publish YANG modules. The procedures described in {{sec-rfc-workflow}} and {{sec-iana-modules}} are designed to ensure consistent and correct versioning of YANG modules across all IETF and IANA publications.

Correct versioning is critical because consumers of YANG modules rely on the semantic version number to understand the compatibility and risk associated with updating to a new module version:

- **PATCH version increments** signal that only editorial changes have been made, indicating very low risk for updates
- **MINOR version increments** signal backwards-compatible additions, indicating that existing implementations will continue to work but new features are available
- **MAJOR version increments** signal non-backwards-compatible changes, indicating that implementations must carefully evaluate the impact before updating

Following the guidance in this document helps ensure that version numbers accurately communicate these compatibility expectations to the YANG module consumer community.

When uncertain about the correct classification or version for a module, the operational recommendation is to choose the more conservative option:

- If uncertain between editorial and backwards-compatible, choose backwards-compatible (MINOR rather than PATCH)
- If uncertain between backwards-compatible and non-backwards-compatible, choose non-backwards-compatible (MAJOR rather than MINOR, and include the NBC extension)

This conservative approach ensures that consumers are appropriately warned about potential compatibility implications, even if the actual risk turns out to be lower than indicated.

# Security Considerations

This document gives instructions to IANA on how to handle YANG modules that are published in RFCs and also YANG modules that are derived from IANA registries.

Incorrect interpretation of this document could cause incorrect handling or versioning of IANA-maintained YANG modules.

This document recommends the usage of various tools.  Bugs or attacks on these tools could cause the tools to give incorrect or misleading guidance.  In all cases, secondary evaluation of output of the tools should be performed to confirm that they are giving the anticipated results.  The *YANG Doctors* or *Operations and Management Area Directors* can also be contacted for further advice, if required.


# IANA Considerations

This document provides operational guidance to IANA and the RFC Editor for managing YANG modules. It does not require IANA to create or modify any registries, nor does it define any new registration procedures.

The guidance in this document is intended to clarify and standardize how IANA processes YANG modules in the "YANG Module Names" registry {{IANA-YANG-PARAMETERS}} and how IANA maintains YANG modules derived from IANA registries.

IANA should follow the procedures described in {{sec-rfc-workflow}} when processing YANG modules from RFCs and the procedures described in {{sec-iana-modules}} when updating IANA-maintained YANG modules.

--- back

# Available Tooling {#appendix-tooling}

This appendix describes tooling available to assist the RFC Editor and IANA in validating and versioning YANG modules. The tools and capabilities described here reflect the state of tooling as of the publication of this document. Tool capabilities are expected to evolve over time, and newer or improved tools may become available. The NETMOD working group and YANG Doctors can provide updated guidance on current best practices for tooling.

## YANG Validation Tools

### pyang

**Purpose**: `pyang` is a comprehensive YANG validator and converter tool that can validate syntax, check for backwards-compatible violations, and generate documentation.

**Primary Use Cases**:

1. Validating YANG module syntax and compliance with IETF conventions
1. Formatting YANG modules in a consistent way prior to publication
1. Suggesting proposed next version when updating a YANG module
1. Generating tree diagrams for documentation

**Installation**: `pyang` is available via PyPI (`pip install pyang`) or from <https://github.com/mbj4668/pyang>.  It is recommended to periodically check and update the version to pick up bugfixes and new functionality (which could include stricter checks).

**Note to RFC Editor and reviewers, some of the tooling enhancements documented here are not yet been merged into the master pyang repository, so depending on publication timing we need to add further references.**

To ensure that `pyang` correctly processes the YANG files, then the correct versions of any YANG module dependencies must also be used, this is often the latest version of the published YANG modules, but if a draft contains a set of YANG modules, or if there are set of drafts with YANG modules being published together then all the YANG modules in those drafts MUST be extracted together and validated.  These dependent YANG modules can either be stored in the same directory as the YANG module being validated/checked, or they can be stored in a separate directory and passed using the ```-p``` argument to provide a path to the directory.

#### Basic YANG Syntax Validation {#pyang-validation}

This command below validates the module syntax and checks compliance with IETF-specific conventions. The output will show any errors or warnings. Normative IETF YANG modules and IANA-maintained YANG modules SHOULD have no errors or warnings before publication.

~~~~ shell
pyang --ietf --strict --max-line-length=69 -Werror -p <dep-module-directory> ietf-module-name.yang
~~~~

#### Consistent formatting of YANG modules {#pyang-formatting}

Pyang can be used to reformat a YANG file, particularly fixing line length issues and correcting any indentation mistakes.  Some complex expressions, e.g., ```must```, ```when``` and path statements may not be automatically split to the correct line length and may need to be done manually.  Running the basic syntax validation command on the output file indicates whether any further manual line folding is required.

~~~~ shell
pyang -f yang --yang-line-length=69 --yang-canonical -Werror -p <dep-module-directory> -o <output-file> <treeOpts> ietf-module-name.yang
~~~~

#### Suggesting proposed next version when updating a YANG module {#pyang-next-version}

Pyang can be used to compare the changes between two YANG module versions and either validate that a suitable next version number has been used, or to suggest what the appropriate next version should be, or if further manual checks should be performed, e.g., for changes to description statements.

If the new module version already includes a version statement for the latest revision then `pyang` can perform a limited set of policy checks against the declared version.  In the current implementation, this is not a full exact-match validation for every possible declared version.  Instead, the tool reports specific cases where the declared version is inconsistent with detected known NBC changes or with certain possible-NBC outcomes.  Otherwise, if the latest revision does not contain a version statement then it will suggest the new version that should be used.

If the previous revision being compared does not contain a `ysv:version` statement, then the tool assumes an old version of 1.0.0 and reports that assumption explicitly in its output.

If the old version is itself a pre-release form, for example with MAJOR = 0 or with pre-release metadata, then the current implementation may be unable to recommend a next release version automatically.

~~~~ shell
pyang --check-update-semver --check-update-from module-name@old-version.yang module-name@new-version.yang
~~~~

**Interpreting Tool Output**

The command output:

- prints the suggested next YANG Semver, when the tool can determine one, based on the changes.  It may print:
  - `ASSUMED-OLD-YANG-SEMVER` line if the previous revision does not contain a `ysv:version` statement.
  - `SUGGESTED-NEXT-YANG-SEMVER: unavailable (...)`, if the previous version is in a pre-release form that the current implementation cannot automatically advance.
- indicates whether the `rev:non-backwards-compatible` extension statement is needed.
- highlights any non-backwards-compatible changes, which are reported as errors.
- indicates if there are changes to any statements, e.g., description, that require further analysis to decide whether a semantic change has occurred and hence if the change is non-backwards-compatible rather than editorial.
- may emit additional semver policy diagnostics if a declared new `ysv:version` is inconsistent with the tool's semver policy checks.

**Example Tool Output 1**:

This example illustrates what the expected output would be for an update to a YANG module that is derived from an IANA registry update that defines a new enum or identity.  This is a minor version change and hence the version change recommended by the tool is from 1.0.0 → 1.1.0.

~~~~ text
SUGGESTED-NEXT-YANG-SEMVER: 1.1.0
~~~~

**Example Tool Output 2**:

This example output below due to deleting an enum entry indicates an NBC change has occurred. Hence the tool recommends a major version number change from 1.0.0 → 2.0.0 and the addition of the ```rev:non-backwards-compatible``` statement.

~~~~ text
SUGGESTED-NEXT-YANG-SEMVER: 2.0.0
NBC-CHANGE(S):
iana-ssh-mac-algs@2026-03-06.yang:45: error: rev:non-backwards-compatible is required for this revision
iana-ssh-mac-algs@2026-03-06.yang:62: error: the enum 'AEAD_AES_256_GCM', defined at iana-ssh-mac-algs@2024-10-16.yang:109 is illegally removed or marked obsolete
iana-ssh-mac-algs@2026-03-06.yang:62: error: the value for enum 'hmac-sha2-256', has changed from 7 to 6 (RFC 7950: sec. 11, p5, bullet 1)
iana-ssh-mac-algs@2026-03-06.yang:62: error: the value for enum 'hmac-sha2-512', has changed from 8 to 7 (RFC 7950: sec. 11, p5, bullet 1)
~~~~

**Example Tool Output 3**:

This example output is for a change to a description statement.  The tool output suggests a version change from 1.0.0 → 1.0.1, which is correct if there is no change in semantics.  It also highlights that it may be necessary to consult with the authors to determine if a semantic change has occurred, if that is not obvious.  If after reviewing, the conclusion is that a semantic change has occurred, then the version change should be from 1.0.0 → 2.0.0 and the `rev:non-backwards-compatible` statement should be added.

~~~~ text
SUGGESTED-NEXT-YANG-SEMVER: 1.0.1
POSSIBLE-NBC-CHANGE(S):
Consult document authors and YANG Doctors.
iana-ssh-mac-algs@2025-03-17.yang:138: warning: the description change may have changed the semantics of the node
~~~~

#### Generating tree diagrams for documentation {#pyang-tree}

Pyang can be used to generate tree diagram output that conforms to {{RFC8340}}.  The command below generates the tree diagram for `ietf-module-name.yang`, limited to a line length of 69 characters and writes it into the specified `output-file`.  The `tree-options` is based on the options in `pyang` that are prefixed with `--tree` and can be seen by running `pyang --help`.  Common options may include printing out grouping (`--tree-print-groupings`) or printing out structures (`--tree-print-structures`).

~~~~ shell
pyang -f tree --tree-line-length=69 -Werror -p <dep-module-directory> -o <output-file> <tree-options> ietf-module-name.yang
~~~~

### yanglint {#yang-lint-validation}

**Purpose**: `yanglint` is a YANG validator and data manipulation tool from the libyang project, useful for validating modules and instance data.

**Primary Use Cases**:

- Validating YANG module syntax
- Checking cross-module dependencies
- Validating instance data against YANG schemas

**Installation**: `yanglint` is part of libyang, available from <https://github.com/CESNET/libyang>

**Syntax Validation**:

~~~~ shell
yanglint -p /path/to/yang/modules module-name.yang
~~~~

The `-p` option specifies a directory containing imported modules.

This command validates the module syntax. No output indicates successful validation; errors will be displayed if found.


### YANG Catalog Tools

**Purpose**: The YANG Catalog (<https://www.yangcatalog.org>) provides online tools for module validation, comparison, and discovery.

**Primary Use Cases**:

- Validating YANG modules without local tool installation
- Comparing different versions of modules
- Viewing module dependencies and impact analysis
- Searching for existing modules and versions

**Usage**:

Access the web interface at <https://www.yangcatalog.org> and use the "Validator" and "YANG Impact Analysis" tools.

**Interpreting Results**:

The online tools provide visual feedback on validation results and module comparisons. The impact analysis tool can show which other modules depend on a given module, helping assess the impact of changes.

## Tool Limitations {#tool-limitations}

While tools are valuable for YANG module validation and versioning, they have a couple of limitations relevant to their usage here:

**Limitation 1: Cannot Always Distinguish Editorial from BC/NBC Changes**

Current tools cannot determine whether a description change is purely editorial (clarifying existing meaning) or non-backwards-compatible (changing meaning in semantically significant way). Human or AI judgment is required to make this distinction.

Example: Changing "Ethernet interface" to "Ethernet interface, includes all Ethernet interface speeds" could be editorial (if those variants were always included).  But changing an "ip" type from a description saying "IPv4 address or IPv6 address" to just "IPv4 address" would be regarded as an NBC change because the scope of the type has clearly changed and may impact users of that type.

**Limitation 2: May Produce False Positives or False Negatives**

Tool implementations may have bugs or may not cover all edge cases in the YANG versioning rules.  Generally, in any ambiguous cases, the tools are being designed to either explicitly flag this or choose the more impactful option.  It is assumed to be better for clients to potentially flag an NBC change that is not really there than to mistakenly flag an NBC change as only being a BC change.

Hence the recommendation is to always review tool output critically and seek additional review ({{sec-additional-guidance}}) when uncertainty remains.


# Summary of IANA Registry Action Scenarios {#appendix-scenarios}

This appendix provides a comprehensive reference of common scenarios encountered when updating IANA-maintained YANG module derived from IANA registries.  Each scenario describes the registry action, the corresponding YANG module change, the classification (NBC/BC/Editorial), the version change required, and whether the `rev:non-backwards-compatible` extension must be added.

## Quick Reference Table

The assumption is that the YANG module uses the registry entry name, numeric identifier, description, status, and any reference fields as part of the YANG entries.  If additional fields from the registry are used in the YANG module (e.g., perhaps the YANG description is constructed from multiple registry fields) then any changes to those fields will require a new version of the YANG module to be published and an appropriate new version number, chosen based on the actual change to the YANG module.

**Important Principle**: The source or trigger of a change (errata, new RFC, registry update, expert review, etc.) does NOT determine whether it is NBC, BC, or Editorial. What matters is the resultant change made to the YANG module content.

| Registry Action | YANG Change | Classification | Version | NBC Ext |
|:----------------|:------------|:---------------|:--------|:--------|
| Add new registration | Add enum/identity | BC | MINOR | No |
| Update reference (Draft → RFC) | Update reference | Editorial | PATCH | No |
| Update reference (obsoleted RFC) | Update reference | Editorial | PATCH | No |
| Add additional reference | Update reference | Editorial | PATCH | No |
| Change or remove reference | Update reference | Editorial | PATCH | No |
| Update description (clarify) | Update description | Editorial | PATCH | No |
| Update description (change meaning) | Update description | NBC | MAJOR | Yes |
| Deprecate entry (keep name) | status deprecated | BC | MINOR | No |
| Obsolete entry | status obsolete | NBC | MAJOR | Yes |
| Rename entry | Change identifier | NBC | MAJOR | Yes |
| Remove obsolete entry | Remove enum/identity | BC | MINOR | No |
| Remove non-obsolete entry | Remove enum/identity | NBC | MAJOR | Yes |
| Change value number | Change value | NBC | MAJOR | Yes |
| Reuse old value (previously removed) | Same as adding new entry| BC | MINOR | No |
| Add footnote | Optionally update description | Editorial | PATCH | No |
| Non-YANG field changes | Depends on how the module is updated | Analyze | Varies | Maybe |
| Errata | Depends on content | Analyze | Varies | Maybe |
| Early alloc expired (left as-is) | No change | N/A | None | No |
| Early alloc expired (removed) | Follow removal rules | NBC | MAJOR | Yes |
| Revive expired (removed) allocation | Add enum/identity | BC | MINOR | No |
{: title="Registry Action → YANG Module Update Reference Table"}

**Key**:

- **BC** = Backwards-Compatible; **NBC** = Non-Backwards-Compatible
- **MAJOR/MINOR/PATCH** refer to the YANG Semver version components
- **NBC Ext** = Whether `rev:non-backwards-compatible` extension is required
- **Varies** or **Maybe** indicates the specific change must be analyzed using the detailed scenarios below

## Detailed Common Scenarios

Note: The following scenarios only contain snippets of YANG to illustrate the change being made and are not intended to represent complete example modules, or be able to compile or validate.

### Scenario 1: Adding a New Registry Entry

**Registry Action**: A new entry is added to an IANA registry.

**YANG Module Change**: Add a new enum value or identity.

**Classification**: Backwards-Compatible (BC)

**Version Change**: Increment MINOR version (e.g., 1.0.0 → 1.1.0)

**NBC Extension Required**: No

**Rationale**: Adding a new type is backwards compatible because it cannot break backwards-compatibility of existing implementations.

**Example**:

Previous version, *1.0.0*:

~~~~ yang
revision 2025-11-01 {
  ysv:version "1.0.0";
  description "Initial revision.";
}

typedef interface-type {
  type enumeration {
    enum ethernet {
      value 6;
      description "Ethernet interface";
    }
  }
}
~~~~

New version, *1.1.0*, after addition of 'wifi' enum type:

~~~~ yang
revision 2025-11-15 {
  ysv:version "1.1.0";
  description "Added 'wifi' (71).";
}
revision 2025-11-01 {
  ysv:version "1.0.0";
  description "Initial revision.";
}

typedef interface-type {
  type enumeration {
    enum ethernet {
      value 6;
      description "Ethernet interface";
    }
    enum wifi {
      value 71;
      description "IEEE 802.11 wireless interface";
    }
  }
}
~~~~

### Scenario 2: Updating References

**Registry Action**: A reference is updated (e.g., RFC obsoleted, additional reference added, draft reference changed to RFC number).

**YANG Module Change**: Update the "reference" statement.

**Classification**: Editorial

**Version Change**: Increment PATCH version (e.g., 2.3.0 → 2.3.1)

**NBC Extension Required**: No

**Rationale**: Reference statements may be added or updated without affecting compatibility of existing implementations.

**Example**:

Previous version, *2.3.0*:

~~~~ yang
revision 2025-11-01 {
  ysv:version "2.3.0";
  description "Added 'foo' (42).";
}

typedef interface-type {
  type enumeration {
    ...
    enum foo {
      value 42;
      description "Foo interface type.";
      reference "RFC 1234";
    }
  }
}
~~~~

New version (*2.3.1*) after the reference for 'foo' is updated to RFC 5678:

~~~~ yang
revision 2025-11-15 {
  ysv:version "2.3.1";
  description "Updated reference for 'foo' (42).";
}
revision 2025-11-01 {
  ysv:version "2.3.0";
  description "Added 'foo' (42).";
}

typedef interface-type {
  type enumeration {
    ...
    enum foo {
      value 42;
      description "Foo interface type.";
      reference "RFC 5678 (obsoletes RFC 1234)";
    }
  }
}
~~~~

### Scenario 3: Deprecating a Registry Entry

**Registry Action**: A registry entry is marked as deprecated (name and description remain visible).

**YANG Module Change**: Change status from "current" to "deprecated".

**Classification**: Backwards-Compatible (BC)

**Version Change**: Increment MINOR version (e.g., 2.3.1 → 2.4.0)

**NBC Extension Required**: No

**Rationale**: Changing status to deprecated is backwards-compatible but the description must also be updated to highlight the difference in meaning between IANA registries and YANG modules.

**Example**:

Previous version, *2.3.1*:

~~~~ yang
revision 2025-11-15 {
  ysv:version "2.3.1";
  description "Updated reference 'foo' (42).";
}

typedef interface-type {
  type enumeration {
    ...
    enum oldtype {
      value 99;
      description "Old interface type";
    }
  }
}

~~~~

New version (*2.4.0*) after deprecation:

~~~~ yang
revision 2025-11-23 {
  ysv:version "2.4.0";
  description "Deprecated 'oldtype' (99).";
}
revision 2025-11-15 {
  ysv:version "2.3.1";
  description "Updated reference 'foo' (42).";
}

typedef interface-type {
  type enumeration {
    ...
    enum oldtype {
      value 99;
      status deprecated;
      description
        "Old interface type. This value is deprecated in the base IANA
        registry which means that its use is NOT RECOMMENDED.";
    }
  }
}
~~~~

### Scenario 4: Obsoleting a Registry Entry

**Registry Action**: A registry entry is marked as obsolete.

**YANG Module Change**: Change status from "deprecated" (or "current") to "obsolete".

**Classification**: Non-Backwards-Compatible (NBC)

**Version Change**: Increment MAJOR version (e.g., 2.4.0 → 3.0.0)

**NBC Extension Required**: Yes

**Rationale**: Changing status to obsolete indicates the value MUST NOT be used, breaking compatibility.  Note, the description comment about deprecation can also be removed.

**Example**:

Previous version (*2.4.0*):

~~~~ yang
revision 2025-11-23 {
  ysv:version "2.4.0";
  description
    "Deprecated 'oldtype' (99).";
}

typedef interface-type {
  type enumeration {
    ...
    enum oldtype {
      value 99;
      status deprecated;
      description
        "Old interface type. This value is deprecated in the base IANA
        registry which means that its use is NOT RECOMMENDED.";
    }
  }
}
~~~~

New version (*3.0.0*) after obsoletion:

~~~~ yang
revision 2025-11-30 {
  ysv:version "3.0.0";
  rev:non-backwards-compatible;
  description
    "Obsoleted 'oldtype' (99).";
}
revision 2025-11-23 {
  ysv:version "2.4.0";
  description
    "Deprecated 'oldtype' (99).";
}

typedef interface-type {
  type enumeration {
    ...
    enum oldtype {
      value 99;
      status obsolete;
      description
        "Old interface type.";
    }
  }
}
~~~~

### Scenario 5: Removing a Registry Entry Completely

**Registry Action**: A registry entry is removed with no trace.

**YANG Module Change**: Remove the enum or identity from the module.

**Classification**: Non-Backwards-Compatible (NBC)

**Version Change**: Increment MAJOR version (e.g., 2.2.0 → 3.0.0)

**NBC Extension Required**: Yes

**Rationale**: Removing a schema node is NBC per Section 3.1.2.1 of {{I-D.ietf-netmod-yang-module-versioning}}.

**Example**:

Previous version (*2.2.0*):

~~~~ yang
revision 2026-02-01 {
  ysv:version "2.2.0";
  description
    "Added 'legacy-wireless' identity.";
}

identity interface-type {
  description
    "Base identity for interface types.";
}

identity legacy-wireless {
  base interface-type;
  description
    "Legacy wireless interface.";
}
~~~~

New version (*3.0.0*) after removal:

~~~~ yang
revision 2026-03-15 {
  ysv:version "3.0.0";
  rev:non-backwards-compatible;
  description
    "Removed 'legacy-wireless' identity.";
}
revision 2026-02-01 {
  ysv:version "2.2.0";
  description
    "Added 'legacy-wireless' identity.";
}

identity interface-type {
  description "Base identity for interface types.";
}
~~~~

### Scenario 6: Renaming a Registry Entry

**Registry Action**: The name of a registry entry is changed.

**YANG Module Change**: Change the enum or identity identifier.

**Classification**: Non-Backwards-Compatible (NBC)

**Version Change**: Increment MAJOR version (e.g., 3.1.0 → 4.0.0)

**NBC Extension Required**: Yes

**Rationale**: Renaming breaks programmatic references to the identifier.

**Example**:

Previous version (*3.1.0*):

~~~~ yang
revision 2026-04-01 {
  ysv:version "3.1.0";
  description "Added 'old-gre' identity.";
}

identity tunnel-type {
  description "Base identity for tunnel types.";
}

identity old-gre {
  base tunnel-type;
  description "GRE tunnel (legacy name).";
}
~~~~

New version (*4.0.0*) after rename:

~~~~ yang
revision 2026-05-01 {
  ysv:version "4.0.0";
  rev:non-backwards-compatible;
  description "Renamed 'old-gre' identity to 'gre'.";
}
revision 2026-04-01 {
  ysv:version "3.1.0";
  description "Added old-gre identity.";
}

identity tunnel-type {
  description "Base identity for tunnel types.";
}

identity gre {
  base tunnel-type;
  description "GRE tunnel.";
}
~~~~

### Scenario 7: Changing a Value Number

**Registry Action**: The numeric value assigned to a registry entry is changed.

**YANG Module Change**: Change the value assigned to an enum.

**Classification**: Non-Backwards-Compatible (NBC)

**Version Change**: Increment MAJOR version (e.g., 2.3.0 → 3.0.0)

**NBC Extension Required**: Yes

**Rationale**: Changing values breaks compatibility for implementations using those values.

**Example**:

Previous version (*2.3.0*):

~~~~ yang
revision 2026-01-10 {
  ysv:version "2.3.0";
  description "Added multiple identities.";
}

typedef interface-type {
  type enumeration {
    enum fastether {
      value 210;
      description "Fast Ethernet interface.";
    }
    enum atm {
      value 211;
      description "ATM interface.";
    }
  }
}
~~~~

New version (*3.0.0*) after value change:

~~~~ yang
revision 2026-02-20 {
  ysv:version "3.0.0";
  rev:non-backwards-compatible;
  description "Changed 'fastether' value to 215.";
}
revision 2026-01-10 {
  ysv:version "2.3.0";
  description "Added multiple identities.";
}

typedef interface-type {
  type enumeration {
    enum fastether {
      value 215;
      description "Fast Ethernet interface.";
    }
    enum atm {
      value 211;
      description "ATM interface.";
    }
  }
}
~~~~

### Scenario 8: Updating Description (Clarification)

**Registry Action**: Description is updated to clarify existing behavior without changing meaning.

**YANG Module Change**: Update the description statement.

**Classification**: Editorial

**Version Change**: Increment PATCH version (e.g., 2.1.3 → 2.1.4)

**NBC Extension Required**: No

**Example**:

Previous version (*2.1.3*):

~~~~ yang
revision 2026-02-05 {
  ysv:version "2.1.3";
  description "Clarified description for 'foo'.";
}

identity ethernet {
  base interface-type;
  description "Ethernet interface.";
}
~~~~

New version (*2.1.4*) after clarification:

~~~~ yang
revision 2026-02-12 {
  ysv:version "2.1.4";
  description "Clarified description for 'ethernet'.";
}
revision 2026-02-05 {
  ysv:version "2.1.3";
  description "Clarified description for 'foo'.";
}

identity ethernet {
  base interface-type;
  description
    "Ethernet interface, includes 10BASE-T, 100BASE-T, and
     1000BASE-T variants.";
}
~~~~

### Scenario 9: Updating Description (Semantic Change)

**Registry Action**: Description is updated in a way that changes the semantic meaning or behavior.

**YANG Module Change**: Update the description statement.

**Classification**: Non-Backwards-Compatible (NBC)

**Version Change**: Increment MAJOR version (e.g., 2.2.0 → 3.0.0)

**NBC Extension Required**: Yes

**Example**:

Previous version (*2.2.0*):

~~~~ yang
revision 2026-03-01 {
  ysv:version "2.2.0";
  description "Defined 'ip' identity.";
}

identity ip {
  base interface-type;
  description "Interface supports IPv4.";
}
~~~~

New version (*3.0.0*) after semantic change:

~~~~ yang
revision 2026-04-01 {
  ysv:version "3.0.0";
  rev:non-backwards-compatible;
  description "Changed description for 'ip' identity";
}
revision 2026-03-01 {
  ysv:version "2.2.0";
  description "Defined 'ip' identity.";
}

identity ip {
  base interface-type;
  description "Interface supports IPv4 and IPv6.";
}
~~~~

### Scenario 10: Handling Errata

**Registry Action**: An errata report is filed for the registry or module.

**YANG Module Change**: Depends on the specific errata content.

**Classification**: Analyze the actual change, not the source (errata vs. new RFC does not determine classification).

**Version Change**: Follow the rules based on the actual change being made to the IANA registry entry.

**NBC Extension Required**: May be required depending on the change.

**Examples**:

- Errata fixes typo in description → Editorial / PATCH
- Errata adds missing enum → BC / MINOR
- Errata corrects wrong value assignment → NBC / MAJOR

# Acknowledgments
{:numbered="false"}

The creation of this document was motivated by questions from IANA team members Amanda Baber and Sabrina Tanamal regarding the correct handling of YANG module versioning. This was followed by a productive in-person discussion at IETF 124 between members of the YANG versioning design team, the IANA team, NETMOD working group chairs, RFC Editor representatives, and the OPS Area Director.

Participants in that meeting included: Amanda Baber, Jason Sterne, Joe Clarke, Kent Watsen, Lou Berger, Mahesh Jethanandani, Per Andersson, Reshad Rahman, Rob Wilton, and Sabrina Tanamal.

Special thanks to Joe Clarke for his presentation on YANG versioning tooling at IETF 124, which informed the tooling guidance in {{appendix-tooling}}.

The authors thank the RFC Editor and IANA teams for their collaboration in refining the operational procedures described in this document.

The authors would like to thank those providing comments on the draft, including: Amanda Baber, Jason Sterne, Joe Clarke, Mahesh Jethanandani, Mohamed Boucadair, Reshad Rahman, Sabrina Tanamal, and Sandy Ginoza.

The authors also thank the NETMOD working group for their extensive work on YANG versioning specifications that form the foundation of this guidance, including the module versioning framework, semantic versioning, and associated tooling.

The initial substantive revision of this document used Claude Sonnet 4.5 to create prose and examples, which have been subsequently reviewed and refined by the YANG Versioning design team and the NETMOD working group.
