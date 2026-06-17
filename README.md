# Infineon SLB9670 Firmware Updates Drop FIPS Certification (CVE-2025-2884 Mitigation)

🔍 **DATA SOURCE DISCLAIMER:** 
The findings and version states documented in this page are derived exclusively from the structural parsing of publicly distributed vendor firmware update payloads. This repository is not affiliated with, endorsed by, or based on official proprietary documentation from Infineon Technologies.


# Infineon OPTIGA™ SLB9670 Firmware Certification Matrix

This matrix maps out the hard technical transition boundary where Infineon firmware branches cross from FIPS/Common Criteria validated states into pure security/operational maintenance releases. 

ℹ️ **TPM SPECIFICATION:** 
This matrix and its associated structural data apply **strictly to TPM 2.0 firmware branches**. This documentation does not cover legacy TPM 1.2 versions.

Use this data to audit your enterprise fleet before applying vendor TPM updates (such as Lenovo PS500721 / CVE-2025-2884), as crossing into the non-certified versions will permanently strip hardware FIPS 140-2 compliance.

⚠️ **CRITICAL ARCHITECTURAL NOTE ON PERMANENCY:** 
Once a device is upgraded to a non-CC certified firmware version (such as 7.66+ or 7.86+), **the change is strictly irreversible**. Due to hardware-enforced anti-rollback protection and the blowing of permanent internal electronic fuses, the TPM chip will block any attempt to downgrade back to a FIPS-certified baseline. Proceeding with these security updates permanently alters the cryptographic capability of the hardware platform.

## SLB9670 (TPM 2.0 Spec Version 1.16)

| Firmware Version | Common Criteria Certified Status | Notes |
| :--- | :---: | :--- |
| **7.40.2098.0** | :white_check_mark: YES | |
| **7.41.2375.0** | :white_check_mark: YES | |
| **7.60.2677.0** | :white_check_mark: YES | |
| **7.61.2785.0** | :white_check_mark: YES | |
| **7.61.2789.0** | :white_check_mark: YES | |
| **7.62.3126.0** | :white_check_mark: YES | CVE-2017-15361 Remediation |
| **7.63.3144.0** | :white_check_mark: YES | |
| **7.63.3353.0** | :white_check_mark: YES | **Final CC certified for Spec 1.16** |
| **7.66.19374.2** | :no_entry_sign: NO | CVE-2025-2884 Remediation |
| **7.67.19690.2** | :no_entry_sign: NO | |

## SLB9670 (TPM 2.0 Spec Version 1.38)

| Firmware Version | Common Criteria Certified Status | Notes |
| :--- | :---: | :--- |
| **7.83.3252.0** | :white_check_mark: YES | Identifies still as spec 1.16, but is on new branch |
| **7.83.3358.0** | :white_check_mark: YES | Identifies still as spec 1.16 |
| **7.85.4555.0** | :white_check_mark: YES | **Final publicly available CC and [FIPS certified](https://csrc.nist.gov/CSRC/media/projects/cryptographic-module-validation-program/documents/security-policies/140sp3492.pdf) for Spec 1.38** |
| **7.85.4567.0** | :white_check_mark: YES | No vendor has released |
| **7.86.19393.2** | :no_entry_sign: NO | CVE-2025-2884 Remediation |
| **7.87.19691.2** | :no_entry_sign: NO | |

## Cross-Specification Upgrade & Patch Rationale

### Cross-Specification Migrations (TPM Spec 1.16 ➔ 1.38)
It is highly recommended to upgrade the physical SLB9670 chip out of the older **TPM Specification 1.16 branch** and into the newer **TPM Specification 1.38 branch** using authorized vendor cross-flashing binaries (e.g., executing an upgrade path from a `7.63.x` version directly to a `7.85.x` version). This allows organizations to update the underlying protocol specification to support newer operating system cryptographic requirements and even gain [FIPS certification](https://csrc.nist.gov/CSRC/media/projects/cryptographic-module-validation-program/documents/security-policies/140sp3492.pdf) with version `7.85`.

> [!NOTE]
> **Specification Revisions vs. TPM Families**
> * **Revisions (e.g., v1.16 to v1.38):** Fully backward compatible. Any BIOS currently supporting a v1.16 chip will work perfectly with the updated v1.38 specification.
> * **Families (e.g., TPM 1.2 to TPM 2.0):** Incompatible architectures. Upgrading a system from TPM 1.2 to 2.0 requires extensive BIOS adaptation and cannot be done without expecting major integration roadblocks. This is out of the context for this page as we are only listing TPM 2.0 family firmware versions for SLB9670.

> [!WARNING]
> Upgrading from 1.16 branch to 1.38 branch will factory reset the SLB9670. You will need to reconfigure all security enrollments.

### Why Infineon Maintained Dual Patch Streams for CVE-2025-2884
Because of the permanent nature of these firmware upgrades, you will notice that Infineon actively released parallel, independent mitigations for **CVE-2025-2884** across *both* branches:
* **Spec 1.16 branch was patched up to 7.66+ / 7.67+**
* **Spec 1.38 branch was patched up to 7.86+ / 7.87+**

Infineon chose to release separate non-CC certified patch forks for both specifications—ensuring all systems could mitigate the security flaw without forcing the reconfiguration of security enrollments.

## Vendor Disclosure Discrepancies & Ecosystem Impact

### ⚠️ Lenovo's Stance (Silent Compliance Drops)
In their official security advisory regarding the discrete TPM library vulnerabilities, **[Lenovo Product Security Advisory PS500721](https://support.lenovo.com/us/en/product_security/ps500721)** addresses the critical mitigation steps required to fix **CVE-2025-2884**. 

However, Lenovo's documentation focuses entirely on the threat remediation and vulnerability patching schedules. **They do not disclose or mention anywhere in the advisory that migrating to these upcoming versions will drop the hardware's active FIPS 140-2 certification.**

### 🔍 Industry Comparison: Dell's Explicit Warning
By contrast, other top-tier hardware vendors approach the compliance transition with explicit transparency. In **[Dell Security Advisory DSA-2025-232](https://www.dell.com/support/kbdoc/en-us/000331010/dsa-2025-232-security-update-for-dell-poweredge-server-for-a-trusted-platform-module-tpm-2-0-firmware-vulnerability)** (which tracks similar cryptographic updates for Nuvoton-based components), Dell explicitly protects their customers by publishing clear architectural notices:

* Dell openly states within their remediation guide that pushing uncertified operational updates **will cause systems to lose active FIPS certification status**.
* They highlight that modern TPM security policies **prevent downgrading** back to previous certified lines once the patch commits.
* Dell explicitly details the existence of distinct, custom branches (such as `7.2.4.1`) specifically compiled to maintain FIPS and Common Criteria certifications while fixing the underlying flaws.
### Operational Recommendations
If your environment mandates active hardware cryptographic validation, do not blindly apply automated BIOS capsule updates from vendors who omit compliance metadata from their security bulletins. Ensure you isolate systems carrying the **Infineon OPTIGA™ SLB9670** chip and halt automated endpoint updates until formal exception logs can be verified by your auditing teams.
