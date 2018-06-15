---
title: Mise à niveau vers le modèle de déploiement Azure Resource Manager pour la pile de sauvegarde de machine virtuelle Azure
description: Processus de mise à niveau et questions fréquentes (FAQ) pour la pile de sauvegarde de machine virtuelle, modèle de déploiement Resource Manager
services: backup, virtual-machines
author: trinadhk
manager: vijayts
tags: azure-resource-manager, virtual-machine-backup
ms.service: backup, virtual-machines
ms.topic: conceptual
ms.date: 03/08/2018
ms.author: trinadhk
ms.openlocfilehash: e822e0c354fd671ee2802506e0e268d4078b395e
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34606900"
---
# <a name="upgrade-to-the-azure-resource-manager-deployment-model-for-azure-vm-backup-stack"></a>Mise à niveau vers le modèle de déploiement Azure Resource Manager pour la pile de sauvegarde de machine virtuelle Azure
Le modèle de déploiement Resource Manager pour la mise à niveau de la pile de sauvegarde de machine virtuelle fournit les améliorations de fonctionnalités suivantes :
* Possibilité d’afficher des instantanés pris dans le cadre d’une tâche de sauvegarde qui peut être récupérée sans attendre la fin du transfert des données. Cela réduit le temps d’attente pour la copie des instantanés dans le coffre avant de déclencher la restauration. De plus, cette possibilité supprime l’exigence de stockage supplémentaire pour la sauvegarde des machines virtuelles Premium excepté pour la première sauvegarde.  

* Réduction des temps de sauvegarde et de restauration en conservant les instantanés localement pendant sept jours.

* Prise en charge de tailles de disque jusqu’à 4 To.

* Possibilité d’utiliser les comptes de stockage d’origine d’une machine virtuelle non gérée lors de la restauration. Cette possibilité existe même quand la machine virtuelle a des disques répartis entre des comptes de stockage. Cela accélère les restaurations pour de nombreuses configurations de machine virtuelle.
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
* La mise à niveau de la pile de sauvegarde de machine virtuelle est unidirectionnelle. Par conséquent, toutes les sauvegardes passent dans ce flux. En raison de son activation au niveau de l’abonnement, toutes les machines virtuelles passent dans ce flux. Tous les ajouts de nouvelles fonctionnalités reposent sur la même pile. La possibilité de contrôler cette option au niveau de la stratégie sera disponible dans de futures versions.

* Les instantanés sont stockés localement pour optimiser la création des points de récupération et également accélérer la restauration. Vous voyez donc des coûts de stockage correspondant aux instantanés pendant la période de sept jours.

* Les instantanés incrémentiels sont stockés sous la forme d’objets blob de pages. Tout client qui utilise des disques non managés est facturé pour les sept jours pendant lesquels les instantanés sont stockés dans son compte de stockage local. Selon le modèle tarifaire actuel, les disques managés n’entraînent pas de frais pour les clients.

* Si vous effectuez une restauration à partir d’un point de récupération d’instantané pour une machine virtuelle Premium, vous voyez un emplacement de stockage temporaire utilisé pendant la création de la machine virtuelle dans le cadre de la restauration.

* Dans le cas des comptes de stockage Premium, les instantanés pris pour la récupération instantanée occupent 10 To de l’espace alloué.

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
