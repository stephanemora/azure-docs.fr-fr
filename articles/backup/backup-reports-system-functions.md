---
title: Fonctions système sur les journaux Azure Monitor
description: Créer des requêtes personnalisées sur les journaux Azure Monitor à l'aide de fonctions système
ms.topic: conceptual
ms.date: 03/01/2021
ms.openlocfilehash: acb45e6ad0250a1f8d10377fdd509e40051f25b9
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105564906"
---
# <a name="system-functions-on-azure-monitor-logs"></a>Fonctions système sur les journaux Azure Monitor

Le service Sauvegarde Azure fournit un ensemble de fonctions, appelées fonctions système ou fonctions de solution, qui sont disponibles par défaut dans vos espaces de travail Log Analytics (LA).
 
Ces fonctions opèrent sur les données figurant dans les [tables Sauvegarde Azure brutes](./backup-azure-reports-data-model.md) de LA, et renvoient les données mises en forme pour vous permettre de récupérer facilement les informations de toutes vos entités en lien avec la sauvegarde, à l'aide de requêtes simples. Les utilisateurs peuvent transmettre des paramètres à ces fonctions pour filtrer les données renvoyées par celles-ci. 

Nous vous recommandons d'utiliser les fonctions système lors de l'interrogation de vos données de sauvegarde dans les espaces de travail LA afin de créer des rapports personnalisés, car celles-ci présentent un certain nombre d'avantages, comme détaillé dans la section ci-dessous.

## <a name="benefits-of-using-system-functions"></a>Avantages des fonctions système

* **Des requêtes plus simples** : l'utilisation de fonctions vous permet de réduire le nombre de jointures nécessaires dans vos requêtes. Par défaut, les fonctions renvoient des schémas « aplatis » qui intègrent toutes les informations relatives à l'entité (instance de sauvegarde, travail, coffre, etc.) interrogée. Par exemple, si vous avez besoin d'une liste de travaux de sauvegarde réussis par nom d'élément de sauvegarde et conteneur associé, un simple appel à la fonction **_AzureBackup_getJobs()** vous donnera toutes les informations que vous recherchez pour chaque travail. A contrario, l'interrogation directe des tables brutes vous obligerait à effectuer plusieurs jointures entre les tables [AddonAzureBackupJobs](./backup-azure-reports-data-model.md#addonazurebackupjobs) et [CoreAzureBackup](./backup-azure-reports-data-model.md#coreazurebackup).

