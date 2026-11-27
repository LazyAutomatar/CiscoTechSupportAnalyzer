# Reporter-inspired capture checks

These checks adapt relevant Network Health Reporter switching alerts to a single
offline show-tech capture. Each includes exact command/line evidence and read-only
verification suggestions. They supplement the existing correlation dashboards.

| Explicit observation | Finding | Interpretation |
| --- | --- | --- |
| `err-disabled` in interface status | Warning | Port disabled at capture; cause still needs verification |
| Connected port with `half` or `a-half` duplex | Warning | Check peer settings/errors; legacy half duplex can be intentional |
| Stack Ring Configuration reports Half | Warning | Reduced ring redundancy; no invented failed cable/member or bandwidth |
| PSU row reports Failed/Faulty/Bad | Warning | Inspect the supply/feed; this does not prove a chassis outage |
| PSU row reports Not Present | Information | Confirm intended redundancy; an empty slot is not a failed supply |
| Global PortFast BPDU Guard default disabled | Information | Review edge-port policy and interface overrides; role is unknown |
| VTP operating mode Server | Information | Review approved VLAN change role; Server is not inherently wrong |
| Explicit `no ip routing` and static routes in the same running configuration | Information | Verify platform/VRF behavior and intent before changing anything |
| Fully parsed uptime below seven days | Information | Establish counter age and incident window; do not infer a crash |

Unknown or unsupported commands do not produce these findings. Disconnected half
duplex, a default gateway alone, missing PSU measurements, cumulative discards,
or missing uptime do not trigger the corresponding alerts. Existing abnormal
reload findings take precedence over the generic recent-uptime notice.

Reporter rules that require live topology, polling deltas, intended device roles,
wireless telemetry or measurements absent from the capture are not inferred.
For example, historical queue drops alone do not prove an AP/CAPWAP outage.
