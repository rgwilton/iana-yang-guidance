---
title: "Guidance to IANA for managing YANG modules"
abbrev: "IANA YANG Guidance"
category: info

docname: draft-verdt-iana-yang-guidance-latest
submissiontype: IETF  # also: "independent", "editorial", "IAB", or "IRTF"
number:
date:
consensus: true
v: 3
area: "OPS"
workgroup: "NETMOD"
keyword:
 - IANA
 - YANG
 - Guidance
venue:
  group: "Network Modelling"
  type: "Working Group"
  mail: "netmod@ietf.org"
  arch: "https://mailarchive.ietf.org/arch/browse/netmod/"
  github: "rgwilton/iana-yang-guidance"
  latest: "https://rgwilton.github.io/iana-yang-guidance/draft-verdt-iana-yang-guidance.html"

author:
 -
    fullname: "Robert Wilton"
    organization: Cisco
    email: "rwilton@cisco.com"

normative:
  RFC7950:
  I-D.ietf-netmod-yang-module-versioning:
  I-D.ietf-netmod-yang-semver:
  I-D.ietf-netmod-rfc8407bis:

informative:
  I-D.ietf-netmod-yang-module-filename:
  I-D.ietf-netmod-yang-schema-comparison:

...

--- abstract

This document provides guidance to the Internet Assigned Numbers Authority (IANA) for managing YANG modules. It covers two primary cases: (1) handling YANG modules published in IETF documents, and (2) managing YANG modules derived from IANA registries. The guidance builds upon the YANG Module Versioning Framework, YANG Semantic Versioning (YANG Semver), and associated IETF standards to ensure consistent and correct versioning of IANA-maintained YANG modules.

--- middle

# Introduction

The Internet Assigned Numbers Authority (IANA) maintains numerous registries that contain protocol parameters, identifiers, and other values used in Internet protocols. Many of these registries have corresponding YANG {{RFC7950}} modules that provide a machine-readable representation of the registry contents. When registries are updated, the corresponding YANG modules must also be updated following specific versioning rules defined by the IETF.

This document provides comprehensive guidance to IANA on:

1. How to handle YANG modules being published in IETF documents
2. How to manage YANG modules derived from IANA registries
3. How to determine the correct version for updated YANG modules
4. How to classify changes as non-backwards-compatible (NBC), backwards-compatible (BC), or editorial
5. When to seek additional expert guidance

The guidance in this document is based on several key IETF documents:

- {{I-D.ietf-netmod-yang-module-versioning}} - Defines updated YANG module revision handling, including rules for non-backwards-compatible changes
- {{I-D.ietf-netmod-yang-semver}} - Defines YANG Semantic Versioning (YANG Semver) for YANG modules
- {{I-D.ietf-netmod-yang-module-filename}} - Defines filename conventions for YANG modules
- {{I-D.ietf-netmod-rfc8407bis}} - Provides general guidelines for YANG module authors

# Conventions and Definitions

{::boilerplate bcp14-tagged}

This document uses the following terminology:

**Non-Backwards-Compatible (NBC) Change**
: A change to a YANG module that does not conform to the backwards-compatible update rules defined in {{I-D.ietf-netmod-yang-module-versioning}}. NBC changes require incrementing the MAJOR version number and adding the `rev:non-backwards-compatible` extension statement.

**Backwards-Compatible (BC) Change**
: A change to a YANG module that conforms to the backwards-compatible update rules defined in {{I-D.ietf-netmod-yang-module-versioning}}. BC changes require incrementing the MINOR version number.

**Editorial Change**
: A change to a YANG module that does not affect the semantic meaning of the module. Editorial changes require incrementing the PATCH version number.

**YANG Semver**
: YANG Semantic Versioning - a version identifier in the format MAJOR.MINOR.PATCH that indicates the compatibility level of a YANG module, as defined in {{I-D.ietf-netmod-yang-semver}}.

