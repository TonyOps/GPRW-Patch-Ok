# GPRW-Patch-Ok

<p>This fix is intended to correct the GPRW to XPRW patch because it contains a certain error in which it presents invalid characters at the end, thus making the purpose of the patch inaccessible. In the original, at the end of each code (47505257 and 58505257, respectively, there is a number 02). Without these characters now, it becomes possible to make the correction correctly.<p>

# GPRW/UPRW/LANC Instant Wake Patch

Similar idea to the "Fixing Shutdown/Restart" section, macOS will instant wake if either USB or power states change while sleeping. To fix this we need to reroute the GPRW/UPRW/LANC calls to a new SSDT, verify you have instant wake issues before trying the below.

Best way to check:

```sh
pmset -g log | grep -e "Sleep.*due to" -e "Wake.*due to"
```

And generally you'll get results like these:

* `Wake [CDNVA] due to GLAN: Using AC`
  * Generally caused by WakeOnLAN enabled, try to disable this option first in the BIOS
  * If WOL wasn't the issue, you can try the below patches
* `Wake [CDNVA] due to HDEF: Using AC`
  * Similar to the GLAN issue
* `Wake [CDNVA] due to XHC: Using AC`
  * Generally caused by WakeOnUSB enabled, try to disable this option first in the BIOS
  * GPRW patch is likely needed
* `DarkWake from Normal Sleep [CDNPB] : due to RTC/Maintenance Using AC`
  * Generally caused by PowerNap
* `Wake reason: RTC (Alarm)`
  * Generally caused by an app waking the system, quitting said app before you sleep should fix it

**Do not use all these patches at once**, look through your DSDT and see what you have:

| SSDT | ACPI Patch | Comments |
| :--- | :--- | :--- |
| [SSDT-GPRW](https://github.com/dortania/OpenCore-Post-Install/blob/master/extra-files/SSDT-GPRW.aml) | [GPRW to XPRW Patch](https://github.com/dortania/OpenCore-Post-Install/blob/master/extra-files/GPRW-Patch.plist) | Use this if you have `Method (GPRW, 2` in your ACPI |
| [SSDT-UPRW](https://github.com/dortania/OpenCore-Post-Install/blob/master/extra-files/SSDT-UPRW.aml) | [UPRW to XPRW Patch](https://github.com/dortania/OpenCore-Post-Install/blob/master/extra-files/UPRW-Patch.plist) | Use this if you have `Method (UPRW, 2` in your ACPI |
| [SSDT-LANC](https://github.com/dortania/OpenCore-Post-Install/blob/master/extra-files/SSDT-LANC.aml) | [LANC to XPRW Patch](https://github.com/dortania/OpenCore-Post-Install/blob/master/extra-files/LANC-Patch.plist) | Use this if you have  `Device (LANC)` in your ACPI |

ACPI Patches and SSDTs courtesy of [Rehabman](https://www.tonymacx86.com/threads/guide-using-clover-to-hotpatch-acpi.200137/), [1Revenger1](https://github.com/1Revenger1) and [Fewtarius](https://github.com/dortania/laptop-guide)
