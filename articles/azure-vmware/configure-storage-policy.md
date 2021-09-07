---
title: Configurer une stratégie de stockage
description: Découvrez comment configurer une stratégie de stockage pour vos machines virtuelles Azure VMware Solution.
ms.topic: how-to
ms.date: 08/31/2021
ms.openlocfilehash: b9535c5765b2ff024537ff44e2e24a76c992dd35
ms.sourcegitcommit: 851b75d0936bc7c2f8ada72834cb2d15779aeb69
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/31/2021
ms.locfileid: "123304053"
---
# <a name="configure-storage-policy"></a>Configurer une stratégie de stockage

Les stratégies de stockage vSAN définissent les exigences de stockage pour vos machines virtuelles. Ces stratégies garantissent le niveau de service exigé pour vos machines virtuelles, car elles déterminent la façon dont le stockage est alloué à la machine virtuelle. Au moins une stratégie de stockage de machine virtuelle est associée à chaque machine virtuelle déployée sur un magasin de données vSAN.

Vous pouvez attribuer une stratégie de stockage de machine virtuelle dans un déploiement initial de machine virtuelle ou lorsque vous effectuez d’autres opérations de machine virtuelle, telles que le clonage ou la migration. Après le déploiement, des utilisateurs CloudAdmin ou des rôles équivalents ne peuvent pas modifier la stratégie de stockage par défaut pour une machine virtuelle. En revanche, des modifications de **stratégie de stockage de machine virtuelle** par disque sont autorisées. 

La commande d’exécution permet aux utilisateurs autorisés de remplacer la stratégie de stockage de machine virtuelle par défaut ou existante par une stratégie disponible pour une machine virtuelle après le déploiement. Aucune modification n’est apportée à la stratégie de stockage de machine virtuelle au niveau disque. Vous pouvez toujours modifier la stratégie de stockage de machine virtuelle au niveau du disque en fonction de vos besoins.


>[!NOTE]
>Les commandes d’exécution sont exécutées successivement dans l’ordre de leur envoi.


Nous expliquons ici comment :

> [!div class="checklist"]
> * Répertorier toutes les stratégies de stockage
> * Définir la stratégie de stockage pour une machine virtuelle
> * Spécifier une stratégie de stockage pour un cluster



## <a name="prerequisites"></a>Prérequis