**IANA-Maintained Module**
: A YANG module maintained by IANA, typically derived from one or more IANA registries. These modules have names starting with "iana-" (e.g., iana-if-type, iana-routing-types).

# Background on YANG Versioning {#sec-background}

## YANG Semantic Versioning

YANG Semantic Versioning (YANG Semver), defined in {{I-D.ietf-netmod-yang-semver}}, uses a three-part version identifier in the format MAJOR.MINOR.PATCH:

- **MAJOR** version increments indicate non-backwards-compatible changes
- **MINOR** version increments indicate backwards-compatible feature additions
- **PATCH** version increments indicate editorial or documentation-only changes

For example, if a YANG module is at version 1.2.3:
- A non-backwards-compatible change would update it to 2.0.0
- A backwards-compatible feature addition would update it to 1.3.0
- An editorial change would update it to 1.2.4

## The rev:non-backwards-compatible Extension

The YANG module versioning framework {{I-D.ietf-netmod-yang-module-versioning}} defines the `rev:non-backwards-compatible` extension statement. This extension MUST be added as a substatement of a revision statement whenever that revision contains non-backwards-compatible changes relative to the previous revision.

Example:

~~~~ yang
  revision 2025-11-15 {
    ysv:version "2.0.0";
    rev:non-backwards-compatible;
    description
      "Removed obsolete interface types. This is a
       non-backwards-compatible change.";
  }
~~~~

## Backwards-Compatible Change Rules

According to {{I-D.ietf-netmod-yang-module-versioning}}, the following types of changes are considered backwards-compatible:

- Adding new data nodes (e.g., new enums, identities, leafs)
- Adding new optional elements
- Changing status from "current" (note: "current" is implied when no status is present) to "deprecated"
- Removing schema nodes with status "obsolete"
- Adding or updating "description" and "reference" statements (if the semantic meaning is unchanged)
- Adding, removing, or changing extension statements (depending on the specific extension)

## Non-Backwards-Compatible Changes

The following types of changes are non-backwards-compatible:

- Removing data nodes (unless already marked obsolete)
- Changing status from "current" (note: "current" is implied when no status is present) or "deprecated" to "obsolete"
- Renaming data nodes or changing their identifiers
- Changing the type of a data node in a way that alters syntax or semantics
- Changing numeric values assigned to enums
- Modifying descriptions in ways that change semantic meaning
- Restricting the allowed value set (e.g., reducing ranges, removing enum values)

# Two Primary Cases for IANA {#sec-cases}

## Case 1: YANG Modules Published in IETF Documents

When IETF documents containing YANG modules are published as RFCs, IANA is responsible for:

1. Registering the YANG module in the "YANG Module Names" registry
2. Ensuring the module file is available via the IANA website
3. Verifying that the module includes the required YANG Semver version identifier
4. Checking that the `rev:non-backwards-compatible` extension is present when required

For modules published in RFCs, the IETF document authors are responsible for determining the correct version and ensuring compliance with versioning rules. IANA's role is primarily verification and registration.

### IANA Actions for Case 1

When processing a YANG module from an Internet Draft:

