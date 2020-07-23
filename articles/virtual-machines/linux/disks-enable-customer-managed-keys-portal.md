---
title: Portail Azure - Activez les clés gérées par le client avec les disques managés - SSE
description: Activez les clés gérées par le client sur vos disques managés à l’aide du portail Azure.
author: roygara
ms.date: 07/10/2020
ms.topic: how-to
ms.author: rogarana
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 0128d2b88d6eb2464ef95824330cfab07ee3703d
ms.sourcegitcommit: f7e160c820c1e2eb57dc480b2a8fd6bef7053e91
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/10/2020
ms.locfileid: "86235660"
---
# <a name="enable-customer-managed-keys-with-server-side-encryption---managed-disks---portal"></a>Activer les clés gérées par le client avec le chiffrement côté serveur - Disque managés - Portail

Le Stockage sur disque Azure vous permet de gérer vos propres clés lors de l’utilisation du chiffrement côté serveur (SSE) pour les disques managés, si vous le souhaitez. Pour obtenir des informations conceptuelles sur SSE avec des clés gérées par le client ainsi que d’autres types de chiffrement de disque managés, consultez la section [Clés gérées par le client](disk-encryption.md#customer-managed-keys) de notre article sur le chiffrement de disque.

## <a name="restrictions"></a>Restrictions

Pour le moment, les clés gérées par le client sont soumises aux restrictions suivantes :

- Si cette fonctionnalité est activée pour votre disque, vous ne pouvez pas la désactiver.
    Pour contourner le problème, vous devez [copier toutes les données](disks-upload-vhd-to-managed-disk-cli.md#copy-a-managed-disk) sur un autre disque managé qui n’utilise pas de clés gérées par le client.
[!INCLUDE [virtual-machines-managed-disks-customer-managed-keys-restrictions](../../../includes/virtual-machines-managed-disks-customer-managed-keys-restrictions.md)]

[!INCLUDE [virtual-machines-disks-encryption-portal](../../../includes/virtual-machines-disks-encryption-portal.md)]

> [!IMPORTANT]
> Les clés managées par le client s’appuient sur des identités managées pour ressources Azure, une fonctionnalité d’Azure Active Directory (Azure AD). Quand vous configurez des clés gérées par le client, une identité managée est automatiquement affectée à vos ressources à l’arrière plan. Si, par la suite, vous déplacez l’abonnement, le groupe de ressources ou le disque managé d’un répertoire Azure AD à un autre, l’identité managée associée aux disques managés n’est pas transférée vers le nouveau locataire, de sorte que les clés gérées par le client peuvent ne plus fonctionner. Pour plus d’informations, consultez [Transfert d’un abonnement entre des répertoires Azure AD](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).

## <a name="next-steps"></a>Étapes suivantes

- [Découvrir les modèles Azure Resource Manager permettant de créer des disques chiffrés avec des clés gérées par le client](https://github.com/ramankumarlive/manageddiskscmkpreview)
- [Répliquer des machines avec des disques activés par les clés gérées par le client](../../site-recovery/azure-to-azure-how-to-enable-replication-cmk-disks.md)
- [Configurer la reprise d’activité des machines virtuelles VMware sur Azure avec PowerShell](../../site-recovery/vmware-azure-disaster-recovery-powershell.md#replicate-vmware-vms)
- [Configurer la récupération d’urgence dans Azure pour les machines virtuelles Hyper-V à l’aide de PowerShell et d’Azure Resource Manager](../../site-recovery/hyper-v-azure-powershell-resource-manager.md#step-7-enable-vm-protection)
