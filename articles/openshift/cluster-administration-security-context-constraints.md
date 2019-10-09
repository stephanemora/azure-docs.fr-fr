---
title: Gestion des contraintes de contexte de sécurité dans Azure Red Hat OpenShift | Microsoft Docs
description: Administrateur de cluster Azure Red Hat OpenShift gérant les contraintes de contexte de sécurité
services: container-service
author: troy0820
ms.author: jzim
ms.service: container-service
ms.topic: article
ms.date: 09/25/2019
ms.openlocfilehash: 8e85ac98683487c6b18be7f502f28cad9a0c2251
ms.sourcegitcommit: d4c9821b31f5a12ab4cc60036fde00e7d8dc4421
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/01/2019
ms.locfileid: "71712840"
---
# <a name="overview"></a>Vue d'ensemble 

Les contraintes de contexte de sécurité permettent aux administrateurs de contrôler les autorisations pour les pods. Pour en savoir plus sur ce type d’API, voir la documentation sur l’architecture des [contraintes de contexte de sécurité](https://https://docs.openshift.com/container-platform/3.11/architecture/additional_concepts/authorization.html). Vous pouvez gérer les contraintes de contexte de sécurité dans votre instance en tant qu’objets API normaux à l’aide de l’interface de ligne de commande.

## <a name="listing-security-context-constraints"></a>Affichage des contraintes de contexte de sécurité

Pour obtenir la liste actuelle des contraintes de contexte de sécurité 

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

## <a name="examining-a-security-context-constraints-object"></a>Examen d’un objet contrainte de contexte de sécurité

Pour examiner une contrainte de contexte de sécurité particulière, utilisez `oc get`, `oc describe` ou `oc edit`.  Par exemple, pour examiner la contrainte de contexte de sécurité **restreint** :
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
## <a name="next-steps"></a>Étapes suivantes
Comment configurer le rôle osa-customer-admin :
> [!div class="nextstepaction"]
> [Intégration d’Azure Active Directory pour Azure Red Hat OpenShift](howto-aad-app-configuration.md) 