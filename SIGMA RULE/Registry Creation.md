## Registry Creation / Modification
title: Suspicious Meterpreter Registry Activity
id: 1a2b3c4d-5678-90ab-cdef-1234567890ab
status: experimental
description: Detects creation or modification of the MeterpreterTest registry key
logsource:
    product: windows
    service: security
detection:
    selection_eventid:
        EventID: 4657
    selection_object:
        ObjectName: "HKCU\\Software\\MeterpreterTest"
    condition: selection_eventid OR selection_object
level: high
