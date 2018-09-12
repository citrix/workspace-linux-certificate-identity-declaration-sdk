# Certificate Identity Declaration SDK for Citrix Workspace app 1809 for Linux

## Compatibility with Workspace app

This is Version 1809 of the Certificate Identity Declaration SDK. It is intended for use with Workspace app for Linux builds in the 1809 series. There are separate versions of the SDK for each of the released architectures (X86, X86_64, and ARMhf) that differ only in the included binaries. 

## Who should use this SDK? 

The purpose of this SDK is to provide developers with a means of declaring the user's smart card identity to a StoreFront server without performing a full smart card based authentication. 

CID (Certificate Identity Declaration) is motivated by trying to get a speed up when using older smart cards which were noticeably slow. The CID protocol bypasses the crypto step the smart card does for logging on to a StoreFront server (the ICA launch still uses smart card though). 

With CID the client just sends to StoreFront the public client certificate to identify the user instead of doing a full SSL handshake. This approach is significantly faster than a proper client certificate logon, especially when slow smart cards are involved. 

The developers who want to create their own plugin must conform to the **"CitrixAuthManagerCustomProtocolSDK.h"** header file available in the **"include"** directory. Refer to the file and to the sample code for more information. 

To use this feature, the CID extension has to be installed and the protocol enabled in the StoreFront site. Please refer to the documentation of the StoreFront Authentication SDK.

## The sample plugin 

To build the sample plugin, the cURL library must be installed on the system, most Linux distribution make it available through their native package managers. 

The sample plugin will build into two modules both named "libCID.so", coming in both debug and retail version, the former containing the debugging symbols. 

The sample plugin can be used with Citrix Workspace app for Linux by adding the following entries to the Auth Manager's configuration file (located by default at /opt/Citrix/ICAClient/config/AuthManConfig.xml): 

```xml
<key>ProtocolOrder</key> 
<value>certificateidentitydeclaration</value>  

<Protocols>     
	<certificateidentitydeclaration>         
		<Enabled>True</Enabled>         
		<KeepLibraryLoaded>True</KeepLibraryLoaded>         
		<Path>/path/to/plugin/libCID.so</Path>         
		<PluginType>CustomProtocolDLL</PluginType>     
	</certificateidentitydeclaration> 
</Protocols> 
```

Duplicate nodes are not allowed in AuthManConfig.xml. If the "Protocols" node already exists, the contents of the example above have to be combined with the existing node. 

After changes had been made to the configuration file, Auth Manager has to be restarted to acquire them. 

The name of the protocol specified in the "ProtocolOrder" key must match the name of the corresponding node under "Protocols". 

For clarity, in this example only Certificate Identity Declaration has been specified in the list of protocols. Other protocols can be added to the list. 

The value of "KeepLibraryLoaded" in the protocol configuration specifies whether the module has to be kept loaded in memory after use. If set to "False", the module will be reloaded upon each use. 

After being loaded and launched, the sample plugin will look for a certificate name "UserIdentifyingCertificate.cer" in the home directory of the current user. This should be the public certificate from the smart card. 

In the example provided, the path and the name of the certificate to use are hard-coded.

## Change history

| Version | Change |
|---|---|
| 13.8 and later | Remove support for the armel platform |
| 13.5 | No change |
| 13.4 | The first release of this SDK. Includes a sample plugin and its source code |