1. **Verify Version Presence**: Check that the module includes a `ysv:version` statement in the most recent revision
2. **Verify NBC Extension**: If the version indicates an NBC change (MAJOR version increment from previous module published in an existing RFC), verify that `rev:non-backwards-compatible` is present
3. **Register Module**: Add the module to the YANG Module Names registry
4. **Publish Module**: Make the module file available on the IANA website
5. **Report Issues**: If versioning problems are found, report them to the Internet Draft authors and the NETMOD working group
[JMC: Why NETMOD?  If the author used the wrong version, I'd think reporting to them alone is fine.]

## Case 2: YANG Modules Derived from IANA Registries

Many IANA registries have corresponding YANG modules that represent registry contents in a machine-readable format. Examples include:

- **iana-if-type** - derived from the Interface Types (ifType) registry
- **iana-routing-types** - derived from Address Family Numbers and SAFI Parameters registries
- **iana-bgp-types** - derived from BGP Parameters registries

When these registries are updated, the corresponding YANG modules MUST be updated accordingly, following the versioning rules defined in this document.

### Characteristics of IANA-Maintained Modules

IANA-maintained modules typically:

- Have names starting with "iana-"
- Contain enumeration typedefs or identity definitions
- Map directly to registry entries
- Are updated more frequently than IETF-defined modules
- Follow a linear version history (no branching)

### IANA Actions for Case 2

When updating an IANA-maintained YANG module:

1. **Identify Registry Change**: Determine what changed in the source registry
2. **Classify Change**: Determine if the change is NBC, BC, or Editorial using the guidance in {{sec-classification}}
3. **Determine Version**: Calculate the new YANG Semver version based on the classification
4. **Update Module**: Make the necessary changes to the YANG module
5. **Add Revision**: Add a new revision statement with the date, version, description, and NBC extension if required
6. **Validate Module**: Use YANG validation tools to check the updated module
7. **Seek Review if Uncertain**: Contact YANG Doctors or the NETMOD WG if classification is unclear
8. **Publish Module**: Make the updated module available on the IANA website

# Classifying Changes {#sec-classification}

The most critical step in updating IANA-maintained YANG modules is correctly classifying the type of change. This determines the version number and whether the `rev:non-backwards-compatible` extension is required.

## Classification Principles

### Principle 1: The Source Does Not Determine Classification

The source or trigger of a change (errata report, new RFC, registry update, etc.) does NOT determine whether it is NBC, BC, or Editorial. What matters is the actual change made to the YANG module.

Example: An errata report could result in any type of change:
- Fixing a typo in a description → Editorial
- Adding a missing enum value → BC
- Correcting an incorrect value assignment → NBC

### Principle 2: Focus on Module Content

Only changes to the module's content affect classification. Changes to:
- Registry metadata not represented in YANG → No module change needed
- Registry fields not mapped to YANG statements → No module change needed
- Comments in the registry → May or may not require module update

### Principle 3: When in Doubt, Ask

If the classification is unclear or complex, IANA SHOULD seek guidance from:
- The YANG Doctors mailing list (yang-doctors@ietf.org)
- The NETMOD Working Group
- The OPS/Management Area Directors

It is better to ask for clarification than to publish an incorrectly versioned module.

## Quick Classification Guide

| Change Type | Classification | Version Change | Add NBC Extension |
|:------------|:--------------|:--------|:----------|
| Add new enum/identity | BC | MINOR | No |
| Update reference statement | Editorial | PATCH | No |
| Change status to deprecated | BC | MINOR | No |
| Change status to obsolete | NBC | MAJOR | Yes |
| Remove enum/identity | NBC | MAJOR | Yes |
| Rename enum/identity | NBC | MAJOR | Yes |
| Change enum value number | NBC | MAJOR | Yes |
| Clarify description (no meaning change) | Editorial | PATCH | No |
| Change description meaning | NBC | MAJOR | Yes |

# Common Scenarios {#sec-scenarios}

This section describes common scenarios that IANA encounters when updating YANG modules derived from registries. For each scenario, the classification, version change, and required actions are specified.

A comprehensive list of scenarios with detailed examples is provided in the companion document "IANA YANG Module Update Guidance - Summary Document" which is included as an appendix to this RFC.

## Adding a New Registry Entry

**Scenario**: A new entry is added to an IANA registry (e.g., a new interface type, new SAFI value).

**YANG Module Change**: Add a new enum value or identity to the corresponding typedef or base identity.

**Classification**: Backwards-Compatible (BC)

**Version Change**: Increment MINOR version (e.g., 1.0.0 → 1.1.0)

**Extension Required**: None

**Example**:

~~~~ yang
  // Previous version 1.0.0
  typedef interface-type {
    type enumeration {
      enum ethernet {
        value 6;
        description "Ethernet interface";
      }
    }
  }

  // New version 1.1.0
  revision 2025-11-15 {
    ysv:version "1.1.0";
    description "Added wifi interface type";
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

## Deprecating a Registry Entry

**Scenario**: A registry entry is marked as deprecated (e.g., because it's being phased out).

**YANG Module Change**: Change the status of the corresponding enum or identity from "current" to "deprecated".

**Classification**: Backwards-Compatible (BC)

**Version Change**: Increment MINOR version (e.g., 1.0.0 → 1.1.0)

**Extension Required**: None

**Rationale**: Per {{I-D.ietf-netmod-yang-module-versioning}}, adding or changing status to "deprecated" is explicitly allowed as a BC change.

**Example**:

~~~~ yang
  // Previous version 1.0.0
  enum oldtype {
    value 99;
    status current;
    description "Old interface type";
  }

  // New version 1.1.0
  revision 2025-11-15 {
    ysv:version "1.1.0";
    description "Deprecated oldtype interface";
  }

  enum oldtype {
    value 99;
    status deprecated;
    description
      "Old interface type. This value is deprecated and
       SHOULD NOT be used in new implementations.";
  }
~~~~

## Obsoleting a Registry Entry

**Scenario**: A registry entry is marked as obsolete (e.g., because support has been removed).

**YANG Module Change**: Change the status of the corresponding enum or identity from "deprecated" (or "current" or implied "current" when no status is specified) to "obsolete".

**Classification**: Non-Backwards-Compatible (NBC)

**Version Change**: Increment MAJOR version (e.g., 1.0.0 → 2.0.0)

**Extension Required**: `rev:non-backwards-compatible` MUST be added

**Rationale**: Changing status to "obsolete" indicates that the value MUST NOT be used, which breaks compatibility with implementations that may still use it.

**Example**:

~~~~ yang
  // Previous version 1.0.0
  enum removedtype {
    value 98;
    status deprecated;
    description "Type being removed";
  }

  // New version 2.0.0
  revision 2025-11-15 {
    ysv:version "2.0.0";
    rev:non-backwards-compatible;
    description
      "Obsoleted removedtype interface. Support has been removed.";
  }

  enum removedtype {
    value 98;
    status obsolete;
    description
      "Obsolete interface type. This value MUST NOT be used.
       Support was removed as of 2025-11-15.";
  }
~~~~

## Updating References

**Scenario**: A reference is updated in the registry (e.g., replacing an obsoleted RFC, adding a new reference, updating a draft reference to an RFC number).

**YANG Module Change**: Update or add to the "reference" statement of the corresponding enum or identity.

**Classification**: Editorial

**Version Change**: Increment PATCH version (e.g., 1.0.0 → 1.0.1)

**Extension Required**: None

**Rationale**: Per RFC 7950, "A 'reference' statement may be added or updated." Since this doesn't change the semantic meaning of the module, it's editorial.

**Note**: If a new enum/identity is being added WITH a reference, that addition is BC (MINOR increment). Later updating just the reference is Editorial (PATCH increment).

**Example**:

~~~~ yang
  revision 2025-10-15 {
    ysv:version "1.0.0";
    description "Initial version";
  }

  enum foo {
    value 42;
    description "Foo interface type";
    reference "RFC 1234";
  }
~~~~
{: align="left" title="Previous module version (1.0.0)"}


~~~~ yang
  revision 2025-11-15 {
    ysv:version "1.0.1";
    description "Updated reference for RFC obsolescence";
  }

  enum foo {
    value 42;
    description "Foo interface type";
    reference "RFC 5678 (obsoletes RFC 1234)";
  }
~~~~
{: align="right" title="New module version (1.0.1)"}


# Available Tooling {#sec-tooling}

Several tools are available to assist IANA in validating and versioning YANG modules. This section describes the primary tools and their recommended usage.

## YANG Validation Tools

### pyang

**pyang** is a widely-used YANG validator and converter. It can:

- Validate YANG syntax
- Check for backwards-compatible violations
- Generate tree diagrams and documentation

**Basic Usage**:

~~~~ shell
  pyang --ietf module-name.yang
~~~~

**Checking for NBC Changes** (comparing two versions):

~~~~ shell
  pyang --check-update-from old-module.yang new-module.yang
~~~~

This command will report violations of the backwards-compatible update rules.
[JMC: My vision is that with a new argument such as `--derive-semver-from` it will produce a new YANG Semver.
Should we hold off on moving this document forward until the work is done?]

### yanglint

**yanglint** (from the libyang project) can:

- Validate YANG modules
- Check cross-module dependencies
- Validate instance data against YANG models

**Basic Usage**:

~~~~ shell
  yanglint module-name.yang
~~~~

### YANG Catalog Tools

The YANG Catalog (https://www.yangcatalog.org) provides online tools for:

- Validating YANG modules
- Comparing module versions
- Viewing module dependencies
- Searching for modules

## Recommended Workflow

1. **Make Changes to Module** - Update the YANG file based on registry changes
2. **Validate Syntax** - Run pyang or yanglint to check for syntax errors
3. **Check for NBC Changes** - Use `pyang --check-update-from` to compare with previous version [JMC: I think giving them something that lets them derive the Semver for _their_ modules would be useful.]
4. **Review Tool Output** - Analyze any reported issues
5. **Determine Version** - Based on classification, set the new version number
6. **Add Revision Statement** - Include date, version, description, and NBC extension if needed
7. **Final Validation** - Validate the complete updated module
8. **Seek Review if Needed** - Contact experts if tool output is unclear or surprising

## Tool Limitations

While tools are very helpful, they have limitations:

- **Cannot determine Editorial vs BC for descriptions** - A description change may or may not change semantics; tools cannot always tell
- **Cannot assess impact** - Tools identify NBC changes but cannot assess the practical impact
- **May have false positives/negatives** - Tool output should be reviewed by a human

Always combine tool usage with the classification guidance in this document and seek expert review when uncertain.

## Future Tool Development

The NETMOD working group is developing additional tools specifically for IANA YANG module management, including:

- Automated registry-to-YANG conversion
- NBC change detection optimized for IANA modules
- Version recommendation based on changes

IANA will be informed as these tools become available.

# Seeking Expert Guidance {#sec-expert-guidance}

## When to Seek Guidance

IANA SHOULD contact YANG experts in the following situations:

1. **Classification Uncertainty** - When it's unclear whether a change is NBC, BC, or Editorial
2. **Complex Changes** - Multiple simultaneous changes to a registry that are difficult to classify individually
3. **Description Changes** - When a description update may alter the semantic meaning
4. **Unusual Situations** - Any scenario not clearly covered in this document
5. **Registry Restructuring** - Major changes to how a registry is organized
6. **Value Reuse** - When considering reusing a previously assigned value number
7. **Tool Disagreement** - When validation tools give unexpected or contradictory results

## How to Seek Guidance

### Primary Contact: YANG Doctors

Email the YANG Doctors mailing list:

- **Email**: yang-doctors@ietf.org
- **Purpose**: Technical review and guidance on YANG modules
- **Response Time**: Typically 1-2 weeks

When emailing, please include:
- Description of the registry change
- The affected YANG module and relevant excerpts
- Your proposed classification and version change
- Specific questions or concerns
- Any relevant tool output

### Secondary Contact: NETMOD WG

For broader issues or when needing working group discussion:
- **Email**: netmod@ietf.org
- **Purpose**: Working group discussion of YANG issues

### Area Director Contact

For urgent issues or escalation:
- **Copy**: OPS/Management Area Directors
- **Purpose**: Escalation and prioritization

## Example Request

~~~~ text
  Subject: YANG Versioning Question - iana-if-type Update

  Dear YANG Doctors,

  I need guidance on classifying a change to the iana-if-type module.

  Registry Change:
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
~~~~

# Security Considerations

This document gives instructions to IANA on how to handle YANG modules that are published in RFCs and also YANG modules that are derived from IANA registries.

Incorrect interpretation of this document could cause incorrect handling or versioning of IANA maintained YANG modules.

This document recommends the usage of various tools.  Bugs or attacks on these tools could cause the tools to give incorrect or misleading guidance.  In all cases, secondary evaluation of output of the tools should be performed to confirm that they are giving the anticipated results.  The _YANG Doctors_ team can also be contacted for further advice, if required.


# IANA Considerations

This entire document relates to guidance to IANA, and hence can be deemed to be considerations to IANA.  **RFC Editor (or IANA), please consider whether this section of the document should be elided.**


--- back

# Appendix A: Summary of Registry Action Scenarios {#appendix-scenarios}

This appendix provides a comprehensive reference table of common registry actions and their corresponding YANG module versioning requirements. For detailed examples and explanations of each scenario, refer to the companion document "IANA YANG Module Update Guidance - Summary Document" (iana-guidance-summary.md in this repository).

## Quick Reference Table

| Registry Action | YANG Change | Change classification | Version Change | NBC Extension Required |
|:----------------|:------------|:---------------|:--------|:-------------------|
| Add new registration | Add enum/identity | BC | MINOR | No |
| Update reference (obsoleted RFC) | Update reference | Editorial | PATCH | No |
| Add additional reference | Update reference | Editorial | PATCH | No |
| Draft → RFC reference | Update reference | Editorial | PATCH | No |
| Deprecate (keep name) | status deprecated | BC | MINOR | No |
| Obsolete entry | status obsolete | NBC | MAJOR | Yes |
| Remove entry completely | Remove enum/identity | NBC | MAJOR | Yes |
| Deprecate + remove name | Remove enum/identity | NBC | MAJOR | Yes |
| Change value number | Change value | NBC | MAJOR | Yes |
| Reuse old value | Add with old value | NBC | MAJOR | Yes |
| Update description (clarify) | Update description | Editorial | PATCH | No |
| Update description (change meaning) | Update description | NBC | MAJOR | Yes |
| Rename entry | Change identifier | NBC | MAJOR | Yes |
| Add footnote | Optionally update | Editorial | PATCH | No |
| Non-YANG field changes | No change | N/A | None | No |
| Errata | Depends on content | Analyze | Varies | Maybe |
| Early alloc expired (left as-is) | No change | N/A | None | No |
| Early alloc expired (removed) | Follow removal rules | NBC | MAJOR | Yes |
| Revive expired allocation | Add enum/identity | BC | MINOR | No |

## Notes on the Table

- **BC** = Backwards-Compatible
- **NBC** = Non-Backwards-Compatible
- **MAJOR/MINOR/PATCH** refer to the YANG Semver version components
- **NBC Extension Required** _Yes_ means `rev:non-backwards-compatible` MUST be added under the new revision statement.
- When **Varies** or **Maybe** is shown, analyze the specific change using the detailed scenario guidance

# Appendix B: Example IANA-Maintained Modules

This appendix shows examples of well-structured IANA-maintained YANG modules.

## Example: iana-if-type Module Structure

~~~~ yang
module iana-if-type {
  yang-version 1.1;
  namespace "urn:ietf:params:xml:ns:yang:iana-if-type";
  prefix ianaift;

  import ietf-yang-revisions { prefix rev; }
  import ietf-yang-semver { prefix ysv; }

  organization
    "Internet Assigned Numbers Authority (IANA)";

  contact
    "Internet Assigned Numbers Authority

     ICANN
     12025 Waterfront Drive, Suite 300
     Los Angeles, CA 90094

     Tel: +1 424 254 5300

     <mailto:iana@iana.org>

     See the Interface Types (ifType) registry:
     <https://www.iana.org/assignments/smi-numbers>";

  description
    "This YANG module defines the iana-if-type typedef, which
     contains YANG definitions for IANA-registered interface types.

     This YANG module is maintained by IANA and reflects the
     'Interface Types (ifType)' registry.

     Copyright (c) 2025 IETF Trust and the persons identified as
     authors of the code.  All rights reserved.

     Redistribution and use in source and binary forms, with or
     without modification, is permitted pursuant to, and subject
     to the license terms contained in, the Revised BSD License
     set forth in Section 4.c of the IETF Trust's Legal Provisions
     Relating to IETF Documents
     (https://trustee.ietf.org/license-info).

     This version of this YANG module is part of RFC XXXX; see
     the RFC itself for full legal notices.";

  // Example revision with new interface type (BC change)
  revision 2025-11-15 {
    ysv:version "2.1.0";
    description
      "Added new interface type 'wifi6e' per registry update.";
    reference
      "RFC XXXX: IANA Guidance for YANG Modules";
  }

  // Example revision with obsoleted type (NBC change)
  revision 2025-10-01 {
    ysv:version "2.0.0";
    rev:non-backwards-compatible;
    description
      "Obsoleted 'arcnet' interface type as support has been removed.";
    reference
      "RFC XXXX: IANA Guidance for YANG Modules";
  }

  // Example earlier revision (BC change)
  revision 2025-09-01 {
    ysv:version "1.1.0";
    description
      "Added new interface type 'virtualEthernet'.";
  }

  // Example initial revision
  revision 2025-01-01 {
    ysv:version "1.0.0";
    description
      "Initial version matching the Interface Types registry
       as of 2025-01-01.";
  }

  typedef interface-type {
    type enumeration {
      enum other {
        value 1;
        description
          "None of the following types.";
      }
      enum ethernet {
        value 6;
        description
          "Ethernet interface, including 10BASE-T, 100BASE-T,
           and 1000BASE-T variants.";
        reference
          "RFC 3635: Definitions of Managed Objects for the
           Ethernet-like Interface Types";
      }
      enum wifi6e {
        value 289;
        description
          "IEEE 802.11ax (Wi-Fi 6E) wireless interface.";
        reference
          "IEEE 802.11ax-2021";
      }
      enum arcnet {
        value 35;
        status obsolete;
        description
          "ARCNET interface. This interface type is obsolete
           and MUST NOT be used. Support was removed 2025-10-01.";
      }
    }
    description
      "This typedef is used to represent the IANA-registered
       interface types. It is derived from the 'Interface Types
       (ifType)' registry.";
    reference
      "IANA Interface Types registry:
       <https://www.iana.org/assignments/smi-numbers>";
  }
}
~~~~

# Acknowledgments
{:numbered="false"}

The creation of this document was due to initial questions from IANA team members Amanda Baber and Sabrina Tanamal, followed by a productive in-person discussion at IETF 124 between members of the YANG versioning design team, IANA team, NETMOD working group chairs, and the OPS Area Director.

Participants in that meeting included: Amanda Baber, Jason Sterne, Joe Clarke, Kent Watsen, Lou Berger, Mahesh Jethanandani, Per Andersson, Reshad Rahman, Rob Wilton, and Sabrina Tanamal.

Special thanks to Joe Clarke for his presentation on YANG versioning tooling at IETF 124, which informed the tooling guidance in this document.

The authors also thank the NETMOD working group for their extensive work on YANG versioning specifications that form the foundation of this guidance.

The initial substantive revision of this document used to Claude Sonnet 4.5 to create the initial prose and examples, which have been subsequently reviewed by the YANG Versioning team.