Assurez-vous que le [niveau minimal d’hôtes est atteint](https://docs.vmware.com/en/VMware-Cloud-on-AWS/services/com.vmware.vsphere.vmc-aws-manage-data-center-vms.doc/GUID-EDBB551B-51B0-421B-9C44-6ECB66ED660B.html).

|  **Configuration RAID** | **Nombre de défaillances tolérées** | **Minimum d’hôtes requis** |
| --- | :---: | :---: |
| RAID-1 (Mise en miroir) <br />Paramètre par défaut.  | 1  | 3  |
| RAID-5 (Code d’effacement)  | 1  | 4  |
| RAID-1 (Mise en miroir)  | 2  | 5  |
| RAID-6 (Code d’effacement)  | 2  | 6  |
| RAID-1 (Mise en miroir)  | 3  | 7  |


 

## <a name="list-storage-policies"></a>Répertorier les stratégies de stockage

Vous allez exécuter la cmdlet `Get-StoragePolicy` pour répertorier les stratégies de stockage basées sur vSAN disponibles qui peuvent être définies sur une machine virtuelle.

1. Connectez-vous au [portail Azure](https://portal.azure.com).

1. Sélectionnez **Commande d’exécution** > **Packages** > **Get-StoragePolicies**.

   :::image type="content" source="media/run-command/run-command-overview-storage-policy.png" alt-text="Capture d’écran montrant comment accéder aux commandes d’exécution de stratégie de stockage disponibles." lightbox="media/run-command/run-command-overview-storage-policy.png":::

1. Fournissez les valeurs requises ou modifiez les valeurs par défaut, puis sélectionnez **Exécuter**.

   :::image type="content" source="media/run-command/run-command-get-storage-policy.png" alt-text="Capture d’écran montrant comment répertorier les stratégies de stockage disponibles.":::
   
   | **Champ** | **Valeur** |
   | --- | --- |
   | **Conserver jusqu’à**  | Période de rétention de la sortie de la cmdlet. La valeur par défaut est 60.  |
   | **Spécifier un nom pour l’exécution**  | Nom alphanumérique, par exemple, **Get-StoragePolicies-Exec1**. |
   | **Délai d'expiration**  |  Période après laquelle une cmdlet s’arrête si son exécution prend trop de temps.  |

1. Consultez **Notifications** pour voir la progression.




## <a name="set-storage-policy-on-vm"></a>Définir une stratégie de stockage sur une machine virtuelle

Vous allez exécuter la cmdlet `Set-AvsVMStoragePolicy` pour modifier des stratégies de stockage basées sur vSAN sur une machine virtuelle individuelle. 

>[!NOTE]
>Vous ne pouvez pas utiliser le client vSphere pour modifier la stratégie de stockage par défaut ou des stratégies de stockage existantes pour une machine virtuelle. 

1. Sélectionnez **Commande d’exécution** > **Packages** > **Set-AvsVMStoragePolicy**.

1. Fournissez les valeurs requises ou modifiez les valeurs par défaut, puis sélectionnez **Exécuter**.

   | **Champ** | **Valeur** |
   | --- | --- |
   | **VMName** | Nom de la machine virtuelle cible. |
   | **StoragePolicyName** | Nom de la stratégie de stockage à définir. Par exemple,**RAID-FTT-1**. |
   | **Conserver jusqu’à**  | Période de rétention de la sortie de la cmdlet. La valeur par défaut est 60.  |
   | **Spécifier un nom pour l’exécution**  | Nom alphanumérique, par exemple, **changeVMStoragePolicy**.  |
   | **Délai d'expiration**  |  Période après laquelle une cmdlet s’arrête si son exécution prend trop de temps.  |

1. Consultez **Notifications** pour voir la progression.


## <a name="specify-storage-policy-for-a-cluster"></a>Spécifier une stratégie de stockage pour un cluster

Vous allez exécuter la cmdlet `Set-ClusterDefaultStoragePolicy` pour spécifier la stratégie de stockage par défaut pour un cluster.

1. Sélectionnez **Commande d’exécution** > **Packages** > **Set-ClusterDefaultStoragePolicy**.

1. Fournissez les valeurs requises ou modifiez les valeurs par défaut, puis sélectionnez **Exécuter**.

   | **Champ** | **Valeur** |
   | --- | --- |
   | **Nom du cluster** | Nom du cluster. |
   | **StoragePolicyName** | Nom de la stratégie de stockage à définir. Par exemple,**RAID-FTT-1**. |
   | **Conserver jusqu’à**  | Période de rétention de la sortie de la cmdlet. La valeur par défaut est 60.  |
   | **Spécifier un nom pour l’exécution**  | Nom alphanumérique, par exemple, **Set-ClusterDefaultStoragePolicy-Exec1**.  |
   | **Délai d'expiration**  |  Période après laquelle une cmdlet s’arrête si son exécution prend trop de temps.  |

1. Consultez **Notifications** pour voir la progression.



## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris à configurer des stratégies de stockage vSAN, vous pouvez en apprendre davantage sur les points suivants :

- [Comment attacher des pools de disques à des hôtes Azure VMware Solution (préversion)](attach-disk-pools-to-azure-vmware-solution-hosts.md) : vous pouvez utiliser des disques en guise de stockage persistant pour une solution VMware Azure afin d’optimiser le coût et les performances.

- [Comment configurer une identité externe pour vCenter](configure-identity-source-vcenter.md) : vCenter a un utilisateur local intégré appelé cloudadmin et affecté au rôle CloudAdmin. L’utilisateur local cloudadmin est utilisé pour configurer des utilisateurs dans Active Directory (AD). La fonctionnalité d’exécution de commande vous permet de configurer Active Directory via LDAP ou LDAP sécurisé pour vCenter en tant que source d’identité externe.
