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
ms.date: 07/16/2019
ms.author: magoedte
ms.openlocfilehash: fbfbd8e26ab3e92f06194322be7ec2be2fb180fd
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68254464"
---
# <a name="manage-log-data-and-workspaces-in-azure-monitor"></a>Gérer les données du journal et les espaces de travail dans Azure Monitor
Azure Monitor stocke les données de journal d’activité dans un espace de travail Log Analytics, qui n’est autre qu’un conteneur de données et d’informations de configuration. Pour gérer l'accès aux données de journal, vous accomplissez diverses tâches administratives liées aux espaces de travail. Vous ou d’autres membres de votre organisation pouvez utiliser plusieurs espaces de travail pour gérer différents ensembles de données provenant de tout ou partie de votre infrastructure informatique.

Cet article explique comment gérer l’accès aux journaux et administrer les espaces de travail qui les contiennent. 

## <a name="create-a-workspace"></a>Créer un espace de travail
Pour créer un espace de travail Log Analytics, vous devez satisfaire aux points suivants :

1. J’ai un abonnement Azure.
2. Choisissez un nom d’espace de travail.
3. Associez l’espace de travail à l’un de vos abonnements et groupes de ressources.
4. Choisissez un emplacement géographique.

Consultez les articles suivants pour plus d’informations sur la création d’un espace de travail :

- [Créer un espace de travail Log Analytics dans le portail Azure](../learn/quick-create-workspace.md)
- [Créer un espace de travail Log Analytics avec Azure CLI 2.0](../learn/quick-create-workspace-cli.md)
- [Créer un espace de travail Log Analytics avec Azure PowerShell](../learn/quick-create-workspace-posh.md)

## <a name="determine-the-number-of-workspaces-you-need"></a>Définition du nombre d’espaces de travail nécessaires
Un espace de travail Log Analytics est une ressource Azure et un conteneur dans lequel les données sont collectées, agrégées, analysées et présentées dans Azure Monitor. Vous pouvez disposer de plusieurs espaces de travail par abonnement Azure et avoir accès à plusieurs espaces de travail, avec la possibilité des les interroger facilement. Cette section décrit dans quelles conditions il peut être utile de créer plusieurs espaces de travail.

Un espace de travail Log Analytics offre :

