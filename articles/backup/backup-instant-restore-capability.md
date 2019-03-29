---
title: Fonctionnalité de restauration instantanée Azure
description: Présentation de la fonctionnalité de restauration instantanée et questions fréquentes (FAQ) sur la pile de sauvegarde de machine virtuelle et le modèle de déploiement Resource Manager
services: backup
author: sogup
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: sogup
ms.openlocfilehash: 21aa01ec8382341de34cca743b9e088598872659
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2019
ms.locfileid: "58578898"
---
# <a name="get-improved-backup-and-restore-performance-with-azure-backup-instant-restore-capability"></a>Améliorer les performances de sauvegarde et de restauration avec la fonctionnalité de restauration instantanée de Sauvegarde Azure

> [!NOTE]
> Suite aux commentaires des utilisateurs, nous avons renommé la **Pile de sauvegarde de machine virtuelle V2** **Restauration instantanée** pour éviter toute confusion avec les fonctionnalités Azure Stack.

Le nouveau modèle pour la restauration instantanée fournit les améliorations de fonctionnalités suivantes :

* Possibilité d’utiliser des instantanés pris dans le cadre d’une tâche de sauvegarde qui peut être récupérée sans attendre la fin du transfert des données dans le coffre. Cela réduit le temps d’attente pour la copie des instantanés dans le coffre avant de déclencher la restauration.
* Réduit les temps de sauvegarde et de restauration en conservant les instantanés localement pendant deux jours par défaut. Cette valeur par défaut est configurable à volonté entre 1 et 5 jours.
* Prise en charge des disques d’une taille maximale de 4 To
* Prend en charge les disques SSD Standard, ainsi que les disques de disque dur Standard et Premium SSD.
*   Possibilité d’utiliser les comptes de stockage d’origine d’une machine virtuelle non gérée (par disque) lors de la restauration. Cette possibilité existe même quand la machine virtuelle a des disques répartis entre des comptes de stockage. Ceci accélère les opérations de restauration pour une grande variété de configurations de machine virtuelle.


## <a name="whats-new-in-this-feature"></a>Nouveautés de cette fonctionnalité

Aujourd’hui, la tâche de sauvegarde consiste en deux phases :

1.  Prise d’un instantané de la machine virtuelle.
2.  Transfert d’un instantané de la machine virtuelle vers le coffre Recovery Services.

Un point de récupération est considéré comme créé seulement après l’exécution des étapes 1 et 2. Dans le cadre de cette mise à niveau, un point de récupération est créé dès que l’instantané est terminé, et vous pouvez utiliser ce point de récupération de type instantané pour effectuer une restauration en utilisant le même flux de restauration. Vous pouvez identifier ce point de récupération dans le portail Azure à l’aide de « instantané » en tant que le type de point de récupération, et une fois la capture instantanée est transférée vers le coffre, le type de point de récupération devient « instantané et coffre ».

![Tâche de sauvegarde dans le modèle de déploiement Resource Manager pour la pile de sauvegarde de machine virtuelle : stockage et coffre](./media/backup-azure-vms/instant-rp-flow.png)

Par défaut, les instantanés sont conservés pendant deux jours. Cette fonctionnalité permet la restauration à partir de ces instantanés il en réduisant les délais de restauration. Il réduit le temps requis pour transformer et de copier des données depuis un coffre.

## <a name="feature-considerations"></a>Considérations sur la fonctionnalité

* Les instantanés sont stockés avec les disques afin d’accélérer la création des points de récupération et les opérations de restauration. Vous voyez donc des coûts de stockage correspondant aux instantanés pris pendant cette période.
* Les instantanés incrémentiels sont stockés sous la forme d’objets blob de pages. Tout utilisateur qui utilise des disques non managés est facturé pour les instantanés stockés dans son compte de stockage local. Étant donné que les collections de points de restauration utilisées par les sauvegardes de machine virtuelle managée utilisent des instantanés d’objet blob au niveau du stockage sous-jacent, pour les disques managés vous voyez les coûts correspondant au tarif de l’instantané d’objet blob et ils sont incrémentiels.
* Pour les comptes de stockage premium, les instantanés pris pour le nombre de points de récupération instantanée dans le nombre maximal de 10 To de l’espace alloué.
* Vous pouvez configurer la rétention des instantanés en fonction des besoins de restauration. Selon vos exigences, vous pouvez définir la rétention des instantanés pendant au moins un jour dans le panneau de stratégie de sauvegarde, comme cela est expliqué ci-dessous. Cela peut vous aider à réduire les coûts de rétention des instantanés si vous n’effectuez pas fréquemment de restaurations.
* Il s’agit d’une mise à un niveau directionnel, une fois mis à niveau vers la restauration instantanée, vous ne pouvez pas revenir en arrière.

>[!NOTE]
>Avec cette mise à niveau de restauration instantanée, la durée de rétention d’instantanés de tous les clients (**tant nouveaux et qu’existants**) est définie sur une valeur par défaut de deux jours. Vous pouvez cependant définir une durée de 1 à 5 jours en fonction de vos besoins.

## <a name="cost-impact"></a>Impact sur les coûts

