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
ms.date: 03/27/2019
ms.author: magoedte
ms.openlocfilehash: fd47b5de2226d88b6295cb28713db2a5d251e768
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2019
ms.locfileid: "58577244"
---
# <a name="manage-log-data-and-workspaces-in-azure-monitor"></a>Gérer les données de journal et les espaces de travail dans Azure Monitor
Azure Monitor stocke les données de journal dans un espace de travail Log Analytics, qui n'est autre qu'un conteneur de données et d'informations de configuration. Pour gérer l'accès aux données de journal, vous accomplissez diverses tâches administratives liées aux espaces de travail. Vous ou d’autres membres de votre organisation pouvez utiliser plusieurs espaces de travail pour gérer différents ensembles de données provenant de tout ou partie de votre infrastructure informatique.

Cet article explique comment gérer l’accès aux journaux et à administrer les espaces de travail qui les contiennent. 

## <a name="create-a-workspace"></a>Créer un espace de travail
Pour créer un espace de travail Analytique de journal, vous devez :

1. J’ai un abonnement Azure.
2. Choisissez un nom d’espace de travail.
3. Associez l’espace de travail à l’un de vos abonnements et groupes de ressources.
4. Choisissez un emplacement géographique.

Consultez les articles suivants pour plus d’informations sur la création d’un espace de travail :

- [Créer un espace de travail Analytique de journal dans le portail Azure](../learn/quick-create-workspace.md)
- [Créer un espace de travail Analytique des journaux avec Azure CLI 2.0](../learn/quick-create-workspace-cli.md)
- [Créer un espace de travail Analytique des journaux avec Azure PowerShell](../learn/quick-create-workspace-posh.md)

## <a name="determine-the-number-of-workspaces-you-need"></a>Définition du nombre d’espaces de travail nécessaires
Un espace de travail Log Analytics est une ressource Azure et un conteneur dans lequel les données sont collectées, agrégées, analysées et présentées dans Azure Monitor. Vous pouvez disposer de plusieurs espaces de travail par abonnement Azure et avoir accès à plusieurs espaces de travail, avec la possibilité des les interroger facilement. Cette section décrit dans quelles conditions il peut être utile de créer plusieurs espaces de travail.

Un espace de travail Log Analytics offre :

