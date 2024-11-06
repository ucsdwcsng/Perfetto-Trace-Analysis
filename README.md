# Perfetto_trace_analyzer
Repo for perfetto scripts and analysis to do power profiling on smartphones

Steps:

0. First push the config to phone (Skip if done already): ```adb push config.pbtx "/"```

1. Command to generate and pull trace all at once, fname is the trace name, and the trace is stored in PErfetto_traces/ folder which is igored via gitignore
```$fname = '100mbps_mimo_0.perfetto-trace'; adb shell "cat /data/local/tmp/config.pbtx | perfetto --txt -c - -o /data/misc/perfetto-traces/$fname"; adb pull "/data/misc/perfetto-traces/$fname" "./Perfetto_traces/."```
