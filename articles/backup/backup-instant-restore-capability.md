---
title: Fonctionnalité de restauration instantanée Azure
description: Présentation de la fonctionnalité de restauration instantanée et questions fréquentes (FAQ) sur la pile de sauvegarde de machine virtuelle et le modèle de déploiement Resource Manager
services: backup
author: sogup
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: sogup
ms.openlocfilehash: cc4f559efecec3f024ce995dcf8f8757eb9cb4fb
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/31/2019
ms.locfileid: "55489686"
---
# <a name="get-improved-backup-and-restore-performance-with-azure-backup-instant-restore-capability"></a>Améliorer les performances de sauvegarde et de restauration avec la fonctionnalité de restauration instantanée de Sauvegarde Azure

> [!NOTE]
> Suite aux commentaires des utilisateurs, nous avons renommé la **Pile de sauvegarde de machine virtuelle V2** **Restauration instantanée** pour éviter toute confusion avec les fonctionnalités Azure Stack.

Le nouveau modèle pour la restauration instantanée fournit les améliorations de fonctionnalités suivantes :

* Possibilité d’utiliser des instantanés pris dans le cadre d’une tâche de sauvegarde qui peut être récupérée sans attendre la fin du transfert des données dans le coffre. Cela réduit le temps d’attente pour la copie des instantanés dans le coffre avant de déclencher la restauration.
* Réduction des durées de sauvegarde et de restauration en conservant les instantanés et les disques pendant sept jours
* Prise en charge des disques d’une taille maximale de 4 To
* Prise en charge des disques SSD Standard
* Possibilité d’utiliser les comptes de stockage d’origine d’une machine virtuelle non gérée (par disque) lors de la restauration. Cette possibilité existe même quand la machine virtuelle a des disques répartis entre des comptes de stockage. Ceci accélère les opérations de restauration pour une grande variété de configurations de machine virtuelle.


## <a name="whats-new-in-this-feature"></a>Nouveautés de cette fonctionnalité

Aujourd’hui, la tâche de sauvegarde consiste en deux phases :

1.  Prise d’un instantané de la machine virtuelle.
2.  Transfert d’un instantané de la machine virtuelle vers le coffre Recovery Services.

Un point de récupération est considéré comme créé seulement après l’exécution des étapes 1 et 2. Dans le cadre de cette mise à niveau, un point de récupération est créé dès que l’instantané est terminé, et vous pouvez utiliser ce point de récupération de type instantané pour effectuer une restauration en utilisant le même flux de restauration. Vous pouvez identifier ce point de récupération dans le portail Azure en choisissant « instantané » comme type de point de récupération, et une fois l’instantané transféré vers le coffre, le type de point de récupération devient « instantané et coffre ».

![Tâche de sauvegarde dans le modèle de déploiement Resource Manager pour la pile de sauvegarde de machine virtuelle : stockage et coffre](./media/backup-azure-vms/instant-rp-flow.png)

Les instantanés sont conservés pendant sept jours. Cette fonctionnalité autorise les opérations de restauration à partir de ces instantanés en réduisant les durées de restauration. Elle réduit le temps nécessaire pour transformer et copier les données du coffre vers le compte de stockage de l’utilisateur pour les scénarios à disques non managés, tandis que pour les utilisateurs de disques managés, elle crée des disques managés à partir des données de sauvegarde.

## <a name="feature-considerations"></a>Considérations sur la fonctionnalité

* Les instantanés sont stockés avec les disques afin d’accélérer la création des points de récupération et les opérations de restauration. Vous voyez donc des coûts de stockage correspondant aux instantanés pris pendant cette période.
* Les instantanés incrémentiels sont stockés sous la forme d’objets blob de pages. Tout utilisateur qui utilise des disques non managés est facturé pour les instantanés stockés dans son compte de stockage local. Étant donné que les collections de points de restauration utilisées par les sauvegardes de machine virtuelle managée utilisent des instantanés d’objet blob au niveau du stockage sous-jacent, pour les disques managés vous voyez les coûts correspondant au tarif de l’instantané d’objet blob et ils sont incrémentiels.
* Dans le cas des comptes de stockage Premium, les instantanés pris pour les points de récupération instantanée comptent pour la limite de 10 To d’espace alloué.

