# SSDT-SATA

Renaming Devices Using SSDT

Whenever possible, using SSDT to rename devices is preferred over binary renaming, as it allows for a macOS-wide approach, which is otherwise impossible since OpenCore applies binary renaming system-wide (unlike Clover). In this section, we discuss how to achieve this and when to use each method.

Patching Principle

The SSDT for renaming a device must meet the following conditions to work:

Scope a device (DeviceObj) in the DSDT at the specific location(s) (PCI path) defined in the "External" section of the SSDT.

If the loaded kernel is "Darwin" (the macOS kernel), do the following:

Disable device X (set the _STA method = Zero) and enable device Y (set the _STA method = 0x0F).

NOTE: For each scope expression you use, there must be a corresponding external reference. See the examples.

Renaming the SATA controller from (SAT1 or SATA0) to SATA

Renaming (SAT1 or SATA0) to SATA is not mandatory (it's purely cosmetic).


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

Device (SATA) // Add SATA device…
{
Name (_ADR, 0x001F0002) // …with address (obtained from DSDT)
Method (_STA, 0, Not Serialized)
{
Return (0x0F) // …and activate it
}
}
}
}
}


Testing and Verification

Make sure you have a working backup of your EFI folder on a flash drive, always commenting out any changes you make.

Export the SSDT as an .aml file (SSDT-SATA.aml)

Add it to /EFI/OC/ACPI and to config.plist.

Save and reboot.

Check that it appears changed in Hackintool under the PCIe section and in IORegistriExplore. If you are not present in our DSDT, you can verify this by opening MaciASL and searching for “SAT”.
![Captura de pantalla 2025-04-24 a las 9 31 13](https://github.com/user-attachments/assets/fdbf3a17-c51b-483b-b2da-0896178aa1f0)

Credits and Resources
Dortania for Rename-SSDT

