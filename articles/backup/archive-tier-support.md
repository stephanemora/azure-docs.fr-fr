---
title: Prise en charge du niveau Archive (préversion)
description: En savoir plus sur la prise en charge du niveau Archive pour le service Sauvegarde Azure
ms.topic: conceptual
ms.date: 02/18/2021
ms.openlocfilehash: 30a7915332d1d7ecab87b0db1ddc6dacc0fa69c9
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102050612"
---
# <a name="archive-tier-support-preview"></a>Prise en charge du niveau Archive (préversion)

Les clients s’appuient sur le service Sauvegarde Azure pour stocker leurs données de sauvegarde, notamment pour une conservation à long terme (LTR), avec des exigences de conservation définies par les règles de conformité de l’organisation. Dans la plupart des cas, les données de sauvegarde plus anciennes sont rarement utilisées, et stockées uniquement à des fins de conformité.

Le service Sauvegarde Azure prend en charge la sauvegarde des points de conservation à long terme dans le niveau Archive, en plus des captures instantanées et du niveau Standard.

## <a name="scope-for-preview"></a>Étendue de la préversion

Charges de travail prises en charge :

- Machines virtuelles Azure
  - Uniquement les points de récupération mensuels et annuels. Les points de récupération quotidiens et hebdomadaires ne sont pas pris en charge.
  - Âge >= 3 mois au niveau Coffre-Standard.
  - Conservation restante >= 6 mois.
  - Aucune dépendance quotidienne ou hebdomadaire active.
- SQL Server dans des machines virtuelles Azure
  - Uniquement les points de récupération complets. Les journaux et les différentiels ne sont pas pris en charge.
  - Âge >= 45 jours au niveau Coffre-Standard.
  - Conservation restante >= 6 mois.
  - Aucune dépendance

Clients pris en charge :

- La fonctionnalité est fournie à l’aide de PowerShell.

