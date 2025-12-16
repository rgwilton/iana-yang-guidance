# IANA and RFC Editor YANG Module Management Guidance - Summary Document

This document provides comprehensive guidance for IANA staff and the RFC Editor on managing YANG modules. It addresses two primary scenarios:

1. **YANG Modules in RFCs**: Managing YANG modules contained in documents approved by the IESG that are being published as RFCs
2. **IANA-Maintained YANG Modules**: Managing YANG modules derived from IANA registries when those registries are updated

This summary document complements the formal IETF draft "Guidance for Managing YANG Modules in RFCs and IANA Registries" (draft-verdt-iana-yang-guidance).

## Background

YANG modules are used to model network management data and protocols. The IETF publishes YANG modules as part of RFCs, and IANA maintains several YANG modules that are derived directly from IANA registries (such as interface types, routing types, etc.). 

When an RFC containing a YANG module is published, both the RFC Editor and IANA have responsibilities to ensure the module is correctly versioned and published. When IANA registries are updated, the corresponding YANG modules must also be updated following specific versioning rules.

## Document Context and Updates

**Original Creation**: This guidance was created in response to questions from IANA staff (Amanda Baber and Sabrina Tanamal) regarding how to correctly version YANG modules when registries are updated.

**Major Restructuring (December 2025)**: The document was significantly restructured to:
- Expand the scope from IANA-only guidance to cover both RFC Editor and IANA responsibilities
- Clarify the two distinct processes: (1) RFC publication workflow and (2) IANA registry update workflow
- Emphasize that this is informational guidance rather than strict rules
- Provide more detailed workflow steps for both the RFC Editor and IANA
- Move detailed tooling guidance to an appendix with explanations of purpose, usage, and interpretation
- Reorganize scenarios into a comprehensive appendix with detailed examples

## Key Concepts

### Change Classification

All changes to YANG modules fall into three categories:

1. **Non-Backwards-Compatible (NBC)** - Changes that break compatibility with previous versions
2. **Backwards-Compatible (BC)** - Changes that maintain compatibility but add functionality
3. **Editorial** - Changes that don't affect functionality or compatibility (documentation only)

### Version Numbering (YANG Semver)

YANG modules use semantic versioning in the format: **MAJOR.MINOR.PATCH**

- **MAJOR** version - Incremented for NBC changes (e.g., 1.0.0 → 2.0.0)
- **MINOR** version - Incremented for BC changes (e.g., 1.0.0 → 1.1.0)
- **PATCH** version - Incremented for editorial changes (e.g., 1.0.0 → 1.0.1)

### Key Extension Statements

1. **rev:non-backwards-compatible** - MUST be added to revision statement when NBC changes occur
2. **ysv:version** - Contains the semantic version (e.g., "1.2.0")

## Registry Action Scenarios and Classifications

### Scenario 1: Adding a New Registration

**Registry Action:** New entry added to IANA registry (e.g., new interface type, new SAFI value)

**YANG Module Change:** Add new enum value or identity

**Classification:** Backwards-Compatible (BC)

**Version Change:** Increment MINOR version (e.g., 1.0.0 → 1.1.0)

**Extension Required:** None

**Example:**
```yang
// Before (version 1.0.0):
enum ethernet {
  value 6;
  description "Ethernet interface";
}

// After (version 1.1.0):
enum ethernet {
  value 6;
  description "Ethernet interface";
}
enum wifi {
  value 71;
  description "IEEE 802.11 wireless interface";
}
```

---

### Scenario 2: Updating Reference (Obsoleted RFC)

**Registry Action:** Replacing reference because an RFC has been obsoleted

**YANG Module Change:** Update reference statement only

**Classification:** Editorial

**Version Change:** Increment PATCH version (e.g., 1.0.0 → 1.0.1)

**Extension Required:** None

**Rationale:** Per RFC 7950, reference statements may be added or updated. Since this doesn't change the semantic meaning, it's editorial.

**Example:**
```yang
// Before (version 1.0.0):
enum foo {
  value 42;
  description "The foo type";
  reference "RFC 1234";
}

// After (version 1.0.1):
enum foo {
  value 42;
  description "The foo type";
  reference "RFC 5678 (obsoletes RFC 1234)";
}
```

