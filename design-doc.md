# SBOMit Design Document

## What is SBOMit?

SBOMit is the name of the project which manages the SBOMit specification format.   A SBOMit document is effectively an SBOM, only with additional verification information added that was generated at the time the supply chain was generated.  This verification information, which uses in-toto attestations and layouts, is able to be validated by a party to get a high degree of assurances about the software.

## What does a SBOMit document contain and why?

A SBOMit document is a signed document that consists of a few different components.  

First and foremost, it contains a series of in-toto attestations that were generated as the described software was created.  This includes detailed information about different steps of the software supply chain, including about the version control system, build process, unit testing, dependencies used, fuzzing, license compliance checks, packaging, etc.  For example, the build system used to compile the software in the SBOM has in-toto metadata with the names and secure hashes for the files that were taken from the VCS to be compiled, the names and secure hashes of the files created during compilation, a series of information about the compiler, and a signature by a private key held by the compiler.
	
This attestation information can be used in coordination with the second item in the SBOMit document: an in-toto layout.  The in-toto layout is signed by the project owner and describes what valid attestation metadata for the project looks like. The layout specifies the private keys for the parties performing the attestations as well as how the steps interconnect.  For example, the VCS must have a signed git tag, which the build system then operates over.  The files compiled by the build system must be the files that were run through the unit tests, which must have passed.   Importantly, an in-toto layout provides a machine-readable policy that can validate the in-toto attestations to ensure that all of those steps were followed, in the right order, on the right items, and with nothing added, skipped, or removed.

The final item that always appears in the SBOMit document is the supplemental SBOM information.  This information along with the in-toto attestations and in-toto layout can be used to derive an actual SBOM in a variety of formats.  The supplemental SBOM information may include things such as the company name or other information which isn’t included by in-toto but which is desirable to be included in the resulting SBOM.  In this way, an SBOM generated from an SBOMit document, can include supplemental information that was not part of the in-toto process.

There is also a means to add addendums to an SBOMit document, which will be described later.

## What are the advantages of a SBOMit document over using a SBOM generation tool that scans the software?

Scanning tools are by their nature looking at a piece of software and then trying to determine what happened before.  By their nature, they are imperfect because they use incomplete information to try to recover what happened in the past.  Practical use has shown that different scanning tools give wildly different results for the same software.  

The in-toto attestation portions of an SBOMit document are generated at the time the software is being processed through the software supply chain.  As a result, this information will be much more accurate because in-toto attestations collect detailed information at the time the software product is being made.  This makes an SBOMit document much more accurate, by its nature.

## What are the advantages of a SBOMit document over simply signing an SBOM?

A signed SBOM is a signed statement by someone holding the signing key at an organization that says that the SBOM is accurate.  If the key is stolen, or the party signing the SBOM is incorrect about how the software was made, then the SBOM will be inaccurate.

An SBOMit document contains cryptographically signed metadata about all of the steps that went into making the software and describes the policy that must be followed.  It is much harder for accidental inaccuracies like skipping a step (which has historically been a problem) or for malicious actions to be undetected.  In-toto also provides greater ability to securely recover from a compromise and to detect and resist malicious actions by a party within an organization.

To use an analogy, an SBOM today is much like a list of ingredients on a product.  Only in practice, this information is commonly inaccurate, could be changed by a malicious party, and is not verified.   Signing an SBOM helps to stop it from being changed by a malicious party, so you know that the ingredient list you are getting was approved by a specific company.  A SBOMit document also describes in detail the process that went into making the product and has metadata and signatures from all the parties involved, including verification that the keys used were current at that time.  So, in the case of an SBOMit document, you have a high degree of assurance that the proper policies and procedures were followed.

For more information on this topic, please see the [What advantages does in-toto provide] FAQ on the in-toto website.

## What if a software supply chain contains insecure steps?  

in-toto attestations are not a substitute for having appropriately secure steps in the software supply chain.  For example, if you use an insecure process of building software that just curls and builds software from a website, in-toto layouts can list this insecure action and verify signed attestations indicating that you did this insecure action.  

This is why projects like SLSA and FRSCA are built as an opinionated set of rules on top of in-toto steps.  They specify which actions are more important for software supply chain security and mandate certain actions.  

These projects are solving different problems at different levels.  In-toto allows you to capture information about your steps, ensure policies about them are applied, handle trust of keys, etc.  Frameworks like SLSA and FRSCA use in-toto as a mechanism to capture and enforce a specific set of policies that result in more secure supply chains.  

There are efforts to make automated tooling for SLSA and FRSCA to work over in-toto layouts and validate compliance.  When these tools mature, they can be integrated to parse an SBOMit document and provide the appropriate scoring level for the software supply chain described therein.   Thus a user may then configure their system to only accept SLSA level 4 or better software, which also has a valid SBOMit document.

## What if an SBOMit document needs to be changed over time?  E.g., asserting it does not have a certain exploitable vulnerability, adding supplemental information later that wasn’t needed at creation time, etc.

An SBOMit document may have a series of post-facto addendums.  These are added after the fact and must be ultimately signed by the original SBOMit document’s master key.  These serve to modify the various fields in the SBOMit document or add supplemental information.  

Using addendums versus creating a new SBOMit document has an advantage.  In this way both the original document and any addendums will be verifiable and the history of these changes are all contained in the SBOMit document.  Thus addendums are the recommended model for handling changes to an SBOMit document.


## Can I derive an SBOM from an SBOMit document?

Yes!  There are various tools in the works that will derive an SBOM from an SBOMit document.  You can get SBOMs in a variety of different formats by using different tools.



