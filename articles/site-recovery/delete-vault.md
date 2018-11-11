---
title: Supprimer un coffre Recovery Services configuré pour le service Azure Site Recovery
description: Découvrez comment supprimer un coffre Recovery Services configuré pour Azure Site Recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.date: 07/06/2018
ms.author: rajani-janaki-ram
ms.openlocfilehash: f351bd715baf86407c249e13aa02cf3c37e374c4
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2018
ms.locfileid: "50212418"
---
# <a name="delete-a-site-recovery-recovery-services-vault"></a>Supprimer un coffre Recovery Services configuré pour Site Recovery

Des dépendances peuvent vous empêcher de supprimer un archivage Azure Site Recovery. Les actions à effectuer varient en fonction du scénario de Site Recovery. Pour supprimer un archivage utilisé dans Sauvegarde Microsoft Azure, voir [Supprimer un archivage Recovery Services](../backup/backup-azure-delete-vault.md).



## <a name="delete-a-site-recovery-vault"></a>Supprimer un archivage Site Recovery 
Pour supprimer l’archivage, suivez les étapes recommandées pour votre scénario.

### <a name="vmware-vms-to-azure"></a>Machines virtuelles VMware vers Azure

1. Supprimez toutes les machines virtuelles protégées en suivant les étapes décrites dans [Désactiver la protection d’un VMware](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure).

2. Supprimez toutes les stratégies de réplication en suivant les étapes décrites dans [Supprimer une stratégie de réplication](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy).

3. Supprimez les références à vCenter en suivant les étapes décrites dans la section [Supprimer un serveur vCenter](vmware-azure-manage-vcenter.md#delete-a-vcenter-server).

4. Supprimez le serveur de configuration en suivant les étapes décrites dans [Désaffecter un serveur de configuration](vmware-azure-manage-configuration-server.md#delete-or-unregister-a-configuration-server).

5. Supprimez l’archivage.


### <a name="hyper-v-vms-with-vmm-to-azure"></a>Machines virtuelles Hyper-V (avec VMM) dans Azure
1. Supprimez toutes les machines virtuelles protégées en suivant les étapes décrites dans [Désactiver la protection d’une machine virtuelle Hyper-V (avec VMM)](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-hyper-v-virtual-machine-replicating-to-azure-using-the-system-center-vmm-to-azure-scenario).

2. Dissociez et supprimez toutes les stratégies de réplication en accédant à votre coffre -> **Infrastructure Site Recovery** -> **For System Center VMM (Pour System Center VMM)** -> **Stratégies de réplication**

3.  Supprimez les références aux serveurs VMM en suivant les étapes décrites dans [Annuler l’inscription d’un serveur VMM connecté](site-recovery-manage-registration-and-protection.md##unregister-a-vmm-server).

4.  Supprimez l’archivage.

### <a name="hyper-v-vms-without-virtual-machine-manager-to-azure"></a>Machines virtuelles Hyper-V (sans Virtual Machine Manager) vers Azure
1. Supprimez toutes les machines virtuelles protégées en suivant les étapes décrites dans [Désactiver la protection d’une machine virtuelle Hyper-V dans un site Hyper-V](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-hyper-v-virtual-machine-hyper-v-to-azure).

2. Dissociez et supprimez toutes les stratégies de réplication en accédant à votre coffre -> **Infrastructure Site Recovery** -> **For Hyper-V Sites (Pour des sites Hyper-V)** -> **Stratégies de réplication**

3. Supprimez les références aux serveurs Hyper-V en suivant les étapes décrites dans [Annuler l’inscription d’un ordinateur hôte Hyper-V](site-recovery-manage-registration-and-protection.md#unregister-a-hyper-v-host-in-a-hyper-v-site).

4. Supprimez le site Hyper-V.

5. Supprimez l’archivage.


## <a name="use-powershell-to-force-delete-the-vault"></a>Utiliser PowerShell pour forcer la suppression de l’archivage 

> [!Important]
> Si vous testez le produit et ne vous inquiétez pas de perdre des données, utilisez la méthode de suppression de force pour supprimer rapidement l’archivage et toutes ses dépendances.
> La commande PowerShell supprime tout le contenu de l’archivage et n’est **pas réversible**.

Pour supprimer l’archivage Site Recovery, même s’il contient des éléments protégés, utilisez les commandes suivantes :

    Connect-AzureRmAccount

    Select-AzureRmSubscription -SubscriptionName "XXXXX"

    $vault = Get-AzureRmRecoveryServicesVault -Name "vaultname"

    Remove-AzureRmRecoveryServicesVault -Vault $vault

Découvrez-en plus sur [Get-AzureRMRecoveryServicesVault](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices/get-azurermrecoveryservicesvault?view=azurermps-6.0.0) et [Remove-AzureRMRecoveryServicesVault](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices/remove-azurermrecoveryservicesvault?view=azurermps-6.0.0).