---

### Scenario 3: Adding Additional Reference

**Registry Action:** Listing additional (not replacement) reference for registration

**YANG Module Change:** Add to or update reference statement

**Classification:** Editorial

**Version Change:** Increment PATCH version (e.g., 1.0.0 → 1.0.1)

**Extension Required:** None

**Example:**
```yang
// Before (version 1.0.0):
enum bar {
  value 43;
  description "The bar type";
  reference "RFC 2222";
}

// After (version 1.0.1):
enum bar {
  value 43;
  description "The bar type";
  reference "RFC 2222, RFC 3333";
}
```

---

### Scenario 4: Updating Draft Reference to RFC Number

**Registry Action:** Assignment made after IESG approval; replacing draft string with RFC number upon publication

**YANG Module Change:** Update reference statement from draft to RFC

**Classification:** Editorial

**Version Change:** Increment PATCH version (e.g., 1.1.0 → 1.1.1)

**Extension Required:** None

**Notes:**
- If the enum/identity was FIRST added with the draft reference, that initial addition is BC and increments MINOR version
- Subsequent update of just the reference (draft → RFC) is editorial and increments PATCH version

**Example:**
```yang
// Initial addition (version 1.0.0 → 1.1.0 - BC/MINOR):
enum newtype {
  value 100;
  description "New experimental type";
  reference "draft-ietf-example-newtype-05";
}

// Later reference update (version 1.1.0 → 1.1.1 - Editorial/PATCH):
enum newtype {
  value 100;
  description "New experimental type";
  reference "RFC 9999";
}
```

---

### Scenario 5: Deprecating a Registration (Name Visible)

**Registry Action:** Chairs decide to mark value as "deprecated" while keeping the name/description visible

**YANG Module Change:** Change status from "current" to "deprecated"

**Classification:** Backwards-Compatible (BC)

**Version Change:** Increment MINOR version (e.g., 1.0.0 → 1.1.0)

**Extension Required:** None

**Notes:** Changing status to "deprecated" is explicitly allowed as BC per the YANG module versioning rules.

**Example:**
```yang
// Before (version 1.0.0):
enum oldtype {
  value 99;
  status current;
  description "Old type, being phased out";
}

// After (version 1.1.0):
enum oldtype {
  value 99;
  status deprecated;
  description
    "Old type, being phased out. This value is deprecated 
     and SHOULD NOT be used in new implementations.";
}
```

---

### Scenario 6: Obsoleting a Registration

**Registry Action:** Chairs decide to mark value as "obsolete"

**YANG Module Change:** Change status from "deprecated" to "obsolete", or directly to "obsolete"

**Classification:** Non-Backwards-Compatible (NBC)

**Version Change:** Increment MAJOR version (e.g., 1.0.0 → 2.0.0)

**Extension Required:** rev:non-backwards-compatible MUST be added

**Notes:** 
- Changing status to "obsolete" is NBC
- Should generally only happen after a period as "deprecated"
- Description should explain why it was obsoleted

**Example:**
```yang
// Before (version 1.0.0):
enum oldtype {
  value 99;
  status deprecated;
  description "Old type, being phased out";
}

// After (version 2.0.0):
revision 2025-11-15 {
  ysv:version "2.0.0";
  rev:non-backwards-compatible;
  description "Obsolete oldtype enum";
}

enum oldtype {
  value 99;
  status obsolete;
  description
    "Old type. This value is obsolete and MUST NOT be used.
     Support for this value has been removed as of 2025-11-15.";
}
```

---

### Scenario 7: Removing a Registration Completely

**Registry Action:** Registration removed completely with no trace

**YANG Module Change:** Remove enum/identity from YANG module

**Classification:** Non-Backwards-Compatible (NBC)

**Version Change:** Increment MAJOR version (e.g., 1.0.0 → 2.0.0)

**Extension Required:** rev:non-backwards-compatible MUST be added

**Notes:**
- This is strongly discouraged; prefer marking as "obsolete" instead
- Removal is NBC because it eliminates a previously valid value
- RFC 7950 does not allow removal of schema nodes

