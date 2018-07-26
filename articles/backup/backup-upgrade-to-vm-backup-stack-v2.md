---
title: Mise à niveau vers la pile de sauvegarde de machine virtuelle Azure V2
description: Processus de mise à niveau et questions fréquentes (FAQ) pour la pile de sauvegarde de machine virtuelle, modèle de déploiement Resource Manager
services: backup
author: trinadhk
manager: vijayts
tags: azure-resource-manager, virtual-machine-backup
ms.service: backup
ms.topic: conceptual
ms.date: 7/18/2018
ms.author: trinadhk
ms.openlocfilehash: c9dff77f6b9fffc02ec94caa3454500772651195
ms.sourcegitcommit: dc646da9fbefcc06c0e11c6a358724b42abb1438
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/18/2018
ms.locfileid: "39136893"
---
# <a name="upgrade-to-azure-vm-backup-stack-v2"></a>Mise à niveau vers la pile de sauvegarde de machine virtuelle Azure V2

Le modèle de déploiement Resource Manager pour la mise à niveau de la pile de sauvegarde de machine virtuelle fournit les améliorations de fonctionnalités suivantes :

* Possibilité d’afficher des instantanés pris dans le cadre d’une tâche de sauvegarde qui peut être récupérée sans attendre la fin du transfert des données. Cela réduit le temps d’attente pour la copie des instantanés dans le coffre avant de déclencher la restauration. De plus, cette possibilité supprime l’exigence de stockage supplémentaire pour la sauvegarde des machines virtuelles Premium excepté pour la première sauvegarde.  

* Réduit les temps de sauvegarde et de restauration en conservant les instantanés localement pendant sept jours.

* Prise en charge de tailles de disque jusqu’à 4 To.

* Possibilité d’utiliser les comptes de stockage d’origine d’une machine virtuelle non gérée lors de la restauration. Cette possibilité existe même quand la machine virtuelle a des disques répartis entre des comptes de stockage. Ceci accélère les opérations de restauration pour une grande variété de configurations de machine virtuelle.
    > [!NOTE]
    > Cela ne revient pas au même que de remplacer la machine virtuelle d’origine. 
    >

## <a name="whats-changing-in-the-new-stack"></a>Changements de la nouvelle pile
Aujourd’hui, la tâche de sauvegarde consiste en deux phases :
1.  Prise d’un instantané de la machine virtuelle. 
2.  Transfert d’un instantané de la machine virtuelle vers le coffre de sauvegarde Azure. 

Un point de récupération est considéré comme créé seulement après l’exécution des étapes 1 et 2. Dans le cadre de la nouvelle pile, un point de récupération est créé dès que l’instantané est terminé. Vous pouvez également effectuer une restauration à partir de ce point de récupération en utilisant le même flux de restauration. Vous pouvez identifier ce point de récupération dans le portail Azure en utilisant « snapshot » (instantané) comme type de point de récupération. Une fois l’instantané transféré vers le coffre, le type de point de récupération devient « snapshot and vault » (instantané et coffre). 

![Tâche de sauvegarde dans le modèle de déploiement Resource Manager pour la pile de sauvegarde de machine virtuelle : stockage et coffre](./media/backup-azure-vms/instant-rp-flow.jpg) 

Par défaut, les instantanés sont conservés pendant sept jours. Cette fonctionnalité permet une exécution plus rapide de la restauration à partir de ces instantanés. Elle réduit le temps nécessaire pour copier les données du coffre vers le compte de stockage du client. 

## <a name="considerations-before-upgrade"></a>Considérations à prendre en compte avant une mise à niveau

* La mise à niveau de la pile de sauvegarde de machine virtuelle est unidirectionnelle : toutes les sauvegardes passent dans ce flux. Comme le changement se produit au niveau de l’abonnement, toutes les machines virtuelles passent dans ce flux. Tous les ajouts de nouvelles fonctionnalités reposent sur la même pile. Actuellement, vous ne pouvez pas contrôler la pile au niveau de la stratégie.

* Les instantanés sont stockés localement pour accélérer la création des points de récupération ainsi que les opérations de restauration. Vous voyez donc des coûts de stockage correspondant aux instantanés pris pendant la période de sept jours.

* Les instantanés incrémentiels sont stockés sous la forme d’objets blob de pages. Tout client qui utilise des disques non managés est facturé pour les sept jours pendant lesquels les instantanés sont stockés dans son compte de stockage local. Selon le modèle de tarification actuel, les disques managés n’entraînent pas de frais pour les clients.

* Si vous restaurez une machine virtuelle Premium à partir d’un point de récupération d’instantané, un emplacement de stockage temporaire est utilisé pendant la création de la machine virtuelle.

* Dans le cas des comptes de stockage Premium, les instantanés pris pour les points de récupération instantanée comptent pour la limite de 10 To d’espace alloué.

## <a name="upgrade"></a>Mise à niveau
### <a name="the-azure-portal"></a>Le portail Azure
Si vous utilisez le portail Azure, une notification s’affiche sur le tableau de bord du coffre. Elle concerne la prise en charge des disques volumineux et les améliorations de la vitesse de sauvegarde et de restauration.

