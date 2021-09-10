---
title: Migrer des outils de gestion Azure depuis Azure Allemagne vers Azure global
description: Cet article fournit des informations sur la migration de vos outils de gestion Azure depuis Azure Allemagne vers Azure global.
ms.topic: article
ms.date: 06/22/2021
author: gitralf
ms.author: ralfwi
ms.service: germany
ms.custom: bfmigrate, devx-track-azurecli
ms.openlocfilehash: 157dded56e3323ed6389f84bd173ba435e8bdc5b
ms.sourcegitcommit: 30e3eaaa8852a2fe9c454c0dd1967d824e5d6f81
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/22/2021
ms.locfileid: "122523979"
---
# <a name="migrate-management-tool-resources-to-global-azure"></a>Migrer les ressources de l’outil de gestion vers Azure global

[!INCLUDE [closure info](../../includes/germany-closure-info.md)]

Cet article contient des informations qui peuvent vous aider à migrer des outils de gestion Azure depuis Azure Allemagne vers Azure global.

## <a name="traffic-manager"></a>Traffic Manager

Azure Traffic Manager peut vous aider à effectuer une migration en douceur. Toutefois, vous ne pouvez pas migrer les profils Traffic Manager que vous créez dans Azure Allemagne vers Azure global. (Lors d’une migration, vous migrez les points de terminaison Traffic Manager vers l’environnement cible. Vous devez donc quand même mettre à jour le profil Traffic Manager.)