**Example:**
```yang
// Before (version 1.0.0):
enum removedtype {
  value 98;
  description "This will be removed";
}

// After (version 2.0.0):
revision 2025-11-15 {
  ysv:version "2.0.0";
  rev:non-backwards-compatible;
  description "Remove removedtype enum";
}

// enum removedtype is now completely removed from the module
```

---

### Scenario 8: Deprecating with Name/Description Removed

**Registry Action:** Chairs decide to mark as "deprecated" AND remove the name/description

**YANG Module Change:** Remove enum/identity (cannot set deprecated without keeping definition)

**Classification:** Non-Backwards-Compatible (NBC)

**Version Change:** Increment MAJOR version (e.g., 1.0.0 → 2.0.0)

**Extension Required:** rev:non-backwards-compatible MUST be added

**Notes:**
- Cannot mark something as deprecated without keeping its definition in YANG
- If name/description must be removed, the enum/identity must be removed entirely
- This should be a two-step process: first deprecate (keeping definition), then later remove/obsolete

---

### Scenario 9: Errata Reports

**Registry Action:** Errata report filed for YANG module or underlying registry

**YANG Module Change:** Depends on the specific errata content

**Classification:** Determined by the actual change, not the source

**Version Change:** Follow rules based on actual change type

**Extension Required:** May be required depending on change

**Notes:**
- The source/trigger (errata, new RFC, etc.) does NOT determine NBC vs BC classification
- What matters is the resulting change to the YANG module itself
- Analyze the actual errata change using other scenarios in this document

**Examples:**
- Errata fixes typo in description → Editorial/PATCH
- Errata adds missing enum → BC/MINOR
- Errata corrects wrong value assignment → NBC/MAJOR

---

### Scenario 10: Early Allocation Expiration (Left As-Is)

**Registry Action:** Early allocation expires; chairs decide to leave registration without marking deprecated or removing

**YANG Module Change:** No change to YANG module

**Classification:** No change

**Version Change:** No version change needed

**Extension Required:** None

**Notes:**
- If the value was never added to a published YANG module, no module update needed
- If already in module and implementation continues, no change needed
- Registry and YANG module can have different status if justified

---

### Scenario 11: Early Allocation Expiration (Deprecated/Removed)

**Registry Action:** Early allocation expires; chairs decide to deprecate or remove

**YANG Module Change:** Follow Scenario 5 (deprecate) or Scenario 7 (remove)

**Classification:** BC (if deprecated) or NBC (if removed/obsoleted)

**Version Change:** MINOR (if deprecated) or MAJOR (if removed/obsoleted)

**Extension Required:** rev:non-backwards-compatible if removed/obsoleted

---

### Scenario 12: Reviving Expired Allocation

**Registry Action:** Previously expired allocation is renewed/revived

**YANG Module Change:** Add back enum/identity (if it was removed)

**Classification:** Backwards-Compatible (BC)

**Version Change:** Increment MINOR version

**Extension Required:** None

**Notes:**
- Treat as adding a new value
- If it was never removed from YANG module, may just be editorial reference update
- If value number is being reused, carefully document this in description

---

### Scenario 13: Changing Registration Value

**Registry Action:** Changing the numeric value assigned to a registration

**YANG Module Change:** Change the value assigned to enum or identity

**Classification:** Non-Backwards-Compatible (NBC)

**Version Change:** Increment MAJOR version (e.g., 1.0.0 → 2.0.0)

**Extension Required:** rev:non-backwards-compatible MUST be added

**Notes:**
- This is rare and strongly discouraged
- Changing numeric values breaks compatibility for implementations using those values

**Example:**
```yang
// Before (version 1.0.0):
enum example {
  value 50;
  description "Example type";
}

// After (version 2.0.0):
revision 2025-11-15 {
  ysv:version "2.0.0";
  rev:non-backwards-compatible;
  description "Change value assignment for example enum";
}

enum example {
  value 55;  // Changed from 50 to 55
  description "Example type (value reassigned)";
}
```

---

### Scenario 14: Reusing a Previously Removed Value

**Registry Action:** Assigning a previously used value number to a new registration

**YANG Module Change:** Add new enum/identity with previously used value

**Classification:** Non-Backwards-Compatible (NBC)

