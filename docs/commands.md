# Historical investigation commands

The submitted investigation created a `pro` alias for the Volatility 2.6 executable, `-f KobayashiMaru.vmem`, and `--profile=WinXPSP2x86`. These examples reflect the documented workflow and require that alias; they are not a runnable tool included in this repository.

```bash
pro imageinfo
ls -lh KobayashiMaru.vmem
pro pstree
pro pstree | grep -i poison
pro pslist
pro dlllist -p 480
pro cmdline
pro filescan | grep -i poisonivy
pro connscan | grep -w 480
pro sockscan | grep -w 480
```

Use full unfiltered results to validate context. A PID filter can accidentally match an address or another numeric column. PID 480 applies only to this memory snapshot; the separate live FTP investigation recorded PID 1504.
