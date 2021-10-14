# nsx-ipam

A sample ipam plugin which leverages NSX for allocating IP to a pod using the CNI specification. Each request for a pod IP will result in API requests towards NSX-T manager to allocate IP from IP pools referenced via NetworkAttachmentDefinition.

The desired features are
1. Use API to allocate/release IP for a POD when the plugin is called. Can be leveraged for either primary or secondary interfaces
2. Support for use in NetworkAttachmentDefinition when multus is sued to allocate IP for secondary interfaces. In this way the IP allocation for secondary interfaces of pods can be controlled centrally using NSX-T manager.
3. Have a audit which will help in clearing stuck IP's due to pod deleted and API being unresponsive for any number of reasons.
4. Start with IPv4 support. 
5. 
