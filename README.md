# MSTP, PVSTP, Rapid-PVSTP

## Spanning Tree Protocol Overview

STP provides loop resolution by managing the physical paths to given network segments. STP allows physical path redundancy while preventing the undesirable effects of active loops in the network. STP forces certain ports into a blocking state. These blocking ports do not forward data frames.

STP performing 3 step

1. Elects one root bridge: Only one bridge can act as the root bridge. The root bridge is the reference point, and all data flows in the network are from the perspective of this switch. All ports on a root bridge are forwarding traffic.
2. Selects the root port on the nonroot bridge: One port on each nonroot bridge is the root port. It is the port with the lowest-cost path from the nonroot bridge to the root bridge. By default, the STP path cost is calculated from the bandwidth of the link. You can also set the STP path cost manually.
3. Selects the designated port on each segment. There is one designated port on each segment. It is selected on the bridge with the lowest-cost path to the root bridge.

| Port Rule | Description |
| ------------- | ------------- |
| Root Port  | This port exists on nonroot bridges and is the switch port with the best path to the root bridge. Root ports forward traffic toward the root bridge. The source MAC address of the frames that are received on the root port that is capable of populating the MAC table. Only one root port is allowed per bridge. |
| Designated Port  | This port exists on root and nonroot bridges. For root bridges, all switch ports are designated ports. For nonroot bridges, a designated port is the switch port that will receive and forward frames toward the root bridge as needed. Only one designated port is allowed per segment. If multiple switches exist on the same segment, an election process determines the designated switch, and the corresponding switch port begins forwarding frames for the segment. Designated ports are capable of populating the MAC table.  |
| NonDesignated Port  | The nondesignated port is a switch port that is not forwarding (blocking) data frames and is not populating the MAC address table with the source addresses of frames that are seen on that segment.  |
| Disabled Port  | The disabled port is a switch port that is shut down.  |

## Spanning Tree Protocol Types and Features

| Protocol  | Standard | Convergence | Number of Trees |
| ------------- | ------------- | ------------| --------------- |
| STP  | 802.1D  | Slow  | One  |
| PVST+  | Cisco  | Slow  | One for every VLAN  |
| RSTP  | 802.1w  | Fast  | One  |
| Rapid PVST+  | Cisco  | Fast  | One for every VLAN  |
| MSTP  | 802.1s Cisco  | Fast  | One for mnultiple VLAN  |

## MSTP

### MSTP Guideline
Follow these restrictions and guidelines to avoid configuration problems:
1. Do not disable spanning tree on any VLAN in any of the PVST bridges.
2. Do no use PVST bridges as the root of CST.
3. Do not connect switches with access links, because access links may partition a VLAN.
4. Ensure that all PVST root bridges have lower (numerically higher) priority than the CST root bridge.
5. Ensure that trunks carry all of the VLANs mapped to an instance or do not carry any VLANs at all
for this instance.
6. Complete any MST configuration that incorporates a large number of either existing or new logical
VLAN ports during a maintenance window because the complete MST database gets reinitialized
for any incremental change (such as adding new VLANs to instances or moving VLANs across
instances)

### Enabling MSTP

Configure MSTP
```
SWICTH1# Configure Terminal
SWITCH1(config)# spanning-tree mode mst
SWITCH1(config)# spanning-tree mst configuration
SWICTH1(config-mst)# name cisco
SWICTH1(config-mst)# revision 1
SWICTH1(config-mst)# instance 1 vlan 1
SWICTH1(config-mst)# instance 2 vlan 10-100
SWICTH1(config-mst)# show pending
SWICTH1(config-mst)# end
SWICTH1# show spanning-tree mst configuration
```
Configure MSTP Instance Parameters
```
SWICTH1(config)# spanning-tree mst 1 priority 49152
```
```
SWICTH1(config)# spanning-tree mst 1 root primary
SWICTH1(config)# spanning-tree mst 2 root secondary
```
Configure MST Instance Port Parameters
```
SWICTH1(config)# interface fastethernet 0/0
SWICTH1(config-if)# spanning-tree mst 1 cost 1234567
SWICTH1(config-if)# spanning-tree mst 1 port-priority 240
```
```
SWITCH1(config)# interface Ethernet 0/2
SWITCH1(config-if)# spanning-tree vlan 1 cost 16
```
references https://www.cisco.com/c/en/us/td/docs/switches/lan/catalyst4500/12-2/25ew/configuration/guide/conf/mst.pdf

