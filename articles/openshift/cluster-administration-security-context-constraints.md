---
title: Zarządzanie ograniczeniami kontekstu zabezpieczeń w systemie Azure Red Hat OpenShift | Microsoft Docs
description: Ograniczenia kontekstu zabezpieczeń dla administratorów klastrów Red Hat OpenShift platformy Azure
services: container-service
author: troy0820
ms.author: jzim
ms.service: container-service
ms.topic: article
ms.date: 09/25/2019
ms.openlocfilehash: f98f55dca8b3dbbfbe03cb8c79691cedb63335a0
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2019
ms.locfileid: "72168993"
---
# <a name="manage-security-context-constraints-in-azure-red-hat-openshift"></a>Zarządzanie ograniczeniami kontekstu zabezpieczeń na platformie Azure Red Hat OpenShift 

Ograniczenia kontekstu zabezpieczeń (SCCs) umożliwiają administratorom klastra kontrolowanie uprawnień dla zasobników. Aby dowiedzieć się więcej na temat tego typu interfejsu API, zobacz [dokumentację architektury dla SCCs](https://docs.openshift.com/container-platform/3.11/architecture/additional_concepts/authorization.html). Możesz zarządzać SCCs w swoim wystąpieniu jako normalnymi obiektami interfejsu API za pomocą interfejsu wiersza polecenia.

## <a name="list-security-context-constraints"></a>Wyświetl listę ograniczeń kontekstu zabezpieczeń

Aby uzyskać bieżącą listę SCCs, użyj tego polecenia: 

```bash
$ oc get scc

NAME               PRIV      CAPS      SELINUX     RUNASUSER          FSGROUP     SUPGROUP    PRIORITY   READONLYROOTFS   VOLUMES
anyuid             false     []        MustRunAs   RunAsAny           RunAsAny    RunAsAny    10         false            [configMap downwardAPI emptyDir persistentVolumeClaim secret]
hostaccess         false     []        MustRunAs   MustRunAsRange     MustRunAs   RunAsAny    <none>     false            [configMap downwardAPI emptyDir hostPath persistentVolumeClaim secret]
hostmount-anyuid   false     []        MustRunAs   RunAsAny           RunAsAny    RunAsAny    <none>     false            [configMap downwardAPI emptyDir hostPath nfs persistentVolumeClaim secret]
hostnetwork        false     []        MustRunAs   MustRunAsRange     MustRunAs   MustRunAs   <none>     false            [configMap downwardAPI emptyDir persistentVolumeClaim secret]
nonroot            false     []        MustRunAs   MustRunAsNonRoot   RunAsAny    RunAsAny    <none>     false            [configMap downwardAPI emptyDir persistentVolumeClaim secret]
privileged         true      [*]       RunAsAny    RunAsAny           RunAsAny    RunAsAny    <none>     false            [*]
restricted         false     []        MustRunAs   MustRunAsRange     MustRunAs   RunAsAny    <none>     false            [configMap downwardAPI emptyDir persistentVolumeClaim secret]
```

## <a name="examine-an-object-for-security-context-constraints"></a>Badanie obiektu pod kątem ograniczeń kontekstu zabezpieczeń

Aby zapoznać się z konkretnym SCC, użyj `oc get`, `oc describe` lub `oc edit`.  Aby na przykład przeanalizować **ograniczoną** wartość SCC, użyj tego polecenia:
```bash
$ oc describe scc restricted
Name:                   restricted
Priority:               <none>
Access:
  Users:                <none>
  Groups:               system:authenticated
Settings:
  Allow Privileged:         false
  Default Add Capabilities:     <none>
  Required Drop Capabilities:       KILL,MKNOD,SYS_CHROOT,SETUID,SETGID
  Allowed Capabilities:         <none>
  Allowed Seccomp Profiles:     <none>
  Allowed Volume Types:         configMap,downwardAPI,emptyDir,persistentVolumeClaim,projected,secret
  Allow Host Network:           false
  Allow Host Ports:         false
  Allow Host PID:           false
  Allow Host IPC:           false
  Read Only Root Filesystem:        false
  Run As User Strategy: MustRunAsRange
    UID:                <none>
    UID Range Min:          <none>
    UID Range Max:          <none>
  SELinux Context Strategy: MustRunAs
    User:               <none>
    Role:               <none>
    Type:               <none>
    Level:              <none>
  FSGroup Strategy: MustRunAs
    Ranges:             <none>
  Supplemental Groups Strategy: RunAsAny
    Ranges:             <none>
```
## <a name="next-steps"></a>Następne kroki
> [!div class="nextstepaction"]
> [Tworzenie klastra usługi Azure Red Hat OpenShift](tutorial-create-cluster.md) 