![Tâche de sauvegarde dans le modèle de déploiement Resource Manager pour la pile de sauvegarde de machine virtuelle : notification de prise en charge](./media/backup-azure-vms/instant-rp-banner.png) 

Pour ouvrir un écran pour la mise à niveau vers la nouvelle pile, sélectionnez la bannière. 

![Tâche de sauvegarde dans le modèle de déploiement Resource Manager pour la pile de sauvegarde de machine virtuelle : mise à niveau](./media/backup-azure-vms/instant-rp.png) 

### <a name="powershell"></a>PowerShell
Exécutez les applets de commande suivantes à partir d’un terminal PowerShell avec élévation des privilèges :
1.  Connectez-vous à votre compte Azure : 

    ```
    PS C:> Connect-AzureRmAccount
    ```

2.  Sélectionnez l’abonnement que vous voulez inscrire pour la préversion :

    ```
    PS C:>  Get-AzureRmSubscription –SubscriptionName "Subscription Name" | Select-AzureRmSubscription
    ```

3.  Inscrivez cet abonnement pour une préversion privée :

    ```
    PS C:>  Register-AzureRmProviderFeature -FeatureName "InstantBackupandRecovery" –ProviderNamespace Microsoft.RecoveryServices
    ```

## <a name="verify-that-the-upgrade-is-finished"></a>Vérifier si la mise à niveau est terminée
À partir d’un terminal PowerShell avec élévation des privilèges, exécutez l’applet de commande suivante :

```
Get-AzureRmProviderFeature -FeatureName "InstantBackupandRecovery" –ProviderNamespace Microsoft.RecoveryServices
```

Si la sortie indique « Registered », votre abonnement est mis à niveau vers le modèle de déploiement Resource Manager pour la pile de sauvegarde de machine virtuelle.

## <a name="frequently-asked-questions"></a>Questions fréquentes (FAQ)

Les questions et réponses suivantes ont été collectées dans les forums et les questions des clients.

### <a name="will-upgrading-to-v2-impact-current-backups"></a>La mise à niveau vers la version 2 affecte-t-elle les sauvegardes actuelles ?

Si vous effectuez une mise à niveau vers la version 2, il n’y a aucun impact sur vos sauvegardes actuelles et il n’est pas nécessaire de reconfigurer votre environnement. Effectuez la mise à niveau : votre environnement de sauvegarde continue de fonctionner comme avant.

### <a name="what-does-it-cost-to-upgrade-to-azure-backup-stack-v2"></a>Combien coûte la mise à niveau vers la version 2 de la pile de sauvegarde Azure ?

La mise à niveau vers la version 2 de la pile de sauvegarde Azure ne coûte rien. Les instantanés sont stockés localement pour accélérer la création des points de récupération et les opérations de restauration. Vous voyez donc des coûts de stockage correspondant aux instantanés pris pendant la période de sept jours.

### <a name="does-upgrading-to-stack-v2-increase-the-premium-storage-account-snapshot-limit-by-10-tb"></a>La mise à niveau vers la version 2 de la pile augmente-t-elle la limite des instantanés du compte de stockage Premium de 10 To ?

Non.

### <a name="in-premium-storage-accounts-do-snapshots-taken-for-instant-recovery-point-occupy-the-10-tb-snapshot-limit"></a>Dans les comptes de stockage Premium, les instantanés créés pour un point de récupération instantanée occupent-ils la limite de 10 To ?

Oui, pour les comptes de stockage Premium, les instantanés pris pour un point de récupération instantanée occupent les 10 To d’espace alloué.

### <a name="how-does-the-snapshot-work-during-the-seven-day-period"></a>Comment fonctionnent les instantanés pendant la période de sept jours ? 

Chaque jour, un nouvel instantané est pris. Il existe sept instantanés individuels. Le service n’effectue **pas** de copie le premier jour, et il ajoute ensuite les modifications au cours des six jours suivants.

### <a name="what-happens-if-the-default-resource-group-is-deleted-accidentally"></a>Que se passe-t-il si le groupe de ressources par défaut est supprimé accidentellement ?

Si le groupe de ressources est supprimé, les instantanés de point de récupération de toutes les machines virtuelles protégées dans cette région sont perdus. Lors de la sauvegarde suivante, le groupe de ressources est recréé et les sauvegardes continuent comme prévu. Cette fonctionnalité n’est pas propre aux points de récupération instantanée.

### <a name="can-i-delete-the-default-resource-group-created-for-instant-recovery-points"></a>Puis-je supprimer le groupe de ressources par défaut créé pour les points de récupération instantanée ?

Le service Sauvegarde Azure crée le groupe de ressources managé. Actuellement, vous ne pouvez pas changer ou modifier le groupe de ressources. Vous ne devez pas non plus verrouiller le groupe de ressources. Ceci ne vaut pas seulement pour la pile V2.
 
### <a name="is-a-v2-snapshot-an-incremental-snapshot-or-full-snapshot"></a>Un instantané v2 est-il un instantané incrémentiel ou un instantané complet ?

Des instantanés incrémentiels sont utilisés pour les disques non managés. Pour les disques managés, l’instantané est un instantané complet.