Les instantanés incrémentiels sont stockés dans le compte de stockage de la machine virtuelle et sont utilisés pour la récupération instantanée. Le fait que l’instantané soit incrémentiel signifie que l’espace occupé par un instantané est égal à l’espace occupé par les pages qui sont écrites après la création de l’instantané. La facturation concerne toujours l’espace utilisé (par Go) occupé par l’instantané, et le prix par Go est identique à celui mentionné dans la [page de tarification](https://azure.microsoft.com/pricing/details/managed-disks/).

>[!NOTE]
> Rétention des captures instantanées est fixée à 5 jours pour les stratégies hebdomadaire.

## <a name="configure-snapshot-retention-using-the-azure-portal"></a>Configurer la rétention des captures instantanées à l’aide du portail Azure

**Tous les utilisateurs des sauvegarde Azure ont été mis à niveau à la restauration instantanée**.

Dans le portail Azure, vous pouvez voir un champ ajouté dans le **stratégie de sauvegarde de machine virtuelle** panneau sous le **la restauration instantanée** section. Vous pouvez modifier la durée de rétention des instantanés à partir du panneau **Stratégie de sauvegarde de machine virtuelle** pour toutes les machines virtuelles associées à la stratégie de sauvegarde spécifique.

![Fonctionnalité de restauration instantanée](./media/backup-azure-vms/instant-restore-capability.png)

Pour configurer la rétention des captures instantanées à l’aide de Powershell, consultez [ce document](backup-azure-vms-automation.md#configuring-instant-restore-snapshot-retention).

## <a name="frequently-asked-questions"></a>Questions fréquentes (FAQ)

### <a name="what-are-the-cost-implications-of-instant-restore"></a>Quelles sont les implications en matière de coût de la restauration instantanée ?
Les instantanés sont stockés avec les disques afin d’accélérer la création des points de récupération et les opérations de restauration. Ainsi, vous verrez des coûts de stockage qui correspondent à la conservation d’instantané sélectionnée dans le cadre de la stratégie de sauvegarde de machine virtuelle.

### <a name="in-premium-storage-accounts-do-the-snapshots-taken-for-instant-recovery-point-occupy-the-10-tb-snapshot-limit"></a>Dans les comptes de stockage Premium, les instantanés pris pour le point de récupération instantanée occupent la limite de capture instantanée de 10 To ?
Oui, pour les comptes de stockage premium, les instantanés pris pour le point de récupération instantanée occupent 10 To d’espace de l’instantané alloué.

### <a name="how-does-the-snapshot-retention-work-during-the-five-day-period"></a>Comment fonctionne la conservation des instantanés pendant la période de cinq jours ?
Chaque jour un nouvel instantané est pris, et il existe cinq instantanés incrémentiels. La taille de l’instantané dépend de l’activité des données, qui se trouvent dans la plupart des cas environ 2 à 7 %.

### <a name="is-an-instant-restore-snapshot-an-incremental-snapshot-or-full-snapshot"></a>Un instantané de restauration instantanée est-il un instantané incrémentiel ou un instantané complet ?
Les instantanés créés dans le cadre de la fonctionnalité de restauration instantanée sont des instantanés incrémentiels.

### <a name="how-can-i-calculate-the-approximate-cost-increase-due-to-instant-restore-feature"></a>Comment faire pour calculer une estimation de l’augmentation du coût due à la fonctionnalité de restauration instantanée ?
Cela dépend du taux d’activité de la machine virtuelle. Dans un état stable, vous pouvez supposer que l’augmentation du coût est égale au rapport entre la période de rétention des instantanés et le taux d’activité quotidien en fonction du coût de stockage en machine virtuelle par Go.

### <a name="if-the-recovery-type-for-a-restore-point-is-snapshot-and-vault-and-i-perform-a-restore-operation-which-recovery-type-will-be-used"></a>Si le type de récupération pour un point de restauration est « instantané et coffre » et que j’effectue une opération de restauration, quel sera le type de récupération utilisé ?
Si le type de récupération est « instantané et coffre », la restauration sera effectuée automatiquement à partir de l’instantané local, ce qui sera beaucoup plus rapide que la restauration effectuée à partir du coffre.

### <a name="what-happens-if-i-select-retention-period-of-restore-point-tier-2-less-than-the-snapshot-tier1-retention-period"></a>Que se passe-t-il si je sélectionne une période de conservation du point de restauration (niveau 2) inférieure à la période de conservation d’instantané (niveau 1) ?
Le nouveau modèle n’autorise la suppression du point de restauration (niveau 2) que si l’instantané (niveau 1) est supprimé. Nous vous recommandons de planifier une période de rétention du point de restauration (niveau 2) supérieure à la période de rétention des instantanés.

### <a name="why-is-my-snapshot-existing-even-after-the-set-retention-period-in-backup-policy"></a>Pourquoi mon instantané existe-t-il même après la période de conservation définie dans la stratégie de sauvegarde ?
Si le point de récupération a un instantané et qu’il s’agit du dernier point de récupération disponible, il est conservé jusqu’à la prochaine sauvegarde réussie. C’est conforme à la stratégie de GC actuelle, qui exige qu’au moins un point de récupération récent soit toujours présent au cas où toutes les sauvegardes ultérieures échoueraient en raison d’un problème sur la machine virtuelle. Dans les scénarios ordinaires, les points de récupération sont nettoyés au maximum 24 heures après leur expiration.
