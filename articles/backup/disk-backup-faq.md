---
title: Foire aux questions sur Sauvegarde des disques Azure
description: Réponses aux questions les plus fréquentes concernant Sauvegarde des disques Azure
ms.topic: conceptual
ms.date: 01/07/2021
ms.openlocfilehash: 4c4c9f4b8388fed95a19c49b705981b9b9bce2e0
ms.sourcegitcommit: 6628bce68a5a99f451417a115be4b21d49878bb2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/18/2021
ms.locfileid: "98556620"
---
# <a name="frequently-asked-questions-about-azure-disk-backup-in-preview"></a>Foire aux questions sur Sauvegarde des disques Azure (préversion)

>[!IMPORTANT]
>Sauvegarde des disques Azure est disponible en préversion sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Pour connaître la disponibilité des régions, consultez la [matrice de prise en charge](disk-backup-support-matrix.md).
>
>[Remplissez ce formulaire](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR1vE8L51DIpDmziRt_893LVUNFlEWFJBN09PTDhEMjVHS05UWFkxUlUzUS4u) pour vous inscrire à la préversion.

Cet article répond aux questions fréquemment posées sur Sauvegarde des disques Azure. Pour plus d’informations sur la disponibilité des régions, les scénarios pris en charge et les limitations de [Sauvegarde des disques Azure](disk-backup-overview.md), consultez la [matrice de prise en charge](disk-backup-support-matrix.md).

## <a name="frequently-asked-questions"></a>Forum aux questions

### <a name="can-i-back-up-the-disk-using-the-azure-disk-backup-solution-if-the-same-disk-is-backed-up-using-azure-virtual-machine-backup"></a>Puis-je sauvegarder le disque à l’aide de la solution Sauvegarde des disques Azure si le même disque est sauvegardé à l’aide de la sauvegarde de machine virtuelle Azure ?

Sauvegarde Azure offre une prise en charge côte à côte pour la sauvegarde des disques managés à l’aide des solutions Sauvegarde des disques et [Sauvegarde des machines virtuelles Azure](backup-azure-vms-introduction.md). Cela est utile lorsque vous avez besoin d’une sauvegarde cohérente d’une application une fois par jour et de sauvegardes plus fréquentes du disque du système d’exploitation ou d’un disque de données spécifique qui soient cohérentes en cas d’incident sans influer sur les performances des applications de production.

### <a name="how-do-i-find-the-snapshot-resource-group-that-i-used-to-configure-backup-for-a-disk"></a>Comment trouver le groupe de ressources d’instantanés que j’ai utilisé pour configurer la sauvegarde d’un disque ?

Dans l’écran **Instance de sauvegarde**, vous pouvez trouver le champ Groupe de ressources d’instantanés dans la section **Informations de base**. Vous pouvez rechercher et sélectionner votre instance de sauvegarde du disque correspondant dans le centre de sauvegarde ou le coffre de sauvegarde.

![Champ Groupe de ressources d’instantanés](./media/disk-backup-faq/snapshot-resource-group.png)

### <a name="what-is-a-snapshot-resource-group"></a>Qu’est-ce qu’un groupe de ressources d’instantanés ?

