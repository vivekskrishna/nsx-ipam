# nsx-ipam

A sample ipam plugin which leverages NSX for allocating IP to a pod using the CNI specification. Each request for a pod IP will result in API requests towards NSX-T manager to allocate IP from IP pools referenced via NetworkAttachmentDefinition.

```
# Execute following command at Kubernetes master
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
                "type": "host-local",
                "ranges": [
                    [ {
                         "subnet": "10.10.0.0/16",
                         "rangeStart": "10.10.1.20",
                         "rangeEnd": "10.10.3.50",
                         "gateway": "10.10.0.254"
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
4. Start with IPv4 support. 
5. Can be used for both macvlan and ipvlan interfaces. Obviate use of DHCP when using ipvlan etc as it requires support for fields like Option 61(client identifier) which some DHCP servers may not support.
6. Provide support for IP based on pod annotation(based on need), so that IP can be preserved for secondary interface even if pod moves to a different worker node for any reason.