**Version Change:** Increment MAJOR version (e.g., 2.0.0 → 3.0.0)

**Extension Required:** rev:non-backwards-compatible MUST be added

**Notes:**
- Values should not be reused until all other values are exhausted
- This is confusing for implementations and should be avoided
- Clearly document in description that value was reused

---

### Scenario 15: Changes to Non-Visible Fields

**Registry Action:** Changes to registry fields not reflected in YANG module (e.g., contact info, additional notes)

**YANG Module Change:** No change to YANG module

**Classification:** No change

**Version Change:** No version change needed

**Extension Required:** None

**Notes:**
- Only changes that affect the YANG module content require module updates
- Registry may have additional fields not represented in YANG

---

### Scenario 16: Adding a Footnote

**Registry Action:** Adding footnote to registration in registry

**YANG Module Change:** Potentially add to description, or no change

**Classification:** Editorial (if added to description) or No change

**Version Change:** Increment PATCH (if description updated) or no change

**Extension Required:** None

**Notes:**
- Evaluate whether footnote content should be reflected in YANG description
- If footnote is purely registry-administrative, may not affect YANG module

---

### Scenario 17: Field Changes Resembling Deprecation

**Registry Action:** Fields updated with text like "MUST NOT be used", "Do not use", etc.

**YANG Module Change:** Consider deprecating or obsoleting in YANG module

**Classification:** Backwards-Compatible (BC) if deprecated, NBC if obsoleted

**Version Change:** MINOR (if deprecated) or MAJOR (if obsoleted)

**Extension Required:** rev:non-backwards-compatible if obsoleted

**Notes:**
- Work with expert reviewers to determine appropriate YANG status
- "SHOULD NOT use" suggests deprecated status
- "MUST NOT use" suggests obsolete status
- Document reasoning clearly in description

---

### Scenario 18: Updating Name or Description

**Registry Action:** Substantive changes to the name or description field

**YANG Module Change:** Update enum identifier name or description text

**Classification:** 
- Changing enum/identity NAME: Non-Backwards-Compatible (NBC)
- Updating description text: Editorial (if meaning unchanged) or BC/NBC (if meaning changed)

**Version Change:**
- Name change: MAJOR
- Description clarification: PATCH
- Description meaning change: Evaluate based on impact

**Extension Required:** rev:non-backwards-compatible if NBC

**Notes:**
- Name changes are always NBC because they break programmatic references
- Description changes must be evaluated for semantic impact
- If description change alters the defined meaning/behavior, it may be NBC

**Example - Name Change (NBC):**
```yang
// Before (version 1.0.0):
enum oldname {
  value 42;
  description "Some interface type";
}

// After (version 2.0.0):
revision 2025-11-15 {
  ysv:version "2.0.0";
  rev:non-backwards-compatible;
  description "Rename oldname to newname";
}

enum newname {  // Name changed - NBC!
  value 42;
  description "Some interface type";
}
```

**Example - Description Clarification (Editorial):**
```yang
// Before (version 1.0.0):
enum foo {
  value 42;
  description "Foo interface";
}

// After (version 1.0.1):
enum foo {
  value 42;
  description 
    "Foo interface type for handling foo protocol traffic";
}
```

---

## Registry-Level Changes

### Scenario 19: Adding New Registry Fields

**Registry Action:** New fields added to registry structure

**YANG Module Change:** Evaluate if new field should be represented in YANG

**Classification:** 
- If field not added to YANG: No change
- If field added as new leaf: Backwards-Compatible (BC)

**Version Change:**
- No YANG change: No version change
- New leaf added: MINOR

---

### Scenario 20: Removing Registry Fields

**Registry Action:** Fields removed from registry structure

**YANG Module Change:** Evaluate if removal affects YANG module

**Classification:**
- If field not in YANG: No change
- If corresponding leaf removed: NBC

**Version Change:**
- No YANG change: No version change
- Leaf removed: MAJOR

---

### Scenario 21: Registry Consolidation

**Registry Action:** Multiple registries consolidated into one

**YANG Module Change:** Potentially combine modules or restructure

**Classification:** Non-Backwards-Compatible (NBC) for major restructuring

