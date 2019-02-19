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
ms.date: 02/07/2019
ms.author: magoedte
ms.openlocfilehash: 4a777c2bd57d40b4bb6c8d36c996b655cb019e5f
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/11/2019
ms.locfileid: "56005367"
---
# <a name="manage-log-analytics-workspaces-in-azure-monitor"></a>Gérer les espaces de travail Log Analytics dans Azure Monitor
Azure Monitor stocke les données de journal dans un espace de travail Log Analytics, qui n'est autre qu'un conteneur de données et d'informations de configuration. Pour gérer l'accès aux données de journal, vous accomplissez diverses tâches administratives liées aux espaces de travail. Vous ou d’autres membres de votre organisation pouvez utiliser plusieurs espaces de travail pour gérer différents ensembles de données provenant de tout ou partie de votre infrastructure informatique.

Pour créer un espace de travail, vous devez :

1. J’ai un abonnement Azure.
2. Choisissez un nom d’espace de travail.
3. Associez l’espace de travail à l’un de vos abonnements et groupes de ressources.
4. Choisissez un emplacement géographique.

## <a name="determine-the-number-of-workspaces-you-need"></a>Définition du nombre d’espaces de travail nécessaires
Un espace de travail Log Analytics est une ressource Azure et un conteneur dans lequel les données sont collectées, agrégées, analysées et présentées dans Azure Monitor.

Vous pouvez disposer de plusieurs espaces de travail par abonnement Azure et avoir accès à plusieurs espaces de travail, avec la possibilité des les interroger facilement. Cette section décrit dans quelles conditions il peut être utile de créer plusieurs espaces de travail.

Un espace de travail Log Analytics offre :

* un emplacement géographique pour le stockage des données ;
* une isolation des données pour définir différents droits d’accès utilisateur ;
* une étendue pour la configuration des paramètres, comme le [niveau tarifaire](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#changing-pricing-tier), la [rétention](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period) et la [limitation des données](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#daily-cap) 

Du point de vue de la consommation, nous vous recommandons de créer aussi peu d’espaces de travail que possible. L’expérience d’administration et de requête n’en sera que plus simple et rapide. Cependant, compte tenu des caractéristiques précédentes, vous pouvez créer plusieurs espaces de travail si :

* Vous travaillez pour une entreprise internationale et vous avez besoin de stocker vos données de journal dans des régions spécifiques pour des raisons de conformité ou de souveraineté des données.
* Vous utilisez Azure et vous souhaitez éviter les frais liés au transfert de données sortantes en configurant un espace de travail dans la même région que les ressources Azure qu’il gère.
* Vous souhaitez allouer des frais à différents départements ou groupes métier en fonction de leur utilisation en créant un espace de travail pour chaque département ou groupe métier dans son propre abonnement Azure.
* Vous êtes un fournisseur de services gérés et vous devez isoler les données Log Analytics des autres données de vos clients.
* Vous gérez plusieurs clients et vous souhaitez que chaque client/département/groupe métier voie uniquement ses propres données, pas celles des autres.

Lorsque vous utilisez des agents Windows pour collecter des données, vous pouvez [configurer chacun d’entre eux pour qu’il fournisse des rapports à un ou plusieurs espaces de travail](../../azure-monitor/platform/agent-windows.md).

Si vous utilisez System Center Operations Manager, chaque groupe d’administration Operations Manager ne peut être connecté qu’à un seul espace de travail. Vous pouvez installer Microsoft Monitoring Agent sur les ordinateurs gérés par Operations Manager et configurer l’agent pour qu’il fournisse des rapports à Operations Manager et à un espace de travail Log Analytics différent.

## <a name="workspace-information"></a>Informations sur l’espace de travail
L'espace de travail Log Analytics accessible via le menu **Azure Monitor** du portail Azure permet d'analyser les données, tandis que le menu **Espaces de travail Log Analytics** permet de créer et de gérer les espaces de travail.
 

1. Connectez-vous au [Portail Azure](https://portal.azure.com) et cliquez sur **Tous les services**. Dans la liste de ressources, saisissez **Log Analytics**. Au fur et à mesure de la saisie, la liste est filtrée. Sélectionnez **Espaces de travail Log Analytics**.  

    ![Portail Azure](media/manage-access/azure-portal-01.png)  

3. Sélectionnez votre espace de travail dans la liste.

4. La page de l’espace de travail affiche des détails sur le démarrage et la configuration, ainsi que des liens vers des informations supplémentaires.  

    ![Détails de l’espace de travail](./media/manage-access/workspace-overview-page.png)  

## <a name="manage-accounts-and-users"></a>Gérer les comptes et les utilisateurs
Chaque espace de travail peut être associé à plusieurs comptes et chaque compte peut également avoir accès à plusieurs espaces de travail. L’accès est géré via des [accès reposant sur un rôle Azure](../../role-based-access-control/role-assignments-portal.md). Ces droits d’accès s’appliquent à la fois sur le portail Azure et sur l’accès à l’API.


Les activités suivantes nécessitent également des autorisations Azure :

| Action                                                          | Autorisations Azure nécessaires | Notes |
|-----------------------------------------------------------------|--------------------------|-------|
| Ajout et suppression de solutions de gestion                        | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/*` <br> `Microsoft.OperationsManagement/*` <br> `Microsoft.Automation/*` <br> `Microsoft.Resources/deployments/*/write` | Ces autorisations doivent être accordées au niveau du groupe de ressources ou de l’abonnement. |
| Modification du niveau tarifaire                                       | `Microsoft.OperationalInsights/workspaces/*/write` | |
| Affichage des données dans les mosaïques de solution *Sauvegarde* et *Site Recovery* | Administrateur/coadministrateur | Accède aux ressources déployées à l’aide du modèle de déploiement Classic |
| Gestion d’un espace de travail dans le portail Azure                        | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/workspaces/*` ||


### <a name="managing-access-to-log-analytics-workspace-using-azure-permissions"></a>Gestion de l'accès à l'espace de travail Log Analytics à l'aide des autorisations Azure
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

Nous vous recommandons d’effectuer des affectations au niveau de la ressource (espace de travail) afin d’assurer un contrôle d’accès précis.  Pour créer des rôles avec les autorisations spécifiques nécessaires, utilisez des [rôles personnalisés](../../role-based-access-control/custom-roles.md).

## <a name="next-steps"></a>Étapes suivantes
* Consultez [Présentation de l’agent Log Analytics](../../azure-monitor/platform/log-analytics-agent.md) pour collecter des données à partir d’ordinateurs dans votre centre de données ou d’un autre environnement cloud.
* Consultez [Collecter des données sur les machines virtuelles Azure](../../azure-monitor/learn/quick-collect-azurevm.md) pour configurer la collecte de données à partir de machines virtuelles Azure.  

