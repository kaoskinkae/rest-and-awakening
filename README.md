## Sleep on a Haswell with Imapro 1.1 and Radeon RX560

Sleep on this computer with the specifications listed above wouldn't go to sleep. I did some research and asked ChapGPT. It gave me two commands I needed to see when it goes to sleep and after sleep, using the following two commands:

Check active devices upon waking:

# pmset -g log | grep "Wake"

And to see if it went to sleep and woke up correctly:

# pmset -g assertions

After sending it to ChapGPT, the first option was that a device like a mouse and keyboard present in the system tried to make the system go to sleep but 
would wake up immediately.

The first option was to create an SSDT-Mouse-Keyboard.aml to block the waking of these two devices, but to no avail.

The second option was to do some research, and that's when two sections were created: SSDT-XHCSPRW.aml and a patch that changed the name of the device: "Rename _PRW to XPRW for XHC."

This is when ChapGPT didn't know or understand the issue well and assumed that it was only necessary to use the Part. But once a video was published on the Hackintosh Spain channel, a colleague's response completely clarified the issue.

"In ACPI architecture, the DSDT is always loaded first because it is the main table that defines the hardware configuration and its methods. SSDTs are secondary tables that can modify or extend the functionality of the DSDT but never directly replace it. For this reason, when we want to modify the behavior of methods already defined in the DSDT, such as the _PRW (Power Resource for Wake) method, we cannot simply define a new method in an SSDT without first performing a "rename" in the ⁠config.plist file. This process consists of renaming _PRW to XPRW, which It prevents the system from recognizing the original method and allows us to define a new _PRW with ⁠Return (Zero)⁠ in an SSDT. The _PRW method is a power management object, and when defined with ⁠Return (Zero)⁠, we indicate that the device does not have the ability to wake the system from sleep. However, if we only create an SSDT with the XPRW method, it will have no effect because XPRW is not an ACPI-recognized method. For the system to accept the modification, the SSDT must contain a new _PRW method with ⁠Return (Zero). This approach is essential for correcting "instant wake" issues in macOS, where certain devices such as USB controllers or network cards can generate unwanted wake-up events.


![Captura de pantalla 2025-05-18 a las 11 08 28](https://github.com/user-attachments/assets/6d37f0a3-b3fd-4e5a-b988-c0d596911523)

![Captura de pantalla 2025-05-18 a las 11 13 06](https://github.com/user-attachments/assets/3cb94abd-8f71-40ce-9ac3-da81d94de4f2)


Therefore, if we are having sleep and wake-up issues, this may be the solution. I'm leaving a screenshot of the SSDT and patch, and a zip file of both the compiled and uncompiled SSDTs.

I hope this helps.