**Version Change:** MAJOR

**Extension Required:** rev:non-backwards-compatible MUST be added

**Notes:**
- This is a major change that requires careful planning
- Consider maintaining old modules as deprecated for transition period
- Coordinate with expert reviewers and working groups

---

### Scenario 22: Registry Deprecated/Obsoleted

**Registry Action:** Entire registry marked as deprecated or obsoleted

**YANG Module Change:** Mark entire module as deprecated or obsolete

**Classification:**
- Deprecated: Backwards-Compatible (BC)
- Obsoleted: Non-Backwards-Compatible (NBC)

**Version Change:**
- Deprecated: MINOR
- Obsoleted: MAJOR

**Extension Required:** rev:non-backwards-compatible if obsoleted

**Example:**
```yang
// Module deprecated (version 1.0.0 → 1.1.0):
revision 2025-11-15 {
  ysv:version "1.1.0";
  description
    "This entire module is now deprecated. Please use
     iana-new-registry-types instead.";
}

// Module obsoleted (version 1.1.0 → 2.0.0):
revision 2026-01-15 {
  ysv:version "2.0.0";
  rev:non-backwards-compatible;
  description
    "This entire module is now obsolete and MUST NOT be used.
     Use iana-new-registry-types instead.";
}
```

---

## Decision Matrix - Quick Reference

| Registry Action | YANG Change | Classification | Version | Extension |
|----------------|-------------|----------------|---------|-----------|
| Add new registration | Add enum/identity | BC | MINOR | None |
| Update reference (obsoleted RFC) | Update reference | Editorial | PATCH | None |
| Add additional reference | Update reference | Editorial | PATCH | None |
| Draft → RFC reference | Update reference | Editorial | PATCH | None |
| Deprecate (keep name) | status deprecated | BC | MINOR | None |
| Obsolete entry | status obsolete | NBC | MAJOR | Required |
| Remove entry | Remove enum/identity | NBC | MAJOR | Required |
| Deprecate (remove name) | Remove enum/identity | NBC | MAJOR | Required |
| Change value number | Change value | NBC | MAJOR | Required |
| Reuse old value | Add with old value | NBC | MAJOR | Required |
| Update description (clarify) | Update description | Editorial | PATCH | None |
| Update description (change meaning) | Update description | NBC | MAJOR | Required |
| Rename entry | Change identifier | NBC | MAJOR | Required |
| Add footnote | Optionally update | Editorial | PATCH | None |
| Non-YANG field changes | No change | N/A | None | None |
| Errata | Depends | Analyze | Varies | Maybe |

---

## Process Guidelines

### When to Seek Expert Guidance

Contact the YANG Doctors mailing list (yang-doctors@ietf.org) and copy the OPS/Management Area Director in these situations:

1. **Unclear Classification** - When it's not obvious if a change is NBC, BC, or Editorial
2. **Complex Registry Updates** - Multiple simultaneous changes to registrations
3. **Semantic Ambiguity** - When description changes may alter meaning
4. **Registry Restructuring** - Major changes to registry organization
5. **Value Reuse** - When considering reusing a previously assigned value
6. **Unusual Situations** - Any scenario not clearly covered in this document

### Version Control Best Practices

1. **One Registry Action Per Module Version** - When possible, batch related registry changes
2. **Clear Revision Descriptions** - Describe exactly what changed and why
3. **Reference the Registry** - Link module revisions to specific registry updates
4. **Document NBC Changes** - Clearly explain why NBC change was necessary

### Module Publication Workflow

1. **Identify Registry Change** - Understand what changed in the registry
2. **Classify Change** - Determine NBC/BC/Editorial using this guidance
3. **Determine Version** - Calculate new MAJOR.MINOR.PATCH version
4. **Update Module** - Make necessary YANG changes
5. **Add revision Statement** - Include date, version, description, and NBC extension if needed
6. **Validate with Tools** - Use YANG validation tools (see Tooling section)
7. **Seek Review if Uncertain** - Contact experts when needed
8. **Publish** - Make updated module available

---

## Important Reminders

