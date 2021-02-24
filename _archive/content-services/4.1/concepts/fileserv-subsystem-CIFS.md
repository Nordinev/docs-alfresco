---
author: [Alfresco Documentation, Alfresco Documentation]
source: 
audience: 
category: Administration
option: file servers subsystem SMB CIFS
---

# Configuring the Common Internet File System \(CIFS\) server

This server includes Java socket-based implementations of the SMB/CIFS protocol that can be used on any platform.

This server can be configured to listen for SMB traffic over the native SMB TCP protocol supported by Windows 2000 and later versions, and the NetBIOS over TCP \(NBT\) protocol, supported by all Windows versions. There is also a Windows-specific interface that uses Win32 NetBIOS API calls using JNI code. This allows the Alfresco CIFS server to run alongside the native Windows file server.

The default configuration uses the JNI-based code under Windows and the Java socket based code under Linux, Solaris, and Mac OS X.

-   **[CIFS file server properties](../concepts/fileserv-CIFS-props.md)**  
The following properties can be configured for the SMB/CIFS server.
-   **[Java-based SMB properties](../concepts/fileserv-CIFS-javaprops.md)**  
The following properties will only take effect on non-Windows servers, where the Java-based SMB implementation is used, unless it is enabled on Windows using the advanced Spring bean definition overrides.
-   **[Windows native SMB](../concepts/fileserv-CIFS-WinSMB.md)**  
The following property will only take effect on Windows servers, where by default a JNI-based CIFS implementation is in use.
-   **[Running the CIFS server from a normal user account](../tasks/fileserv-CIFS-useracc.md)**  
To avoid running the Alfresco set up as root and be able use the CIFS server with standard ports, it can be configured to run using non-privileged ports, and then you can use firewall rules to forward requests from the privileged ports \(TCP 139/445 UDP 137/138\) to the non-privileged ports.
-   **[Configuring advanced Spring overrides for the CIFS server](../tasks/fileserv-CIFS-adv.md)**  
The SMB/CIFS server beans are declared in the file-servers-context.xml file in <configRoot\>\\classes\\alfresco\\subsystems\\fileServers\\default\\.
-   **[Configuring CIFS on Windows Server 2008 R2](../tasks/fileserv-CIFS-WS2008R2.md)**  
 The following instructions describe how to configure the Alfresco CIFS server on Windows Server 2008 R2.

**Parent topic:**[Configuring file servers](../concepts/fileserv-subsystem-intro.md)
