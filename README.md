# BCDR Lab03 sandbox definition

Cyber Range sandbox for BCDR Lab03, where students design and build a two-node
Corosync/Pacemaker cluster with a quorum device. It is a cut-down CR08 topology
(`jstanesic/cr08` at `2c78d61`). Lab03 needs only three of CR08's seven hosts, and
25 full CR08 sandboxes do not fit the OpenStack capacity.

| Host | Image | Flavor | Access net | Cluster net | Role |
|---|---|---|---|---|---|
| lin1 | ubuntu-noble-x86_64 | c2_r4_d20 | 10.10.10.6 | 10.10.20.6 | cluster node |
| lin2 | ubuntu-noble-x86_64 | c2_r4_d20 | 10.10.10.7 | 10.10.20.7 | cluster node |
| lin3 | ubuntu-noble-x86_64 | c2_r4_d20 | 10.10.10.9 | | qnetd quorum device, test client |

The router (10.10.10.1, 10.10.20.1) gives the nodes internet access. Per sandbox that is
6 vCPU, 12 GB RAM and 60 GB disk for the hosts, against 22 vCPU, 32 GB and 310 GB for CR08.

Names, addresses and network layout match CR08 because the lab text and the grading key
use them. The networks are defined the same way as in CR08, where the floating
addresses 10.10.10.70 and 10.10.10.71 (Pacemaker `IPaddr2`) were reachable from lin3.

## Provisioning

- `localuser` / `Password123!` with passwordless sudo on all three hosts.
- ufw disabled, so corosync (UDP 5405), pcsd (TCP 2224) and qnetd (TCP 5403) pass.
- `apt-get update` with retries, which fails the build if a sandbox has no internet.
- iptables installed.
- On lin1 and lin2, `/etc/hosts` maps `lin1` and `lin2` to their cluster addresses, the
  loopback entry for the node's own name is removed, and cloud-init no longer manages the
  file.

The cluster packages are not pre-installed. Students install `pacemaker corosync pcs
corosync-qdevice` plus `apache2` or `nginx` on lin1/lin2, and `pcs corosync-qnetd` on lin3.
