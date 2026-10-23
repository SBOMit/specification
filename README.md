# SBOMit

SBOMit is a supply-chain security framework that generates accurate SBOMs directly from in-toto attestations, ensuring that component inventories reflect the real dependencies used during the build process. SBOMit leverages the [witness](https://github.com/in-toto/witness) tool to capture build-time filesystem, process, and execution events, and converts these authenticated provenance records into a complete, verifiable SBOM.

In addition to filesystem and process tracing, SBOMit includes network tracking to observe outbound connections and capture dynamically downloaded build-time dependencies. This ensures that transient or runtime-fetched components are not missed by traditional SBOM tools.

SBOMit outputs a cryptographically verifiable SBOM enriched with:

1. **Build-Time Dependency Discovery**
Extracted from witness attestations, including files read/written, processes executed, and dynamically generated artifacts.

2. **Network-Based Dependency Capture**
Mapping network requests to dependency sources (e.g., Cargo crates, pip packages, OS packages) to detect ephemeral dependencies not captured statically.

3. **Provenance-Backed Integrity**
Every SBOM element is derived from authenticated in-toto attestations, enabling downstream verification, reproducibility analysis, and policy enforcement.

For the detailed specification, please refer to:  
📄 **[Specification](specification.md)**

The SBOMit specification is licensed under the  
📜 **[Creative Commons Attribution 4.0 International Public License](LICENSE.md)**

---

# Meetings

- **Schedule:** Every Friday at 9:00 AM US Eastern Time  
  📍 [Zoom Meeting Link](https://zoom-lfx.platform.linuxfoundation.org/meeting/94671938246?password=035ca9e9-5194-491c-863b-69b21a596c67)

- **Notes:**  
  📝 [Meeting Notes](https://docs.google.com/document/d/1-nHXMqvWNzgOxAq08O8Wu2BTHz0U60yBoAklrJAMaRc/edit?usp=sharing)

## Code of Conduct

- Let others talk (don’t interrupt)
- Be polite when you disagree
- Be respectful of others’ time  
  - Avoid rambling  
  - Limit excessive agreement/piggybacking
- Topics that begin to dominate will be deferred to a future meeting with a dedicated discussion slot

