<div align="center">

# verity-org

### Zero-CVE container images, always.

Automatically patched · Signed · Attested · Ready to pull.

<br>

[![Wolfi](https://img.shields.io/badge/built_on-Wolfi-4A90D9?style=flat-square)](https://wolfi.dev)
[![Signed](https://img.shields.io/badge/signed-cosign_keyless-2ECC71?style=flat-square)](https://docs.sigstore.dev)
[![SBOM](https://img.shields.io/badge/SBOM-attested-8E44AD?style=flat-square)](https://docs.github.com/en/actions/security-guides/using-artifact-attestations)
[![Zero CVE](https://img.shields.io/badge/policy-zero--CVE-E74C3C?style=flat-square)](https://github.com/verity-org)

</div>

---

## What we do

Most container images ship with known vulnerabilities — not because maintainers don't care, but because patching is slow and manual. We automate that entirely.

Every image in this registry is:

- **Scanned daily** against the latest CVE databases with Trivy
- **Patched automatically** using Copa (verity) or rebuilt from Wolfi packages (integer)
- **Signed** with keyless cosign via GitHub OIDC — no long-lived keys
- **Attested** with a full SBOM so you know exactly what's inside

If a CVE drops today, the patched image is published today.

---

## Projects

<table>
<tr>
<td width="50%" valign="top">

### [verity](https://github.com/verity-org/verity)

Tracks upstream images (nginx, postgres, redis, …) and continuously patches them with [Copa](https://github.com/project-copacetic/copacetic). Drop-in replacements — same base, zero known CVEs.

**How it works**
1. Discover all tracked images and tags
2. Scan each with Trivy
3. Patch OS packages in-place with Copa
4. Re-scan to verify zero CVEs
5. Sign, attest, publish

</td>
<td width="50%" valign="top">

### [integer](https://github.com/verity-org/integer)

Builds minimal images from scratch using [Wolfi](https://wolfi.dev) — a security-first Linux distribution designed for containers. No CVE inheritance from upstream base images.

**How it works**
1. Compose images from curated Wolfi packages
2. Build multi-arch with apko
3. Gate on zero CVEs with Trivy
4. Sign, attest, publish

</td>
</tr>
</table>

---

## Guarantees

| Property | How |
|----------|-----|
| **Zero known CVEs** | Hard Trivy gate — build fails if any CVE is found |
| **Tamper-evident** | Every image signed by digest with keyless cosign |
| **Auditable** | SBOM attached as a GitHub attestation, verifiable offline |
| **Always fresh** | Daily rebuild + patch cycle, not just on release |
| **Multi-arch** | `linux/amd64` and `linux/arm64` on every image |

---

## Verify an image

```sh
# Verify the cosign signature
cosign verify ghcr.io/verity-org/<image>:<tag> \
  --certificate-oidc-issuer https://token.actions.githubusercontent.com \
  --certificate-identity-regexp 'https://github.com/verity-org/'

# Verify the SBOM attestation
gh attestation verify oci://ghcr.io/verity-org/<image>:<tag> \
  --owner verity-org
```

---

<div align="center">

All images are free to use. Published to `ghcr.io/verity-org`.

</div>
