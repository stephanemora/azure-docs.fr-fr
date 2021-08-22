---
title: Résoudre les problèmes liés au volume LDAP pour Azure NetApp Files | Microsoft Docs
description: Décrit les résolutions de conditions d’erreur que vous pourriez rencontrer lors de la configuration de volumes LDAP pour Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 04/05/2021
ms.author: b-juche
ms.openlocfilehash: 0008c6034eba2de58684276cb5f12c6b2398cac1
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122562357"
---
# <a name="troubleshoot-ldap-volume-issues"></a>Résoudre les problèmes liés au volume LDAP

Cet article décrit les résolutions de conditions d’erreur que vous pourriez rencontrer lors de la configuration de volumes LDAP.

## <a name="errors-and-resolutions-for-ldap-volumes"></a>Erreurs et résolutions pour volumes LDAP

|     Conditions d’erreur    |     Résolutions    |
|-|-|
| Erreur lors de la création d’un volume SMB avec la valeur ldapEnabled définie sur true : <br> `Error Message: ldapEnabled option is only supported with NFS protocol volume. ` | Vous ne pouvez pas créer un volume SMB avec LDAP activé. <br> Créez des volumes SMB avec LDAP désactivé. |
| Erreur lors de la mise à jour de la valeur du paramètre ldapEnabled pour un volume existant : <br> `Error Message: ldapEnabled parameter is not allowed to update` |  Vous ne pouvez pas modifier le paramètre de l’option LDAP après avoir créé un volume. <br> Ne mettez pas à jour le paramètre de l’option LDAP sur un volume créé. Pour plus de détails, consultez [Configurer ADDS LDAP avec des groupes étendus pour l’accès au volume NFS](configure-ldap-extended-groups.md). |
| Erreur lors de la création d’un volume NFS avec LDAP activé : <br> `Could not query DNS server` <br> `Sample error message:` <br> `"log": time="2020-10-21 05:04:04.300" level=info msg=Res method=GET url=/v2/Volumes/070d0d72-d82c-c893-8ce3-17894e56cea3  x-correlation-id=9bb9e9fe-abb6-4eb5-a1e4-9e5fbb838813 x-request-id=c8032cb4-2453-05a9-6d61-31ca4a922d85 xresp="200:  {\"created\":\"2020-10-21T05:02:55.000Z\",\"lifeCycleState\":\"error\",\"lifeCycleStateDetails\":\"Error when creating - Could not query DNS server. Verify that the network configuration is correct and that DNS servers are available.\",\"name\":\"smb1\",\"ownerId\ \":\"8c925a51-b913-11e9-b0de-9af5941b8ed0\",\"region\":\"westus2stage\",\"volumeId\":\"070d0d72-d82c-c893-8ce3-` |  Cette erreur se produit parce que le DNS est inaccessible. <br> <ul><li> Vérifiez si vous avez configuré le site approprié (étendue de site) pour Azure NetApp Files. </li><li> La raison pour laquelle le DNS est inaccessible peut être une adresse IP DNS incorrecte ou des problèmes de mise en réseau. Vérifiez l’adresse IP de DNS entrée dans la connexion AD, et assurez-vous qu’elle est correcte. </li><li> Assurez-vous que l’AD et le volume se trouvent dans la même région et dans le même réseau virtuel. S’ils se trouvent dans des réseaux virtuels différents, assurez-vous que le peering de réseaux virtuels est établi entre les deux réseaux virtuels.</li></ul> |
| Erreur lors de la création du volume à partir d’une capture instantanée : <br> `Aggregate does not exist` | Azure NetApp Files ne prend pas en charge l’approvisionnement d’un nouveau volume avec LDAP activé à partir d’une capture instantanée appartenant à un volume avec LDAP désactivé. <br> Essayez de créer un volume avec LDAP désactivé à partir de la capture instantané donnée. |

## <a name="next-steps"></a>Étapes suivantes  

* [Configurer ADDS LDAP avec des groupes étendus pour l’accès au volume NFS](configure-ldap-extended-groups.md)
* [Créer un volume NFS pour Azure NetApp Files](azure-netapp-files-create-volumes.md)
* [Créer un volume double protocole pour Azure NetApp Files](create-volumes-dual-protocol.md)