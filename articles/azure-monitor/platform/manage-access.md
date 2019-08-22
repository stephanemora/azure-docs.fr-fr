---
title: Gérer les espaces de travail Log Analytics dans Azure Monitor | Microsoft Docs
description: Vous pouvez gérer les espaces de travail Log Analytics dans Azure Monitor en accomplissant diverses tâches administratives sur les utilisateurs, les comptes, les espaces de travail et les comptes Azure.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: d0e5162d-584b-428c-8e8b-4dcaa746e783
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/05/2019
ms.author: magoedte
ms.openlocfilehash: 59e5bbaf8deccdd8218e9c5590266070ed3b5ebb
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/19/2019
ms.locfileid: "69624335"
---
# <a name="manage-log-data-and-workspaces-in-azure-monitor"></a>Gérer les données du journal et les espaces de travail dans Azure Monitor

Azure Monitor stocke les données de [journal](data-platform-logs.md) dans un espace de travail Log Analytics, qui n’est autre qu’un conteneur de données et d’informations de configuration. Pour gérer l’accès aux données de journal, vous accomplissez diverses tâches administratives liées à vos espaces de travail.

Cet article explique comment gérer l’accès aux journaux et administrer les espaces de travail qui les contiennent, à savoir :

* Comment accorder un accès aux utilisateurs ayant besoin d’accéder à des données de journal à partir de ressources spécifiques à l’aide du Contrôle d’accès en fonction du rôle (RBAC) Azure.

* Comment accorder l’accès à l’espace de travail à l’aide d’autorisations d’espace de travail.

* Comment accorder l’accès aux utilisateurs qui doivent pouvoir accéder aux données de journal dans un tableau spécifique de l’espace de travail à l’aide d’Azure RBAC.

## <a name="define-access-control-mode"></a>Définir le mode de contrôle d’accès

Vous pouvez afficher le mode de contrôle d’accès configuré sur un espace de travail à partir du portail Azure ou avec Azure PowerShell.  Vous pouvez modifier ce paramètre à l’aide de l’une des méthodes prises en charge suivantes :

* Portail Azure

* Azure PowerShell

* Modèle Azure Resource Manager

### <a name="configure-from-the-azure-portal"></a>Configurer à partir du portail Azure

Vous pouvez afficher le mode de contrôle d’accès à l’espace de travail actuel dans la page **Vue d’ensemble** de l’espace de travail, dans le menu **Espace de travail Log Analytics**.

![Afficher le mode de contrôle d’accès à l’espace de travail](media/manage-access/view-access-control-mode.png)