* **Transition plus fluide à partir de l'événement de diagnostic hérité** : l'utilisation de fonctions système vous permet de passer en douceur de l'[événement de diagnostic hérité](./backup-azure-diagnostic-events.md#legacy-event) (AzureBackupReport en mode AzureDiagnostics) aux [événements spécifiques aux ressources](./backup-azure-diagnostic-events.md#diagnostics-events-available-for-azure-backup-users). Toutes les fonctions système fournies par le service Sauvegarde Azure vous permettent de spécifier un paramètre pour déterminer si la fonction doit interroger les données uniquement à partir des tables spécifiques aux ressources, ou à la fois à partir de la table héritée et des tables spécifiques aux ressources (avec déduplication des enregistrements).
    * Si vous avez réussi à migrer vers les tables spécifiques aux ressources, vous pouvez choisir d'exclure la table héritée de l'interrogation.
    * Si vous êtes actuellement en cours de migration et que vous disposez dans la table héritée de données dont vous avez besoin à des fins d'analyse, vous pouvez choisir d'inclure la table héritée. Au terme de la transition, si vous n'avez plus besoin des données de la table héritée, vous pouvez simplement mettre à jour la valeur du paramètre transmis à la fonction dans vos requêtes, afin d'exclure la table héritée.
    * Si vous n'utilisez que la table héritée, les fonctions continueront de fonctionner lorsque vous choisirez d'inclure celle-ci via le même paramètre. Il est toutefois recommandé de [basculer au plus tôt vers les tables spécifiques aux ressources](./backup-azure-diagnostic-events.md#steps-to-move-to-new-diagnostics-settings-for-a-log-analytics-workspace).

* **Réduction du risque d'interruption des requêtes personnalisées** : si le service Sauvegarde Azure améliore le schéma des tables LA sous-jacentes pour prendre en charge de futurs scénarios de création de rapports, la définition des fonctions sera également mise à jour pour tenir compte des modifications apportées au schéma. Ainsi, si vous utilisez des fonctions système pour créer des requêtes personnalisées, vos requêtes ne seront pas interrompues, même si des modifications sont apportées au schéma sous-jacent des tables.

> [!NOTE]
> Les fonctions système sont gérées par Microsoft et leurs définitions ne peuvent pas être modifiées par les utilisateurs. Si vous avez besoin de fonctions modifiables, vous pouvez créer des [fonctions enregistrées](../azure-monitor/logs/functions.md) dans LA.

## <a name="types-of-system-functions-offered-by-azure-backup"></a>Types de fonctions système offertes par le service Sauvegarde Azure

* **Fonctions principales** : ces fonctions vous permettent d'interroger n'importe laquelle des entités clés du service Sauvegarde Azure, comme les instances de sauvegarde, les coffres, les stratégies, les travaux et les entités de facturation. Par exemple, la fonction **_AzureBackup_getBackupInstances** renvoie la liste de toutes les instances de sauvegarde qui figurent dans votre environnement à compter de la dernière date de fin (au format UTC). Les paramètres et le schéma renvoyé pour chacune de ces fonctions principales sont résumés plus loin dans cet article.

* **Fonctions de tendance** : ces fonctions renvoient des enregistrements historiques de vos entités en lien avec la sauvegarde (par exemple, instances de sauvegarde ou groupes de facturation), et fournissent les tendances quotidiennes, hebdomadaires et mensuelles des métriques clés (par exemple, nombre ou stockage consommé) qui se rapportent à ces entités. Les paramètres et le schéma renvoyé pour chacune de ces fonctions de tendance sont résumés plus loin dans cet article.

> [!NOTE]
> Actuellement, les fonctions système renvoient des données jusqu'au dernier jour de fin (UTC). Les données de la journée partielle en cours ne sont pas renvoyées. Ainsi, si vous cherchez à récupérer des enregistrements pour la journée en cours, vous devrez utiliser les tables LA brutes.


## <a name="list-of-system-functions"></a>Liste des fonctions système

### <a name="core-functions"></a>Fonctions Core

#### <a name="_azurebackup_getvaults"></a>_AzureBackup_GetVaults()

Cette fonction renvoie la liste de tous les coffres Recovery Services de votre environnement Azure qui sont associés à l'espace de travail LA.

**Paramètres**

| **Nom du paramètre** | **Description** | **Obligatoire ?** | **Exemple de valeur** |
| -------------------- | ------------- | --------------- | ----------------- |
| RangeStart     | N'utilisez ce paramètre et le paramètre RangeEnd que si vous avez besoin de récupérer tous les enregistrements associés aux coffres pour la période comprise entre RangeStart et RangeEnd. Par défaut, les valeurs de RangeStart et RangeEnd sont nulles. Par conséquent, la fonction ne récupèrera que le dernier enregistrement de chaque coffre. | N | « 2021-03-03 00:00:00 » |
| RangeEnd     | N'utilisez ce paramètre et le paramètre RangeStart que si vous avez besoin de récupérer tous les enregistrements associés aux coffres pour la période comprise entre RangeStart et RangeEnd. Par défaut, les valeurs de RangeStart et RangeEnd sont nulles. Par conséquent, la fonction ne récupèrera que le dernier enregistrement de chaque coffre. | N |« 2021-03-10 00:00:00 »|
| VaultSubscriptionList   | Utilisez ce paramètre pour filtrer la sortie de la fonction sur un ensemble d'abonnements pour lesquels il existe des données de sauvegarde. En spécifiant une liste d'ID d'abonnements séparés par des virgules comme paramètre de cette fonction, vous récupérerez uniquement les coffres figurant dans les abonnements spécifiés. Par défaut, la valeur de ce paramètre est « * », ce qui permet à la fonction de rechercher des enregistrements dans tous les abonnements. | N | « 00000000-0000-0000-0000-000000000000,11111111-1111-1111-1111-111111111111 »|
| VaultLocationList     | Utilisez ce paramètre pour filtrer la sortie de la fonction sur un ensemble de régions pour lesquelles il existe des données de sauvegarde. En spécifiant une liste de régions séparées par des virgules comme paramètre de cette fonction, vous récupérerez uniquement les coffres figurant dans les régions spécifiées. Par défaut, la valeur de ce paramètre est « * », ce qui permet à la fonction de rechercher des enregistrements dans toutes les régions. | N | « eastus,westus »|
| VaultList    |Utilisez ce paramètre pour filtrer la sortie de la fonction sur un ensemble de coffres particulier. En spécifiant une liste de noms de coffres séparés par des virgules comme paramètre de cette fonction, vous récupérerez uniquement les enregistrements se rapportant aux coffres spécifiés. Par défaut, la valeur de ce paramètre est « * », ce qui permet à la fonction de rechercher des enregistrements dans tous les coffres. | N |« vault1,vault2,vault3 »|
| VaultTypeList     | Utilisez ce paramètre pour filtrer la sortie de la fonction sur les enregistrements appartenant à un type de coffre particulier. Actuellement, le seul type de coffre pris en charge est « Microsoft.RecoveryServices/vaults », qui correspond à la valeur par défaut de ce paramètre. | N | « Microsoft.RecoveryServices/vaults »|
| ExcludeLegacyEvent  | Utilisez ce paramètre pour choisir d'interroger ou non les données de la table AzureDiagnostics héritée. Si la valeur de ce paramètre est false, la fonction interroge les données de la table AzureDiagnostics et des tables spécifiques aux ressources. Si la valeur de ce paramètre est true, la fonction interroge les données des tables spécifiques aux ressources uniquement. La valeur par défaut est true. | N | true |

**Champs renvoyés**

| **Nom du champ** | **Description** |
| -------------- | --------------- |
| UniqueId | Clé primaire spécifiant l'ID unique du coffre |
| Id | ID Azure Resource Manager (ARM) du coffre |
| Nom | Nom du coffre |
| SubscriptionId | ID de l'abonnement dans lequel figure le coffre |
| Emplacement | Emplacement dans lequel figure le coffre |
| VaultStore_StorageReplicationType | Type de réplication de stockage associé au coffre |
| Étiquettes | Étiquettes du coffre |
| TimeGenerated | Horodatage de l'enregistrement |
| Type |  Type du coffre, à savoir « Microsoft.RecoveryServices/vaults »|

#### <a name="_azurebackup_getpolicies"></a>_AzureBackup_GetPolicies()

Cette fonction renvoie la liste des stratégies de sauvegarde en cours d'utilisation dans votre environnement Azure, ainsi que des informations détaillées sur chaque stratégie, telles que le type de source de données, le type de réplication de stockage, etc.

**Paramètres**

| **Nom du paramètre** | **Description** | **Obligatoire ?** | **Exemple de valeur** |
| -------------------- | ------------- | --------------- | ----------------- |
| RangeStart     | N'utilisez ce paramètre et le paramètre RangeStart que si vous avez besoin de récupérer tous les enregistrements associés aux stratégies pour la période comprise entre RangeStart et RangeEnd. Par défaut, les valeurs de RangeStart et RangeEnd sont nulles. Par conséquent, la fonction ne récupèrera que le dernier enregistrement de chaque stratégie. | N | « 2021-03-03 00:00:00 » |
| RangeEnd     | N'utilisez ce paramètre et le paramètre RangeStart que si vous avez besoin de récupérer tous les enregistrements associés aux stratégies pour la période comprise entre RangeStart et RangeEnd. Par défaut, les valeurs de RangeStart et RangeEnd sont nulles. Par conséquent, la fonction ne récupèrera que le dernier enregistrement de chaque stratégie. | N |« 2021-03-10 00:00:00 »|
| VaultSubscriptionList   | Utilisez ce paramètre pour filtrer la sortie de la fonction sur un ensemble d'abonnements pour lesquels il existe des données de sauvegarde. En spécifiant une liste d'ID d'abonnements séparés par des virgules comme paramètre de cette fonction, vous récupérerez uniquement les stratégies figurant dans les abonnements spécifiés. Par défaut, la valeur de ce paramètre est « * », ce qui permet à la fonction de rechercher des enregistrements dans tous les abonnements. | N | « 00000000-0000-0000-0000-000000000000,11111111-1111-1111-1111-111111111111 »|
| VaultLocationList     | Utilisez ce paramètre pour filtrer la sortie de la fonction sur un ensemble de régions pour lesquelles il existe des données de sauvegarde. En spécifiant une liste de régions séparées par des virgules comme paramètre de cette fonction, vous récupérerez uniquement les stratégies figurant dans les régions spécifiées. Par défaut, la valeur de ce paramètre est « * », ce qui permet à la fonction de rechercher des enregistrements dans toutes les régions. | N | « eastus,westus »|
| VaultList    |Utilisez ce paramètre pour filtrer la sortie de la fonction sur un ensemble de coffres particulier. En spécifiant une liste de coffres séparés par des virgules comme paramètre de cette fonction, vous récupérerez uniquement les enregistrements de stratégies se rapportant aux coffres spécifiés. Par défaut, la valeur de ce paramètre est « * », ce qui permet à la fonction de rechercher des enregistrements de stratégies dans tous les coffres. | N |« vault1,vault2,vault3 »|
| VaultTypeList     | Utilisez ce paramètre pour filtrer la sortie de la fonction sur les enregistrements appartenant à un type de coffre particulier. Actuellement, le seul type de coffre pris en charge est « Microsoft.RecoveryServices/vaults », qui correspond à la valeur par défaut de ce paramètre. | N | « Microsoft.RecoveryServices/vaults »|
| ExcludeLegacyEvent  | Utilisez ce paramètre pour choisir d'interroger ou non les données de la table AzureDiagnostics héritée. Si la valeur de ce paramètre est false, la fonction interroge les données de la table AzureDiagnostics et des tables spécifiques aux ressources. Si la valeur de ce paramètre est true, la fonction interroge les données des tables spécifiques aux ressources uniquement. La valeur par défaut est true. | N | true |
| BackupSolutionList | Utilisez ce paramètre pour filtrer la sortie de la fonction sur un ensemble de solutions de sauvegarde utilisées dans votre environnement Azure. Par exemple, si vous spécifiez « Azure Virtual Machine Backup,SQL in Azure VM Backup,DPM » comme valeur de ce paramètre, la fonction renvoie uniquement les enregistrements qui sont liés aux éléments sauvegardés à l'aide de Sauvegarde de machines virtuelles Azure, SQL dans Sauvegarde de machines virtuelles Azure ou DPM dans Sauvegarde Azure. Par défaut, la valeur de ce paramètre est « * », ce qui permet à la fonction de renvoyer des enregistrements se rapportant à toutes les solutions de sauvegarde prises en charge par le service Rapports de sauvegarde (les valeurs prises en charge sont « Sauvegarde de machines virtuelles Azure », « SQL dans Sauvegarde de machines virtuelles Azure », « SAP HANA dans Sauvegarde de machines virtuelles Azure », « Sauvegarde Azure (Azure Files) », « Agent de sauvegarde Azure », « DPM », « Serveur de sauvegarde Azure » ou une combinaison de ces valeurs séparées par des virgules. | N | « Azure Virtual Machine Backup,SQL in Azure VM Backup,DPM,Azure Backup Agent » |

**Champs renvoyés**

| **Nom du champ** | **Description** |
| -------------- | --------------- |
| UniqueId | Clé primaire spécifiant l'ID unique de la stratégie |
| Id | ID Azure Resource Manager (ARM) de la stratégie |
| Nom | Nom de la stratégie |
| Solution de sauvegarde | Solution de sauvegarde à laquelle la stratégie est associée. Par exemple, Sauvegarde de machines virtuelles Azure, SQL dans Sauvegarde de machines virtuelles Azure, etc. |
| TimeGenerated | Horodatage de l'enregistrement |
| VaultUniqueId | Clé étrangère qui fait référence au coffre associé à la stratégie |
| VaultResourceId | ID Azure Resource Manager (ARM) du coffre associé à la stratégie |
| VaultName | Nom du coffre associé à la stratégie |
| VaultTags | Étiquettes du coffre associé à la stratégie |
| VaultLocation | Emplacement du coffre associé à la stratégie |
| VaultSubscriptionId | ID d'abonnement du coffre associé à la stratégie |
| VaultStore_StorageReplicationType | Type de réplication de stockage du coffre associé à la stratégie |
| VaultType | Type du coffre, à savoir « Microsoft.RecoveryServices/vaults » |
| ExtendedProperties | Propriétés supplémentaires de la stratégie |

#### <a name="_azurebackup_getjobs"></a>_AzureBackup_GetJobs()

Cette fonction renvoie la liste de tous les travaux en lien avec la sauvegarde et la restauration qui ont été déclenchés dans un intervalle de temps spécifié, ainsi que des informations détaillées sur chaque travail, telles que son état, sa durée, les données transférées, etc.

**Paramètres**

| **Nom du paramètre** | **Description** | **Obligatoire ?** | **Exemple de valeur** |
| -------------------- | ------------- | --------------- | ----------------- |
| RangeStart     | Utilisez ce paramètre et le paramètre RangeEnd pour obtenir la liste de tous les travaux ayant débuté dans la période comprise entre RangeStart et RangeEnd. | O | « 2021-03-03 00:00:00 » |
| RangeEnd     | Utilisez ce paramètre et le paramètre RangeStart pour obtenir la liste de tous les travaux ayant débuté dans la période comprise entre RangeStart et RangeEnd. | O |« 2021-03-10 00:00:00 »|
| VaultSubscriptionList   | Utilisez ce paramètre pour filtrer la sortie de la fonction sur un ensemble d'abonnements pour lesquels il existe des données de sauvegarde. En spécifiant une liste d'ID d'abonnements séparés par des virgules comme paramètre de cette fonction, vous récupérerez uniquement les travaux associés aux coffres des abonnements spécifiés. Par défaut, la valeur de ce paramètre est « * », ce qui permet à la fonction de rechercher des enregistrements dans tous les abonnements. | N | « 00000000-0000-0000-0000-000000000000,11111111-1111-1111-1111-111111111111 »|
| VaultLocationList     | Utilisez ce paramètre pour filtrer la sortie de la fonction sur un ensemble de régions pour lesquelles il existe des données de sauvegarde. En spécifiant une liste de régions séparées par des virgules comme paramètre de cette fonction, vous récupérerez uniquement les travaux associés aux coffres des régions spécifiées. Par défaut, la valeur de ce paramètre est « * », ce qui permet à la fonction de rechercher des enregistrements dans toutes les régions. | N | « eastus,westus »|
| VaultList    | Utilisez ce paramètre pour filtrer la sortie de la fonction sur un ensemble de coffres particulier. En spécifiant une liste de noms de coffres séparés par des virgules comme paramètre de cette fonction, vous récupérerez uniquement les travaux se rapportant aux coffres spécifiés. Par défaut, la valeur de ce paramètre est « * », ce qui permet à la fonction de rechercher des travaux dans tous les coffres. | N |« vault1,vault2,vault3 »|
| VaultTypeList     | Utilisez ce paramètre pour filtrer la sortie de la fonction sur les enregistrements appartenant à un type de coffre particulier. Actuellement, le seul type de coffre pris en charge est « Microsoft.RecoveryServices/vaults », qui correspond à la valeur par défaut de ce paramètre. | N | « Microsoft.RecoveryServices/vaults »|
| ExcludeLegacyEvent  | Utilisez ce paramètre pour choisir d'interroger ou non les données de la table AzureDiagnostics héritée. Si la valeur de ce paramètre est false, la fonction interroge les données de la table AzureDiagnostics et des tables spécifiques aux ressources. Si la valeur de ce paramètre est true, la fonction interroge les données des tables spécifiques aux ressources uniquement. La valeur par défaut est true. | N | true |
| BackupSolutionList | Utilisez ce paramètre pour filtrer la sortie de la fonction sur un ensemble de solutions de sauvegarde utilisées dans votre environnement Azure. Par exemple, si vous spécifiez « Azure Virtual Machine Backup,SQL in Azure VM Backup,DPM » comme valeur de ce paramètre, la fonction renvoie uniquement les enregistrements qui sont liés aux éléments sauvegardés à l'aide de Sauvegarde de machines virtuelles Azure, SQL dans Sauvegarde de machines virtuelles Azure ou DPM dans Sauvegarde Azure. Par défaut, la valeur de ce paramètre est « * », ce qui permet à la fonction de renvoyer des enregistrements se rapportant à toutes les solutions de sauvegarde prises en charge par le service Rapports de sauvegarde (les valeurs prises en charge sont « Sauvegarde de machines virtuelles Azure », « SQL dans Sauvegarde de machines virtuelles Azure », « SAP HANA dans Sauvegarde de machines virtuelles Azure », « Sauvegarde Azure (Azure Files) », « Agent de sauvegarde Azure », « DPM », « Serveur de sauvegarde Azure » ou une combinaison de ces valeurs séparées par des virgules. | N | « Azure Virtual Machine Backup,SQL in Azure VM Backup,DPM,Azure Backup Agent » |
| JobOperationList | Utilisez ce paramètre pour filtrer la sortie de la fonction sur un type de travail spécifique. Par exemple, Sauvegarde ou Restauration. Par défaut, la valeur de ce paramètre est « * », ce qui permet à la fonction de rechercher à la fois les travaux de sauvegarde et de restauration. | N | « Backup » | 
| JobStatusList | Utilisez ce paramètre pour filtrer la sortie de la fonction sur un état de travail spécifique. Par exemple, Completed, Failed, etc. Par défaut, la valeur de ce paramètre est « * », ce qui permet à la fonction de rechercher tous les travaux indépendamment de leur état. | N | « Failed,CompletedWithWarnings » |
| JobFailureCodeList | Utilisez ce paramètre pour filtrer la sortie de la fonction sur un code d'échec spécifique. Par défaut, la valeur de ce paramètre est « * », ce qui permet à la fonction de rechercher tous les travaux indépendamment du code d'échec. | N | « Success »
| DatasourceSetName | Utilisez ce paramètre pour filtrer la sortie de la fonction sur une ressource parente particulière. Par exemple, pour renvoyer les instances de SQL dans Sauvegarde de machines virtuelles Azure appartenant à la machine virtuelle « testvm », spécifiez _testvm_ comme valeur de ce paramètre. Par défaut, la valeur est « * », ce qui permet à la fonction de rechercher des enregistrements dans toutes les instances de sauvegarde. | N | « testvm » |
| BackupInstanceName | Utilisez ce paramètre pour rechercher des travaux sur une instance de sauvegarde particulière, par nom. Par défaut, la valeur est « * », ce qui permet à la fonction de rechercher des enregistrements dans toutes les instances de sauvegarde. | N | « testvm » |
| ExcludeLog | Utilisez ce paramètre pour exclure les travaux de journal des résultats de la fonction (afin d'améliorer les performances des requêtes). Par défaut, la valeur de ce paramètre est true, ce qui permet à la fonction d'exclure les travaux de journal. | N | true


**Champs renvoyés**

| **Nom du champ** | **Description** |
| -------------- | --------------- |
| UniqueId | Clé primaire spécifiant l'ID unique du travail |
| OperationCategory | Catégorie de l'opération en cours d'exécution. Par exemple, Sauvegarde, Restauration |
| Opération | Détails de l'opération en cours d'exécution. Par exemple, journal (pour la sauvegarde de journal)|
| État | État du travail. Par exemple, Completed, Failed, CompletedWithWarnings |
| ErrorTitle | Code d'échec du travail |
| StartTime | Date et heure de début du travail |
| DurationInSecs | Durée du travail, en secondes |
| DataTransferredInMBs | Données transférées par le travail, en Mo |
| RestoreJobRPDateTime | Date et heure de création du point de récupération en cours de récupération |
| RestoreJobRPLocation | Emplacement auquel a été stocké le point de récupération en cours de récupération |
| BackupInstanceUniqueId | Clé étrangère qui fait référence à l'instance de sauvegarde associée au travail |
| BackupInstanceId | ID Azure Resource Manager (ARM) de l'instance de sauvegarde associée au travail |
| BackupInstanceFriendlyName | Nom de l'instance de sauvegarde associée au travail |
| DatasourceResourceId | ID Azure Resource Manager (ARM) de la source de données sous-jacente associée au travail. Par exemple, ID Azure Resource Manager (ARM) de la machine virtuelle |
| DatasourceFriendlyName | Nom convivial de la source de données sous-jacente associée au travail |
| DatasourceType | Type de la source de données associée au travail. Par exemple, « Microsoft.Compute/virtualMachines » |
| BackupSolution | Solution de sauvegarde à laquelle le travail est associé. Par exemple, Sauvegarde de machines virtuelles Azure, SQL dans Sauvegarde de machines virtuelles Azure, etc. |
| DatasourceSetResourceId | ID Azure Resource Manager (ARM) de la ressource parente de la source de données (le cas échéant). Par exemple, pour une source de données SQL dans une machine virtuelle Azure, ce champ contiendra l'ID Azure Resource Manager (ARM) de la machine virtuelle dans laquelle figure SQL Database |
| DatasourceSetType | Type de la ressource parente de la source de données (le cas échéant). Par exemple, pour une source de données SAP HANA dans une machine virtuelle Azure, ce champ sera Microsoft.Compute/virtualMachines, car la ressource parente est une machine virtuelle Azure |
| VaultResourceId | ID Azure Resource Manager (ARM) du coffre associé au travail |
| VaultUniqueId | Clé étrangère qui fait référence au coffre associé au travail |
| VaultName | Nom du coffre associé au travail |
| VaultTags | Étiquettes du coffre associé au travail |
| VaultSubscriptionId | ID d'abonnement du coffre associé au travail |
| VaultLocation | Emplacement du coffre associé au travail |
| VaultStore_StorageReplicationType | Type de réplication de stockage du coffre associé au travail |
| VaultType | Type du coffre, à savoir « Microsoft.RecoveryServices/vaults » |
| TimeGenerated | Horodatage de l'enregistrement |

#### <a name="_azurebackup_getbackupinstances"></a>_AzureBackup_GetBackupInstances()

Cette fonction renvoie la liste des instances de sauvegarde associées à vos coffres Recovery Services, ainsi que des informations détaillées sur chaque instance de sauvegarde, comme la consommation de stockage cloud, la stratégie associée, etc.

**Paramètres**

| **Nom du paramètre** | **Description** | **Obligatoire ?** | **Exemple de valeur** |
| -------------------- | ------------- | --------------- | ----------------- |
| RangeStart     | N'utilisez ce paramètre et le paramètre RangeEnd que si vous avez besoin de récupérer tous les enregistrements associés aux instances de sauvegarde pour la période comprise entre RangeStart et RangeEnd. Par défaut, les valeurs de RangeStart et RangeEnd sont nulles. Par conséquent, la fonction ne récupèrera que le dernier enregistrement de chaque instance de sauvegarde. | N | « 2021-03-03 00:00:00 » |
| RangeEnd     | N'utilisez ce paramètre et le paramètre RangeStart que si vous avez besoin de récupérer tous les enregistrements associés aux instances de sauvegarde pour la période comprise entre RangeStart et RangeEnd. Par défaut, les valeurs de RangeStart et RangeEnd sont nulles. Par conséquent, la fonction ne récupèrera que le dernier enregistrement de chaque instance de sauvegarde. | N |« 2021-03-10 00:00:00 »|
| VaultSubscriptionList   | Utilisez ce paramètre pour filtrer la sortie de la fonction sur un ensemble d'abonnements pour lesquels il existe des données de sauvegarde. En spécifiant une liste d'ID d'abonnements séparés par des virgules comme paramètre de cette fonction, vous récupérerez uniquement les instances de sauvegarde figurant dans les abonnements spécifiés. Par défaut, la valeur de ce paramètre est « * », ce qui permet à la fonction de rechercher des enregistrements dans tous les abonnements. | N | « 00000000-0000-0000-0000-000000000000,11111111-1111-1111-1111-111111111111 »|
| VaultLocationList     | Utilisez ce paramètre pour filtrer la sortie de la fonction sur un ensemble de régions pour lesquelles il existe des données de sauvegarde. En spécifiant une liste de régions séparées par des virgules comme paramètre de cette fonction, vous récupérerez uniquement les instances de sauvegarde figurant dans les régions spécifiées. Par défaut, la valeur de ce paramètre est « * », ce qui permet à la fonction de rechercher des enregistrements dans toutes les régions. | N | « eastus,westus »|
| VaultList    |Utilisez ce paramètre pour filtrer la sortie de la fonction sur un ensemble de coffres particulier. En spécifiant une liste de coffres séparés par des virgules comme paramètre de cette fonction, vous récupérerez uniquement les enregistrements d'instances de sauvegarde se rapportant aux coffres spécifiés. Par défaut, la valeur de ce paramètre est « * », ce qui permet à la fonction de rechercher des enregistrements d'instances de sauvegarde dans tous les coffres. | N |« vault1,vault2,vault3 »|
| VaultTypeList     | Utilisez ce paramètre pour filtrer la sortie de la fonction sur les enregistrements appartenant à un type de coffre particulier. Actuellement, le seul type de coffre pris en charge est « Microsoft.RecoveryServices/vaults », qui correspond à la valeur par défaut de ce paramètre. | N | « Microsoft.RecoveryServices/vaults »|
| ExcludeLegacyEvent  | Utilisez ce paramètre pour choisir d'interroger ou non les données de la table AzureDiagnostics héritée. Si la valeur de ce paramètre est false, la fonction interroge les données de la table AzureDiagnostics et des tables spécifiques aux ressources. Si la valeur de ce paramètre est true, la fonction interroge les données des tables spécifiques aux ressources uniquement. La valeur par défaut est true. | N | true |
| BackupSolutionList | Utilisez ce paramètre pour filtrer la sortie de la fonction sur un ensemble de solutions de sauvegarde utilisées dans votre environnement Azure. Par exemple, si vous spécifiez « Azure Virtual Machine Backup,SQL in Azure VM Backup,DPM » comme valeur de ce paramètre, la fonction renvoie uniquement les enregistrements qui sont liés aux éléments sauvegardés à l'aide de Sauvegarde de machines virtuelles Azure, SQL dans Sauvegarde de machines virtuelles Azure ou DPM dans Sauvegarde Azure. Par défaut, la valeur de ce paramètre est « * », ce qui permet à la fonction de renvoyer des enregistrements se rapportant à toutes les solutions de sauvegarde prises en charge par le service Rapports de sauvegarde (les valeurs prises en charge sont « Sauvegarde de machines virtuelles Azure », « SQL dans Sauvegarde de machines virtuelles Azure », « SAP HANA dans Sauvegarde de machines virtuelles Azure », « Sauvegarde Azure (Azure Files) », « Agent de sauvegarde Azure », « DPM », « Serveur de sauvegarde Azure » ou une combinaison de ces valeurs séparées par des virgules. | N | « Azure Virtual Machine Backup,SQL in Azure VM Backup,DPM,Azure Backup Agent » |
| ProtectionInfoList | Utilisez ce paramètre pour choisir d'inclure uniquement les instances de sauvegarde qui sont activement protégées, ou d'inclure également les instances pour lesquelles la protection a été arrêtée et les instances pour lesquelles la sauvegarde initiale est en attente. Les valeurs prises en charge sont « Protected », « ProtectionStopped », « InitialBackupPending » ou une combinaison de ces valeurs séparées par des virgules. Par défaut, la valeur est « * », ce qui permet à la fonction de rechercher toutes les instances de sauvegarde indépendamment des détails de protection. | N | « Protected » |
| DatasourceSetName | Utilisez ce paramètre pour filtrer la sortie de la fonction sur une ressource parente particulière. Par exemple, pour renvoyer les instances de SQL dans Sauvegarde de machines virtuelles Azure appartenant à la machine virtuelle « testvm », spécifiez _testvm_ comme valeur de ce paramètre. Par défaut, la valeur est « * », ce qui permet à la fonction de rechercher des enregistrements dans toutes les instances de sauvegarde. | N | « testvm » |
| BackupInstanceName | Utilisez ce paramètre pour rechercher une instance de sauvegarde particulière, par nom. Par défaut, la valeur est « * », ce qui permet à la fonction de rechercher toutes les instances de sauvegarde. | N | « testvm » |
| DisplayAllFields | Utilisez ce paramètre pour choisir de ne récupérer qu'un sous-ensemble des champs renvoyés par la fonction. Si la valeur de ce paramètre est false, la fonction élimine les informations relatives au stockage et au point de rétention des résultats de la fonction. Cela est particulièrement utile lorsque vous utilisez cette fonction comme étape intermédiaire dans une requête plus volumineuse et que vous devez optimiser les performances de la requête en éliminant les colonnes dont vous n'avez pas besoin pour l'analyse. Par défaut, la valeur de ce paramètre est true, ce qui permet à la fonction de renvoyer tous les champs relatifs à l'instance de sauvegarde. | N | true |

**Champs renvoyés**

| **Nom du champ** | **Description** |
| -------------- | --------------- |
| UniqueId | Clé primaire spécifiant l'ID unique de l'instance de sauvegarde |
| Id | ID Azure Resource Manager (ARM) de l'instance de sauvegarde |
| FriendlyName | Nom convivial de l’élément de l'instance de sauvegarde |
| ProtectionInfo | Informations sur les paramètres de protection de l'instance de sauvegarde. Par exemple, protection configurée, protection arrêtée, sauvegarde initiale en attente |
| LatestRecoveryPoint | Date et heure du dernier point de récupération associé à l'instance de sauvegarde |
| OldestRecoveryPoint | Date et heure du point de récupération le plus ancien associé à l'instance de sauvegarde |
| SourceSizeInMBs | Taille du serveur frontal de l'instance de sauvegarde, en Mo |
| VaultStore_StorageConsumptionInMBs | Stockage cloud total consommé par l'instance de sauvegarde au niveau standard du coffre |
| DataSourceFriendlyName | Nom convivial de la source de données correspondant à l'instance de sauvegarde |
| BackupSolution | Solution de sauvegarde à laquelle l'instance de sauvegarde est associée. Par exemple, Sauvegarde de machines virtuelles Azure, SQL dans Sauvegarde de machines virtuelles Azure, etc. |
| DatasourceType | Type de la source de données correspondant à l'instance de sauvegarde Par exemple, « Microsoft.Compute/virtualMachines » |
| DatasourceResourceId | ID Azure Resource Manager (ARM) de la source de données sous-jacente correspondant à l'instance de sauvegarde. Par exemple, ID Azure Resource Manager (ARM) de la machine virtuelle |
| DatasourceSetFriendlyName | Nom convivial de la ressource parente de la source de données (le cas échéant). Par exemple, pour une source de données SQL dans une machine virtuelle Azure, ce champ contiendra le nom de la machine virtuelle dans laquelle figure SQL Database |
| DatasourceSetResourceId | ID Azure Resource Manager (ARM) de la ressource parente de la source de données (le cas échéant). Par exemple, pour une source de données SQL dans une machine virtuelle Azure, ce champ contiendra l'ID Azure Resource Manager (ARM) de la machine virtuelle dans laquelle figure SQL Database |
| DatasourceSetType | Type de la ressource parente de la source de données (le cas échéant). Par exemple, pour une source de données SAP HANA dans une machine virtuelle Azure, ce champ sera Microsoft.Compute/virtualMachines, car la ressource parente est une machine virtuelle Azure |
| PolicyName | Nom de la stratégie associée à l'instance de sauvegarde |
| PolicyUniqueId | Clé étrangère qui fait référence à la stratégie associée à l'instance de sauvegarde  |
| PolicyId | ID Azure Resource Manager (ARM) de la stratégie associée à l'instance de sauvegarde |
| VaultResourceId | ID Azure Resource Manager (ARM) du coffre associé à l'instance de sauvegarde |
| VaultUniqueId | Clé étrangère qui fait référence au coffre associé à l'instance de sauvegarde |
| VaultName | Nom du coffre associé à l'instance de sauvegarde |
| VaultTags | Étiquettes du coffre associé à l'instance de sauvegarde |
| VaultSubscriptionId | ID d'abonnement du coffre associé à l'instance de sauvegarde |
| VaultLocation | Emplacement du coffre associé à l'instance de sauvegarde |
| VaultStore_StorageReplicationType | Type de réplication de stockage du coffre associé à l'instance de sauvegarde |
| VaultType | Type du coffre, à savoir « Microsoft.RecoveryServices/vaults » |
| TimeGenerated | Horodatage de l'enregistrement |

#### <a name="_azurebackup_getbillinggroups"></a>_AzureBackup_GetBillingGroups()

Cette fonction renvoie la liste de toutes les entités de facturation en lien avec la sauvegarde (groupes de facturation), ainsi que des informations sur les principaux composants de facturation, comme la taille du serveur frontal et le stockage cloud total.

**Paramètres**

| **Nom du paramètre** | **Description** | **Obligatoire ?** | **Exemple de valeur** |
| -------------------- | ------------- | --------------- | ----------------- |
| RangeStart     | N'utilisez ce paramètre et le paramètre RangeEnd que si vous avez besoin de récupérer tous les enregistrements associés aux groupes de facturation pour la période comprise entre RangeStart et RangeEnd. Par défaut, les valeurs de RangeStart et RangeEnd sont nulles. Par conséquent, la fonction ne récupèrera que le dernier enregistrement de chaque groupe de facturation. | N | « 2021-03-03 00:00:00 » |
| RangeEnd     | N'utilisez ce paramètre et le paramètre RangeStart que si vous avez besoin de récupérer tous les enregistrements associés aux groupes de facturation pour la période comprise entre RangeStart et RangeEnd. Par défaut, les valeurs de RangeStart et RangeEnd sont nulles. Par conséquent, la fonction ne récupèrera que le dernier enregistrement de chaque groupe de facturation. | N |« 2021-03-10 00:00:00 »|
| VaultSubscriptionList   | Utilisez ce paramètre pour filtrer la sortie de la fonction sur un ensemble d'abonnements pour lesquels il existe des données de sauvegarde. En spécifiant une liste d'ID d'abonnements séparés par des virgules comme paramètre de cette fonction, vous récupérerez uniquement les groupes de facturation figurant dans les abonnements spécifiés. Par défaut, la valeur de ce paramètre est « * », ce qui permet à la fonction de rechercher des enregistrements dans tous les abonnements. | N | « 00000000-0000-0000-0000-000000000000,11111111-1111-1111-1111-111111111111 »|
| VaultLocationList     | Utilisez ce paramètre pour filtrer la sortie de la fonction sur un ensemble de régions pour lesquelles il existe des données de sauvegarde. En spécifiant une liste de régions séparées par des virgules comme paramètre de cette fonction, vous récupérerez uniquement les groupes de facturation figurant dans les régions spécifiées. Par défaut, la valeur de ce paramètre est « * », ce qui permet à la fonction de rechercher des enregistrements dans toutes les régions. | N | « eastus,westus »|
| VaultList    |Utilisez ce paramètre pour filtrer la sortie de la fonction sur un ensemble de coffres particulier. En spécifiant une liste de coffres séparés par des virgules comme paramètre de cette fonction, vous récupérerez uniquement les enregistrements d'instances de sauvegarde se rapportant aux coffres spécifiés. Par défaut, la valeur de ce paramètre est « * », ce qui permet à la fonction de rechercher des enregistrements de groupes de facturation dans tous les coffres. | N |« vault1,vault2,vault3 »|
| VaultTypeList     | Utilisez ce paramètre pour filtrer la sortie de la fonction sur les enregistrements appartenant à un type de coffre particulier. Actuellement, le seul type de coffre pris en charge est « Microsoft.RecoveryServices/vaults », qui correspond à la valeur par défaut de ce paramètre. | N | « Microsoft.RecoveryServices/vaults »|
| ExcludeLegacyEvent  | Utilisez ce paramètre pour choisir d'interroger ou non les données de la table AzureDiagnostics héritée. Si la valeur de ce paramètre est false, la fonction interroge les données de la table AzureDiagnostics et des tables spécifiques aux ressources. Si la valeur de ce paramètre est true, la fonction interroge les données des tables spécifiques aux ressources uniquement. La valeur par défaut est true. | N | true |
| BackupSolutionList | Utilisez ce paramètre pour filtrer la sortie de la fonction sur un ensemble de solutions de sauvegarde utilisées dans votre environnement Azure. Par exemple, si vous spécifiez « Azure Virtual Machine Backup,SQL in Azure VM Backup,DPM » comme valeur de ce paramètre, la fonction renvoie uniquement les enregistrements qui sont liés aux éléments sauvegardés à l'aide de Sauvegarde de machines virtuelles Azure, SQL dans Sauvegarde de machines virtuelles Azure ou DPM dans Sauvegarde Azure. Par défaut, la valeur de ce paramètre est « * », ce qui permet à la fonction de renvoyer des enregistrements se rapportant à toutes les solutions de sauvegarde prises en charge par le service Rapports de sauvegarde (les valeurs prises en charge sont « Sauvegarde de machines virtuelles Azure », « SQL dans Sauvegarde de machines virtuelles Azure », « SAP HANA dans Sauvegarde de machines virtuelles Azure », « Sauvegarde Azure (Azure Files) », « Agent de sauvegarde Azure », « DPM », « Serveur de sauvegarde Azure » ou une combinaison de ces valeurs séparées par des virgules. | N | « Azure Virtual Machine Backup,SQL in Azure VM Backup,DPM,Azure Backup Agent » |
| BillingGroupName | Utilisez ce paramètre pour rechercher un groupe de facturation spécifique, par nom. Par défaut, la valeur est « * », ce qui permet à la fonction de rechercher tous les groupes de facturation. | N | « testvm » |

**Champs renvoyés**

| **Nom du champ** | **Description** |
| -------------- | --------------- |
| UniqueId | Clé primaire spécifiant l'ID unique du groupe de facturation |
| FriendlyName | Nom convivial du groupe de facturation |
| Nom | Nom du groupe de facturation |
| Type | Type de groupe de facturation. Par exemple, ProtectedContainer ou BackupItem |
| SourceSizeInMBs | Taille du serveur frontal du groupe de facturation, en Mo |
| VaultStore_StorageConsumptionInMBs | Stockage cloud total consommé par le groupe de facturation au niveau standard du coffre |
| BackupSolution | Solution de sauvegarde à laquelle le groupe de facturation est associé. Par exemple, Sauvegarde de machines virtuelles Azure, SQL dans Sauvegarde de machines virtuelles Azure, etc. |
| VaultResourceId | ID Azure Resource Manager (ARM) du coffre associé au groupe de facturation |
| VaultUniqueId | Clé étrangère qui fait référence au coffre associé au groupe de facturation |
| VaultName | Nom du coffre associé au groupe de facturation |
| VaultTags | Étiquettes du coffre associé au groupe de facturation |
| VaultSubscriptionId | ID d'abonnement du coffre associé au groupe de facturation |
| VaultLocation | Emplacement du coffre associé au groupe de facturation |
| VaultStore_StorageReplicationType | Type de réplication de stockage du coffre associé au groupe de facturation |
| VaultType | Type du coffre, à savoir « Microsoft.RecoveryServices/vaults » |
| TimeGenerated | Horodatage de l'enregistrement |
| ExtendedProperties | Propriétés supplémentaires du groupe de facturation |

### <a name="trend-functions"></a>Fonctions de tendance

#### <a name="_azurebackup_getbackupinstancestrends"></a>_AzureBackup_GetBackupInstancesTrends()

Cette fonction renvoie des enregistrements historiques pour chaque instance de sauvegarde, ce qui vous permet d'examiner les principales tendances quotidiennes, hebdomadaires et mensuelles liées au nombre d'instances de sauvegarde et à la consommation de stockage, sur plusieurs niveaux de granularité.

**Paramètres**

| **Nom du paramètre** | **Description** | **Obligatoire ?** | **Exemple de valeur** |
| -------------------- | ------------- | --------------- | ----------------- |
| RangeStart     | Utilisez ce paramètre et le paramètre RangeEnd pour récupérer tous les enregistrements associés aux instances de sauvegarde pour la période comprise entre RangeStart et RangeEnd. | O | « 2021-03-03 00:00:00 » |
| RangeEnd     | Utilisez ce paramètre et le paramètre RangeStart pour récupérer tous les enregistrements associés aux instances de sauvegarde pour la période comprise entre RangeStart et RangeEnd. | O |« 2021-03-10 00:00:00 »|
| VaultSubscriptionList   | Utilisez ce paramètre pour filtrer la sortie de la fonction sur un ensemble d'abonnements pour lesquels il existe des données de sauvegarde. En spécifiant une liste d'ID d'abonnements séparés par des virgules comme paramètre de cette fonction, vous récupérerez uniquement les instances de sauvegarde figurant dans les abonnements spécifiés. Par défaut, la valeur de ce paramètre est « * », ce qui permet à la fonction de rechercher des enregistrements dans tous les abonnements. | N | « 00000000-0000-0000-0000-000000000000,11111111-1111-1111-1111-111111111111 »|
| VaultLocationList     | Utilisez ce paramètre pour filtrer la sortie de la fonction sur un ensemble de régions pour lesquelles il existe des données de sauvegarde. En spécifiant une liste de régions séparées par des virgules comme paramètre de cette fonction, vous récupérerez uniquement les instances de sauvegarde figurant dans les régions spécifiées. Par défaut, la valeur de ce paramètre est « * », ce qui permet à la fonction de rechercher des enregistrements dans toutes les régions. | N | « eastus,westus »|
| VaultList    |Utilisez ce paramètre pour filtrer la sortie de la fonction sur un ensemble de coffres particulier. En spécifiant une liste de coffres séparés par des virgules comme paramètre de cette fonction, vous récupérerez uniquement les enregistrements d'instances de sauvegarde se rapportant aux coffres spécifiés. Par défaut, la valeur de ce paramètre est « * », ce qui permet à la fonction de rechercher des enregistrements d'instances de sauvegarde dans tous les coffres. | N |« vault1,vault2,vault3 »|
| VaultTypeList     | Utilisez ce paramètre pour filtrer la sortie de la fonction sur les enregistrements appartenant à un type de coffre particulier. Actuellement, le seul type de coffre pris en charge est « Microsoft.RecoveryServices/vaults », qui correspond à la valeur par défaut de ce paramètre. | N | « Microsoft.RecoveryServices/vaults »|
| ExcludeLegacyEvent  | Utilisez ce paramètre pour choisir d'interroger ou non les données de la table AzureDiagnostics héritée. Si la valeur de ce paramètre est false, la fonction interroge les données de la table AzureDiagnostics et des tables spécifiques aux ressources. Si la valeur de ce paramètre est true, la fonction interroge les données des tables spécifiques aux ressources uniquement. La valeur par défaut est true. | N | true |
| BackupSolutionList | Utilisez ce paramètre pour filtrer la sortie de la fonction sur un ensemble de solutions de sauvegarde utilisées dans votre environnement Azure. Par exemple, si vous spécifiez « Azure Virtual Machine Backup,SQL in Azure VM Backup,DPM » comme valeur de ce paramètre, la fonction renvoie uniquement les enregistrements qui sont liés aux éléments sauvegardés à l'aide de Sauvegarde de machines virtuelles Azure, SQL dans Sauvegarde de machines virtuelles Azure ou DPM dans Sauvegarde Azure. Par défaut, la valeur de ce paramètre est « * », ce qui permet à la fonction de renvoyer des enregistrements se rapportant à toutes les solutions de sauvegarde prises en charge par le service Rapports de sauvegarde (les valeurs prises en charge sont « Sauvegarde de machines virtuelles Azure », « SQL dans Sauvegarde de machines virtuelles Azure », « SAP HANA dans Sauvegarde de machines virtuelles Azure », « Sauvegarde Azure (Azure Files) », « Agent de sauvegarde Azure », « DPM », « Serveur de sauvegarde Azure » ou une combinaison de ces valeurs séparées par des virgules. | N | « Azure Virtual Machine Backup,SQL in Azure VM Backup,DPM,Azure Backup Agent » |
| ProtectionInfoList | Utilisez ce paramètre pour choisir d'inclure uniquement les instances de sauvegarde qui sont activement protégées, ou d'inclure également les instances pour lesquelles la protection a été arrêtée et les instances pour lesquelles la sauvegarde initiale est en attente. Les valeurs prises en charge sont « Protected », « ProtectionStopped », « InitialBackupPending » ou une combinaison de ces valeurs séparées par des virgules. Par défaut, la valeur est « * », ce qui permet à la fonction de rechercher toutes les instances de sauvegarde indépendamment des détails de protection. | N | « Protected » |
| DatasourceSetName | Utilisez ce paramètre pour filtrer la sortie de la fonction sur une ressource parente particulière. Par exemple, pour renvoyer les instances de SQL dans Sauvegarde de machines virtuelles Azure appartenant à la machine virtuelle « testvm », spécifiez _testvm_ comme valeur de ce paramètre. Par défaut, la valeur est « * », ce qui permet à la fonction de rechercher des enregistrements dans toutes les instances de sauvegarde. | N | « testvm » |
| BackupInstanceName | Utilisez ce paramètre pour rechercher une instance de sauvegarde particulière, par nom. Par défaut, la valeur est « * », ce qui permet à la fonction de rechercher toutes les instances de sauvegarde. | N | « testvm » |
| DisplayAllFields | Utilisez ce paramètre pour choisir de ne récupérer qu'un sous-ensemble des champs renvoyés par la fonction. Si la valeur de ce paramètre est false, la fonction élimine les informations relatives au stockage et au point de rétention des résultats de la fonction. Cela est particulièrement utile lorsque vous utilisez cette fonction comme étape intermédiaire dans une requête plus volumineuse et que vous devez optimiser les performances de la requête en éliminant les colonnes dont vous n'avez pas besoin pour l'analyse. Par défaut, la valeur de ce paramètre est true, ce qui permet à la fonction de renvoyer tous les champs relatifs à l'instance de sauvegarde. | N | true |
| AggregationType | Utilisez ce paramètre pour spécifier la granularité temporelle à laquelle les données doivent être récupérées. Si la valeur de ce paramètre est « Daily », la fonction renvoie un enregistrement par instance de sauvegarde par jour, ce qui vous permet d'analyser les tendances quotidiennes en termes de consommation de stockage et de nombre d'instances de sauvegarde. Si la valeur de ce paramètre est « Weekly », la fonction renvoie un enregistrement par instance de sauvegarde par semaine, ce qui vous permet d'analyser les tendances hebdomadaires. De même, vous pouvez spécifier « Monthly » pour analyser les tendances mensuelles. La valeur par défaut est « Daily ». Si vous examinez des données sur des périodes plus longues, utilisez « Weekly » ou « Monthly » pour améliorer les performances des requêtes et faciliter l'analyse des tendances. | N | « Weekly » |

**Champs renvoyés**

| **Nom du champ** | **Description** |
| -------------- | --------------- |
| UniqueId | Clé primaire spécifiant l'ID unique de l'instance de sauvegarde |
| Id | ID Azure Resource Manager (ARM) de l'instance de sauvegarde |
| FriendlyName | Nom convivial de l’élément de l'instance de sauvegarde |
| ProtectionInfo | Informations sur les paramètres de protection de l'instance de sauvegarde. Par exemple, protection configurée, protection arrêtée, sauvegarde initiale en attente |
| LatestRecoveryPoint | Date et heure du dernier point de récupération associé à l'instance de sauvegarde |
| OldestRecoveryPoint | Date et heure du point de récupération le plus ancien associé à l'instance de sauvegarde |
| SourceSizeInMBs | Taille du serveur frontal de l'instance de sauvegarde, en Mo |
| VaultStore_StorageConsumptionInMBs | Stockage cloud total consommé par l'instance de sauvegarde au niveau standard du coffre |
| DataSourceFriendlyName | Nom convivial de la source de données correspondant à l'instance de sauvegarde |
| BackupSolution | Solution de sauvegarde à laquelle l'instance de sauvegarde est associée. Par exemple, Sauvegarde de machines virtuelles Azure, SQL dans Sauvegarde de machines virtuelles Azure, etc. |
| DatasourceType | Type de la source de données correspondant à l'instance de sauvegarde Par exemple, « Microsoft.Compute/virtualMachines » |
| DatasourceResourceId | ID Azure Resource Manager (ARM) de la source de données sous-jacente correspondant à l'instance de sauvegarde. Par exemple, ID Azure Resource Manager (ARM) de la machine virtuelle |
| DatasourceSetFriendlyName | Nom convivial de la ressource parente de la source de données (le cas échéant). Par exemple, pour une source de données SQL dans une machine virtuelle Azure, ce champ contiendra le nom de la machine virtuelle dans laquelle figure SQL Database |
| DatasourceSetResourceId | ID Azure Resource Manager (ARM) de la ressource parente de la source de données (le cas échéant). Par exemple, pour une source de données SQL dans une machine virtuelle Azure, ce champ contiendra l'ID Azure Resource Manager (ARM) de la machine virtuelle dans laquelle figure SQL Database |
| DatasourceSetType | Type de la ressource parente de la source de données (le cas échéant). Par exemple, pour une source de données SAP HANA dans une machine virtuelle Azure, ce champ sera Microsoft.Compute/virtualMachines, car la ressource parente est une machine virtuelle Azure |
| PolicyName | Nom de la stratégie associée à l'instance de sauvegarde |
| PolicyUniqueId | Clé étrangère qui fait référence à la stratégie associée à l'instance de sauvegarde  |
| PolicyId | ID Azure Resource Manager (ARM) de la stratégie associée à l'instance de sauvegarde |
| VaultResourceId | ID Azure Resource Manager (ARM) du coffre associé à l'instance de sauvegarde |
| VaultUniqueId | Clé étrangère qui fait référence au coffre associé à l'instance de sauvegarde |
| VaultName | Nom du coffre associé à l'instance de sauvegarde |
| VaultTags | Étiquettes du coffre associé à l'instance de sauvegarde |
| VaultSubscriptionId | ID d'abonnement du coffre associé à l'instance de sauvegarde |
| VaultLocation | Emplacement du coffre associé à l'instance de sauvegarde |
| VaultStore_StorageReplicationType | Type de réplication de stockage du coffre associé à l'instance de sauvegarde |
| VaultType | Type du coffre, à savoir « Microsoft.RecoveryServices/vaults » |
| TimeGenerated | Horodatage de l'enregistrement |

#### <a name="_azurebackup_getbillinggroupstrends"></a>_AzureBackup_GetBillingGroupsTrends()

Cette fonction renvoie des enregistrements historiques pour chaque groupe de facturation, ce qui vous permet d'examiner les principales tendances quotidiennes, hebdomadaires et mensuelles liées à la taille du serveur frontal et à la consommation de stockage, sur plusieurs niveaux de granularité.

**Paramètres**

| **Nom du paramètre** | **Description** | **Obligatoire ?** | **Exemple de valeur** |
| -------------------- | ------------- | --------------- | ----------------- |
| RangeStart     | Utilisez ce paramètre et le paramètre RangeEnd pour récupérer tous les enregistrements associés aux groupes de facturation pour la période comprise entre RangeStart et RangeEnd. | O | « 2021-03-03 00:00:00 » |
| RangeEnd     | Utilisez ce paramètre et le paramètre RangeStart pour récupérer tous les enregistrements associés aux groupes de facturation pour la période comprise entre RangeStart et RangeEnd. | O |« 2021-03-10 00:00:00 »|
| VaultSubscriptionList  | Utilisez ce paramètre pour filtrer la sortie de la fonction sur un ensemble d'abonnements pour lesquels il existe des données de sauvegarde. En spécifiant une liste d'ID d'abonnements séparés par des virgules comme paramètre de cette fonction, vous récupérerez uniquement les groupes de facturation figurant dans les abonnements spécifiés. Par défaut, la valeur de ce paramètre est « * », ce qui permet à la fonction de rechercher des enregistrements dans tous les abonnements. | N | « 00000000-0000-0000-0000-000000000000,11111111-1111-1111-1111-111111111111 »|
| VaultLocationList     | Utilisez ce paramètre pour filtrer la sortie de la fonction sur un ensemble de régions pour lesquelles il existe des données de sauvegarde. En spécifiant une liste de régions séparées par des virgules comme paramètre de cette fonction, vous récupérerez uniquement les groupes de facturation figurant dans les régions spécifiées. Par défaut, la valeur de ce paramètre est « * », ce qui permet à la fonction de rechercher des enregistrements dans toutes les régions. | N | « eastus,westus »|
| VaultList    |Utilisez ce paramètre pour filtrer la sortie de la fonction sur un ensemble de coffres particulier. En spécifiant une liste de coffres séparés par des virgules comme paramètre de cette fonction, vous récupérerez uniquement les enregistrements d'instances de sauvegarde se rapportant aux coffres spécifiés. Par défaut, la valeur de ce paramètre est « * », ce qui permet à la fonction de rechercher des enregistrements de groupes de facturation dans tous les coffres. | N |« vault1,vault2,vault3 »|
| VaultTypeList     | Utilisez ce paramètre pour filtrer la sortie de la fonction sur les enregistrements appartenant à un type de coffre particulier. Actuellement, le seul type de coffre pris en charge est « Microsoft.RecoveryServices/vaults », qui correspond à la valeur par défaut de ce paramètre. | N | « Microsoft.RecoveryServices/vaults »|
| ExcludeLegacyEvent  | Utilisez ce paramètre pour choisir d'interroger ou non les données de la table AzureDiagnostics héritée. Si la valeur de ce paramètre est false, la fonction interroge les données de la table AzureDiagnostics et des tables spécifiques aux ressources. Si la valeur de ce paramètre est true, la fonction interroge les données des tables spécifiques aux ressources uniquement. La valeur par défaut est true. | N | true |
| BackupSolutionList | Utilisez ce paramètre pour filtrer la sortie de la fonction sur un ensemble de solutions de sauvegarde utilisées dans votre environnement Azure. Par exemple, si vous spécifiez « Azure Virtual Machine Backup,SQL in Azure VM Backup,DPM » comme valeur de ce paramètre, la fonction renvoie uniquement les enregistrements qui sont liés aux éléments sauvegardés à l'aide de Sauvegarde de machines virtuelles Azure, SQL dans Sauvegarde de machines virtuelles Azure ou DPM dans Sauvegarde Azure. Par défaut, la valeur de ce paramètre est « * », ce qui permet à la fonction de renvoyer des enregistrements se rapportant à toutes les solutions de sauvegarde prises en charge par le service Rapports de sauvegarde (les valeurs prises en charge sont « Sauvegarde de machines virtuelles Azure », « SQL dans Sauvegarde de machines virtuelles Azure », « SAP HANA dans Sauvegarde de machines virtuelles Azure », « Sauvegarde Azure (Azure Files) », « Agent de sauvegarde Azure », « DPM », « Serveur de sauvegarde Azure » ou une combinaison de ces valeurs séparées par des virgules. | N | « Azure Virtual Machine Backup,SQL in Azure VM Backup,DPM,Azure Backup Agent » |
| BillingGroupName | Utilisez ce paramètre pour rechercher un groupe de facturation spécifique, par nom. Par défaut, la valeur est « * », ce qui permet à la fonction de rechercher tous les groupes de facturation. | N | « testvm » |
| AggregationType | Utilisez ce paramètre pour spécifier la granularité temporelle à laquelle les données doivent être récupérées. Si la valeur de ce paramètre est « Daily », la fonction renvoie un enregistrement par groupe de facturation par jour, ce qui vous permet d'analyser les tendances quotidiennes en termes de consommation de stockage et de taille de serveur frontal. Si la valeur de ce paramètre est « Weekly », la fonction renvoie un enregistrement par instance de sauvegarde par semaine, ce qui vous permet d'analyser les tendances hebdomadaires. De même, vous pouvez spécifier « Monthly » pour analyser les tendances mensuelles. La valeur par défaut est « Daily ». Si vous examinez des données sur des périodes plus longues, utilisez « Weekly » ou « Monthly » pour améliorer les performances des requêtes et faciliter l'analyse des tendances. | N | « Weekly » |

**Champs renvoyés**

| **Nom du champ** | **Description** |
| -------------- | --------------- |
| UniqueId | Clé primaire spécifiant l'ID unique du groupe de facturation |
| FriendlyName | Nom convivial du groupe de facturation |
| Nom | Nom du groupe de facturation |
| Type | Type de groupe de facturation. Par exemple, ProtectedContainer ou BackupItem |
| SourceSizeInMBs | Taille du serveur frontal du groupe de facturation, en Mo |
| VaultStore_StorageConsumptionInMBs | Stockage cloud total consommé par le groupe de facturation au niveau standard du coffre |
| BackupSolution | Solution de sauvegarde à laquelle le groupe de facturation est associé. Par exemple, Sauvegarde de machines virtuelles Azure, SQL dans Sauvegarde de machines virtuelles Azure, etc. |
| VaultResourceId | ID Azure Resource Manager (ARM) du coffre associé au groupe de facturation |
| VaultUniqueId | Clé étrangère qui fait référence au coffre associé au groupe de facturation |
| VaultName | Nom du coffre associé au groupe de facturation |
| VaultTags | Étiquettes du coffre associé au groupe de facturation |
| VaultSubscriptionId | ID d'abonnement du coffre associé au groupe de facturation |
| VaultLocation | Emplacement du coffre associé au groupe de facturation |
| VaultStore_StorageReplicationType | Type de réplication de stockage du coffre associé au groupe de facturation |
| VaultType | Type du coffre, à savoir « Microsoft.RecoveryServices/vaults » |
| TimeGenerated | Horodatage de l'enregistrement |
| ExtendedProperties | Propriétés supplémentaires du groupe de facturation |

## <a name="sample-queries"></a>Exemples de requêtes

Vous trouverez ci-dessous quelques exemples de requêtes qui vous permettront de vous familiariser avec l'utilisation des fonctions système.

- Tous les travaux de sauvegarde de machine virtuelle Azure qui ont échoué sur un intervalle de temps donné

    ````Kusto
    _AzureBackup_GetJobs("2021-03-05", "2021-03-06") //call function with RangeStart and RangeEnd parameters set, and other parameters with default value
    | where BackupSolution=="Azure Virtual Machine Backup" and Status=="Failed"
    | project BackupInstanceFriendlyName, BackupInstanceId, OperationCategory, Status,  JobStartDateTime=StartTime, JobDuration=DurationInSecs/3600, ErrorTitle, DataTransferred=DataTransferredInMBs
    ````

- Tous les travaux de sauvegarde de journal SQL sur un intervalle de temps donné

    ````Kusto
    _AzureBackup_GetJobs("2021-03-05", "2021-03-06","*","*","*","*",true,"*","*","*","*","*","*",false) //call function with RangeStart and RangeEnd parameters set, ExcludeLog parameter as false, and other parameters with default value
    | where BackupSolution=="SQL in Azure VM Backup" and Operation=="Log"
    | project BackupInstanceFriendlyName, BackupInstanceId, OperationCategory, Status,  JobStartDateTime=StartTime, JobDuration=DurationInSecs/3600, ErrorTitle, DataTransferred=DataTransferredInMBs
    ````

- Tendance hebdomadaire en termes de stockage de sauvegarde consommé pour la machine virtuelle « testvm »

    ````Kusto
    _AzureBackup_GetBackupInstancesTrends("2021-01-01", "2021-03-06","*","*","*","*",false,"*","*","*","*",true, "Weekly") //call function with RangeStart and RangeEnd parameters set, AggregationType parameter as Weekly, and other parameters with default value
    | where BackupSolution == "Azure Virtual Machine Backup"
    | where FriendlyName == "testvm"
    | project TimeGenerated, VaultStore_StorageConsumptionInMBs
    | render timechart 
    ````

## <a name="next-steps"></a>Étapes suivantes
[En savoir plus sur la solution Rapports de sauvegarde](./configure-reports.md)