1. **Extension is Sticky** - Once rev:non-backwards-compatible is added, it indicates NBC changes occurred
2. **Source Doesn't Matter** - Whether change came from errata, new RFC, or registry update doesn't determine classification
3. **Actual Change Matters** - Focus on what actually changed in the YANG module
4. **When In Doubt, Ask** - Better to check with experts than publish incorrect version
5. **Editorial is NOT Trivial** - Editorial changes still need new revision and version increment
6. **Deprecate Before Obsolete** - Multi-step process is preferred for removing functionality

---

## Tooling Support

Detailed tooling guidance is provided in the main guidance document. Key tools include:

- **pyang** - YANG validation and NBC change detection
- **yanglint** - Module validation
- **YANG Catalog tools** - Version comparison and impact analysis

Refer to the "Available Tooling" section of the main guidance document for detailed usage instructions.

---

## References

This summary is based on:
- draft-ietf-netmod-yang-module-versioning - Updated YANG Module Revision Handling
- draft-ietf-netmod-yang-semver - YANG Semantic Versioning
- draft-ietf-netmod-rfc8407bis - Guidelines for YANG Module Authors
- Email correspondence between IANA staff and NETMOD working group
- IETF 124 meeting discussions

---

## Revision History

- 2025-11-11: Initial version created based on IANA questions and NETMOD WG guidance
- 2025-12-10: Major restructuring to expand scope to include RFC Editor, reorganize content into clear workflow sections, and add comprehensive appendices

## Detailed Restructuring Instructions (December 2025)

The following restructuring was performed based on detailed requirements:

### Document Scope Changes
1. Changed target audience from "IANA only" to "RFC Editor and IANA"
2. Identified two distinct process steps:
   - Managing YANG modules in documents approved by IESG for RFC publication
   - Managing IANA-maintained YANG modules when registries are updated

### Document Structure
The document was reorganized into:

1. **Abstract**: Concise summary without excessive detail
2. **Introduction**: 
   - Describes two scenarios (RFC publication and IANA registry updates)
   - Emphasizes informational guidance rather than strict rules
   - References key specifications (module-versioning, semver, filename)
   - Provides document roadmap
3. **Conventions and Definitions**: Standard terminology
4. **Background on YANG Versioning**:
   - References Section 3.1 of I-D.ietf-netmod-yang-module-versioning for BC rules
   - Notes refinement of RFC 7950 Section 11
   - Lists specific rules applicable to IANA-maintained modules (enumerations and identities)
   - Explains module immutability principle
5. **Section 4 - YANG Modules in RFCs**:
   - Core requirements (4 items): Semver version, NBC extension, revision immutability, RFC code markers
   - Workflow steps: Pre-release versions, RFC Editor processing, finalizing versions, IANA delay, coordinated publication
   - Guidance on determining correct versions
   - IANA verification responsibilities
6. **Section 5 - IANA-Maintained YANG Modules**:
   - Overview and characteristics
   - 8-step process: Follow RFC rules, identify change, apply changes, use tooling, update revision, validate, seek review, publish
   - Simplified decision process for common cases
   - Reference to detailed scenarios in appendix
7. **Section 6 - Seeking Expert Guidance**: When and how to contact experts
8. **Section 7 - Operational Considerations**: 
   - Purpose of correct versioning
   - Conservative approach when uncertain
9. **Section 8 - Security Considerations**: Existing text preserved
10. **Section 9 - IANA Considerations**: Updated for dual audience
11. **Appendix A - Tooling**: 
    - Moved from main body
    - Added note that tools evolve
    - Enhanced descriptions: purpose, installation, usage, output interpretation
    - Detailed workflow with examples
    - Comprehensive limitations section
    - Future tool development notes
12. **Appendix B - Scenarios**: 
    - Quick reference table
    - Detailed scenarios with examples (10 key scenarios)
    - Classification principles

### Key Principles Emphasized
- Source/trigger of change does NOT determine classification
- Module revision immutability
- Pre-release versions for drafts
- Conservative approach when uncertain
- Coordination between RFC Editor and IANA
- Tool-assisted but human-verified versioning

### References and Acknowledgments
- Updated to reflect collaboration with both RFC Editor and IANA
- Proper credit to IETF 124 meeting participants
- Recognition of NETMOD WG specifications