1. Connectez-vous au portail Azure sur [https://portal.azure.com](https://portal.azure.com).
1. Dans le portail Azure, sélectionnez Espaces de travail Log Analytics > votre espace de travail.

Vous pouvez changer ce paramètre dans la page **Propriétés** de l’espace de travail. La possibilité de changer le paramètre est désactivée si vous n’êtes pas autorisé à configurer l’espace de travail.

![Changer le mode d’accès à l’espace de travail](media/manage-access/change-access-control-mode.png)

### <a name="configure-using-powershell"></a>Configurer à l’aide de PowerShell

Pour examiner le mode de contrôle d’accès pour tous les espaces de travail dans l’abonnement, utilisez la commande suivante :

```powershell
Get-AzResource -ResourceType Microsoft.OperationalInsights/workspaces -ExpandProperties | foreach {$_.Name + ": " + $_.Properties.features.enableLogAccessUsingOnlyResourcePermissions}
```

La sortie doit ressembler à ce qui suit :

```
DefaultWorkspace38917: True
DefaultWorkspace21532: False
```

La valeur `False` indique que l’espace de travail est configuré avec le mode d’accès en fonction du contexte de l’espace de travail.  La valeur `True` indique que l’espace de travail est configuré avec le mode d’accès en fonction du contexte de la ressource.

> [!NOTE]
> Si un espace de travail est retourné sans valeur booléenne et est vide, cela correspond également aux résultats d’une valeur `False`.
>

Utilisez le script suivant pour définir le mode de contrôle d’accès pour un espace de travail spécifique sur l’autorisation en fonction du contexte de la ressource :

```powershell
$WSName = "my-workspace"
$Workspace = Get-AzResource -Name $WSName -ExpandProperties
if ($Workspace.Properties.features.enableLogAccessUsingOnlyResourcePermissions -eq $null)
    { $Workspace.Properties.features | Add-Member enableLogAccessUsingOnlyResourcePermissions $true -Force }
else
    { $Workspace.Properties.features.enableLogAccessUsingOnlyResourcePermissions = $true }
Set-AzResource -ResourceId $Workspace.ResourceId -Properties $Workspace.Properties -Force
```

Utilisez le script suivant pour définir le mode de contrôle d’accès pour tous les espaces de travail dans l’abonnement sur l’autorisation en fonction du contexte de la ressource :

```powershell
Get-AzResource -ResourceType Microsoft.OperationalInsights/workspaces -ExpandProperties | foreach {
if ($_.Properties.features.enableLogAccessUsingOnlyResourcePermissions -eq $null)
    { $_.Properties.features | Add-Member enableLogAccessUsingOnlyResourcePermissions $true -Force }
else
    { $_.Properties.features.enableLogAccessUsingOnlyResourcePermissions = $true }
Set-AzResource -ResourceId $_.ResourceId -Properties $_.Properties -Force
```

### <a name="configure-using-a-resource-manager-template"></a>Configurer à l’aide d’un modèle Resource Manager

Pour configurer le mode d’accès dans un modèle Azure Resource Manager, définissez sur une des valeurs suivantes l’indicateur de fonctionnalité **enableLogAccessUsingOnlyResourcePermissions** sur l’espace de travail.

* **false** : définir l’espace de travail sur les autorisations en fonction du contexte de l’espace de travail. Il s’agit du paramétrage par défaut si l’indicateur n’est pas défini.
* **true** : définir l’espace de travail sur les autorisations en fonction du contexte de la ressource.

## <a name="manage-accounts-and-users"></a>Gérer les comptes et les utilisateurs

Les autorisations appliquées à l’espace de travail pour un utilisateur particulier sont définies par son [mode d’accès](design-logs-deployment.md#access-mode) et le [mode de contrôle d’accès](design-logs-deployment.md#access-control-mode) de l’espace de travail. Avec le mode de contrôle d’accès **en fonction du contexte de l’espace de travail**, vous pouvez afficher tous les journaux figurant dans l’espace de travail que vous êtes autorisé à consulter, car les requêtes dans ce mode sont étendues à toutes les données de toutes les tables au sein de l’espace de travail. Avec le mode de contrôle d’accès **en fonction du contexte de la ressource**, vous affichez les données des journal figurant dans l’espace de travail pour une ressource, un groupe de ressources ou un abonnement particuliers lorsque vous effectuez une recherche directement à partir de la ressource dans le portail Azure auquel vous avez accès. Dans ce mode, l’étendue des requêtes englobe uniquement les données associées à cette ressource.

### <a name="workspace-permissions"></a>Autorisations d’espace de travail

Chaque espace de travail peut être associé à plusieurs comptes et chaque compte peut également avoir accès à plusieurs espaces de travail. L’accès est géré via de l’[accès en fonction du rôle Azure](../../role-based-access-control/role-assignments-portal.md).

Les activités suivantes nécessitent également des autorisations Azure :

|Action |Autorisations Azure nécessaires |Notes |
|-------|-------------------------|------|
| Ajout et suppression de solutions de supervision | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/*` <br> `Microsoft.OperationsManagement/*` <br> `Microsoft.Automation/*` <br> `Microsoft.Resources/deployments/*/write` | Ces autorisations doivent être accordées au niveau du groupe de ressources ou de l’abonnement. |
| Modification du niveau tarifaire | `Microsoft.OperationalInsights/workspaces/*/write` | |
| Affichage des données dans les mosaïques de solution *Sauvegarde* et *Site Recovery* | Administrateur/coadministrateur | Accède aux ressources déployées à l’aide du modèle de déploiement Classic |
| Gestion d’un espace de travail dans le portail Azure | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/workspaces/*` ||
| Afficher les propriétés de base de l’espace de travail et entrer dans le panneau Espace de travail dans le portail | `Microsoft.OperationalInsights/workspaces/read` ||
| Interroger des journaux à l’aide d’une interface | `Microsoft.OperationalInsights/workspaces/query/read` ||
| Accéder à tous les types de journaux à l’aide de requêtes | `Microsoft.OperationalInsights/workspaces/query/*/read` ||
| Accéder à une table de journaux spécifique | `Microsoft.OperationalInsights/workspaces/query/<table_name>/read` ||
| Lire les clés de l’espace de travail pour autoriser l’envoi des journaux à cet espace de travail | `Microsoft.OperationalInsights/workspaces/sharedKeys/action` ||

## <a name="manage-access-using-azure-permissions"></a>Gérer l’accès à l’aide d’autorisations Azure

Pour accorder l’accès à l’espace de travail Log Analytics à l’aide des autorisations Azure, suivez les étapes de la page [Utiliser les attributions de rôle pour gérer l’accès à vos ressources d’abonnement Azure](../../role-based-access-control/role-assignments-portal.md).

Azure intègre deux rôles utilisateur pour les espaces de travail Log Analytics :

* Lecteur Log Analytics
* Contributeur Log Analytics

Les membres du rôle *Lecteur Log Analytics* peuvent effectuer les opérations suivantes :

* Visualisation et recherche de toutes les données d’analyse
* Visualisation des paramètres d’analyse, notamment la configuration des diagnostics Azure sur toutes les ressources Azure

Le rôle Lecteur Log Analytics inclut les actions Azure suivantes :

| Type    | Autorisation | Description |
| ------- | ---------- | ----------- |
| Action | `*/read`   | Possibilité de visualiser toutes les ressources Azure et la configuration des ressources. Inclut la visualisation des éléments suivants : <br> État d’extension de machine virtuelle <br> Configuration des diagnostics Azure sur les ressources <br> Totalité des paramètres et propriétés de l’ensemble des ressources. <br> Pour les espaces de travail, il permet aux autorisations illimitées de lire les paramètres de l’espace de travail et d’exécuter des requêtes sur les données. Consultez des options plus granulaires ci-dessus. |
| Action | `Microsoft.OperationalInsights/workspaces/analytics/query/action` | Déprécié. Il n’est pas nécessaire de les affecter aux utilisateurs. |
| Action | `Microsoft.OperationalInsights/workspaces/search/action` | Déprécié. Il n’est pas nécessaire de les affecter aux utilisateurs. |
| Action | `Microsoft.Support/*` | Possibilité d’ouvrir des cas de support |
|Non-action | `Microsoft.OperationalInsights/workspaces/sharedKeys/read` | Interdiction de lecture de la clé d’espace de travail requise pour l’utilisation de l’API de collecte de données et pour l’installation des agents. Cela empêche l’utilisateur d’ajouter de nouvelles ressources à l’espace de travail. |

Les membres du rôle *Contributeur Log Analytics* peuvent effectuer les opérations suivantes :

* Lecture de toutes les données de surveillance avec le rôle Lecteur Log Analytics
* Création et configuration des comptes Automation
* Ajout et suppression de solutions de gestion

    > [!NOTE]
    > Pour réussir ces deux actions, cette autorisation doit être accordée au niveau du groupe de ressources ou de l’abonnement.

* Lecture des clés de compte de stockage
* Configuration de la collecte de journaux d’activité à partir du stockage Azure
* Modification des paramètres d’analyse pour les ressources Azure, notamment :
  * Ajout de l’extension de machine virtuelle à des machines virtuelles
  * Configuration des diagnostics Azure sur toutes les ressources Azure

> [!NOTE]
> La possibilité d’ajouter une extension de machine virtuelle à une machine virtuelle vous offre un contrôle total sur une machine virtuelle.

Le rôle Contributeur Log Analytics inclut les actions Azure suivantes :

| Autorisation | Description |
| ---------- | ----------- |
| `*/read`     | Possibilité de visualiser toutes les ressources et la configuration des ressources. Inclut la visualisation des éléments suivants : <br> État d’extension de machine virtuelle <br> Configuration des diagnostics Azure sur les ressources <br> Totalité des paramètres et propriétés de l’ensemble des ressources. <br> Pour les espaces de travail, cela permet aux utilisateurs disposant d’autorisations illimitées de lire les paramètres de l’espace de travail et d’interroger les données. Consultez des options plus granulaires ci-dessus. |
| `Microsoft.Automation/automationAccounts/*` | Possibilité de créer et configurer les comptes Azure Automation, et notamment d’ajouter et modifier des runbooks |
| `Microsoft.ClassicCompute/virtualMachines/extensions/*` <br> `Microsoft.Compute/virtualMachines/extensions/*` | Ajout, mise à jour et suppression d’extensions de machine virtuelle, notamment l’extension Microsoft Monitoring Agent et l’extension Agent OMS pour Linux |
| `Microsoft.ClassicStorage/storageAccounts/listKeys/action` <br> `Microsoft.Storage/storageAccounts/listKeys/action` | Visualisation de la clé du compte de stockage ; opération requise pour la configuration de Log Analytics pour la lecture des journaux d’activité à partir des comptes de stockage Azure |
| `Microsoft.Insights/alertRules/*` | Ajout, mise à jour et suppression de règles d’alerte |
| `Microsoft.Insights/diagnosticSettings/*` | Ajout, mise à jour et suppression de paramètres de diagnostic sur les ressources Azure |
| `Microsoft.OperationalInsights/*` | Ajout, mise à jour et suppression de configuration pour les espaces de travail Log Analytics |
| `Microsoft.OperationsManagement/*` | Ajout et suppression de solutions de gestion |
| `Microsoft.Resources/deployments/*` | Création et suppression de déploiements ; opération requise pour l’ajout et la suppression de solutions, d’espaces de travail et de comptes Automation |
| `Microsoft.Resources/subscriptions/resourcegroups/deployments/*` | Création et suppression de déploiements ; opération requise pour l’ajout et la suppression de solutions, d’espaces de travail et de comptes Automation |

L’ajout et la suppression d’utilisateurs au niveau d’un rôle utilisateur requièrent les autorisations `Microsoft.Authorization/*/Delete` et `Microsoft.Authorization/*/Write`.

Utilisez ces rôles pour accorder aux utilisateurs l’accès à différentes étendues :

* Abonnement : accès à tous les espaces de travail de l’abonnement
* Groupe de ressources : accès à tous les espaces de travail du groupe de ressources
* Ressource : accès uniquement à l’espace de travail spécifié

Vous devez effectuer des affectations au niveau de la ressource (espace de travail) afin d’assurer un contrôle d’accès précis.  Pour créer des rôles avec les autorisations spécifiques nécessaires, utilisez des [rôles personnalisés](../../role-based-access-control/custom-roles.md).

### <a name="resource-permissions"></a>Autorisations de ressource

Quand les utilisateurs interrogent les journaux à partir d’un espace de travail en utilisant un accès en fonction du contexte de la ressource, ils disposent des autorisations suivantes sur la ressource :

| Autorisation | Description |
| ---------- | ----------- |
| `Microsoft.Insights/logs/<tableName>/read`<br><br>Exemples :<br>`Microsoft.Insights/logs/*/read`<br>`Microsoft.Insights/logs/Heartbeat/read` | Capacité d’afficher toutes les données de journal pour la ressource.  |
| `Microsoft.Insights/diagnosticSettings/write` | Possibilité de configurer les paramètres de diagnostic pour autoriser la configuration des journaux pour cette ressource. |

L’autorisation `/read` est généralement accordée à partir d’un rôle disposant d’autorisations _\*/read ou_  _\*_ tel que les rôles prédéfinis [Lecteur](../../role-based-access-control/built-in-roles.md#reader) et [Contributeur](../../role-based-access-control/built-in-roles.md#contributor). Notez que les rôles personnalisés qui incluent des actions spécifiques ou des rôles intégrés dédiés peuvent ne pas inclure cette autorisation.

Consultez [RBAC au niveau table](#table-level-rbac) ci-après si vous souhaitez créer différents contrôles d’accès pour différentes tables.

## <a name="table-level-rbac"></a>RBAC au niveau table

Le **RBAC au niveau table** vous permet de définir un contrôle plus précis sur les données dans un espace de travail Log Analytics en plus des autres autorisations. Avec ce contrôle, vous pouvez définir des types de données spécifiques qui sont accessibles uniquement à un ensemble spécifique d’utilisateurs.

Vous implémentez le contrôle d’accès au niveau table avec des [rôles personnalisés Azure](../../role-based-access-control/custom-roles.md) pour accorder ou refuser l’accès à des [tables](../log-query/logs-structure.md) spécifiques dans l’espace de travail. Ces rôles sont appliqués aux espaces de travail dont le [mode de contrôle d’accès](design-logs-deployment.md#access-control-mode) est en fonction du contexte de l’espace de travail ou en fonction du contexte de la ressource, quel que soit le [mode d’accès](design-logs-deployment.md#access-mode) de l’utilisateur.

Créez un [rôle personnalisé](../../role-based-access-control/custom-roles.md) avec les actions suivantes pour définir le contrôle d’accès à une table.

* Pour accorder l’accès à une table, incluez-la dans la section **Actions** de la définition de rôle.
* Pour refuser l’accès à une table, incluez-la dans la section **NotActions** de la définition de rôle.
* Utilisez * pour spécifier toutes les tables.

Par exemple, pour créer un rôle ayant accès aux tables _Heartbeat_ et _AzureActivity_, créez un rôle personnalisé à l’aide des actions suivantes :

```
"Actions":  [
              "Microsoft.OperationalInsights/workspaces/query/Heartbeat/read",
              "Microsoft.OperationalInsights/workspaces/query/AzureActivity/read"
  ],
```

Pour créer un rôle ayant accès uniquement à la table _SecurityBaseline_, créez un rôle personnalisé à l’aide des actions suivantes :

```
    "Actions":  [
        "Microsoft.OperationalInsights/workspaces/query/SecurityBaseline/read"
    ],
    "NotActions":  [
        "Microsoft.OperationalInsights/workspaces/query/*/read"
    ],
```

### <a name="custom-logs"></a>Journaux d’activité personnalisés

 Les journaux personnalisés sont créés à partir de sources de données telles que des journaux personnalisés et l’API Collecteur de données HTTP. Le moyen le plus simple d’identifier le type de journal consiste à vérifier les tables listées sous [Journaux personnalisés dans le schéma de journal](../log-query/get-started-portal.md#understand-the-schema).

 Vous ne pouvez pas accorder ou refuser l’accès à des journaux personnalisés spécifiques, mais vous pouvez accorder ou refuser l’accès à la totalité d’entre eux. Pour créer un rôle ayant accès à tous les journaux personnalisés, créez un rôle personnalisé à l’aide des actions suivantes :

```
    "Actions":  [
        "Microsoft.OperationalInsights/workspaces/query/Tables.Custom/read"
    ],
```

### <a name="considerations"></a>Considérations

* Si un utilisateur se voit accorder une autorisation de lecture globale avec les rôles Lecteur ou Contributeur standard qui incluent l’action _\*/read_, cette autorisation se substitue au contrôle d’accès par table et donne à l’utilisateur l’accès à toutes les données de journal.
* Si un utilisateur se voit accorder un accès par table mais aucune autre autorisation, il peut accéder aux données de journal à partir de l’API, mais pas du portail Azure. Pour fournir l’accès à partir du portail Azure, utilisez le Lecteur Log Analytics comme rôle de base.
* Les administrateurs de l’abonnement ont accès à tous les types de données indépendamment des autres paramètres d’autorisation.
* Les propriétaires d’espace de travail sont traités comme tout autre utilisateur pour le contrôle d’accès par table.
* Nous vous conseillons d’attribuer des rôles à des groupes de sécurité plutôt qu’à des utilisateurs individuels afin de réduire le nombre d’affectations. En outre, cette approche facilite l’utilisation des outils de gestion de groupe existants pour configurer et vérifier l’accès.

## <a name="next-steps"></a>Étapes suivantes

* Consultez [Présentation de l’agent Log Analytics](../../azure-monitor/platform/log-analytics-agent.md) pour collecter des données à partir d’ordinateurs dans votre centre de données ou d’un autre environnement cloud.

* Pour configurer la collecte de données à partir de machines virtuelles Azure, voir [Collecter des données sur les machines virtuelles Azure](../../azure-monitor/learn/quick-collect-azurevm.md).