## <a name="cost-impact"></a>Impact sur les coûts

Les instantanés incrémentiels sont stockés dans le compte de stockage de la machine virtuelle et sont utilisés pour la récupération instantanée. Le fait que l’instantané soit incrémentiel signifie que l’espace occupé par un instantané est égal à l’espace occupé par les pages qui sont écrites après la création de l’instantané. La facturation concerne toujours l’espace utilisé (par Go) occupé par l’instantané, et le prix par Go est identique à celui mentionné dans la [page de tarification](https://azure.microsoft.com/pricing/details/managed-disks/).


## <a name="upgrading-to-instant-restore"></a>Mise à niveau vers la restauration instantanée

Si vous utilisez le portail Azure, une notification s’affichera sur le tableau de bord du coffre. Elle concerne la prise en charge des disques volumineux et les améliorations de la vitesse de sauvegarde et de restauration.

![Tâche de sauvegarde dans le modèle de déploiement Resource Manager pour la pile de sauvegarde de machine virtuelle : notification de prise en charge](./media/backup-azure-vms/instant-rp-banner.png)

Pour ouvrir un écran pour la mise à niveau vers la restauration instantanée, sélectionnez la bannière.

![Tâche de sauvegarde dans le modèle de déploiement Resource Manager pour la pile de sauvegarde de machine virtuelle : mise à niveau](./media/backup-azure-vms/instant-rp.png)

Vous pouvez aussi accéder à la page **Propriétés** du coffre pour accéder à l’option **Mise à niveau** sous **Pile de sauvegarde de machine virtuelle**.

![Tâche de sauvegarde dans la pile de sauvegarde de machine virtuelle -- Page Propriétés](./media/backup-azure-vms/instant-restore-capability-properties.png)


## <a name="upgrade-to-instant-restore-using-powershell"></a>Mettre à niveau vers la restauration instantanée à l’aide de PowerShell

Si vous souhaitez travailler en libre-service et effectuer une mise à niveau vers la restauration instantanée, exécutez les applets de commande suivantes à partir d’un terminal PowerShell avec élévation de privilèges :

1.  Connectez-vous à votre compte Azure :

    ```
    PS C:> Connect-AzureRmAccount
    ```

2.  Sélectionnez l’abonnement à inscrire :

    ```
    PS C:>  Get-AzureRmSubscription –SubscriptionName "Subscription Name" | Select-AzureRmSubscription
    ```

3.  Inscrivez cet abonnement :

    ```
    PS C:>  Register-AzureRmProviderFeature -FeatureName "InstantBackupandRecovery" –ProviderNamespace Microsoft.RecoveryServices
    ```

## <a name="upgrade-to-instant-restore-using-cli"></a>Mettre à niveau vers la restauration instantanée à l’aide de l’interface CLI

À partir d'un interpréteur de commandes, exécutez les commandes suivantes :

1.  Connectez-vous à votre compte Azure :

    ```
    az login
    ```

2.  Sélectionnez l’abonnement à inscrire :

    ```
    az account set --subscription "Subscription Name"
    ```

3.  Inscrivez cet abonnement :

    ```
    az feature register --namespace Microsoft.RecoveryServices --name InstantBackupandRecovery
    ```

## <a name="verify-that-the-upgrade-is-successful"></a>Vérifier si la mise à niveau a réussi

### <a name="powershell"></a>PowerShell
À partir d’un terminal PowerShell avec élévation des privilèges, exécutez l’applet de commande suivante :

```
Get-AzureRmProviderFeature -FeatureName "InstantBackupandRecovery" -ProviderNamespace Microsoft.RecoveryServices
```

### <a name="cli"></a>Interface de ligne de commande
À partir d’un interpréteur de commandes, exécutez la commande suivante :

```
az feature show --namespace Microsoft.RecoveryServices --name InstantBackupandRecovery
```

Si la sortie indique « Registered », votre abonnement est mis à niveau vers la restauration instantanée.

## <a name="frequently-asked-questions"></a>Questions fréquentes (FAQ)

### <a name="what-are-the-cost-implications-of-instant-restore"></a>Quelles sont les implications en matière de coût de la restauration instantanée ?
Les instantanés sont stockés avec les disques afin d’accélérer la création des points de récupération et les opérations de restauration. Ainsi, vous verrez des coûts de stockage qui correspondent à la conservation d’instantané sélectionnée dans le cadre de la stratégie de sauvegarde de machine virtuelle.

### <a name="in-premium-storage-accounts-do-the-snapshots-taken-for-instant-recovery-point-occupy-the-10-tb-snapshot-limit"></a>Dans les comptes de stockage Premium, les instantanés créés pour un point de récupération instantanée occupent-ils la limite de 10 To ?
Oui, pour les comptes de stockage Premium, les instantanés pris pour un point de récupération instantanée occupent 10 To d’espace d’instantané alloué.

### <a name="how-does-the-snapshot-retention-work-during-the-five-day-period"></a>Comment fonctionne la conservation des instantanés pendant la période de cinq jours ?
Chaque jour un nouvel instantané est pris, et il existe cinq instantanés incrémentiels. La taille de l’instantané dépend du taux d’activité des données, qui est dans la plupart des cas de deux à sept pour cent.

### <a name="is-an-instant-restore-snapshot-an-incremental-snapshot-or-full-snapshot"></a>Un instantané de restauration instantanée est-il un instantané incrémentiel ou un instantané complet ?
Les instantanés créés dans le cadre de la fonctionnalité de restauration instantanée sont des instantanés incrémentiels.

### <a name="how-can-i-calculate-the-approximate-cost-increase-due-to-instant-restore-feature"></a>Comment faire pour calculer une estimation de l’augmentation du coût due à la fonctionnalité de restauration instantanée ?
Cela dépend du taux d’activité de la machine virtuelle. Dans un état stable, vous pouvez supposer que l’augmentation du coût est égale à la période de conservation d’instantané * le taux d’activité quotidien par machine virtuelle * le coût de stockage par Go.

### <a name="if-the-recovery-type-for-a-restore-point-is-snapshot-and-vault-and-i-perform-a-restore-operation-which-recovery-type-will-be-used"></a>Si le type de récupération pour un point de restauration est « instantané et coffre » et que j’effectue une opération de restauration, quel sera le type de récupération utilisé ?
Si le type de récupération est « instantané et coffre », la restauration sera effectuée automatiquement à partir de l’instantané local, ce qui sera beaucoup plus rapide que la restauration effectuée à partir du coffre.

### <a name="what-happens-if-i-select-retention-period-of-restore-point-tier-2-less-than-the-snapshot-tier1-retention-period"></a>Que se passe-t-il si je sélectionne une période de conservation du point de restauration (niveau 2) inférieure à la période de conservation d’instantané (niveau 1) ?
Le nouveau modèle n’autorise la suppression du point de restauration (niveau 2) que si l’instantané (niveau 1) est supprimé. Actuellement, nous prenons en charge une période de conservation de sept jours pour la suppression de l’instantané (niveau 1), donc la période de conservation de moins de sept jours du point de restauration (niveau 2) n’est pas honorée. Nous vous recommandons de planifier une période de conservation du point de restauration (niveau 2) supérieure à sept jours.

### <a name="why-is-my-snapshot-existing-even-after-the-set-retention-period-in-backup-policy"></a>Pourquoi mon instantané existe-t-il même après la période de conservation définie dans la stratégie de sauvegarde ?
Si le point de récupération a un instantané et qu’il s’agit du dernier point de récupération disponible, il est conservé jusqu’à la prochaine sauvegarde réussie. C’est conforme à la stratégie de GC actuelle, qui exige qu’au moins un point de récupération récent soit toujours présent au cas où toutes les sauvegardes ultérieures échoueraient en raison d’un problème sur la machine virtuelle. Dans les scénarios ordinaires, les points de récupération sont nettoyés au plus 48 heures après leur expiration.