* Un emplacement géographique pour le stockage de données.
* Isolation des données pour définir les droits d’accès utilisateur différentes en mode centré sur l’espace de travail. Non applicables lorsque vous travaillez en mode centré sur les ressources.
* Étendue de la configuration des paramètres tels que [tarifaire](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#changing-pricing-tier), [rétention](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period) et [limitation des données](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#daily-cap).
* Frais associés à la rétention et l’ingestion de données sont effectuées sur la ressource de l’espace de travail.

Du point de vue de la consommation, nous vous recommandons de créer aussi peu d’espaces de travail que possible. L’expérience d’administration et de requête n’en sera que plus simple et rapide. Cependant, compte tenu des caractéristiques précédentes, vous pouvez créer plusieurs espaces de travail si :

* Vous travaillez pour une entreprise internationale et vous avez besoin de stocker vos données de journal dans des régions spécifiques pour des raisons de conformité ou de souveraineté des données.
* Vous utilisez Azure et vous souhaitez éviter les frais liés au transfert de données sortantes en configurant un espace de travail dans la même région que les ressources Azure qu’il gère.
* Vous êtes un fournisseur de services gérés et vous devez isoler les données Log Analytics des autres données de vos clients.
* Vous gérez plusieurs clients et que vous souhaitez que chaque client / service / groupe d’entreprise pour afficher leurs propres données, mais pas les données à partir d’autres, et il n’est pas nécessaire pour un client croisé consolidé / département / vue de groupe d’entreprise. ».

Lorsque vous utilisez des agents Windows pour collecter des données, vous pouvez [configurer chacun d’entre eux pour qu’il fournisse des rapports à un ou plusieurs espaces de travail](../../azure-monitor/platform/agent-windows.md).

Si vous utilisez System Center Operations Manager, chaque groupe d’administration Operations Manager ne peut être connecté qu’à un seul espace de travail. Vous pouvez installer Microsoft Monitoring Agent sur les ordinateurs gérés par Operations Manager et configurer l’agent pour qu’il fournisse des rapports à Operations Manager et à un espace de travail Log Analytics différent.

Une fois que l’architecture de l’espace de travail est défini, vous devez appliquer cette stratégie sur les ressources Azure avec [Azure Policy](../../governance/policy/overview.md). Ceci peut fournir une définition intégrée qui s’appliquera automatiquement à toutes les ressources Azure. Par exemple, vous pouvez définir une stratégie pour vous assurer que toutes vos ressources Azure dans une région particulière envoyé toutes leurs journaux de diagnostic à un espace de travail particulier.

## <a name="view-workspace-details"></a>Afficher les détails d’espace de travail
Pendant l’analyse des données dans votre espace de travail Analytique de journal à partir de la **Azure Monitor** menu dans le portail Azure, créer et gérer des espaces de travail dans le **espaces de travail Analytique de journal** menu.
 

1. Connectez-vous au [Portail Azure](https://portal.azure.com) et cliquez sur **Tous les services**. Dans la liste de ressources, saisissez **Log Analytics**. Au fur et à mesure de la saisie, la liste est filtrée. Sélectionnez **Espaces de travail Log Analytics**.  

    ![Portail Azure](media/manage-access/azure-portal-01.png)  

3. Sélectionnez votre espace de travail dans la liste.

4. La page de l’espace de travail affiche des détails sur l’espace de travail de mise en route, configuration et des liens pour plus d’informations.  

    ![Détails de l’espace de travail](./media/manage-access/workspace-overview-page.png)  


## <a name="workspace-permissions-and-scope"></a>Étendue et des autorisations de l’espace de travail
Les données à un utilisateur ayant accès à sont déterminées par plusieurs facteurs qui sont répertoriées dans le tableau suivant. Chacune est décrite dans les sections ci-dessous.

| Facteur | Description |
|:---|:---|
| [Mode d’accès](#access-modes) | Méthode utilisée pour l’utilisateur accède à l’espace de travail.  Définit l’étendue des données disponibles et le mode de contrôle d’accès qui est appliqué. |
| [Mode de contrôle d’accès](#access-control-mode) | Définition de l’espace de travail qui définit si les autorisations sont appliquées au niveau de la ressource ou d’espace de travail. |
| [autorisations](#manage-accounts-and-users) | Autorisations appliquées à l’individu ou des groupes d’utilisateurs pour l’espace de travail ou la ressource. Définit les données que l’utilisateur aura accès à. |
| [RBAC au niveau table](#table-level-rbac) | Autorisations granulaires facultatives qui s’applique à tous les utilisateurs, quel que soit leur mode d’accès ou le mode de contrôle d’accès. Définit les types de données un utilisateur peut accéder. |



## <a name="access-modes"></a>Modes d’accès
Le _mode d’accès_ fait référence à la façon dont un utilisateur accède à un espace de travail Analytique de journal et définit l’étendue des données, ils peuvent accéder. 

**Centré sur l’espace de travail**: Dans ce mode, un utilisateur peut afficher tous les journaux dans l’espace de travail qu’ils sont autorisés à. L’étendue des requêtes dans ce mode pour toutes les données dans toutes les tables dans l’espace de travail. C’est le mode d’accès utilisé lorsque les journaux sont accessibles avec l’espace de travail en tant que l’étendue, comme lorsque vous sélectionnez **journaux** à partir de la **Azure Monitor** menu dans le portail Azure.

**Centrée sur les ressources**: Lorsque vous accédez à l’espace de travail pour une ressource particulière, comme lorsque vous sélectionnez **journaux** à partir d’un menu de ressources dans le portail Azure, vous pouvez afficher les journaux pour seulement cette ressource dans toutes les tables que vous avez accès. Dans ce mode, les requêtes sont limitées uniquement aux données associées à cette ressource. Ce mode permet également de contrôle d’un accès précis en fonction du rôle (RBAC). 

> [!NOTE]
> Les journaux sont disponibles pour les requêtes centrée sur les ressources uniquement si elles ont été correctement associés à la ressource appropriée. Actuellement, les ressources suivantes ont des limitations : 
> - Ordinateurs situés en dehors d’Azure
> - Service Fabric
> - Application Insights
> - Containers
> - Journaux personnalisés créés par les API du collecteur de données HTTP
>
> Vous pouvez tester si les journaux sont correctement associés à leurs ressources en exécutant une requête et en inspectant les enregistrements vous intéresse. Si l’ID de ressource correct se trouve dans le [_ResourceId](log-standard-properties.md#_resourceid) propriété, alors que les données est disponible pour les requêtes centrée sur les ressources.

### <a name="comparing-access-modes"></a>Comparaison des modes d’accès

Le tableau suivant récapitule les modes d’accès :

| | Centré sur l’espace de travail | Centrée sur les ressources |
|:---|:---|:---|
| Qui sert chaque modèle ? | Administration centrale. Administrateurs qui ont besoin pour configurer la collecte de données et les utilisateurs qui ont besoin d’accéder à un large éventail de ressources. Également actuellement requis pour les utilisateurs aient accès aux journaux des ressources en dehors d’Azure. | Équipes de l’application. Administrateurs de ressources Azure en cours d’analyse. |
| Qu’un utilisateur a-t-il besoin pour afficher les journaux ? | Autorisations pour l’espace de travail. Consultez **autorisations de l’espace de travail** dans [gérer les comptes et utilisateurs](#manage-accounts-and-users). | Accès en lecture à la ressource. Consultez **les autorisations de ressource** dans [gérer les comptes et utilisateurs](#manage-accounts-and-users). Les autorisations peuvent être héritées (comme le groupe de ressources contenant) ou directement affectée à la ressource. Autorisation dans les journaux pour la ressource est automatiquement attribuée. |
| Quelle est la portée des autorisations ? | Espace de travail. Les utilisateurs ayant accès à l’espace de travail peuvent interroger tous les journaux dans cet espace de travail à partir de tables qu’ils sont autorisés à. Consultez [contrôle d’accès de Table](#table-level-rbac) | Ressources Azure. Utilisateur peut interroger les journaux pour les ressources qu’ils ont accès à partir de n’importe quel espace de travail, mais ne peuvent pas interroger les journaux pour d’autres ressources. |
| Comment peut-il journaux d’accès utilisateur ? | Démarrer **journaux** de **Azure Monitor** menu ou **espaces de travail Analytique de journal**. | Démarrer **journaux** dans le menu pour la ressource Azure. |


## <a name="access-control-mode"></a>Mode de contrôle d'accès
Le _mode de contrôle d’accès_ est un paramètre sur chaque espaces de travail qui définit comment les autorisations sont déterminées pour cet espace de travail.

**Nécessitent des autorisations de l’espace de travail**:  Ce mode de contrôle n’autorise pas les RBAC granulaire. Pour un utilisateur puisse accéder à l’espace de travail, ils doivent avoir des autorisations à l’espace de travail ou à des tables spécifiques. 

Si un utilisateur accède à l’espace de travail en mode centré sur l’espace de travail, ils ont accès à toutes les données des tables qui leur ont été accordées pour l’accès. Si un utilisateur accède à l’espace de travail en mode centré sur les ressources, ils auront accès uniquement aux données pour cette ressource dans toutes les tables qui leur ont été accordées pour l’accès.

Il s’agit du paramètre par défaut pour tous les espaces de travail créés avant mars 2019.

**Utiliser les autorisations de ressource ou un espace de travail**: Ce mode de contrôle permet RBAC granulaire. Les utilisateurs bénéficient d’un accès uniquement aux données associées aux ressources, ils peuvent afficher par le biais des autorisations Azure, pour lesquels ils disposent de ressources `read` autorisation. 

Lorsqu’un utilisateur accède à l’espace de travail en mode centré sur l’espace de travail, les autorisations de l’espace de travail s’appliqueront. Lorsqu’un utilisateur accède à l’espace de travail en mode centré sur les ressources, uniquement des autorisations de ressources sont vérifiées et autorisations de l’espace de travail va être ignorées. Activer le RBAC pour un utilisateur en supprimant d’autorisations de l’espace de travail et en permettant de leurs autorisations de ressource pour être reconnu.

Il s’agit du paramètre par défaut pour tous les espaces de travail créés après mars 2019.

> [!NOTE]
> Si un utilisateur dispose uniquement des autorisations de ressources à l’espace de travail, ils seulement seront en mesure d’accéder à l’espace de travail à l’aide à [mode centré sur les ressources](#access-modes).


### <a name="define-access-control-mode-in-azure-portal"></a>Définir le mode de contrôle d’accès dans le portail Azure
Vous pouvez afficher le mode de contrôle d’accès espace de travail actuel sur le **vue d’ensemble** page pour l’espace de travail dans le **espace de travail Analytique de journal** menu.

![Mode de contrôle de vue espace de travail access](media/manage-access/view-access-control-mode.png)

Vous pouvez modifier ce paramètre sur le **propriétés** page pour l’espace de travail. Modification du paramètre va être désactivée si vous n’êtes pas autorisé à configurer l’espace de travail.

![Modifier le mode d’accès espace de travail](media/manage-access/change-access-control-mode.png)

### <a name="define-access-control-mode-in-powershell"></a>Définir le mode de contrôle d’accès dans PowerShell

Utilisez la commande suivante pour examiner le mode de contrôle d’accès pour tous les espaces de travail dans l’abonnement :

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

Utilisez le script suivant pour définir le mode de contrôle d’accès pour tous les espaces de travail dans l’abonnement

```powershell
Get-AzResource -ResourceType Microsoft.OperationalInsights/workspaces -ExpandProperties | foreach {
if ($_.Properties.features.enableLogAccessUsingOnlyResourcePermissions -eq $null) 
    { $_.Properties.features | Add-Member enableLogAccessUsingOnlyResourcePermissions $true -Force }
else 
    { $_.Properties.features.enableLogAccessUsingOnlyResourcePermissions = $true }
Set-AzResource -ResourceId $_.ResourceId -Properties $_.Properties -Force
```

### <a name="define-access-mode-in-resource-manager-template"></a>Définir le mode d’accès dans le modèle Resource Manager
Pour configurer le mode d’accès dans un modèle Azure Resource Manager, définissez le **enableLogAccessUsingOnlyResourcePermissions** fonctionnalité indicateur sur l’espace de travail à une des valeurs suivantes.

- **False**: Définir l’espace de travail aux autorisations centré sur l’espace de travail. Il s’agit du paramètre par défaut si l’indicateur n’est pas défini.
- **True**: Définir l’espace de travail aux autorisations centrée sur les ressources.


## <a name="manage-accounts-and-users"></a>Gérer les comptes et les utilisateurs
Les autorisations pour l’espace de travail qui sont appliquées à un utilisateur particulier sont définies par leur mode d’accès et le [mode de contrôle d’accès](#access-control-mode) de l’espace de travail. **Autorisations de l’espace de travail** sont appliqués lorsqu’un utilisateur accède à n’importe quel espace de travail à l’aide **centré sur l’espace de travail** dans [mode centré sur l’espace de travail](#access-modes). **Autorisations de ressources** sont appliqués lorsqu’un utilisateur accède à un espace de travail avec **utiliser les autorisations de ressource ou un espace de travail** [mode de contrôle d’accès](#access-control-mode) à l’aide de [mode centré sur les ressources ](#access-modes).

### <a name="workspace-permissions"></a>Autorisations de l’espace de travail
Chaque espace de travail peut être associé à plusieurs comptes et chaque compte peut également avoir accès à plusieurs espaces de travail. L’accès est géré via des [accès reposant sur un rôle Azure](../../role-based-access-control/role-assignments-portal.md). 


Les activités suivantes nécessitent également des autorisations Azure :

| Action                                                          | Autorisations Azure nécessaires | Notes |
|-----------------------------------------------------------------|--------------------------|-------|
| Ajout et suppression de solutions de surveillance                        | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/*` <br> `Microsoft.OperationsManagement/*` <br> `Microsoft.Automation/*` <br> `Microsoft.Resources/deployments/*/write` | Ces autorisations doivent être accordées au niveau du groupe de ressources ou de l’abonnement. |
| Modification du niveau tarifaire                                       | `Microsoft.OperationalInsights/workspaces/*/write` | |
| Affichage des données dans les mosaïques de solution *Sauvegarde* et *Site Recovery* | Administrateur/coadministrateur | Accède aux ressources déployées à l’aide du modèle de déploiement Classic |
| Gestion d’un espace de travail dans le portail Azure                        | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/workspaces/*` ||


#### <a name="manage-access-to-log-analytics-workspace-using-azure-permissions"></a>Gérer l’accès à l’espace de travail Log Analytique à l’aide d’autorisations Azure 
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
| Action | `*/read`   | Possibilité de visualiser toutes les ressources Azure et la configuration des ressources. Inclut la visualisation des éléments suivants : <br> État d’extension de machine virtuelle <br> Configuration des diagnostics Azure sur les ressources <br> Totalité des paramètres et propriétés de l’ensemble des ressources |
| Action | `Microsoft.OperationalInsights/workspaces/analytics/query/action` | Possibilité d’exécuter des requêtes à l’aide de la fonction Recherche dans les journaux v2 |
| Action | `Microsoft.OperationalInsights/workspaces/search/action` | Possibilité d’exécuter des requêtes à l’aide de la fonction Recherche dans les journaux v1 |
| Action | `Microsoft.Support/*` | Possibilité d’ouvrir des cas de support |
|Non-action | `Microsoft.OperationalInsights/workspaces/sharedKeys/read` | Interdiction de lecture de la clé d’espace de travail requise pour l’utilisation de l’API de collecte de données et pour l’installation des agents. Cela empêche l’utilisateur d’ajouter de nouvelles ressources à l’espace de travail. |


Les membres du rôle *Contributeur Log Analytics* peuvent effectuer les opérations suivantes :
- Lecture de toutes les données de surveillance avec le rôle Lecteur Log Analytics  
- Création et configuration des comptes Automation  
- Ajout et suppression de solutions de gestion    
    > [!NOTE] 
    > Pour réussir ces deux actions, cette autorisation doit être accordée au niveau du groupe de ressources ou de l’abonnement.  

- Lecture des clés de compte de stockage   
- Configuration de la collecte de journaux à partir du stockage Azure  
- Modification des paramètres d’analyse pour les ressources Azure, notamment :
  - Ajout de l’extension de machine virtuelle à des machines virtuelles
  - Configuration des diagnostics Azure sur toutes les ressources Azure

> [!NOTE] 
> La possibilité d’ajouter une extension de machine virtuelle à une machine virtuelle vous offre un contrôle total sur une machine virtuelle.

Le rôle Contributeur Log Analytics inclut les actions Azure suivantes :

| Autorisation | Description |
| ---------- | ----------- |
| `*/read`     | Possibilité de visualiser toutes les ressources et la configuration des ressources. Inclut la visualisation des éléments suivants : <br> État d’extension de machine virtuelle <br> Configuration des diagnostics Azure sur les ressources <br> Totalité des paramètres et propriétés de l’ensemble des ressources |
| `Microsoft.Automation/automationAccounts/*` | Possibilité de créer et configurer les comptes Azure Automation, et notamment d’ajouter et modifier des runbooks |
| `Microsoft.ClassicCompute/virtualMachines/extensions/*` <br> `Microsoft.Compute/virtualMachines/extensions/*` | Ajout, mise à jour et suppression d’extensions de machine virtuelle, notamment l’extension Microsoft Monitoring Agent et l’extension Agent OMS pour Linux |
| `Microsoft.ClassicStorage/storageAccounts/listKeys/action` <br> `Microsoft.Storage/storageAccounts/listKeys/action` | Visualisation de la clé du compte de stockage ; opération requise pour la configuration de Log Analytics pour la lecture des journaux à partir des comptes de stockage Azure |
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

Vous devez effectuer les attributions au niveau des ressources (espace de travail) afin d’assurer le contrôle d’accès précis.  Pour créer des rôles avec les autorisations spécifiques nécessaires, utilisez des [rôles personnalisés](../../role-based-access-control/custom-roles.md).

### <a name="resource-permissions"></a>Autorisations de ressources 
Lors de l’interrogation des utilisateurs ouvre une session à partir d’un espace de travail à l’aide d’accès centrée sur les ressources, ils amèneront disposer des autorisations suivantes sur la ressource :

| Autorisation | Description |
| ---------- | ----------- |
| `Microsoft.Insights/logs/<tableName>/read`<br><br>Exemples :<br>`Microsoft.Insights/logs/*/read`<br>`Microsoft.Insights/logs/Heartbeat/read` | Capacité d’afficher toutes les données de journal pour la ressource.  |


Cette autorisation est accordée généralement à partir d’un rôle qui inclut  _\*/lecture ou_ _\*_ autorisations tels que le compte [lecteur](../../role-based-access-control/built-in-roles.md#reader) et [ Contributeur](../../role-based-access-control/built-in-roles.md#contributor) rôles. Notez que les rôles personnalisés qui incluent des actions spécifiques ou des rôles intégrés dédiés peut ne pas incluent cette autorisation.

Consultez [définissant le contrôle d’accès par table](#table-level-rbac) ci-dessous si vous souhaitez créer le contrôle d’accès différents pour différentes tables.


## <a name="table-level-rbac"></a>RBAC au niveau table
**RBAC au niveau de la table** vous permet de fournir un contrôle plus précis à des données dans un espace de travail Analytique de journal en plus des autres autorisations. Ce contrôle vous permet de définir les types de données spécifiques qui sont accessibles uniquement à un ensemble spécifique d’utilisateurs.

Implémenter le contrôle d’accès de table avec [des rôles personnalisés Azure](../../role-based-access-control/custom-roles.md) pour accorder ou refuser l’accès à spécifique [tables](../log-query/log-query-overview.md#how-azure-monitor-log-data-is-organized) dans l’espace de travail. Ces rôles sont appliqués aux espaces de travail avec centré sur l’espace de travail ou centré sur les ressources [modes de contrôle d’accès](#access-control-mode) , quel que soit l’utilisateur [mode d’accès](#access-modes).

Créer un [rôle personnalisé](../../role-based-access-control/custom-roles.md) avec les actions suivantes pour définir l’accès au contrôle d’accès de table.

- Pour accorder l’accès à une table, incluez-le dans le **Actions** section de la définition de rôle.
- Pour refuser l’accès à une table, incluez-le dans le **NotActions** section de la définition de rôle.
- Utilisez * pour spécifier toutes les tables.

Par exemple, pour créer un rôle ayant accès à la _pulsation_ et _AzureActivity_ tables, créer un rôle personnalisé à l’aide des actions suivantes :

```
"Actions":  [
              "Microsoft.OperationalInsights/workspaces/query/Heartbeat/read",
              "Microsoft.OperationalInsights/workspaces/query/AzureActivity/read"
  ],
```

Pour créer un rôle ayant accès aux seuls _SecurityBaseline_ et aucune autre table, ne créez un rôle personnalisé à l’aide des actions suivantes :

```
    "Actions":  [
        "Microsoft.OperationalInsights/workspaces/query/SecurityBaseline/read"
    ],
    "NotActions":  [
        "Microsoft.OperationalInsights/workspaces/query/*/read"
    ],
```

### <a name="custom-logs"></a>Journaux personnalisés
 Journaux personnalisés sont créés par des sources de données telles que les journaux personnalisés et les API de collecte de données HTTP. Le moyen le plus simple d’identifier le type de journal est en vérifiant les tables répertoriées sous [journaux personnalisés dans le schéma de journal](../log-query/get-started-portal.md#understand-the-schema).

 Vous ne pouvez pas actuellement accorder ou refuser l’accès aux journaux personnalisés individuels, mais vous pouvez accorder ou refuser l’accès à tous les journaux personnalisés. Pour créer un rôle ayant accès à tous les journaux personnalisés, créez un rôle personnalisé à l’aide des actions suivantes :

```
    "Actions":  [
        "Microsoft.OperationalInsights/workspaces/query/Tables.Custom/read"
    ],
```

### <a name="considerations"></a>Considérations

- Si un utilisateur se voit accorder global en lecture avec les rôles de lecteur ou collaborateur standards qui incluent le  _\*/lecture_ action, il remplace le contrôle d’accès par table et donnent leur accès à toutes les données de journal.
- Si un utilisateur se voit accorder un accès par table mais aucune autre autorisation, ils seraient en mesure d’accéder aux données de journal à partir de l’API, mais pas dans le portail Azure. Pour fournir l’accès au portail Azure, utilisez lecture du journal Analytique en tant que son rôle de base.
- Pour l’abonnement, les administrateurs ont accès à tous les types de données indépendamment des autres paramètres d’autorisation.
- Propriétaires de l’espace de travail sont traités comme tout autre utilisateur pour contrôler l’accès par table.
- Vous devez attribuer des rôles aux groupes de sécurité plutôt que des utilisateurs individuels afin de réduire le nombre d’affectations. Cela sera également vous aider à utiliser des outils de gestion de groupe existants pour configurer et vérifier l’accès.




## <a name="next-steps"></a>Étapes suivantes
* Consultez [Présentation de l’agent Log Analytics](../../azure-monitor/platform/log-analytics-agent.md) pour collecter des données à partir d’ordinateurs dans votre centre de données ou d’un autre environnement cloud.
* Consultez [Collecter des données sur les machines virtuelles Azure](../../azure-monitor/learn/quick-collect-azurevm.md) pour configurer la collecte de données à partir de machines virtuelles Azure.  

