
# Renaming Devices via SSDT

Whenever possible, using SSDTs for renaming Devices is preferred over using binary renames because you can limit it to macOS which is impossible otherwise since OpenCore applies binary renames system-wide (unlike Clover). In this section we take a look at how this can be achieved and when to use which approach.

## Patching Principle

The SSDT to rename a device must conform to the following conditions in order to work:

Look for ("Scope") a Device (DeviceObj) in the DSDT at specific location(s) (PCI path) defined in the "External" Section of the SSDT
If the loaded Kernel is "Darwin" (= the macOS Kernel) do the following:
Disable Device X (set Method _STA = Zero) and
Enable Device Y (set Method _STA = 0x0F)

*NOTE: For each Scope expression you are using there has to be a corresponding External reference.*

Rename SATA Controller from SAT1/SAT0 to SATA
Renaming SAT1 to SATA is not a requirement (it's purely cosmetic), but it's an easy to understand example (read the comments indicated by // for explanations):

![Captura de pantalla 2025-04-24 a las 14 06 35](https://github.com/user-attachments/assets/e91316a9-967f-4c3b-8623-b6868089142d)

   
Testing and verifying
Make sure you have a backup of your working EFI folder on a FAT32 formatted flash drive
Export the SSDT as .aml file
Add it to /EFI/OC/ACPI and config.plist
Save and reboot
Run IORegistry Exlorer
Search for SAT
The output should look like this:
SATA

![Captura de pantalla 2025-04-24 a las 14 07 10](https://github.com/user-attachments/assets/ca9e61a0-4e01-4065-85e8-31ddb635ad1a)

![Captura de pantalla 2025-04-24 a las 14 07 34](https://github.com/user-attachments/assets/8fdf3bdc-6a2e-4b87-9452-3c0ad14712f6)





