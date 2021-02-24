---
author: [Alfresco Documentation, Alfresco Documentation]
source: 
audience: 
category: Administration
option: audit
---

# Audit filters

This section describes how to use Alfresco global properties to **filter audit data** generated by any audit data producer.

**Audit data producers** call *AuditComponent.recordAuditValues\(rootPath, auditMap\)* once for each event to be audited. Filters are applied to reject events so that their values are never used by **audit configurations**. The *rootPath* identifies the data producer and the *auditMap* is the event data. The *rootPath* value and keys in the map represent a tree structure.

-   **[Example rootPath and auditMap](../concepts/audit-40-example-rootpath.md)**  
The last component in the *rootPath* is considered by the *AuditFilter* to be the event **action**. The keys in an audit map identify each audit value. Global properties may be defined to accept or reject each value. If any value in an audit map is rejected, the whole map is rejected. So that one does not have to define too many properties, a *default* event action property may be defined. This will be inherited by all actions unless a property is defined for a particular event action.
-   **[Example filter](../concepts/audit-40-example-filter.md)**  
Each property value defines a list of regular expressions that will be used to match the actual audit map values.
-   **[Redirected properties](../concepts/audit-40-redirected-props.md)**  
It is possible for one property to reference another property.
-   **[Debug information](../concepts/audit-40-debug-info.md)**  
The PropertyAuditFilter provides log4j debug information \(in the alfresco.log file\) when it rejects values. Turning on this debug can generate large volumes of output. **Enable debug**
-   **[Audit filter customizations](../concepts/audit-40-custom.md)**  
You can define additional filter properties and override predefined filter values.

**Parent topic:**[Auditing Alfresco](../concepts/audit-intro.md)