Vous pouvez définir des points de terminaison supplémentaires dans l’environnement cible à l’aide de Traffic Manager pendant qu’il est en cours d’exécution dans l’environnement source. Lorsque Traffic Manager est en cours d’exécution dans le nouvel environnement, vous pouvez toujours définir des points de terminaison que vous n’avez pas encore migrés dans l’environnement source. Ce scénario est appelé le [scénario bleu-vert](https://azure.microsoft.com/blog/blue-green-deployments-using-azure-traffic-manager/). Ce scénario implique les étapes suivantes :

1. Créez un profil Traffic Manager dans Azure global.
1. Définissez les points de terminaison dans Azure Allemagne.
1. Modifiez votre enregistrement DNS CNAME dans le nouveau profil Traffic Manager.
1. Désactivez l’ancien profil Traffic Manager.
1. Migrez et configurez les points de terminaison. Pour chaque point de terminaison dans Azure Allemagne :
   1. Migrez le point de terminaison vers Azure global.
   1. Modifiez le profil Traffic Manager pour utiliser le nouveau point de terminaison.

Pour plus d’informations :

- Actualisez vos connaissances en suivant les [tutoriels sur Traffic Manager](../traffic-manager/index.yml).
- Examinez la [vue d’ensemble de Traffic Manager](../traffic-manager/traffic-manager-overview.md).
- En savoir plus sur la [création d’un profil Traffic Manager](../traffic-manager/quickstart-create-traffic-manager-profile.md).
- En savoir plus sur le [scénario de bleu-vert](https://azure.microsoft.com/blog/blue-green-deployments-using-azure-traffic-manager/).

## <a name="azure-backup"></a>Sauvegarde Azure

Le service Sauvegarde Azure fournit des solutions simples, sécurisées et économiques pour sauvegarder vos données et les récupérer à partir du cloud Microsoft Azure. Le déplacement des données de sauvegarde est désormais activé des régions Allemagne Centre (GEC) et Allemagne Nord-Est (GNE) vers la région Allemagne Centre-Ouest (GWC) via des cmdlets PowerShell.

### <a name="prerequisite-for-moving-hybrid-workloads"></a>Conditions préalables au déplacement des charges de travail hybrides

Une fois l’opération de déplacement démarrée, les sauvegardes sont arrêtées dans les coffres existants. Il est donc important de protéger vos données dans un nouveau coffre dans la région GWC pour les charges de travail hybrides (serveur Data Protection Manager [DPM]/serveur de sauvegarde Azure [MABS]/Microsoft Azure Recovery Services [MARS]) avant de commencer à déplacer les données de sauvegarde des régions.
Pour commencer à protéger dans un nouveau coffre :

1. Créez un nouveau coffre (VaultN) dans la région Allemagne Centre-Ouest.
1. Réinscrivez votre serveur DPM/MABS/agent MARS auprès de VaultN.
1. Attribuez une stratégie et commencez à faire des sauvegardes.

La sauvegarde initiale sera une copie complète suivie de sauvegardes incrémentielles.

>[!Important]
>- Avant de lancer l’opération de déplacement des données de sauvegarde, assurez-vous que la première sauvegarde complète dans VaultN est terminée.
>- Pour DPM/MABS, conservez la phrase secrète du coffre d’origine dans un endroit sûr, car vous en aurez besoin pour restaurer les données du coffre cible. Sans la phrase secrète d’origine, la restauration à partir du coffre source est impossible.

### <a name="step-1-download-the-resources"></a>Étape 1 : Télécharger les ressources

Téléchargez et installez les ressources requises.

1. [Téléchargez](https://github.com/PowerShell/PowerShell/releases/tag/v7.0.3) la version la plus récente de PowerShell (PowerShell 7).
1. Utilisez le module Az.RecoveryServices version 4.2.0 disponible dans Azure Cloud Shell.
1. [Mettez à jour](https://aka.ms/azurebackup_agent) tous les agents MARS à la version la plus récente.
1. Validez votre phrase secrète. Si vous devez la régénérer, suivez les [étapes de validation](https://support.microsoft.com/topic/mandatory-update-for-azure-backup-for-microsoft-azure-recovery-services-agent-411e371c-aace-e134-de6b-bf9fda48026e#section-3).

### <a name="step-2-create-a-target-vault-in-gwc"></a>Étape 2 : Créer un coffre cible dans la région Allemagne Centre-Ouest

Créez un coffre cible (Vault 2) dans la région Allemagne Centre-Ouest. Pour savoir comment créer un coffre, consultez [Créer et configurer un coffre Recovery Services](../backup/backup-create-rs-vault.md).

>[!Note]
>- Assurez-vous que le coffre ne contient aucun élément protégé.
>- Assurez-vous que le coffre cible dispose de la redondance nécessaire (stockage localement redondant [LRS] ou stockage géoredondant [GRS]).

### <a name="step-3---use-powershell-to-trigger-backup-data-move"></a>Étape 3 : Utiliser PowerShell pour déclencher le déplacement des données de sauvegarde

#### <a name="get-the-source-vault-from-gne-or-gec"></a>Récupérer le coffre source à partir des régions Allemagne Centre ou Allemagne Nord-Est

Exécutez ces cmdlets :

1. `Connect-AzAccount -Environment AzureGermanCloud`
1. `Set-AzContext -Subscription "subscriptionName"`
1. `$srcVault = Get-AzRecoveryServicesVault -name “srcVault” -ResourceGroupName “TestSourceRG”`

>[!Note]
>- `srcVault` = Coffre source
>- `TestSourceRG` = Groupe de ressources source

#### <a name="get-the-target-vault-in-gwc"></a>Récupérer le coffre cible dans la région Allemagne Centre-Ouest

Exécutez ces cmdlets :

1. `Connect-AzAccount`
1. `Set-AzContext -Subscription "subscriptionName"`
1. `$trgVault = Get-AzRecoveryServicesVault -name “targetVault” -ResourceGroupName “TestTargetRG”`

>[!Note]
>- `targetVault` = Coffre cible
>- `TestTargetRG` = Groupe de ressources test

#### <a name="perform-validation"></a>Effectuer la validation
 
Exécutez ces cmdlets :

1. `$validated = $false`
1. `$validated = Test-AzRecoveryServicesDSMove -SourceVault $srcVault -TargetVault $trgVault`

#### <a name="initializeprepare-ds-move"></a>Initialiser/préparer le déplacement DS

Exécutez ces cmdlets :

1. `Connect-AzAccount -Environment AzureGermanCloud`
1. `Set-AzContext -SubscriptionName $srcSub`
1. ```azurepowershell
   if($validated) {
           $corr = Initialize-AzRecoveryServicesDSMove -SourceVault $srcVault -TargetVault $trgVault
                       }
   ```
1. `$corr`

#### <a name="trigger-ds-move"></a>Déclencher un déplacement DS

Exécutez ces cmdlets :

1. `Connect-AzAccount`
1. `Set-AzContext -SubscriptionName $trgSub`
1. `Copy-AzRecoveryServicesVault - CorrelationIdForDataMove $corr -SourceVault $srcVault -TargetVault $trgVault -Force`

Vous pouvez surveiller l’opération à l’aide de la cmdlet `Get-AzRecoveryServicesBackupJob`.

>[!Note] 
>- Pendant l’opération de déplacement des données de sauvegarde, tous les éléments de sauvegarde sont déplacés vers un état transitoire. Dans cet état, les nouveaux points de récupération ne sont pas créés, et les anciens points de récupération ne sont pas nettoyés.
>- Comme cette fonctionnalité est activée dans les régions Allemagne Centre et Allemagne Nord-Est, nous vous recommandons d’effectuer ces étapes sur un petit coffre et de valider le déplacement. En cas de réussite, effectuez ces étapes sur tous les coffres.
>- Outre le fait que le déplacement des données de sauvegarde est déclenché pour l’ensemble du coffre, le déplacement se fait par conteneur (machines virtuelles, serveurs DPM et MABS et agents MARS). Suivez la progression des déplacements par conteneur dans la section **Travaux**. 

 ![surveiller la progression des travaux de déplacement](./media/germany-migration-management-tools/track-move-jobs.png)

Pendant l’opération de déplacement, les actions suivantes sont bloquées sur le coffre source :

- Nouvelles sauvegardes planifiées
- Arrêter la sauvegarde avec suppression des données
- Supprimer des données
- Reprendre la sauvegarde
- Modifier la stratégie

### <a name="step-4-check-the-status-of-the-move-job"></a>Étape 4 : Vérifier l’état du travail de déplacement

L’opération de déplacement des données de sauvegarde se fait par conteneur. Pour les sauvegardes de machines virtuelles Azure, les sauvegardes de machines virtuelles sont considérées comme des conteneurs. Pour indiquer la progression de l’opération de déplacement des données de sauvegarde, un travail est créé pour chaque conteneur.

Pour surveiller les travaux, exécutez ces cmdlets :

1. `Get-AzRecoveryServicesBackupJob -Operation BackupDataMove -VaultId $trgVault.ID`
1. `$Jobs = Get-AzRecoveryServicesBackupJob -Operation BackupDataMove -VaultId $trgVault.ID`
1. `Get-AzRecoveryServicesBackupJobDetail -Job $Jobs[0] -VaultId $trgVault.ID`
1. `$JobDetails.ErrorDetails`

### <a name="step-5-post-move-operations"></a>Étape 5 : Opérations post-déplacement

Une fois que l’opération de déplacement des données de sauvegarde de tous les conteneurs vers le coffre cible est terminée, aucune autre action n’est requise pour les sauvegardes de machines virtuelles.



#### <a name="verify-the-movement-of-containers-is-complete"></a>Vérifier que le déplacement des conteneurs est terminé

Pour vérifier si tous les conteneurs du coffre source ont été déplacés vers le coffre cible, accédez au coffre cible et recherchez tous les conteneurs dans ce coffre.

Exécutez la cmdlet suivante pour répertorier toutes les machines virtuelles déplacées du coffre source au coffre cible :

```azurepowershell
Get-AzRecoveryServicesBackupContainer -BackupManagementType “AzureVM” -VaultId $trgVault.ID
```

#### <a name="verify-the-movement-of-policies-is-complete"></a>Vérifier que le déplacement des stratégies est terminé

Une fois que les données de sauvegarde ont été correctement déplacées vers la nouvelle région, toutes les stratégies qui ont été appliquées aux éléments de sauvegarde de la machine virtuelle Azure dans le coffre source sont appliquées au coffre cible.

Pour vérifier si toutes les stratégies ont été déplacées du coffre source au coffre cible, accédez au coffre cible et exécutez la cmdlet suivante pour obtenir la liste de toutes les stratégies déplacées :

```azurepowershell
Get-AzRecoveryServicesBackupProtectionPolicy -VaultId $trgVault.ID
```

Ces stratégies continuent de s’appliquer sur vos données de sauvegarde après l’opération de déplacement afin de poursuivre la gestion du cycle de vie des points de récupération déplacés.

Pour éviter le nettoyage soudain de plusieurs points de récupération (qui peuvent avoir expiré pendant le processus de déplacement ou expirer immédiatement après le processus de déplacement), le nettoyage des points de récupération plus anciens est suspendu pendant une période de 10 jours après le déplacement. Pendant cette période, les données supplémentaires liées aux anciens points de récupération ne vous sont pas facturées.

>[!Important]
>Si vous avez besoin de récupérer à partir de ces points de récupération plus anciens, récupérez-les immédiatement pendant cette période de 10 jours. Une fois cette période de sécurité écoulée, les stratégies appliquées à chacun des éléments de sauvegarde prendront effet et mettront en œuvre le nettoyage des points de récupération plus anciens.

#### <a name="restore-operations"></a>Opérations de restauration

**Restaurer des machines virtuelles Azure**

Pour les machines virtuelles Azure, vous pouvez restaurer à partir des points de récupération dans le coffre cible.

#### <a name="configure-mars-agent"></a>Configurer l’agent MARS

1. Inscrivez-vous à nouveau dans le coffre cible.
1. Effectuez une restauration à partir des points de récupération.
1. Réinscrivez Post Recovery dans le nouveau coffre (VaultN) et reprenez les sauvegardes.

>[!Note]
>Tant que l’agent MARS est inscrit dans le coffre cible, aucune nouvelle sauvegarde n’est effectuée.

#### <a name="configure-dpmmabs"></a>Configurer DPM/MABS

**Recommandé**

Utilisez la méthode DPM externe pour effectuer la restauration. Pour plus d’informations, consultez [Récupérer des données à partir du serveur de sauvegarde Azure](../backup/backup-azure-alternate-dpm-server.md).

>[!Note]
>- La récupération à l’emplacement d’origine (OLR) n’est pas prise en charge.
>- Les sauvegardes se poursuivront dans VaultN pour toutes les machines inscrites.

**Autre option**

Pour la récupération à l’emplacement d’origine (OLR) :

1. Réinscrivez le serveur DPM/MABS dans le coffre cible.
1. Effectuez une opération de restauration.
1. Réinscrivez le serveur DPM/MABS dans le nouveau coffre.

>[!Note]
>Limites de l’utilisation de DPM : <br><br> <ul><li>L’opération de sauvegarde de toutes les machines inscrites sur le serveur DPM s’arrête lorsque vous connectez le serveur DPM au coffre cible.</li><li>Une fois le serveur DPM réinscrit dans le nouveau coffre après la restauration, des vérifications de cohérence ont lieu (le temps nécessaire à ces vérifications dépend de la quantité de données) avant de reprendre les sauvegardes.</li></ul>

### <a name="error-codes"></a>Codes d’erreur

#### <a name="usererrorconflictingdatamoveonvault"></a>UserErrorConflictingDataMoveOnVault 

**Message :** Une autre opération de déplacement de données est en cours sur le coffre. 

**Scénario :** Vous essayez d’effectuer une opération de déplacement de données sur un coffre source, alors qu’une autre opération de déplacement de données est déjà en cours sur le même coffre source.

**Action recommandée :** Attendez que l’opération de déplacement de données en cours se termine, puis réessayez.

#### <a name="usererroroperationnotallowedduringdatamove"></a>UserErrorOperationNotAllowedDuringDataMove

**Message :** Cette opération n’est pas autorisée, car une opération de déplacement de données est en cours. 

**Scénario :** Pendant que l’opération de déplacement de données est en cours, les opérations suivantes ne sont pas autorisées dans le coffre source :
 
- Arrêter la sauvegarde avec conservation des données 
- Arrêter la sauvegarde avec suppression des données 
- Supprimer des données de sauvegarde 
- Reprendre la sauvegarde 
- Modifier la stratégie

**Action recommandée :** Attendez que l’opération de déplacement de données se termine, puis réessayez. [En savoir plus](#azure-backup) sur les opérations prises en charge.

#### <a name="usererrornocontainersfoundfordatamove"></a>UserErrorNoContainersFoundForDataMove 

**Message :** Aucun conteneur de ce coffre n’est pris en charge pour l’opération de déplacement de données. 

**Scénario :** Ce message s’affiche si :

- Le coffre source ne contient aucun conteneur. 
- Le coffre source ne contient que des conteneurs qui ne sont pas pris en charge. 
- Le coffre source contient tous les conteneurs qui ont été précédemment déplacés vers un coffre cible et que vous avez indiqué IgnoreMoved = true dans l’API.

**Action recommandée :** [Renseignez-vous](#azure-backup) sur les conteneurs pris en charge pour le déplacement des données.

#### <a name="usererrordatamovenotsupportedatcontainerlevel"></a>UserErrorDataMoveNotSupportedAtContainerLevel 

**Message :** L’opération de déplacement de données n’est pas prise en charge au niveau du conteneur. 

**Scénario :** Vous avez choisi une opération de déplacement de données au niveau du conteneur. 

**Action recommandée :** Essayez l’opération de déplacement de données au niveau du coffre.

### <a name="usererrordatamovenotallowedcontainer-registrationinprogress"></a>UserErrorDataMoveNotAllowedContainer RegistrationInProgress 

**Message :** L’opération de déplacement de données n’est pas autorisée, car une opération d’inscription de conteneur est en cours dans le coffre source. 

**Scénario :** Une opération d’inscription de conteneur était en cours dans le coffre source lorsque vous avez essayé de déplacer des données. 

**Action recommandée :** Essayez l’opération de déplacement de données après un certain temps.

#### <a name="usererrordatamovenotallowedtargetvaultnotempty"></a>UserErrorDataMoveNotAllowedTargetVaultNotEmpty 

**Message :** L’opération de déplacement de données n’est pas autorisée, car le coffre cible a des conteneurs déjà inscrits. 

**Scénario :** Certains conteneurs sont déjà inscrits dans le coffre cible choisi. 

**Action recommandée :** Essayez l’opération de déplacement de données sur un coffre cible vide.

#### <a name="usererrorunsupportedsourceregionfordatamove"></a>UserErrorUnsupportedSourceRegionForDataMove 

**Message :** L’opération de déplacement de données n’est pas prise en charge depuis cette région. 

**Scénario :** La région source n’est pas valide.

**Action recommandée :** Vérifiez la [liste des régions prises en charge](#azure-backup) pour le déplacement des données.

#### <a name="usererrorunsupportedtargetregionfordatamove"></a>UserErrorUnsupportedTargetRegionForDataMove 

**Message :** L’opération de déplacement de données n’est pas prise en charge vers cette région.

**Scénario :** L’ID de la région cible n’est pas valide. 

**Action recommandée :** Vérifiez la [liste des régions prises en charge](#azure-backup) pour le déplacement des données.


#### <a name="usererrordatamovetargetvaultwithprivate-endpointnotsupported"></a>UserErrorDataMoveTargetVaultWithPrivate EndpointNotSupported 

**Message :** Impossible de déplacer les données, car le coffre cible sélectionné a des points de terminaison privés. 

**Scénario :** Des points de terminaison privés sont activés dans le coffre cible. 

**Action recommandée :** Supprimez les points de terminaison privés et recommencez l’opération de déplacement. [En savoir plus](#azure-backup) sur les opérations prises en charge.

### <a name="usererrordatamovesourcevaultwithprivate-endpointnotsupported"></a>UserErrorDataMoveSourceVaultWithPrivate EndpointNotSupported 

**Message :** Impossible de déplacer les données, car le coffre source sélectionné a des points de terminaison privés.

**Scénario :** Des points de terminaison privés sont activés dans le coffre source.

**Action recommandée :** Supprimez les points de terminaison privés et recommencez l’opération de déplacement. [En savoir plus](../backup/private-endpoints.md#deleting-private-endpoints) sur les opérations prises en charge.

#### <a name="usererrordatamovesourcevaultwithcmk-notsupported"></a>UserErrorDataMoveSourceVaultWithCMK NotSupported 

**Message :** Impossible de déplacer les données, car le chiffrement du coffre source sélectionné est activé. 

**Scénario :** Les clés gérées par le client (CMK) sont activées dans le coffre source.

**Action recommandée :** [Renseignez-vous](#azure-backup) sur les opérations prises en charge.

#### <a name="usererrordatamovetargetvaultwithcmknotsupported"></a>UserErrorDataMoveTargetVaultWithCMKNotSupported 

**Message :** Impossible de déplacer les données, car le chiffrement du coffre cible sélectionné est activé. 

**Scénario :** Les clés gérées par le client (CMK) sont activées dans le coffre cible.

**Action recommandée :** [Renseignez-vous](#azure-backup) sur les opérations prises en charge.

## <a name="scheduler"></a>Scheduler

La mise hors service d’Azure Scheduler est en cours. Pour créer des tâches de planification, vous pouvez utiliser [Azure Logic Apps](../logic-apps/logic-apps-overview.md) dans Azure global à la place.

Pour plus d’informations :

- Apprenez-en davantage en suivant les [tutoriels Azure Logic Apps](../logic-apps/tutorial-build-schedule-recurring-logic-app-workflow.md).
- Examinez la [vue d’ensemble d’Azure Logic Apps](../logic-apps/logic-apps-overview.md).

## <a name="network-watcher"></a>Network Watcher

Actuellement, il n’est pas possible de migrer une instance Azure Network Watcher depuis Azure Allemagne vers Azure global. Nous vous recommandons de créer et de configurer une nouvelle instance Network Watcher dans Azure global. Ensuite, comparez les résultats entre l’ancien et le nouvel environnement. 

Pour plus d’informations :

- Actualisez vos connaissances en suivant les [tutoriels sur Network Watcher](../network-watcher/index.yml).
- Examinez la [vue d’ensemble de Network Watcher](../network-watcher/network-watcher-monitoring-overview.md).
- En savoir plus sur les [journaux d’activité des flux de groupe de sécurité réseau](../network-watcher/network-watcher-nsg-flow-logging-portal.md).
- Apprenez-en davantage sur le [moniteur de connexion](../network-watcher/connection-monitor.md).

## <a name="site-recovery"></a>Site Recovery

Vous ne pouvez pas migrer votre configuration Azure Site Recovery actuelle vers Azure global. Vous devez configurer une nouvelle solution Site Recovery dans Azure global.

Pour plus d’informations sur Site Recovery et pour savoir comment migrer des machines virtuelles depuis Azure Allemagne vers Azure global, consultez [Comment utiliser Site Recovery](./germany-migration-compute.md#compute-iaas).

Actualisez vos connaissances en complétant ces tutoriels pas à pas :

- [Récupération d'urgence d'Azure vers Azure](../site-recovery/azure-to-azure-about-networking.md)
- [Récupération d’urgence de VMware vers Azure](../site-recovery/site-recovery-deployment-planner.md)
- [Récupération d’urgence de Hyper-V vers Azure](../site-recovery/hyper-v-deployment-planner-overview.md)

## <a name="azure-policies"></a>Stratégies Azure

Vous ne pouvez pas migrer directement des stratégies depuis Azure Allemagne vers Azure global. En règle générale, l’étendue des stratégies assignées change durant la migration. Cela est particulièrement vrai lorsque l’abonnement est différent dans l’environnement cible, comme dans ce scénario. Toutefois, vous pouvez conserver les définitions de stratégie et les réutiliser dans Azure global.

Dans Azure CLI, exécutez la commande suivante pour répertorier toutes les stratégies de votre environnement actuel.

> [!NOTE]
> Veillez à basculer vers l’environnement AzureGermanCloud dans Azure CLI avant d’exécuter les commandes suivantes.


```azurecli
az policy definition list --query '[].{Type:policyType,Name:name}' --output table
```

Exportez uniquement les stratégies qui ont la valeur **PolicyType****personnalisée**. Exportez **policyRule** dans un fichier. L’exemple suivant exporte la stratégie personnalisée « Allow Germany Central Only » (version courte : `allowgconly`) vers un fichier dans le dossier actuel : 

```azurecli
az policy definition show --name allowgconly --output json --query policyRule > policy.json
```

Le fichier d’exportation ressemblera à l’exemple suivant :

```json
{
  "if": {
    "not": {
      "equals": "germanycentral",
      "field": "location"
    }
  },
  "then": {
    "effect": "Deny"
  }
}
```

Ensuite, basculez vers l’environnement Azure global. Modifiez la règle de stratégie en modifiant le fichier. Par exemple, remplacez `germanycentral` par `westeurope`.

```json
{
  "if": {
    "not": {
      "equals": "westeurope",
      "field": "location"
    }
  },
  "then": {
    "effect": "Deny"
  }
}
```

Créez la nouvelle stratégie :

```azurecli
cat policy.json |az policy definition create --name "allowweonly" --rules @-
```

Vous disposez maintenant d’une nouvelle stratégie nommée `allowweonly`. La stratégie n’accepte que la région Europe Ouest.

Attribuez la stratégie aux étendues de votre nouvel environnement selon vos besoins. Vous pouvez renseigner les anciennes affectations dans Azure Allemagne en exécutant la commande suivante :

```azurecli
az policy assignment list
```

Pour plus d’informations :

- Actualisez vos connaissances en complétant les [tutoriels sur les stratégies Azure](../governance/policy/tutorials/create-and-manage.md).
- Découvrez comment [afficher les stratégies à l’aide de l’interface Azure CLI](../governance/policy/tutorials/create-and-manage.md#view-policy-definitions-with-azure-cli) ou [afficher des stratégies à l’aide de PowerShell](../governance/policy/tutorials/create-and-manage.md#view-policy-definitions-with-powershell).
- Découvrez comment [créer une définition de stratégie à l’aide de l’interface Azure CLI](../governance/policy/tutorials/create-and-manage.md#create-a-policy-definition-with-azure-cli) ou [créer une définition de stratégie à l’aide de PowerShell](../governance/policy/tutorials/create-and-manage.md#create-a-policy-definition-with-powershell).

## <a name="frequently-asked-questions"></a>Forum aux questions

### <a name="where-can-i-move-the-backup-data"></a>Où puis-je déplacer les données de sauvegarde ?

Vous pouvez déplacer vos données de sauvegarde des coffres Recovery Services des régions Allemagne Centre et Allemagne Nord-Est vers la région Allemagne Centre-Ouest.

### <a name="what-backup-data-can-i-move"></a>Quelles sont les données de sauvegarde que je peux déplacer ?

À partir du 21 juin 2021, vous pouvez déplacer les données de sauvegarde des charges de travail suivantes d’une région à l’autre :

- Machines virtuelles Azure
- Charges de travail hybrides
- Sauvegarde de fichiers/dossiers à l’aide de l’agent Microsoft Azure Recovery Services (MARS)
- Serveur Data Protection Manager (DPM)
- Serveur Azure Backup (MABS)

### <a name="how-can-i-move-backup-data-to-another-region"></a>Comment puis-je déplacer les données de sauvegarde vers une autre région ?

Pour garantir que les données des régions existantes ne sont pas perdues, Sauvegarde Azure a activé le déplacement des données de sauvegarde des régions Allemagne Centre et Allemagne Nord-Est vers la région Allemagne Centre-Ouest.

Pendant la migration, les sauvegardes s’arrêtent dans les régions Allemagne Centre et Allemagne Nord-Est. Il est donc essentiel de protéger les charges de travail dans la nouvelle région avant de commencer l’opération de migration.

### <a name="what-to-do-if-the-backup-data-move-operation-fails"></a>Que faire si l’opération de déplacement des données de sauvegarde échoue ?

Elle peut échouer en raison des scénarios d’erreur suivants :

| Messages d’erreur    | Causes |
| --- | --- |
| Indiquez un coffre cible vide. Le coffre cible ne doit pas contenir d’éléments ni de conteneurs de sauvegarde. | Vous avez choisi un coffre cible qui contient déjà des éléments protégés. |
| Les données de Sauvegarde Azure ne peuvent être déplacées que vers des régions cibles prises en charge. | Vous avez choisi un coffre cible dans une région qui ne fait pas partie des régions prises en charge pour le déplacement. |

Vous devez effectuer une nouvelle tentative de sauvegarde à partir de zéro en exécutant la même commande (ci-dessous) avec un nouveau coffre cible vide, ou vous pouvez réessayer et déplacer les éléments ayant échoué depuis le coffre source en l’indiquant avec un indicateur.

```azurepowershell
   if($validated) {
                              $corr = Initialize-AzRecoveryServicesDSMove -SourceVault $srcVault -TargetVault $trgVault -RetryOnlyFailed
                      }
```

### <a name="is-there-a-cost-involved-in-moving-this-backup-data"></a>Le déplacement de ces données de sauvegarde entraîne-t-il un coût ?

Non. Le déplacement de vos données de sauvegarde d’une région à une autre n’entraîne aucun coût supplémentaire. Sauvegarde Azure prend en charge le coût du déplacement des données entre les régions. Une fois l’opération de déplacement terminée, vous disposerez d’une période non facturée de 10 jours seulement. Après cette période, la facturation commencera dans le coffre cible.

### <a name="if-i-face-issues-in-moving-backup-data-whom-should-i-contact"></a>Si je rencontre des problèmes lors du déplacement des données de sauvegarde, qui dois-je contacter ?

Pour tout problème concernant le déplacement de données de sauvegarde des régions Allemagne Centre ou Allemagne Nord-Est vers la région Allemagne Centre-Ouest, écrivez-nous à l’adresse [GESupportAzBackup@microsoft.com](mailto:GESupportAzBackup@microsoft.com).

## <a name="next-steps"></a>Étapes suivantes

Informez-vous sur les outils, techniques et suggestions pour migrer des ressources dans les catégories de service suivantes :

- [Calcul](./germany-migration-compute.md)
- [Réseau](./germany-migration-networking.md)
- [Stockage](./germany-migration-storage.md)
- [Web](./germany-migration-web.md)
- [Bases de données](./germany-migration-databases.md)
- [Analyse](./germany-migration-analytics.md)
- [IoT](./germany-migration-iot.md)
- [Intégration](./germany-migration-integration.md)
- [Identité](./germany-migration-identity.md)
- [Sécurité](./germany-migration-security.md)
- [Média](./germany-migration-media.md)