Sauvegarde des disques Azure offre une sauvegarde de niveau opérationnel pour le disque managé. Autrement dit, les instantanés créés pendant les opérations de sauvegarde planifiées et à la demande sont stockées dans un groupe de ressources au sein de votre abonnement. Sauvegarde Azure offre une restauration instantanée, car les instantanés incrémentiels sont stockés dans votre abonnement. Ce groupe de ressources est appelé « groupe de ressources d’instantanés ». Pour plus d’informations, consultez [Configurer la sauvegarde](backup-managed-disks.md#configure-backup).

### <a name="why-must-the-snapshot-resource-group-be-in-same-subscription-as-that-of-the-disk-being-backed-up"></a>Pourquoi le groupe de ressources d’instantanés doit-il se trouver dans le même abonnement que celui du disque à sauvegarder ?

Vous ne pouvez pas créer d’instantané incrémentiel pour un disque particulier en dehors de l’abonnement associé à ce disque. Par conséquent, choisissez le groupe de ressources dans le même abonnement que celui du disque à sauvegarder. En savoir plus sur les [instantanés incrémentiels](https://docs.microsoft.com/azure/virtual-machines/windows/disks-incremental-snapshots-portal#restrictions) pour les disques managés.

### <a name="why-do-i-need-to-provide-role-assignments-to-be-able-to-configure-backups-perform-scheduled-and-on-demand-backups-and-restore-operations"></a>Pourquoi dois-je fournir des attributions de rôles pour pouvoir configurer des sauvegardes, effectuer des sauvegardes planifiées et à la demande et effectuer des opérations de restauration ?

Sauvegarde des disques Azure utilise l’approche de moindre privilège pour découvrir, protéger et restaurer les disques managés dans vos abonnements. Pour ce faire, Sauvegarde Azure utilise l’identité managée du [coffre de sauvegarde](backup-vault-overview.md) pour accéder à d’autres ressources Azure. Une identité managée affectée par le système est limitée à une par ressource et est liée au cycle de vie de cette ressource. Vous pouvez accorder des autorisations à l’identité managée à l’aide du contrôle d’accès en fonction du rôle Azure (Azure RBAC). L’identité managée est un principal de service d’un type spécial qui ne peut être utilisé qu’avec des ressources Azure. Découvrez-en plus sur les [identités managées](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview). Par défaut, le coffre de sauvegarde n’a pas l’autorisation d’accéder au disque à sauvegarder, de créer des instantanés récurrents, de supprimer des instantanés après la période de rétention ni de restaurer un disque à partir d’une sauvegarde. En accordant explicitement des attributions de rôles à l’identité managée du coffre de sauvegarde, vous contrôlez la gestion des autorisations sur les ressources des abonnements.

### <a name="why-does-backup-policy-limit-the-retention-duration"></a>Pourquoi la stratégie de sauvegarde limite-t-elle la durée de rétention ?

Sauvegarde des disques Azure utilise des instantanés incrémentiels, qui sont limités à 200 instantanés par disque. Pour vous permettre d’effectuer des sauvegardes à la demande en dehors des sauvegardes planifiées, la stratégie de sauvegarde limite le nombre total de sauvegardes à 180. En savoir plus sur les [instantanés incrémentiels](https://docs.microsoft.com/azure/virtual-machines/windows/disks-incremental-snapshots-portal#restrictions) pour les disques managés.

### <a name="how-does-the-hourly-and-daily-backup-frequency-work-in-the-backup-policy"></a>Comment fonctionne la fréquence de sauvegarde horaire et quotidienne dans la stratégie de sauvegarde ?

Sauvegarde des disques Azure propose plusieurs sauvegardes par jour. Si vous avez besoin de sauvegardes plus fréquentes, choisissez la fréquence de sauvegarde **Toutes les heures**. Les sauvegardes sont planifiées en fonction de l’intervalle **Temps** sélectionné. Par exemple, si vous sélectionnez **Toutes les 4 heures**, les sauvegardes sont effectuées environ toutes les 4 heures, de sorte que les sauvegardes sont réparties de manière égale sur la journée. Si une sauvegarde par jour suffit, choisissez la fréquence de sauvegarde **Tous les jours**. Dans la fréquence de sauvegarde quotidienne, vous pouvez spécifier l’heure de la journée à laquelle vos sauvegardes doivent s’effectuer. Il est important de noter que l’heure de la journée indique l’heure de début de la sauvegarde, et non l’heure de fin de la sauvegarde. Le temps nécessaire pour terminer l’opération de sauvegarde dépend de différents facteurs, notamment l’attrition entre les sauvegardes consécutives. Toutefois, Sauvegarde des disques Azure est une sauvegarde sans agent qui utilise des [instantanés incrémentiels](https://docs.microsoft.com/azure/virtual-machines/windows/disks-incremental-snapshots-portal) qui n’ont pas d’impact sur les performances des applications de production.

### <a name="why-does-the-backup-vaults-redundancy-setting-not-apply-to-the-backups-stored-in-operational-tier-the-snapshot-resource-group"></a>Pourquoi le paramètre de redondance du coffre de sauvegarde ne s’applique-t-il pas aux sauvegardes stockées dans le niveau opérationnel (le groupe de ressources d’instantanés) ?

Sauvegarde Azure utilise des [instantanés incrémentiels](https://docs.microsoft.com/azure/virtual-machines/windows/disks-incremental-snapshots-portal#restrictions) de disques managés qui stockent uniquement les modifications différentielles apportées aux disques depuis le dernier instantané sur le stockage HDD Standard, quel que soit le type de stockage du disque parent. Pour plus de fiabilité, les instantanés incrémentiels sont stockés par défaut sur un stockage redondant interzone (ZRS) dans les régions qui le prennent en charge. Actuellement, Sauvegarde des disques Azure prend en charge les sauvegardes opérationnelles des disques managés qui ne copient pas les sauvegardes dans le stockage du coffre de sauvegarde. Ainsi, le paramètre de redondance du stockage de sauvegarde du coffre de sauvegarde ne s’applique pas aux points de récupération.

### <a name="can-i-use-backup-center-to-configure-backups-and-manage-backup-instances-for-azure-disks"></a>Puis-je utiliser le centre de sauvegarde pour configurer des sauvegardes et gérer des instances de sauvegarde pour les disques Azure ?

Oui, Sauvegarde des disques Azure est intégrée au [centre de sauvegarde](backup-center-overview.md), qui fournit une **expérience de gestion unifiée unique** dans Azure pour permettre aux entreprises de régir, superviser, exploiter et analyser les sauvegardes à grande échelle. Vous pouvez également utiliser le coffre de sauvegarde pour sauvegarder, restaurer et gérer les instances de sauvegarde qui sont protégées dans le coffre.

### <a name="why-do-i-need-to-create-a-backup-vault-and-not-use-a-recovery-services-vault"></a>Pourquoi dois-je créer un coffre de sauvegarde et ne pas utiliser un coffre Recovery Services ?

Un coffre de sauvegarde est une entité de stockage dans Azure qui héberge des données de sauvegarde pour certaines charges de travail plus récentes prises en charge par Sauvegarde Azure. Vous pouvez utiliser des coffres de sauvegarde afin de stocker des données de sauvegarde pour différents services Azure, comme des serveurs Azure Database pour PostgreSQL, des disques Azure et des charges de travail plus récentes qui seront prises en charge par Sauvegarde Azure. Les coffres de sauvegarde facilitent l’organisation de vos données de sauvegarde tout en réduisant le temps nécessaire à leur gestion. Pour en savoir plus, consultez [Coffres de sauvegarde](https://docs.microsoft.com/azure/backup/backup-vault-overview).

### <a name="can-the-disk-to-be-backed-up-and-the-backup-vault-be-in-different-subscriptions"></a>Le disque à sauvegarder et le coffre de sauvegarde peuvent-ils se trouver dans des abonnements différents ?

Oui, le disque managé source à sauvegarder et le coffre de sauvegarde peuvent se trouver dans des abonnements différents.

### <a name="can-the-disk-to-be-backed-up-and-the-backup-vault-be-in-different-regions"></a>Le disque à sauvegarder et le coffre de sauvegarde peuvent-ils se trouver dans des régions différentes ?

Non. Actuellement, le disque managé source à sauvegarder et le coffre de sauvegarde doivent se trouver dans la même région.

### <a name="can-i-restore-a-disk-into-a-different-subscription"></a>Puis-je restaurer un disque dans un autre abonnement ?

Oui, vous pouvez restaurer le disque sur un abonnement différent de celui du disque managé source à partir duquel la sauvegarde est effectuée.

### <a name="can-i-back-up-multiple-disks-together"></a>Puis-je sauvegarder plusieurs disques ensemble ?

Non, les instantanés ponctuels de plusieurs disques rattachés à une machine virtuelle ne sont pas pris en charge. Pour plus d’informations, consultez [Configurer la sauvegarde](backup-managed-disks.md#configure-backup) et, pour en savoir plus sur les limitations, consultez la [matrice de prise en charge](disk-backup-support-matrix.md).

### <a name="what-are-my-options-to-back-up-disks-across-multiple-subscriptions"></a>Quelles sont les options permettant de sauvegarder des disques sur plusieurs abonnements ?

Actuellement, l’utilisation du portail Azure pour configurer la sauvegarde des disques est limitée à un maximum de 20 disques d’un même abonnement.

### <a name="what-is-a-target-resource-group"></a>Qu’est-ce qu’un groupe de ressources cible ?

Lors d’une opération de restauration, vous pouvez choisir l’abonnement et un groupe de ressources dans lequel vous souhaitez restaurer le disque. Sauvegarde Azure créera de nouveaux disques à partir du point de récupération dans le groupe de ressources sélectionné. C’est ce qu’on appelle un groupe de ressources cible. Notez que l’identité managée du coffre de sauvegarde nécessite l’attribution d’un rôle sur le groupe de ressources cible pour pouvoir effectuer l’opération de restauration. Pour plus d’informations, consultez la [documentation relative à la restauration](restore-managed-disks.md).

### <a name="what-are-the-permissions-used-by-azure-backup-during-backup-and-restore-operation"></a>Quelles sont les autorisations utilisées par Sauvegarde Azure lors des opérations de sauvegarde et de restauration ?

Voici les actions utilisées dans le rôle **Lecteur de sauvegarde de disque** attribué sur le **disque** à sauvegarder :

« Microsoft.Compute/disks/read »

« Microsoft.Compute/disks/beginGetAccess/action »

Voici les actions utilisées dans le rôle **Contributeur d’instantanés de disque** attribué sur le **groupe de ressources d’instantanés** :

« Microsoft.Compute/snapshots/delete »

« Microsoft.Compute/snapshots/write »

« Microsoft.Compute/snapshots/read »

« Microsoft.Storage/storageAccounts/write »

« Microsoft.Storage/storageAccounts/read »

« Microsoft.Storage/storageAccounts/delete »

« Microsoft.Resources/subscriptions/resourceGroups/read »

« Microsoft.Storage/storageAccounts/listkeys/action »

« Microsoft.Compute/snapshots/beginGetAccess/action »

« Microsoft.Compute/snapshots/endGetAccess/action »

« Microsoft.Compute/disks/beginGetAccess/action »

Voici les actions utilisées dans le rôle **Opérateur de restauration de disque** attribué sur le **groupe de ressources cible** :

« Microsoft.Compute/disks/write »

« Microsoft.Compute/disks/read »

« Microsoft.Resources/subscriptions/resourceGroups/read »

>[!NOTE]
>Les autorisations relatives à ces rôles peuvent changer à l’avenir, en fonction des fonctionnalités ajoutées par le service Sauvegarde Azure.

## <a name="next-steps"></a>Étapes suivantes

- [Matrice de prise en charge de sauvegarde de disques Azure](disk-backup-support-matrix.md)
