
Renaming Devices via SSDT
Whenever possible, using SSDTs for renaming Devices is preferred over using binary renames because you can limit it to macOS which is impossible otherwise since OpenCore applies binary renames system-wide (unlike Clover). In this section we take a look at how this can be achieved and when to use which approach.

Patching Principle
The SSDT to rename a device must conform to the following conditions in order to work:

Look for ("Scope") a Device (DeviceObj) in the DSDT at specific location(s) (PCI path) defined in the "External" Section of the SSDT
If the loaded Kernel is "Darwin" (= the macOS Kernel) do the following:
Disable Device X (set Method _STA = Zero) and
Enable Device Y (set Method _STA = 0x0F)
NOTE: For each Scope expression you are using there has to be a corresponding External reference. See examples.

Example 1: Rename SATA Controller from SAT1 to SATA
Renaming SAT1 to SATA is not a requirement (it's purely cosmetic), but it's an easy to understand example (read the comments indicated by // for explanations):

DefinitionBlock ("", "SSDT", 2, "5T33Z0", "SATA", 0x00001000)
{
    External (_SB_.PCI0, DeviceObj)         // Adjust ACPI Paths according to your DSDT
    External (_SB_.PCI0.SAT1, DeviceObj)    // Adjust Device name as needed
    
    If (_OSI ("Darwin"))                    // If the macOS Kernel is running…
    {
        Scope (\_SB.PCI0)                   // …look here for…
        {
            Scope (SAT1)                    // …Device SAT1 and…
            {
                Method (_STA, 0, NotSerialized) // 
                {
                    Return (Zero)          // … set its Status to 0 (disable it)
                }
            }

            Device (SATA)                  // Add Device SATA…
            {   
                Name (_ADR, 0x001F0002)    // …with Address (get it from DSDT)
                Method (_STA, 0, NotSerialized)
                {
                    Return (0x0F)          // …and enable it
                }
            }
        }
    }
}
Testing and verifying
Make sure you have a backup of your working EFI folder on a FAT32 formatted flash drive
Export the SSDT as .aml file
Add it to /EFI/OC/ACPI and config.plist
Save and reboot
Run IORegistry Exlorer
Search for SAT
The output should look like this:
SATA


If you don't add the Name (_ADR,0x…) portion to the code, the controller will still work, but you won't find it in the IO Registry:
SAD
In general, if the device name you want to change is used in other ACPI tables besides the DSDT, you have to change it there as well, so the device name is the same for all ACPI tables. If that's the case, using a binary rename which applies system-wide so all occurrences are found and renamed is preferable.
