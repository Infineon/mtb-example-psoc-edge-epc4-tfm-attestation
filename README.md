# PSOC&trade; Edge MCU: TF-M based Initial Attestation application - EPC4

This is a TF-M based code example for demonstrating the Initial Attestation on PSOC&trade; Edge MCU. The example demonstrates how to initialize the TF-M NS interface in CM33 NS and CM55 NSPE projects and use the Initial Attestation service offered by TF-M to generate a attestation token. The example also shows how to verify the attestation token with IAK public key. The code example is designed to work on an Edge Protect Category 4 (EPC4) MCU. For more information about Edge Protect Category, see [here](https://www.infineon.com/promo/edge-protect).

This code example has a 3-project structure: CM33 Secure, CM33 Non-Secure, and CM55 projects. Extended Boot launches the Edge Protect bootloader, which then SRAM-loads and launches TF-M SPE (CM33 Secure project). TF-M then launches the CM33 NSPE application, which further launches the M55 application.

- **proj_cm33_s:** TF-M is available as source code in the *mtb_shared* directory. The proj_cm33_s project is completely built out of this TF-M library. Only Makefile and dependencies are present in this project directory that makes use of the TF-M library. TF-M application is programmed in the external flash and SRAM-loaded for execution.

- **proj_cm33_ns:** This is the NSPE project which contains the TF-M interface and calls the Platform Security Architecture (PSA) APIs to use TF-M services. The CM33 NS application is executed from the external flash and does not use RTOS.

- **proj_cm55:** This is the M55 NSPE project. This project also has a TF-M interface but does not call PSA APIs. You need to only include the required PSA interface header files for the project to be ready to use PSA APIs. The CM55 project is executed from the external flash and does not use RTOS.

[View this README on GitHub.](https://github.com/Infineon/mtb-example-psoc-edge-epc4-tfm-attestation)

[Provide feedback on this code example.](https://cypress.co1.qualtrics.com/jfe/form/SV_1NTns53sK2yiljn?Q_EED=eyJVbmlxdWUgRG9jIElkIjoiQ0UyNDIwOTMiLCJTcGVjIE51bWJlciI6IjAwMi00MjA5MyIsIkRvYyBUaXRsZSI6IlBTT0MmdHJhZGU7IEVkZ2UgTUNVOiBURi1NIGJhc2VkIEluaXRpYWwgQXR0ZXN0YXRpb24gYXBwbGljYXRpb24gLSBFUEM0IiwicmlkIjoidGVqYXMga2FkZ2FvbmthciIsIkRvYyB2ZXJzaW9uIjoiMi4wLjAiLCJEb2MgTGFuZ3VhZ2UiOiJFbmdsaXNoIiwiRG9jIERpdmlzaW9uIjoiTUNEIiwiRG9jIEJVIjoiSUNXIiwiRG9jIEZhbWlseSI6IlBTT0MifQ==)

See the [Design and implementation](docs/design_and_implementation.md) for the functional description of this code example.


## Requirements

- [ModusToolbox&trade;](https://www.infineon.com/modustoolbox) v3.6 or later (tested with v3.6)
- Board support package (BSP) minimum required version for:
   - KIT_PSE84_EVAL_EPC4: v1.0.0
- Programming language: C
- Associated parts: All [PSOC&trade; Edge EPC4 MCU](https://www.infineon.com/products/microcontroller/32-bit-psoc-arm-cortex/32-bit-psoc-edge-arm) parts


## Supported toolchains (make variable 'TOOLCHAIN')

- GNU Arm&reg; Embedded Compiler v14.2.1 (`GCC_ARM`) – Default value of `TOOLCHAIN`
- Arm&reg; Compiler v6.22 (`ARM`)
- IAR C/C++ Compiler v9.50.2 (`IAR`)


## Supported kits (make variable 'TARGET')

- [PSOC&trade; Edge E84 Evaluation Kit](https://www.infineon.com/KIT_PSE84_EVAL) (`KIT_PSE84_EVAL_EPC4`) – Default value of `TARGET`

## Hardware setup

This example DOES NOT use the board's default configuration.

Ensure the following jumper and pin configurations on the board:

- BOOT SW must be in the LOW/OFF position
- J20 and J21 must be in the tristate/not connected (NC) position


## Software setup

See the [ModusToolbox&trade; tools package installation guide](https://www.infineon.com/ModusToolboxInstallguide) for information about installing and configuring the tools package.

Install a terminal emulator if you do not have one. Instructions in this document use [Tera Term](https://teratermproject.github.io/index-en.html).


### TF-M tools

This tool requires Python to be installed and added to the `PATH` environment variable. TF-M tools offers an *iat-verifier* tool which will be used in this code example to verify the attestation token.

1. Clone the [TF-M tools](https://github.com/TrustedFirmware-M/tf-m-tools/tree/main) repository, preferably in \<Workspace>.  

2. Open *tf-m-tools\iat-verifier\iatverifier\psa_2_0_0_token_verifier.py* and update the *MeasurementValueClaim* claim as optional as follows:

   ```
   (MeasurementValueClaim, {'verifier': self, 'necessity': Claim.OPTIONAL}),
   ```

   > **Note:** The EPC4 device does not include the SHA-256 hash value of the ROM firmware in the attestation token because it is the Root of Trust. This results in token verification failure with the IAT Verifier tool. The IAT Verifier tool expects all *MeasurementValueClaim* to be part of the token. To avoid the token verification failure, this workaround can be used. 

3. Open modus-shell and navigate to the *tf-m-tools\iat-verifier* directory and execute the following command to install the dependencies required for this tool:

   ```
   pip3 install .
   ```

For more details, see the [iat-verifier documentation](https://github.com/TrustedFirmware-M/tf-m-tools/blob/main/docs/iat-verifier.rst).


## Operation

### Add the Edge Protect bootloader

Add *proj_bootloader* to this code example as a first step. Follow the steps #2 to #9 in the **Operation** section of the [Edge Protect bootloader](https://github.com/Infineon/mtb-example-edge-protect-bootloader) code example's *README.md* file.
  
   > **Note:** [Edge Protect bootloader](https://github.com/Infineon/mtb-example-edge-protect-bootloader) *README.md* describes how to add the bootloader to **Basic Secure App**. Add it to **this** code example instead.


### Program the application

See [Using the code example](docs/using_the_code_example.md) for instructions on creating a project, opening it in various supported IDEs, and performing tasks such as building, programming, and debugging the application within the respective IDEs.

> **Note:** Windows has a 260-character path length limit; therefore, for the TF-M application to build successfully, ensure that <WorkspacePath>\<Application Name> (including the backslash) path is less than or equal to 32 characters.

1. Connect the board to your PC using the provided USB cable through the KitProg3 USB connector

2. Open a terminal program and select the KitProg3 COM port. Set the serial port parameters to 8N1 and 115200 baud

3. Program the board with the TF-M attestation application. After programming, the application starts automatically. Confirm that "PSOC Edge MCU: Trusted Firmware-M (TF-M) based Initial Attestation Application" is displayed on the UART terminal

   **Figure 2. Terminal output on program startup**

   ![](images/terminal_attestation_logs.png)

4. Open modus-shell/terminal and navigate to the <WorkspacePath>\<Application Name> directory to execute the `extract_iat_iak_pkey.sh` script. 

   > **Note:** Before running the script, ensure that the *edgeprotecttools* executable is added in the `PATH` environment variable. To use the script, update the `OPENOCD_PATH` variable in the `extract_iat_iak_pkey.sh` script to point to the `OPENOCD_PATH` path. The default installation path is *C:/Infineon/Tools/ModusToolboxProgtools-\<version\>/openocd*. After updating the `OPENOCD_PATH`, execute the following command to extract the attestation token and IAK public key

   ```
   sh scripts/extract_iat_iak_pkey.sh
   ```

5. Use the IAT verifier tool to verify the token signature and structure. 

   1. Copy the generated *IAK_PUB_KEY.pem* and *Attestation_token.cbor* files to the *<WorkspacePath>\tf-m-tools\iat-verifier* directory
   
   2. Open modus-shell/terminal and go to <WorkspacePath>\tf-m-tools\iat-verifier directory
   
   3. Execute the following command to verify the attestation token. A successful token verification proves the device identity and genuineness
      ```
      python scripts/check_iat -t PSA-2.0.0-token -k IAK_PUB_KEY.pem Attestation_token.cbor
      ```
   You should see the following output which indicates the attestation token verification was successful.
   ```
   Signature OK
   Token format OK
   ```

## Related resources

Resources  | Links
-----------|----------------------------------
Application notes  | [AN235935](https://www.infineon.com/AN235935) – Getting started with PSOC&trade; Edge E8 MCU on ModusToolbox&trade; software
Application notes | **AN240096** - Getting started with Trusted Firmware-M (TF-M) on PSOC&trade; Edge
Code examples  | [Using ModusToolbox&trade;](https://github.com/Infineon/Code-Examples-for-ModusToolbox-Software) on GitHub
Device documentation | [PSOC&trade; Edge MCU datasheets](https://www.infineon.com/products/microcontroller/32-bit-psoc-arm-cortex/32-bit-psoc-edge-arm#documents) <br> [PSOC&trade; Edge MCU reference manuals](https://www.infineon.com/products/microcontroller/32-bit-psoc-arm-cortex/32-bit-psoc-edge-arm#documents)
Development kits | Select your kits from the [Evaluation board finder](https://www.infineon.com/cms/en/design-support/finder-selection-tools/product-finder/evaluation-board)
Libraries  | [mtb-dsl-pse8xxgp](https://github.com/Infineon/mtb-dsl-pse8xxgp) – Device support library for PSE8XXGP <br> [retarget-io](https://github.com/Infineon/retarget-io) – Utility library to retarget STDIO messages to a UART port
Tools  | [ModusToolbox&trade;](https://www.infineon.com/modustoolbox) – ModusToolbox&trade; software is a collection of easy-to-use libraries and tools enabling rapid development with Infineon MCUs for applications ranging from wireless and cloud-connected systems, edge AI/ML, embedded sense and control, to wired USB connectivity using PSOC&trade; Industrial/IoT MCUs, AIROC&trade; Wi-Fi and Bluetooth&reg; connectivity devices, XMC&trade; Industrial MCUs, and EZ-USB&trade;/EZ-PD&trade; wired connectivity controllers. ModusToolbox&trade; incorporates a comprehensive set of BSPs, HAL, libraries, configuration tools, and provides support for industry-standard IDEs to fast-track your embedded application development

<br>


## Other resources

Infineon provides a wealth of data at [www.infineon.com](https://www.infineon.com) to help you select the right device, and quickly and effectively integrate it into your design.


## Document history

Document title: *CE242093* - *PSOC&trade; Edge MCU: TF-M based Initial Attestation Application - EPC4*

 Version | Description of change
 ------- | ---------------------
 1.x.0   | New code example <br> Early access release
 2.0.0   | GitHub release
<br>

All referenced product or service names and trademarks are the property of their respective owners.

The Bluetooth&reg; word mark and logos are registered trademarks owned by Bluetooth SIG, Inc., and any use of such marks by Infineon is under license.

PSOC&trade;, formerly known as PSoC&trade;, is a trademark of Infineon Technologies. Any references to PSoC&trade; in this document or others shall be deemed to refer to PSOC&trade;.

---------------------------------------------------------

© Cypress Semiconductor Corporation, 2023-2025. This document is the property of Cypress Semiconductor Corporation, an Infineon Technologies company, and its affiliates ("Cypress").  This document, including any software or firmware included or referenced in this document ("Software"), is owned by Cypress under the intellectual property laws and treaties of the United States and other countries worldwide.  Cypress reserves all rights under such laws and treaties and does not, except as specifically stated in this paragraph, grant any license under its patents, copyrights, trademarks, or other intellectual property rights.  If the Software is not accompanied by a license agreement and you do not otherwise have a written agreement with Cypress governing the use of the Software, then Cypress hereby grants you a personal, non-exclusive, nontransferable license (without the right to sublicense) (1) under its copyright rights in the Software (a) for Software provided in source code form, to modify and reproduce the Software solely for use with Cypress hardware products, only internally within your organization, and (b) to distribute the Software in binary code form externally to end users (either directly or indirectly through resellers and distributors), solely for use on Cypress hardware product units, and (2) under those claims of Cypress's patents that are infringed by the Software (as provided by Cypress, unmodified) to make, use, distribute, and import the Software solely for use with Cypress hardware products.  Any other use, reproduction, modification, translation, or compilation of the Software is prohibited.
<br>
TO THE EXTENT PERMITTED BY APPLICABLE LAW, CYPRESS MAKES NO WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, WITH REGARD TO THIS DOCUMENT OR ANY SOFTWARE OR ACCOMPANYING HARDWARE, INCLUDING, BUT NOT LIMITED TO, THE IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE.  No computing device can be absolutely secure.  Therefore, despite security measures implemented in Cypress hardware or software products, Cypress shall have no liability arising out of any security breach, such as unauthorized access to or use of a Cypress product. CYPRESS DOES NOT REPRESENT, WARRANT, OR GUARANTEE THAT CYPRESS PRODUCTS, OR SYSTEMS CREATED USING CYPRESS PRODUCTS, WILL BE FREE FROM CORRUPTION, ATTACK, VIRUSES, INTERFERENCE, HACKING, DATA LOSS OR THEFT, OR OTHER SECURITY INTRUSION (collectively, "Security Breach").  Cypress disclaims any liability relating to any Security Breach, and you shall and hereby do release Cypress from any claim, damage, or other liability arising from any Security Breach.  In addition, the products described in these materials may contain design defects or errors known as errata which may cause the product to deviate from published specifications. To the extent permitted by applicable law, Cypress reserves the right to make changes to this document without further notice. Cypress does not assume any liability arising out of the application or use of any product or circuit described in this document. Any information provided in this document, including any sample design information or programming code, is provided only for reference purposes.  It is the responsibility of the user of this document to properly design, program, and test the functionality and safety of any application made of this information and any resulting product.  "High-Risk Device" means any device or system whose failure could cause personal injury, death, or property damage.  Examples of High-Risk Devices are weapons, nuclear installations, surgical implants, and other medical devices.  "Critical Component" means any component of a High-Risk Device whose failure to perform can be reasonably expected to cause, directly or indirectly, the failure of the High-Risk Device, or to affect its safety or effectiveness.  Cypress is not liable, in whole or in part, and you shall and hereby do release Cypress from any claim, damage, or other liability arising from any use of a Cypress product as a Critical Component in a High-Risk Device. You shall indemnify and hold Cypress, including its affiliates, and its directors, officers, employees, agents, distributors, and assigns harmless from and against all claims, costs, damages, and expenses, arising out of any claim, including claims for product liability, personal injury or death, or property damage arising from any use of a Cypress product as a Critical Component in a High-Risk Device. Cypress products are not intended or authorized for use as a Critical Component in any High-Risk Device except to the limited extent that (i) Cypress's published data sheet for the product explicitly states Cypress has qualified the product for use in a specific High-Risk Device, or (ii) Cypress has given you advance written authorization to use the product as a Critical Component in the specific High-Risk Device and you have signed a separate indemnification agreement.
<br>
Cypress, the Cypress logo, and combinations thereof, ModusToolbox, PSoC, CAPSENSE, EZ-USB, F-RAM, and TRAVEO are trademarks or registered trademarks of Cypress or a subsidiary of Cypress in the United States or in other countries. For a more complete list of Cypress trademarks, visit www.infineon.com. Other names and brands may be claimed as property of their respective owners.

