# Specification

This document serves as the 0.1 specification for SBOMit (SBOM on in-toto).
The specification should not be considered stable at this time.

## Introduction

### Motivation
A major problem with the state of an SBOM today is that the documents are 
often inaccurate.  This is largely because the documents are derived by looking
at the resulting software and trying to understand what happened in the past.

This specification proposes a means to generate metadata for an SBOM while the
software is being created.  Furthermore, the means by which this information
is captured uses (in-toto)[https://in-toto.io] attestations and layouts.  This 
provides cryptograpic validation that this information is correct, handles 
key distribution and management to indicate which parties should be trusted
for each step, and captures information about the environment in which the 
steps are run.  

As a result, using SBOMit provides a more accurate SBOM when parties are 
honest.  When malicious parties interfere in the process, SBOMit provides 
a mix of traceability (knowing which party was malicious) and prevention 
(blocking malicious software from being trusted), depending on how the 
in-toto steps are configured..


### Definitions

SIT -- An SBOM which is derived from a SBOMit document.  It can
be in any SBOM format.  It points back to the SBOMit document, which was used
to generate it. 

SBOMit document -- A long form document which contains sufficient information
to verify the supply chain process is correct.  It may be used to generate one
or more SIT.

## Threat model

One key goal of our design is to provide as much security as possible in all 
cases.  Security should degrade gracefully when the attacker gains new 
capabilities.  Hence, we will consider attacker models where the attacker is 
exceedingly powerful and try to restrict the damage they can do even in those 
cases.

There are two types of protection SBOMit commonly provides:
* traceability -- this enables a party with an SBOMit document and the supply 
  chain to later determine which parties acted in a malicious manner.  Note
  that this may require some further manual analysis (e.g., to determine if
  an emitted binary is actually a correct output of a build server given
  a set of source code).  However, importantly, properties like non-repudiation
  hold, so that a party cannot appear to be innocent given a re-execution of a
  supply chain with all honest participants.

* prevention -- this is when an attack is blocked from having any impact.  This
  often occurs because an attack causes some aspect of validation to fail,
  resulting in no impact to the end user.  This can include situations like an
  inability to inject malicious metadata into the supply chain, a policy check
  in an in-toto layout causing a resulting SBOMit document to be rejected,
  or an inability to sign because of a lack of access to a private key trusted
  for that signature.

Note that a party verifying a SIT (only) does not gain the same security
guarantees.  They instead gain the same sort of guarantees that signed SBOMs
have today.  However, the SIT contains a reference to the SBOM document, so
it may be used to obtain the necessary information to perform more complete
verification.


The **actors** of the system are the following, each of whom possess a unique 
private key:
* a series of in-toto functionaries that perform the actual steps of the 
  software supply chain.  For example, a build server is functionary.

* a SIT mutator (per SIT), which is a process that adds supplemental information
  that will appear in an SIT.  This is likely performed by a combination
  of human actions (e.g., listing supplemental information) and automated
  tooling (e.g., adding information for a specific extended SBOM format).

* a SIT generator (per SIT), which generates the actual SIT file.

* an in-toto layout creator, who specifies the keys used by other parties
  and writes the policy (in-toto layout) that indicates how different steps 
  of the software supply chain interrelate.  This party also serves as the
  SBOMit root-of-trust.

* a (possibly empty) set of in-toto sub-layout creators.  These are identical
  in action to the in-toto layout creator, but they are only trusted for the
  subset of the layout which the layout creator delegates.  



We are able to limit attacker damage and/or trace the location of the 
compromise(s) in cases where:
* an attacker may possess cryptographic keys for any of the functionaries (the
  parties performing the software supply chain steps) in the system.  
  **Impact:** Prevents modification of items not allowed by the in-toto layout.
  Provides traceability in all cases.  No impact without the ability to 
  interject this metadata into the supply chain.
* an attacker may tamper with one or more steps of the software supply chain.
  For example, the build process, testing, packaging, etc.  
  **Impact:** Identical to the prior case.
* an attacker obtains a sub-layout key.  Note that this also requires the 
  ability to inject sublayout metadata into the supply chain such that other
  parties include it.  **Impact:** The impact could be prevented depending on 
  the restrictions placed upon the delegated sub-layout.  However, traceability
  always exists.  This is also similar to a functionary compromise.
* an attacker may become a man-in-the-middle between any steps of the 
  system.  **Impact:** Without further capabilities, this has no impact.
* an attacker may possess the key used to sign the SBOM resulting from the
  SBOMit process.  **Impact:** Prevention for parties performing full SBOMit
  verification.  Traceability otherwise.
* an attacker may compromise an SBOM mutator key or an SBOM mutator may act
  maliciously.  **Impact:** Traceability in all cases.  Depending on the 
  changes made to the SBOM, this may be detected by review of the resulting
  SBOM.  Changes that override in-toto derived information are specifically 
  flagged and unlikely to be accepted. XXXXXX
* an attacker is able to compromise a SIT generator key or a SIT generator 
  directly. **Impact:** Protection for clients who obtain the SBOMit document.
  Traceability for other clients. Note that the SIT will contain a refernce to
  the SBOMit document, but this also may be modified in this case.  However,
  the client should have the in-toto layout key and so will notice this 
  action if retrieving the SBOMit document from the the SIT.
* an attacker is able to compromise the in-toto layout key, which serves as
  the root-of-trust for the system.  **Impact:** Traceability.  Later analysis
  can show this was the cause, but users will trust a new, maliciously 
  generated layout which replaces signing keys.

From (Endor Lab's top 10 OSS risks)[https://www.endorlabs.com/blog/introducing-the-top-10-open-source-software-oss-risks], 
our design largely addresses:

* OSS-RISK-1
* OSS-RISK-2
* OSS-RISK-5
* OSS-RISK-9


Orthogonal systems that can be used along with SBOMit:

* A software supply chain tool which attests to the quality of steps, such 
as FRSCA and SLSA.  This helps with knowing that an individual action is 
actually a good security practice, could judge the quality of implementation 
for different tools, determine if the code base metrics are indicative of 
quality, understand if the licence is appropriate, etc.  This helps to address
OSS-RISK-4, OSS-RISK-8, and OSS-RISK-10.  OSS-RISK-7 may also be addressed
either by this or by end user diligence.

* Some means like Sigstore's root of trust is needed to Handle how users know 
the correct name / root of trust for the software they are installing.  This 
relates to OSS-RISK-3.  

* Recursing into components like the packages inside of a container image when 
the build process does not otherwise do so.  This relates to OSS-RISK-6.  As 
both tooling and in-toto adoption increase, this issue should naturally be 
addressed as more metadata becomes available.

There is also (a more detailed analysis which shows why these properties 
hold)[TODO]

## Format/required fields

### SBOMit Document

#### Overview

There are four items in a SBOMit document:

* An in-toto layout.  This specifies the policy that the supply chain followed.
  This includes information about the key used to sign each SIT and 
  corresponding the SIT generation process for each.  It must be signed by the
  layout key trusted for the SBOMit document.

* A collection of in-toto metadata.  This includes sub-layouts, link metadata,
  and attestations.  This must validate according to the in-toto layout and
  so must be signed by the appropriate functionaries and sub-layout creators.

* The SBOM mutation.  This metadata describes how to mutate the SBOM
  information derived from the in-toto metadata.  It essentially serves to 
  flesh out the derived SBOM to make it more complete that what can be 
  strictly verified.  This is in ?ISO 1234 JSON patch? format.

* Zero or more addendums.  These are represented as diffs to the prior sections
  of the document.  It enables one to easily append or modify information while
  retaining historical information.  These are also in JSON patch format.

#### Detailed format

TODO

### SIT

#### Overview

A SIT is a valid SBOM (of any type), with the following two constraints:

* it has a field indicating the SBOMit document that it was derived from.  

* it is signed by the functionary key listed in the related step of the 
  in-toto layout

#### Detailed format

TODO...



## Workflows

There are two main workflows for SBOMit documents and SITs: generation and 
verification.

### Generation

#### SBOMit document
To generate a SBOMit document, one must generate the consituent parts.  For the
in-toto layouts, sub-layouts, attestations, and links, this process is 
described in the in-toto project's documentation.  Note however, that for the 
purposes of SBOMit, the layout is required to include certain information 
so that this information may be used to populate the SIT.  

Generating the mutator by hand may done by starting with a null mutator and 
then using tooling to generate a SIT in the correct format.  The SIT file may 
be modified and the JSON diff may be computed.

Similarly, if a SBOMit document is modified, one may elect to add an addendum
to modify it instead of generating a fresh document.  This is easily done by
generating the new SBOMit document and then creating a JSON diff.

#### SIT 

The SIT is generated by first deriving common information from in-toto and
placing it in an intermediate format called X.  This format is effectively
a SBOM itself which matches the NTIA format....

Then the tool specified in the layout may be used to derive the SIT in the
appropriate end format.  Note also, that multiple SITs may be generated from 
the same SBOMit document.  So, for example, one SBOMit document could generate
both a SPDX SIT and a CycloneDX SIT.

An important part of generating the SIT is applying the mutator to the output
of the tool used to derive the SIT.  This adds or modifies information in the 
resulting SIT as instructed.  This works as though applying a JSON patch to 
the SIT document.

It is recommended that the SIT generation tool provides a warning if the
mutator modifies (e.g., replaces or removes) portions of the SIT derived from
the in-toto information.  This is becuase this will be flagged as a security
risk when verification of the SBOMit document is performed.

### Verification

#### SBOMit document

SBOMit document verification proceeds by the following steps:

* verifying the in-toto layout over the provided in-toto metadata.  
* checking each SIT to ensure they are signed by the correct SIT generator
* generate each SIT using the relevant tooling to examine the mutator 
  behavior.  Any parts of the SIT that the mutator has chosen to overwrite or 
  remove represent a substantial security risk.  These are specifically 
  flagged during validation.  The user must specify a flag like 
  --insecure-allow-in-toto-override or respond to a similarly scary alert to 
  install the software.

If this matches the generated SITs, then the SBOMit document verification
has succeeded.

#### SIT 

A SIT may be verified in two ways.  

First, the SBOMit document may be downloaded and then verified.  This provides 
strong security guarantees, but is a fairly heavyweight operation.

Second, the SIT may have its signature checked, much like any signed SBOM.
This provides a much weaker set of security guarantees.  However, assuming the
SIT generator performs verification correctly and is uncompromised, this does
provide a meaningful level of security.

