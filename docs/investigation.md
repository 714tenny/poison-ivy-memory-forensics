# Poison Ivy Memory Forensics Investigation — Investigation Notes

## Objective and evidence

Investigate a supplied volatile-memory image for malicious activity. The original report identifies `KobayashiMaru.vmem`, approximately 512 MB, examined in a SimSpace Kali investigation VM. Analysis was performed on a copied evidence folder.

## Method

1. Run `imageinfo` and inspect suggested profiles. `WinXPSP2x86` and `WinXPSP3x86` were suggested; the investigation used `WinXPSP2x86`.
2. Review `pstree` and isolate the suspect process.
3. Confirm its parent relationship with `pslist`.
4. Correlate `dlllist`, `cmdline`, and `filescan` outputs.
5. Examine `connscan` and `sockscan` for legacy Windows network artifacts.
6. Review strings for user-environment and credential exposure. Do not publish recovered secrets.

## Findings and evidence strength

| Observation | Recorded result | What it supports |
| --- | --- | --- |
| OS/profile | Windows XP x86; `WinXPSP2x86` selected | Interpretation of the image's structures |
| Image size | Approximately 512 MB | Captured image size, not proof of maximum installed RAM |
| Process | `poisonivy.exe`, PID 480 | Suspicious running process in this snapshot |
| Parent | `Explorer.EXE`, PID 404 | Interactive-user process ancestry; initial infection vector unproven |
| Command line | `C:\WINDOWS\System32\poisonivy.exe` | Executable path associated with PID 480 |
| File objects | `\Device\HarddiskVolume1\WINDOWS\system32\poisonivy.exe` | Consistent file-object references |
| DLLs | `ws2_32.dll`, `mswsock.dll`, `wsock32.dll`, among others | Networking capability; these DLLs are also used legitimately |
| Scanned TCP artifact | PID 480; remote `192.168.5.98:3460` | Connection structure associated with the suspect process |
| User context | `Daniel Faraday` in profile paths and environment strings | Correlated course-provided user identity |
| Credential artifact | Plaintext credential string recovered | Exposure in RAM; does not prove that malware stole it |

### Correction to the original narrative

The original report's prose listed the local endpoint as `0.0.0.0:1074`. The attached `connscan` screenshot shows **`0.0.0.0:1037`**, PID 480, remote `192.168.5.98:3460`. This repository follows the screenshot and records the discrepancy explicitly.

`connscan` searches memory for connection structures and can recover stale artifacts. The wildcard local address is preserved as displayed; it is not treated as proof of a usable live connection or an independently confirmed C2 session. The filtered `sockscan` screenshot shows no matching output for PID 480.

## Assessment

The process name, System32 path, ancestry, networking libraries, file objects, and TCP artifact together support a high-priority suspected backdoor investigation. They are stronger than a process-name match alone. This publication summarizes the submitted analysis; the source memory image was not rerun while preparing it.

## Proposed incident response

Isolate the affected endpoint, preserve volatile and disk evidence, investigate the executable and persistence locations, review communication with the recorded remote endpoint, and reset exposed credentials from a trusted device. Restore from a trusted build after determining scope. These recommendations were not executed or validated in the report.

## Limitations

No executable hash, disk image, phishing message, malware-family binary verification, or complete network capture is included. Initial access, confirmed exfiltration, and attribution to other malware remain unproven. The published screenshots omit the plaintext-password recovery page.