## PVSTP
note : normally PVST+ is default STP for cisco switch and already configured from the start
### Enabling PVST+
Configure PVST+
```
SWICTH1# Configure Terminal
SWITCH1(config)# spanning-tree mode pvst
SWITCH1(config)# spanning-tree vlan 1,10
```
Configure PVST+ Instance Prameters
```
SWITCH1(config)# spanning-tree vlan 1 priority 23456
SWITCH1(config)# spanning-tree vlan 1 root primary
SWITCH1(config)# spanning-tree vlan 10 root secondary
```
Configure per Port Prameters
```
SWICTH1(config)# interface fastethernet 0/0
SWICTH1(config-if)# spanning-tree cost 1234567
SWICTH1(config-if)# spanning-tree port-priority 240
```
## Rapid-PVSTP
Configure Papid-PVSTP
```
SWICTH1# Configure Terminal
SWITCH1(config)# spanning-tree mode rapid-pvst
```
Configure Rapid-PVSTP Instance Prameters
```
SWITCH1(config)# spanning-tree vlan 1 priority 23456
SWITCH1(config)# spanning-tree vlan 1 root primary
SWITCH1(config)# spanning-tree vlan 10 root secondary
```
Configure per Port Prameters
```
SWICTH1(config)# interface fastethernet 0/0
SWICTH1(config-if)# spanning-tree cost 1234567
SWICTH1(config-if)# spanning-tree port-priority 240
```
```
SWITCH1(config)# interface Ethernet 0/2
SWITCH1(config-if)# spanning-tree vlan 1 cost 16
```

## Other Additional Commands
```
SWICTH1(config)# spanning-tree hello-time <seconds>
SWICTH1(config)# spanning-tree forward-time <seconds>
SWICTH1(config)# spanning-tree max-age <seconds>
```
in MSTP is it quite different
```
SWICTH1(config)# spanning-tree mst hello-time <seconds>
SWICTH1(config)# spanning-tree mst forward-time <seconds>
SWICTH1(config)# spanning-tree mst max-age <seconds>
```
```
SWICTH1(config)# interface ethernet 0/1
SWICTH1(config-if)# spanning-tree link-type point-to-point
```
PortFast and BPDU Guard and loppguard
```
SWICTH1(config)# interface FastEthernet0/1
SWITCH1(config-if)# spanning-tree portfast
SWITCH1(config-if)# spanning-tree bpduguard enable
```
```
SWITCH1(config)# spanning-tree portfast bpduguard default
SWITCH1(config)# spanning-tree portfast default
````
```
Switch(config)# spanning-tree loopguard [default | interface interface-id]
```
To enable Loop Guard globally for all ports, use the "spanning-tree loopguard default" command.
To enable Loop Guard on a specific interface, use the "spanning-tree loopguard interface interface-id" command.

## BEST PRACTICE


1. Use Rapid Spanning Tree Protocol (RSTP): RSTP is an improvement over the original STP and provides faster convergence times. It is recommended to use RSTP (802.1w) or its Cisco proprietary equivalent, Per-VLAN Rapid Spanning Tree (PVRST), to minimize the impact of topology changes and ensure quicker network recovery.
2. Enable PortFast: PortFast allows access ports to transition immediately to the forwarding state, bypassing the listening and learning states. It is used on ports connected to end devices to speed up their connectivity and avoid unnecessary STP delays. However, do not enable PortFast on ports that connect to other switches or network devices.
3. Implement BPDU Guard: BPDU Guard protects against accidental or unauthorized connections of switches or bridges to PortFast-enabled ports. When a BPDU (Bridge Protocol Data Unit) is received on a PortFast-enabled port, the port is immediately put into the errdisable state to prevent the creation of STP loops.
4. Use EtherChannel: Utilize EtherChannel or Link Aggregation to bundle multiple physical links between switches into a single logical link. This provides higher bandwidth and redundancy while preventing STP from blocking redundant links. Use protocols like Port Aggregation Protocol (PAgP) or Link Aggregation Control Protocol (LACP) to negotiate the formation of the EtherChannel.
5. Configure Root Bridge Placement: Designate the root bridge placement strategically to optimize the STP topology. Place the root bridge at the center of the network or in the core layer to minimize the diameter of the STP tree and ensure efficient traffic flow.
6. Implement Loop Guard: Loop Guard helps prevent the formation of unidirectional link failures and protects against the creation of temporary bridging loops. Enable Loop Guard on ports that should be designated as designated ports, but stop receiving BPDUs.
7. Monitor and Troubleshoot: Regularly monitor STP operation, convergence times, and logs to identify and resolve any potential issues. Utilize commands like show spanning-tree, debug spanning-tree, and network monitoring tools to gain visibility into STP operation and performance.

