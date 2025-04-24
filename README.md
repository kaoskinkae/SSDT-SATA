# SSDT-SATA

Renaming Devices Using SSDT

Whenever possible, using SSDT to rename devices is preferred over binary renaming, as it allows for a macOS-wide approach, which is otherwise impossible since OpenCore applies binary renaming system-wide (unlike Clover). In this section, we discuss how to achieve this and when to use each method.

Patching Principle

The SSDT for renaming a device must meet the following conditions to work:

Scope a device (DeviceObj) in the DSDT at the specific location(s) (PCI path) defined in the "External" section of the SSDT.

If the loaded kernel is "Darwin" (the macOS kernel), do the following:

Disable device X (set the _STA method = Zero) and enable device Y (set the _STA method = 0x0F).


NOTE: For each scope expression you use, there must be a corresponding external reference. See the examples.

Renaming the SATA controller from (SAT1 or SAT0) to SATA

Renaming (SAT1 or SAT0) to SATA is not mandatory (it's purely cosmetic).


![Captura de pantalla 2025-04-24 a las 9 36 05](https://github.com/user-attachments/assets/c0680600-786f-4232-923b-bc326d7df99e)




Testing and Verification

Make sure you have a working backup of your EFI folder on a flash drive, always commenting out any changes you make.

Export the SSDT as an .aml file (SSDT-SATA.aml)

Add it to /EFI/OC/ACPI and to config.plist.

Save and reboot.

Check that it appears changed in Hackintool under the PCIe section and in IORegistriExplore. If you are not present in our DSDT, you can verify this by opening MaciASL and searching for “SAT”.

![Captura de pantalla 2025-04-24 a las 11 07 17](https://github.com/user-attachments/assets/03ed820c-5395-45dd-b67e-6de3c7d687ad)


![Captura de pantalla 2025-04-24 a las 9 31 13](https://github.com/user-attachments/assets/fdbf3a17-c51b-483b-b2da-0896178aa1f0)

![Captura de pantalla 2025-04-24 a las 11 05 34](https://github.com/user-attachments/assets/0eba83c5-5d7b-4c40-86c8-1c166f40a054)


Credits and Resources
Dortania for Rename-SSDT

