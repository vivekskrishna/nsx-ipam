# nsx-ipam

A sample ipam plugin which leverages NSX for allocating IP to a pod using the CNI specification. Each request for a pod IP will result in API requests towards NSX-T manager to allocate IP from IP pools referenced via NetworkAttachmentDefinition.

```
Example use of nsx ipam plugin
$ cat <<EOF | kubectl create -f -
apiVersion: "k8s.cni.cncf.io/v1"
kind: NetworkAttachmentDefinition
metadata:
  name: macvlan-conf-1
spec:
  config: '{
            "cniVersion": "0.3.0",
            "type": "macvlan",
            "master": "eth1",
            "mode": "bridge",
            "ipam": {
                "type": "nsx-ipam",
                "ranges": [
                    [ {
                         "pool": "nsx-pool-name" <--- will be single one, will support an array in future
                    } ]
                ]
            }
        }'
EOF
```

The desired features are
1. Use API to allocate/release IP for a POD when the plugin is called. Can be leveraged for either primary or secondary interfaces
2. Support for use in NetworkAttachmentDefinition when multus is sued to allocate IP for secondary interfaces. In this way the IP allocation for secondary interfaces of pods can be controlled centrally using NSX-T manager.
3. Have a audit which will help in clearing stuck IP's due to pod deleted and API being unresponsive for any number of reasons.
4. Have an optional way where the plugin can be used by antrea to allocate ip blocks to worker nodes based on config in NSX(needs to be a separate project)
5. Start with IPv4 support. 
6. Can be used for both macvlan and ipvlan interfaces. Obviate use of DHCP when using ipvlan etc as it requires support for fields like Option 61(client identifier) which some DHCP servers may not support.
7. Provide support for IP based on pod annotation(based on need), so that IP can be preserved for secondary interface even if pod moves to a different worker node for any reason.
8. have the ability to populate a default gateway/ static routes etc using data populated in NSX-T manager for the referenced pool
9. reuse the tag-scope under nsx ip pool for route dest cidr and next hop so that routes can be programmed.(might need feature addition in future)

To do in later phase
1. support for ipv6
2. multiple ip pool so that ip can be allocated from any one of them
3. support for integration with antrea so that IP can be allocated for pods from nsx.