>[!NOTE]
>La prise en charge du niveau Archive pour les machines virtuelles Azure et SQL Server dans Azure est en préversion publique limitée avec des inscriptions limitées. Pour vous inscrire à la prise en charge d’archives, utilisez ce [lien](https://aka.ms/ArchivePreviewInterestForm).

## <a name="get-started-with-powershell"></a>Bien démarrer avec PowerShell

1. Téléchargez le [module PowerShell le plus récent](https://github.com/Azure/azure-powershell/tree/Az.RecoveryServices-preview) (préversion).
1. Connectez-vous à Azure à l’aide la cmdlet [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount).
1. Connectez-vous à votre abonnement :

   `Set-AzContext -Subscription "SubscriptionName"`

1. Obtenez le coffre :

    `$vault =  Get-AzRecoveryServicesVault -ResourceGroupName "rgName" -Name "vaultName"`

1. Obtenez la liste des éléments de sauvegarde :

    `$BackupItemList = Get-AzRecoveryServicesBackupItem -vaultId $vault.ID -BackupManagementType "AzureVM/AzureWorkload" -WorkloadType "AzureVM/MSSQL"`

1. Obtenez l’élément de sauvegarde.

    - Pour des machines virtuelles Azure :

        `$bckItm = $BackupItemList | Where-Object {$_.Name -match '<vmName>'}`

    - Pour des machines virtuelles SQL Server dans Azure :

        `$bckItm = $BackupItemList | Where-Object {$_.Name -match '<dbName>' -and $_.ContainerName -match '<vmName>'}`

## <a name="use-powershell"></a>Utiliser PowerShell

### <a name="check-archivable-recovery-points"></a>Vérifier les points de récupération archivables

```azurepowershell
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -VaultId $vault.ID -Item $bckItm  -IsReadyForMove $true -TargetTier VaultArchive
```

Cette commande a pour effet de répertorier tous les points de récupération associés à un élément de sauvegarde spécifique, qui sont prêts à être déplacés vers une archive.

### <a name="check-why-a-recovery-point-cannot-be-moved-to-archive"></a>Vérifier pourquoi un point de récupération ne peut pas être déplacé vers une archive

```azurepowershell
$rp[0].RecoveryPointMoveReadinessInfo["ArchivedRP"]
```

Où `$rp[0]` est le point de récupération dont vous souhaitez savoir pourquoi il n’est pas archivable.

Exemple de sortie :

```output
IsReadyForMove  AdditionalInfo
--------------  --------------
False           Recovery-Point Type is not eligible for archive move as it is already moved to archive tier
```

### <a name="check-recommended-set-of-archivable-points-only-for-azure-vms"></a>Vérifier l’ensemble recommandé de points archivables (uniquement pour les machines virtuelles Azure)

Les points de récupération associés à une machine virtuelle sont incrémentiels par nature. Quand un point de récupération est déplacé vers l’archive, il est au préalable converti en sauvegarde complète. Les économies de coûts associées au déplacement vers une archive dépendent donc de l’évolution de la source de données.

Par conséquent, le service Sauvegarde Azure a proposé un ensemble recommandé de points de récupération permettant de réaliser des économies de coûts en cas de déplacement simultané.

>[!NOTE]
>Les économies de coûts dépendent de diverses raisons et peuvent différer pour deux instances quelconques.

```azurepowershell
$RecommendedRecoveryPointList = Get-AzRecoveryServicesBackupRecommendedArchivableRPGroup -Item $bckItm -VaultId $vault.ID
```

### <a name="move-to-archive"></a>Déplacer une archive

```azurepowershell
Move-AzRecoveryServicesBackupRecoveryPoint -VaultId $vault.ID -RecoveryPoint $rp[2] -SourceTier VaultStandard -DestinationTier VaultArchive
```

Cette commande déplace un point de récupération archivable vers UNE archive. Elle retourne un travail utilisable pour effectuer le suivi de l’opération de déplacement tant à partir du portail qu’avec PowerShell.

### <a name="view-archived-recovery-points"></a>Afficher les points de récupération archivés

Cette commande retourne tous les points de récupération archivés.

```azurepowershell
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -VaultId $vault.ID -Item $bckItm -Tier VaultArchive
```

### <a name="restore-with-powershell"></a>Restaurer avec PowerShell

Pour les points de récupération dans l’archive, le service Sauvegarde Azure fournit une méthodologie de restauration intégrée.

La restauration intégrée est un processus en deux étapes. La première étape consiste à réalimenter les points de récupération stockés dans l’archive, et à stocker celle-ci temporairement au niveau Coffre-Standard pour une durée (également appelée « durée de réalimentation ») de 10 à 30 jours. La durée par défaut est de 15 jours. Il existe deux priorités de réalimentation : Standard et Haute. Apprenez-rn davantage sur la [priorité de réalimentation](https://docs.microsoft.com/azure/storage/blobs/storage-blob-rehydration#rehydrate-an-archived-blob-to-an-online-tier).

>[!NOTE]
>
>- Une fois la durée de réalimentation sélectionnée, il n’est pas possible de la modifier et les points de récupération réalimentés restent au niveau Standard pendant la durée de réalimentation.
>- L’étape supplémentaire de réalimentation entraîne un coût.

Pour plus d’informations sur les différentes méthodes de restauration pour les machines virtuelles Azure, consultez [Restaurer une machine virtuelle Azure avec PowerShell](backup-azure-vms-automation.md#restore-an-azure-vm).

```azurepowershell
Restore-AzRecoveryServicesBackupItem -VaultLocation $vault.Location -RehydratePriority "Standard" -RehydrateDuration 15 -RecoveryPoint $rp -StorageAccountName "SampleSA" -StorageAccountResourceGroupName "SArgName" -TargetResourceGroupName $vault.ResourceGroupName -VaultId $vault.ID
```

Pour restaurer SQL Server, procédez [comme suit](backup-azure-sql-automation.md#restore-sql-dbs). Les paramètres supplémentaires requis sont **RehydrationPriority** et **RehydrationDuration**.

### <a name="view-jobs-from-powershell"></a>Afficher les travaux à partir de PowerShell

Pour afficher les travaux de déplacement et de restauration, utilisez la cmdlet PowerShell suivante :

```azurepowershell
Get-AzRecoveryServicesBackupJob -VaultId $vault.ID
```

## <a name="use-the-portal"></a>Utiliser le portail

### <a name="check-archived-recovery-point"></a>Vérifier un point de récupération archivé

Vous pouvez maintenant afficher tous les points de récupération qui ont été déplacés vers l’archive.

![Tous les points de récupération.](./media/archive-tier-support/restore-points.png)

### <a name="restore-in-the-portal"></a>Restaurer dans le portail

Pour les points de récupération qui ont été déplacés vers l’archive, la restauration nécessite que vous ajoutiez les paramètres de durée de réalimentation et de priorité de réhydratation.

![Restaurer dans le portail.](./media/archive-tier-support/restore-in-portal.png)

### <a name="view-jobs-in-the-portal"></a>Afficher les travaux dans le portail

![Afficher les travaux dans le portail.](./media/archive-tier-support/view-jobs-portal.png)

### <a name="modify-protection"></a>Modifier la protection

Vous pouvez modifier la protection d’une source de données de deux manières :

- Modifier une stratégie existante
- Protection de la source de source avec une nouvelle stratégie

Dans les deux cas, la nouvelle stratégie est appliquée à tous les points de récupération plus anciens du niveau Standard, ainsi qu’à ceux du niveau Archive. Ainsi, des points de récupération plus anciens peuvent être supprimés en cas de changement de stratégie.

Lorsque les points de récupération sont déplacés vers l’archive, ils sont soumis à une période de suppression précoce de 180 jours. Les frais sont calculés au prorata. Si un point de récupération qui n’est pas resté au niveau Archive pendant 180 jours est supprimé, cela occasionne un coût équivalent à 180 moins le nombre de jours qu’il est resté au niveau Standard.

Les points de récupération qui ne sont pas restés au niveau Archive pendant au minimum six mois occasionnent un coût de suppression précoce.

## <a name="stop-protection-and-delete-data"></a>Arrêter la protection et supprimer les données

L’option Arrêter la protection et supprimer les données supprime tous les points de récupération. Pour les points de récupération qui ne sont pas restés pendant une durée de 180 jours au niveau Archive, leur suppression occasionne un coût de suppression précoce.

## <a name="error-codes-and-troubleshooting-steps"></a>Codes d’erreur et étapes de résolution des problèmes

Il existe plusieurs codes d’erreur qui apparaissent quand un point de récupération ne peut pas être déplacé vers une archive.

### <a name="recoverypointtypenoteligibleforarchive"></a>RecoveryPointTypeNotEligibleForArchive

**Message d’erreur** : Le type de point de récupération ne peut pas être déplacé vers l’archive.

**Description** : ce code d’erreur s’affiche quand le type de point de récupération sélectionné ne peut pas être déplacé vers l’archive.

**Action recommandée** : vérifiez l’éligibilité du point de récupération [ici](#scope-for-preview).

### <a name="recoverypointhaveactivedependencies"></a>RecoveryPointHaveActiveDependencies

**Message d’erreur** : Un point de récupération avec des dépendances actives pour la restauration ne peut pas être déplacé vers l’archive.

**Description** : le point de récupération sélectionné a des dépendances actives et ne peut donc pas être déplacé vers l’archive.

**Action recommandée** : vérifiez l’éligibilité du point de récupération [ici](#scope-for-preview).

### <a name="minlifespaninstandardrequiredforarchive"></a>MinLifeSpanInStandardRequiredForArchive

**Message d’erreur** : Le point de récupération ne peut pas être déplacé vers Archive, car la durée de vie passée dans le niveau Standard de coffre est inférieure au minimum nécessaire.

**Description** : le point de récupération doit rester au niveau Standard pendant un minimum de trois mois pour les machines virtuelles Azure, et 45 jours pour SQL Server sur des machines virtuelles Azure.

**Action recommandée** : vérifiez l’éligibilité du point de récupération [ici](#scope-for-preview).

### <a name="minremaininglifespaninarchiverequired"></a>MinRemainingLifeSpanInArchiveRequired

**Message d’erreur** : La durée de vie restante du point de récupération est inférieure au minimum nécessaire.

**Description** : la durée de vie minimale requise pour un point de récupération pour l’éligibilité du déplacement d’archive est de six mois.

**Action recommandée** : vérifiez l’éligibilité du point de récupération [ici](#scope-for-preview).

### <a name="usererrorrecoverypointalreadyinarchivetier"></a>UserErrorRecoveryPointAlreadyInArchiveTier

**Message d’erreur** : Le point de récupération ne peut pas être déplacé vers Archive, car il est déjà dans le niveau Archive.

**Description** : le point de récupération sélectionné figure déjà dans l’archive. Il n’est donc pas possible de le déplacer vers l’archive.

### <a name="usererrordatasourcetypeisnotsupportedforrecommendationapi"></a>UserErrorDatasourceTypeIsNotSupportedForRecommendationApi

**Message d’erreur** : Le type de source de données n’est pas éligible pour l’API Recommandation.

**Description** : l’API Recommandation s’applique uniquement aux machines virtuelles Azure. Elle ne s’applique pas au type de source de donnée sélectionné.

### <a name="usererrorrecoverypointalreadyrehydrated"></a>UserErrorRecoveryPointAlreadyRehydrated

**Message d’erreur** : le point de récupération est déjà réalimenté. La réalimentation n’est pas autorisée sur ce point de récupération.

**Description** : le point de récupération sélectionné est déjà réalimenté.

### <a name="usererrorrecoverypointisnoteligibleforarchivemove"></a>UserErrorRecoveryPointIsNotEligibleForArchiveMove

**Message d’erreur** : Le point de récupération ne peut pas être déplacé vers Archive.

**Description** : le point de récupération sélectionné n’est pas éligible pour un déplacement vers l’archive.

### <a name="usererrorrecoverypointnotrehydrated"></a>UserErrorRecoveryPointNotRehydrated

**Message** **d’erreur** : le point de récupération d’archive n’est pas réalimenté. Réessayez la restauration une fois la réalimentation terminée sur le point de récupération Archive.

**Description** : le point de récupération n’est pas réalimenté. Essayez de restaurer après avoir réalimenté le point de récupération.

### <a name="usererrorrecoverypointrehydrationnotallowed"></a>UserErrorRecoveryPointRehydrationNotAllowed

**Message** **d’erreur** : La réalimentation n’est prise en charge que pour les points de récupération Archive.

**Description** : la réalimentation n’est pas autorisée pour le point de récupération sélectionné.

### <a name="usererrorrecoverypointrehydrationalreadyinprogress"></a>UserErrorRecoveryPointRehydrationAlreadyInProgress

**Message d’erreur** : La réhydratation est déjà en cours pour le point de récupération d’archive.

**Description** : la réalimentation du point de récupération sélectionné est déjà en cours.

### <a name="rpmovenotsupportedduetoinsufficientretention"></a>RPMoveNotSupportedDueToInsufficientRetention

**Message d’erreur** : Impossible de déplacer le point de récupération vers le niveau Archive en raison d’une durée de conservation insuffisante spécifiée dans la stratégie.

**Action recommandée** : mettez à jour la stratégie sur l’élément protégé avec le paramètre de conservation approprié, puis réessayez.

### <a name="rpmovereadinesstobedetermined"></a>RPMoveReadinessToBeDetermined

**Message d’erreur** : Nous essayons encore de déterminer si ce point de récupération peut être déplacé.

**Description** : l’état de préparation au déplacement du point de récupération n’est pas encore déterminée.

**Action recommandée** : vérifiez à nouveau après avoir attendu un certain temps.

## <a name="frequently-asked-questions"></a>Forum aux questions

### <a name="what-will-happen-to-archive-recovery-points-if-i-stop-protection-and-retain-data"></a>Que se passe-t-il pour les points de récupération d’archive si j’arrête la protection et conserve les données ?

Le point de récupération reste dans l’archive définitivement. Pour plus d’informations, consultez [Impact de l’arrêt de la protection sur les points de récupération](manage-recovery-points.md#impact-of-stop-protection-on-recovery-points).

## <a name="next-steps"></a>Étapes suivantes

- [Tarifs Sauvegarde Azure](azure-backup-pricing.md)
