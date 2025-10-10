[Click here](../README.md) to view the README.

## Design and implementation

This code example demonstrates how to use Trusted Firmware-M (TF-M) with PSOC&trade; Edge MCU to generate an Initial Attestation Token. The attestation token is used to prove the genuineness of the device. 

Trusted Firmware-M (TF-M) implements the Secure Processing Environment (SPE) for Armv8-M, Armv8.1-M architectures (e.g., the Cortex&reg;-M33, Cortex&reg;-M23, Cortex&reg;-M55, Cortex&reg;-M85 processors) and dual-core platforms. It is the Platform Security Architecture (PSA) reference implementation aligning with PSA-certified guidelines, enabling chips, real-time operating systems, and devices to become PSA certified. For more details, see the [Trusted Firmware-M Documentation](https://tf-m-user-guide.trustedfirmware.org/).

The extended boot launches the Edge Protect bootloader from RRAM. The bootloader authenticates the CM33 secure, CM33 non-secure, and CM55 projects which are placed in the external flash, and then SRAM-loads and launches the CM33 secure application. The CM33 project contains TF-M. The Edge Protect bootloader passes the boot measurements to TF-M for initial attestation service.

TF-M creates an isolated space between the M33 secure and M33 non-secure images. TF-M offers the Initial Attestation service which can be used by the non-secure application. The service is placed in an isolated TF-M partition. TF-M is available in source code format as a library in the *mtb_shared* directory. The CM33 secure application does not have any source files, but instead includes this TF-M library for building TF-M.

During the boot sequence, TF-M's Secure Partition Manager (SPM) forms the SPE by using protection units and initializes all the TF-M partitions. After initializing the partitions, TF-M launches the M33 NSPE project from external flash which enables M55 and initializes  M33 NSPE <-> M55 NSPE interface using Secure Request Framework (SRF). The M33 NSPE project requests the TF-M service for an attestation token. The attestation service needs a challenge as an input for token generation. Typically, this is a random number of size 32/48/64 bytes. Here, in the code example, the M33 NSPE uses the TF-M Crypto service to generate a random number of size 32 bytes. The generated random number is passed as an input to the Initial Attestation service. The service returns the attestation token. The attestation token is stored in the shared memory in SRAM1. In this example, M55 is not used so it is put in Deep Sleep.

The code example is shipped with the `extract_iat_iak_pkey.sh` shell script, available in the *<WorkspacePath>\<Application Name>\scripts* directory. The script performs the following tasks:

- Extracts the attestation token from the SRAM1 shared memory and stores it in a CBOR file
- Extracts the alias certificate from the device
- Extracts the IAK public key from the alias certificate and stores it in a PEM file

All the extracted assets are stored in the *\<WorkspacePath\>\\\<Application Name\>* directory. To verify the genuineness of the device, the attestation token is shared to a verification entity such as a cloud service. The entity uses the IAK public key to verify the signature and claims of attestation token. Here, the *iat-verifier* tool from TF-M tools is used, which is a Python-based tool, to verify the signature and structure of the token locally. The extracted IAK public key and attestation token should be passed to the `check_iat` script to verify the token.  

> **Note:** The `check_iat` script does not verify the individual claims; it only verifies the token structure and signature. Typically, all the received list of claims are verified against a database of known measurements for each component by the verification service.

For more information about the claims, see **AN240096** - Getting started with Trusted Firmware-M (TF-M) on PSOC™ Edge and [IHI0085-PSA_Certified_Attestation_API](https://arm-software.github.io/psa-api/attestation/).

You can check the contents of your token by using the `decompile_token` script of the *iat-verifier* tool. Use modus-shell and execute the following command from the *<WorkspacePath>\tf-m-tools\iat-verifier* directory:

```
python scripts/decompile_token -t PSA-2.0.0-token Attestation_token.cbor
```

**Table 1. Application projects**

Project | Description
--------|------------------------
proj_cm33_s | TF-M (SPE)
proj_cm33_ns | M33 NSPE
proj_cm55 | M55 NSPE 

<br>