* Un emplacement géographique pour le stockage des données.
* Une isolation des données pour définir différents droits d’accès utilisateur en mode centré sur l’espace de travail. Cette isolation est superflue en mode centré sur la ressource.
* Une étendue pour la configuration des paramètres, comme le [niveau tarifaire](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#changing-pricing-tier), la [conservation](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period) et la [limitation des données](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#daily-cap).
* Les frais liés à l’ingestion et à la conservation des données sont rattachés à la ressource d’espace de travail.

Du point de vue de la consommation, nous vous recommandons de créer aussi peu d’espaces de travail que possible. L’expérience d’administration et de requête n’en sera que plus simple et rapide. Cependant, compte tenu des caractéristiques précédentes, vous pouvez créer plusieurs espaces de travail si :

* Vous travaillez pour une entreprise internationale et vous avez besoin de stocker vos données de journal dans des régions spécifiques pour des raisons de conformité ou de souveraineté des données.
* Vous utilisez Azure et vous souhaitez éviter les frais liés au transfert de données sortantes en configurant un espace de travail dans la même région que les ressources Azure qu’il gère.
* Vous êtes un fournisseur de services gérés et vous devez isoler les données Log Analytics des autres données de vos clients.
* Vous gérez plusieurs clients et vous souhaitez que chaque client / service / groupe d’entreprise ait uniquement accès à ses propres données ; en outre, une vue consolidée englobant les différents clients / services / groupes d’entreprise n’est pas nécessaire.

Lorsque vous utilisez des agents Windows pour collecter des données, vous pouvez [configurer chacun d’entre eux pour qu’il fournisse des rapports à un ou plusieurs espaces de travail](../../azure-monitor/platform/agent-windows.md).

Si vous utilisez System Center Operations Manager, chaque groupe d’administration Operations Manager ne peut être connecté qu’à un seul espace de travail. Vous pouvez installer Microsoft Monitoring Agent sur les ordinateurs gérés par Operations Manager et configurer l’agent pour qu’il fournisse des rapports à Operations Manager et à un espace de travail Log Analytics différent.

Une fois définie l’architecture de l’espace de travail, vous devez appliquer cette stratégie sur les ressources Azure avec [Azure Policy](../../governance/policy/overview.md). Ce dernier peut fournir une définition intégrée qui s’applique automatiquement à toutes les ressources Azure. Par exemple, vous pouvez définir une stratégie afin que toutes vos ressources Azure dans une région particulière envoient tous leurs journaux de diagnostic à un espace de travail particulier.

## <a name="view-workspace-details"></a>Afficher les détails de l’espace de travail
Votre espace de travail Log Analytics accessible à partir du menu **Azure Monitor** du portail Azure permet d’analyser les données, tandis que le menu **Espaces de travail Log Analytics** permet de créer et de gérer les espaces de travail.
 

1. Connectez-vous au [Portail Azure](https://portal.azure.com) et cliquez sur **Tous les services**. Dans la liste de ressources, saisissez **Log Analytics**. Au fur et à mesure de la saisie, la liste est filtrée. Sélectionnez **Espaces de travail Log Analytics**.  

    ![Portail Azure](media/manage-access/azure-portal-01.png)  

3. Sélectionnez votre espace de travail dans la liste.

4. La page de l’espace de travail affiche des détails sur l’espace de travail, le démarrage et la configuration, ainsi que des liens vers des informations supplémentaires.  

    ![Détails de l’espace de travail](./media/manage-access/workspace-overview-page.png)  


## <a name="workspace-permissions-and-scope"></a>Étendue et autorisations de l’espace de travail
Les données auxquelles un utilisateur a accès sont déterminées par plusieurs facteurs, listés dans le tableau suivant. Chacun est décrit dans les sections ci-dessous.

| Facteur | Description |
|:---|:---|
| [Mode d’accès](#access-modes) | Méthode utilisée par l’utilisateur pour accéder à l’espace de travail.  Définit l’étendue des données disponibles et le mode de contrôle d’accès qui est appliqué. |
| [Mode de contrôle d’accès](#access-control-mode) | Paramètre de l’espace de travail qui détermine si des autorisations sont appliquées au niveau de la ressource ou de l’espace de travail. |
| [autorisations](#manage-accounts-and-users) | Autorisations appliquées à des utilisateurs spécifiques ou à des groupes d’utilisateurs pour l’espace de travail ou la ressource. Définit les données auxquelles l’utilisateur a accès. |
| [RBAC au niveau table](#table-level-rbac) | Autorisations granulaires facultatives qui s’appliquent à tous les utilisateurs, quel que soit leur mode d’accès ou mode de contrôle d’accès. Définit les types de données auxquels un utilisateur peut accéder. |



## <a name="access-modes"></a>Modes d’accès
Le _mode d’accès_ fait référence à la façon dont un utilisateur accède à un espace de travail Log Analytics et définit l’étendue des données auxquelles il peut accéder. 

**Centré sur l’espace de travail** : dans ce mode, un utilisateur peut afficher tous les journaux dans l’espace de travail pour lequel il dispose d’autorisations. Dans ce mode, l’étendue des requêtes englobe toutes les données de toutes les tables dans l’espace de travail. C’est ce mode d’accès qui est utilisé quand vous accédez aux journaux alors que l’étendue définie est l’espace de travail. C’est par exemple le cas quand vous sélectionnez **Journaux** à partir du menu **Azure Monitor** dans le portail Azure.

**Centré sur la ressource** : quand vous accédez à l’espace de travail pour une ressource particulière, par exemple, quand vous sélectionnez **Journaux** à partir d’un menu de ressources dans le portail Azure, vous pouvez voir les journaux pour seulement cette ressource dans toutes les tables auxquelles vous avez accès. Dans ce mode, l’étendue des requêtes englobe uniquement les données associées à cette ressource. Ce mode autorise également le contrôle d’accès en fonction du rôle (RBAC) granulaire. 

> [!NOTE]
> Les journaux sont disponibles pour les requêtes centrées sur la ressource uniquement s’ils ont été correctement associés à la ressource appropriée. Les ressources suivantes ont des limitations : 
> - Ordinateurs situés en dehors d’Azure
> - Service Fabric
> - Application Insights
> - Containers
>
> Vous pouvez tester si les journaux sont correctement associés à leurs ressources en exécutant une requête et en inspectant les enregistrements qui vous intéressent. Si l’ID de ressource correct se trouve dans la propriété [_ResourceId](log-standard-properties.md#_resourceid), les données sont disponibles pour les requêtes centrées sur la ressource.

### <a name="comparing-access-modes"></a>Comparaison des modes d’accès

Le tableau suivant récapitule les modes d’accès :

| | Centré sur l’espace de travail | Centré sur la ressource |
|:---|:---|:---|
| À qui chaque modèle s’adresse-t-il ? | Administration centrale. Les administrateurs qui ont besoin de configurer une collecte de données et les utilisateurs qui ont besoin d’accéder à un large éventail de ressources. Également nécessaire pour les utilisateurs qui doivent accéder aux journaux des ressources situées en dehors d’Azure. | Équipes d’application. Administrateurs de ressources Azure en cours de supervision. |
| De quoi un utilisateur a-t-il besoin pour voir les journaux ? | Autorisations sur l’espace de travail. Consultez **Autorisations d’espace de travail** dans [Gérer les comptes et les utilisateurs](#manage-accounts-and-users). | Accès en lecture à la ressource. Consultez **Autorisations de ressource** dans [Gérer les comptes et les utilisateurs](#manage-accounts-and-users). Les autorisations peuvent être héritées (par exemple du groupe de ressources les contenant) ou directement attribuées à la ressource. L’autorisation sur les journaux pour la ressource est automatiquement attribuée. |
| Quelle est l’étendue des autorisations ? | Espace de travail. Les utilisateurs ayant accès à l’espace de travail peuvent interroger tous les journaux dans cet espace de travail à partir des tables sur lesquelles ils ont des autorisations. Consultez [Contrôle d’accès aux tables](#table-level-rbac). | Ressource Azure. L’utilisateur peut interroger les journaux pour les ressources auxquelles ils ont accès à partir de n’importe quel espace de travail, mais il ne peut pas interroger les journaux associés à d’autres ressources. |
| Comment l’utilisateur peut-il accéder aux journaux ? | Démarrez **Journaux** dans le menu **Azure Monitor** ou **Espaces de travail Log Analytics**. | Démarrez **Journaux** dans le menu associé à la ressource Azure. |


## <a name="access-control-mode"></a>Mode de contrôle d’accès
Le _mode de contrôle d’accès_ est un paramètre sur chaque espace de travail qui définit comment les autorisations sont déterminées pour l’espace de travail concerné.

**Exiger des autorisations d’espace de travail** :  ce mode de contrôle n’autorise pas un contrôle RBAC précis. Pour qu’un utilisateur puisse accéder à l’espace de travail, il doit avoir des autorisations sur l’espace de travail ou sur des tables spécifiques. 

Si un utilisateur accède à l’espace de travail en mode centré sur l’espace de travail, il a accès à toutes les données de toutes les tables auxquelles il est autorisé à accéder. Si un utilisateur accède à l’espace de travail en mode centré sur la ressource, il a accès uniquement aux données pour cette ressource dans toutes les tables auxquelles il est autorisé à accéder.

Il s’agit du paramétrage par défaut pour tous les espaces de travail créés avant mars 2019.

**Utiliser les autorisations de ressource ou d’espace de travail** : ce mode de contrôle autorise un contrôle RBAC précis. Les utilisateurs bénéficient d’un accès uniquement aux données associées aux ressources qu’ils peuvent afficher par le biais d’autorisations Azure, ressources pour lesquelles ils disposent de l’autorisation `read`. 

Quand un utilisateur accède à l’espace de travail en mode centré sur l’espace de travail, les autorisations d’espace de travail s’appliquent. Quand un utilisateur accède à l’espace de travail en mode centré sur la ressource, seules les autorisations de ressource sont vérifiées, les autorisations d’espace de travail étant ignorées. Activez le contrôle RBAC pour un utilisateur en le supprimant des autorisations d’espace de travail et en permettant à ses autorisations de ressource d’être reconnues.

Il s’agit du paramétrage par défaut pour tous les espaces de travail créés après mars 2019.

> [!NOTE]
> Si un utilisateur dispose uniquement d’autorisations de ressource sur l’espace de travail, il ne peut accéder à l’espace de travail qu’en [mode centré sur la ressource](#access-modes).


### <a name="define-access-control-mode-in-azure-portal"></a>Définir le mode de contrôle d’accès dans le portail Azure
Vous pouvez afficher le mode de contrôle d’accès à l’espace de travail actuel dans la page **Vue d’ensemble** de l’espace de travail, dans le menu **Espace de travail Log Analytics**.

![Afficher le mode de contrôle d’accès à l’espace de travail](media/manage-access/view-access-control-mode.png)

Vous pouvez changer ce paramètre dans la page **Propriétés** de l’espace de travail. La possibilité de changer le paramètre est désactivée si vous n’êtes pas autorisé à configurer l’espace de travail.

![Changer le mode d’accès à l’espace de travail](media/manage-access/change-access-control-mode.png)

### <a name="define-access-control-mode-in-powershell"></a>Définir le mode de contrôle d’accès dans PowerShell

Pour examiner le mode de contrôle d’accès pour tous les espaces de travail dans l’abonnement, utilisez la commande suivante :

```powershell
Get-AzResource -ResourceType Microsoft.OperationalInsights/workspaces -ExpandProperties | foreach {$_.Name + ": " + $_.Properties.features.enableLogAccessUsingOnlyResourcePermissions} 
```

Pour définir le mode de contrôle d’accès pour un espace de travail spécifique, utilisez le script suivant :

```powershell
$WSName = "my-workspace"
$Workspace = Get-AzResource -Name $WSName -ExpandProperties
if ($Workspace.Properties.features.enableLogAccessUsingOnlyResourcePermissions -eq $null) 
    { $Workspace.Properties.features | Add-Member enableLogAccessUsingOnlyResourcePermissions $true -Force }
else 
    { $Workspace.Properties.features.enableLogAccessUsingOnlyResourcePermissions = $true }
Set-AzResource -ResourceId $Workspace.ResourceId -Properties $Workspace.Properties -Force
```

Pour définir le mode de contrôle d’accès pour tous les espaces de travail dans l’abonnement, utilisez le script suivant :

```powershell
Get-AzResource -ResourceType Microsoft.OperationalInsights/workspaces -ExpandProperties | foreach {
if ($_.Properties.features.enableLogAccessUsingOnlyResourcePermissions -eq $null) 
    { $_.Properties.features | Add-Member enableLogAccessUsingOnlyResourcePermissions $true -Force }
else 
    { $_.Properties.features.enableLogAccessUsingOnlyResourcePermissions = $true }
Set-AzResource -ResourceId $_.ResourceId -Properties $_.Properties -Force
```

### <a name="define-access-mode-in-resource-manager-template"></a>Définir le mode d’accès dans un modèle Resource Manager
Pour configurer le mode d’accès dans un modèle Azure Resource Manager, définissez sur une des valeurs suivantes l’indicateur de fonctionnalité **enableLogAccessUsingOnlyResourcePermissions** sur l’espace de travail.

- **false** : définir l’espace de travail sur les autorisations centrées sur l’espace de travail. Il s’agit du paramétrage par défaut si l’indicateur n’est pas défini.
- **true** : définir l’espace de travail sur les autorisations centrées sur la ressource.


## <a name="manage-accounts-and-users"></a>Gérer les comptes et les utilisateurs
Les autorisations sur l’espace de travail qui sont appliquées à un utilisateur particulier sont définies par son mode d’accès et le [mode de contrôle d’accès](#access-control-mode) de l’espace de travail. Les **autorisations d’espace de travail** sont appliquées quand un utilisateur accède à n’importe quel espace de travail en utilisant le [mode d’accès](#access-modes) **centré sur l’espace de travail**. Les **autorisations de ressource** sont appliquées quand un utilisateur accède à un espace de travail avec le [mode de contrôle d’accès](#access-control-mode) **Utiliser les autorisations de ressource ou d’espace de travail** en utilisant le [mode centré sur la ressource](#access-modes).

### <a name="workspace-permissions"></a>Autorisations d’espace de travail
Chaque espace de travail peut être associé à plusieurs comptes et chaque compte peut également avoir accès à plusieurs espaces de travail. L’accès est géré via des [accès reposant sur un rôle Azure](../../role-based-access-control/role-assignments-portal.md). 


Les activités suivantes nécessitent également des autorisations Azure :

||Action |Autorisations Azure nécessaires |Notes |
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



#### <a name="manage-access-to-log-analytics-workspace-using-azure-permissions"></a>Gérer l’accès à l’espace de travail Log Analytics à l’aide des autorisations Azure 
Pour accorder l’accès à l’espace de travail Log Analytics à l’aide des autorisations Azure, suivez les étapes de la page [Utiliser les attributions de rôle pour gérer l’accès à vos ressources d’abonnement Azure](../../role-based-access-control/role-assignments-portal.md).

Azure intègre deux rôles utilisateur pour les espaces de travail Log Analytics :
- Lecteur Log Analytics
- Contributeur Log Analytics

Les membres du rôle *Lecteur Log Analytics* peuvent effectuer les opérations suivantes :
- Visualisation et recherche de toutes les données d’analyse 
- Visualisation des paramètres d’analyse, notamment la configuration des diagnostics Azure sur toutes les ressources Azure

Le rôle Lecteur Log Analytics inclut les actions Azure suivantes :

| Type    | Autorisation | Description |
| ------- | ---------- | ----------- |
| Action | `*/read`   | Possibilité de visualiser toutes les ressources Azure et la configuration des ressources. Inclut la visualisation des éléments suivants : <br> État d’extension de machine virtuelle <br> Configuration des diagnostics Azure sur les ressources <br> Totalité des paramètres et propriétés de l’ensemble des ressources. <br> Pour les espaces de travail, il permet aux autorisations illimitées de lire les paramètres de l’espace de travail et d’exécuter des requêtes sur les données. Consultez des options plus granulaires ci-dessus. |
| Action | `Microsoft.OperationalInsights/workspaces/analytics/query/action` | Déprécié. Il n’est pas nécessaire de les affecter aux utilisateurs. |
| Action | `Microsoft.OperationalInsights/workspaces/search/action` | Déprécié. Il n’est pas nécessaire de les affecter aux utilisateurs. |
| Action | `Microsoft.Support/*` | Possibilité d’ouvrir des cas de support |
|Non-action | `Microsoft.OperationalInsights/workspaces/sharedKeys/read` | Interdiction de lecture de la clé d’espace de travail requise pour l’utilisation de l’API de collecte de données et pour l’installation des agents. Cela empêche l’utilisateur d’ajouter de nouvelles ressources à l’espace de travail. |


Les membres du rôle *Contributeur Log Analytics* peuvent effectuer les opérations suivantes :
- Lecture de toutes les données de surveillance avec le rôle Lecteur Log Analytics  
- Création et configuration des comptes Automation  
- Ajout et suppression de solutions de gestion    
    > [!NOTE] 
    > Pour réussir ces deux actions, cette autorisation doit être accordée au niveau du groupe de ressources ou de l’abonnement.  

- Lecture des clés de compte de stockage   
- Configuration de la collecte de journaux d’activité à partir du stockage Azure  
- Modification des paramètres d’analyse pour les ressources Azure, notamment :
  - Ajout de l’extension de machine virtuelle à des machines virtuelles
  - Configuration des diagnostics Azure sur toutes les ressources Azure

> [!NOTE] 
> La possibilité d’ajouter une extension de machine virtuelle à une machine virtuelle vous offre un contrôle total sur une machine virtuelle.

Le rôle Contributeur Log Analytics inclut les actions Azure suivantes :

| Autorisation | Description |
| ---------- | ----------- |
| `*/read`     | Possibilité de visualiser toutes les ressources Azure et la configuration des ressources. Inclut la visualisation des éléments suivants : <br> État d’extension de machine virtuelle <br> Configuration des diagnostics Azure sur les ressources <br> Totalité des paramètres et propriétés de l’ensemble des ressources. <br> Pour les espaces de travail, il permet aux autorisations illimitées de lire les paramètres de l’espace de travail et d’exécuter des requêtes sur les données. Consultez des options plus granulaires ci-dessus. |
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
- Abonnement : accès à tous les espaces de travail de l’abonnement
- Groupe de ressources : accès à tous les espaces de travail du groupe de ressources
- Ressource : accès uniquement à l’espace de travail spécifié

Vous devez effectuer des affectations au niveau de la ressource (espace de travail) afin d’assurer un contrôle d’accès précis.  Pour créer des rôles avec les autorisations spécifiques nécessaires, utilisez des [rôles personnalisés](../../role-based-access-control/custom-roles.md).

### <a name="resource-permissions"></a>Autorisations de ressource 
Quand les utilisateurs interrogent les journaux à partir d’un espace de travail en utilisant un accès centré sur la ressource, ils disposent des autorisations suivantes sur la ressource :

| Autorisation | Description |
| ---------- | ----------- |
| `Microsoft.Insights/logs/<tableName>/read`<br><br>Exemples :<br>`Microsoft.Insights/logs/*/read`<br>`Microsoft.Insights/logs/Heartbeat/read` | Capacité d’afficher toutes les données de journal pour la ressource.  |
| `Microsoft.Insights/diagnosticSettings/write ` | Possibilité de configurer les paramètres de diagnostic pour autoriser la configuration des journaux pour cette ressource. |

Cette autorisation est accordée généralement à partir d’un rôle qui inclut des autorisations _\*/read ou_ _\*_ tel que les rôles [Lecteur](../../role-based-access-control/built-in-roles.md#reader) et [Contributeur](../../role-based-access-control/built-in-roles.md#contributor) intégrés. Notez que les rôles personnalisés qui incluent des actions spécifiques ou des rôles intégrés dédiés peuvent ne pas inclure cette autorisation.

Consultez [RBAC au niveau table](#table-level-rbac) ci-après si vous souhaitez créer différents contrôles d’accès pour différentes tables.


## <a name="table-level-rbac"></a>RBAC au niveau table
Le **RBAC au niveau table** vous permet de fournir un contrôle plus précis sur les données dans un espace de travail Log Analytics en plus des autres autorisations. Avec ce contrôle, vous pouvez définir des types de données spécifiques qui sont accessibles uniquement à un ensemble spécifique d’utilisateurs.

Vous implémentez le contrôle d’accès au niveau table avec des [rôles personnalisés Azure](../../role-based-access-control/custom-roles.md) pour accorder ou refuser l’accès à des [tables](../log-query/logs-structure.md) spécifiques dans l’espace de travail. Ces rôles sont appliqués aux espaces de travail dont le [mode de contrôle d’accès](#access-control-mode) est centré sur l’espace de travail ou centré sur la ressource, quel que soit le [mode d’accès](#access-modes) de l’utilisateur.

Créez un [rôle personnalisé](../../role-based-access-control/custom-roles.md) avec les actions suivantes pour définir le contrôle d’accès à une table.

- Pour accorder l’accès à une table, incluez-la dans la section **Actions** de la définition de rôle.
- Pour refuser l’accès à une table, incluez-la dans la section **NotActions** de la définition de rôle.
- Utilisez * pour spécifier toutes les tables.

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
 Les journaux personnalisés sont créés par des sources de données telles que l’API Collecte de données HTTP. Le moyen le plus simple d’identifier le type de journal consiste à vérifier les tables listées sous [Journaux personnalisés dans le schéma de journal](../log-query/get-started-portal.md#understand-the-schema).

 Vous ne pouvez pas accorder ou refuser l’accès à des journaux personnalisés spécifiques, mais vous pouvez accorder ou refuser l’accès à la totalité d’entre eux. Pour créer un rôle ayant accès à tous les journaux personnalisés, créez un rôle personnalisé à l’aide des actions suivantes :

```
    "Actions":  [
        "Microsoft.OperationalInsights/workspaces/query/Tables.Custom/read"
    ],
```

### <a name="considerations"></a>Considérations

- Si un utilisateur se voit accorder une autorisation de lecture globale avec les rôles Lecteur ou Contributeur standard qui incluent l’action _\*/read_, cette autorisation se substitue au contrôle d’accès par table et donne à l’utilisateur l’accès à toutes les données de journal.
- Si un utilisateur se voit accorder un accès par table mais aucune autre autorisation, il peut accéder aux données de journal à partir de l’API, mais pas du portail Azure. Pour fournir l’accès au portail Azure, utilisez Lecteur Log Analytics comme rôle de base.
- Les administrateurs de l’abonnement ont accès à tous les types de données indépendamment de tout autre paramètre d’autorisation.
- Les propriétaires d’espace de travail sont traités comme tout autre utilisateur pour le contrôle d’accès par table.
- Nous vous conseillons d’attribuer des rôles à des groupes de sécurité plutôt qu’à des utilisateurs individuels afin de réduire le nombre d’affectations. En outre, cette approche facilite l’utilisation des outils de gestion de groupe existants pour configurer et vérifier l’accès.




## <a name="next-steps"></a>Étapes suivantes
* Consultez [Présentation de l’agent Log Analytics](../../azure-monitor/platform/log-analytics-agent.md) pour collecter des données à partir d’ordinateurs dans votre centre de données ou d’un autre environnement cloud.
* Consultez [Collecter des données sur les machines virtuelles Azure](../../azure-monitor/learn/quick-collect-azurevm.md) pour configurer la collecte de données à partir de machines virtuelles Azure.  

