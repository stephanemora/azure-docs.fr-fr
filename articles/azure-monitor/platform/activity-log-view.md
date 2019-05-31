---
title: Afficher les événements de journal d’activité Azure dans Azure Monitor
description: Afficher le journal d’activité Azure dans Azure Monitor et récupérer avec PowerShell, CLI et API REST.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/10/2019
ms.author: johnkem
ms.subservice: logs
ms.openlocfilehash: 32578f77f2b3f30d80953bdd1099d22c945c640b
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66248113"
---
# <a name="view-and-retrieve-azure-activity-log-events"></a>Afficher et récupérer des événements de journal d’activité Azure

Le [journal d’activité Azure](activity-logs-overview.md) fournit des informations sur les événements de niveau d’abonnement qui se sont produites dans Azure. Cet article fournit des détails sur les différentes méthodes pour l’affichage et la récupération des événements du journal d’activité.

## <a name="azure-portal"></a>Portail Azure
Afficher le journal d’activité pour toutes les ressources à partir de la **moniteur** menu dans le portail Azure. Afficher le journal d’activité pour une ressource particulière à partir de la **journal d’activité** option dans le menu d’une ressource.

![Afficher le journal d’activité](./media/activity-logs-overview/view-activity-log.png)

Vous pouvez filtrer les événements de journal d’activité par les champs suivants :

* **Intervalle de temps**: Heure de début et de fin pour les événements.
* **Catégorie** : Comme décrit dans la catégorie d’événement [catégories dans le journal d’activité](activity-logs-overview.md#categories-in-the-activity-log).
* **Abonnement**: Un ou plusieurs noms d’abonnement Azure.
* **Groupe de ressources** : Un ou plusieurs groupes de ressources dans les abonnements sélectionnés.
* **Ressource (nom)** :-le nom d’une ressource spécifique.
* **Type de ressource** : Le type de ressource, par exemple _Microsoft.Compute/virtualmachines_.
* **Nom de l’opération** -le nom d’une opération Azure Resource Manager, par exemple _Microsoft.SQL/servers/Write_.
* **Gravité** : Niveau de gravité de l’événement. Les valeurs disponibles sont _information_, _avertissement_, _erreur_, _critique_.
* **Événement lancé par** : L’utilisateur ayant effectué l’opération.
* **Ouvre recherche**: Ouvrez la zone de recherche de texte recherche cette chaîne dans les champs de tous les événements.

### <a name="view-change-history"></a>Afficher l’historique de modification

Lorsque vous examinez le journal d’activité, elle peut aider à voir ce que les modifications apportées pendant cette heure de l’événement. Vous pouvez afficher ces informations avec **l’historique des modifications**. Sélectionnez un événement dans le journal d’activité que vous souhaitez rechercher plus profondément dans. Sélectionnez le **historique (version préliminaire) des modifications** onglet Afficher toutes les modifications associées avec cet événement.

![Modifier la liste d’historique pour un événement](media/activity-logs-overview/change-history-event.png)

Si des modifications sont apportées à l’événement associées, vous verrez une liste des modifications que vous pouvez sélectionner. Cela ouvre le **historique (version préliminaire) des modifications** page. Dans cette page, vous voyez les modifications apportées à la ressource. Comme vous pouvez le voir à partir de l’exemple suivant, nous sommes en mesure de voir non seulement que la machine virtuelle modifié tailles, mais ce que la taille de machine virtuelle précédente était avant la modification et qu’il a été remplacé par.

![Page de l’historique de modification montrant les différences](media/activity-logs-overview/change-history-event-details.png)

Pour en savoir plus sur l’historique des modifications, consultez [obtenir les modifications des ressources](../../governance/resource-graph/how-to/get-resource-changes.md).


## <a name="log-analytics-workspace"></a>Espace de travail Log Analytics
Cliquez sur **journaux** en haut de la **journal d’activité** page pour ouvrir la [Analytique de journal d’activité de solution de surveillance](activity-log-collect.md) pour l’abonnement. Vous pourrez ainsi afficher analytique pour le journal d’activité et d’exécuter [enregistrer des requêtes](../log-query/log-query-overview.md) avec la **AzureActivity** table. Si votre journal d’activité n’est pas connecté à un espace de travail Analytique de journal, vous devrez effectuer cette configuration.



## <a name="powershell"></a>PowerShell
Utilisez le [Get-AzLog](https://docs.microsoft.com/powershell/module/az.monitor/get-azlog) applet de commande pour récupérer le journal d’activité à partir de PowerShell. Voici quelques exemples courants.

> [!NOTE]
> `Get-AzLog` fournit uniquement 15 jours d'historique. Utilisez le **- MaxEvents** paramètre pour interroger les N derniers événements au-delà de 15 jours. Pour accéder aux événements antérieurs à 15 jours, utilisez l’API REST ou le Kit de développement logiciel. Si vous n'incluez pas **StartTime**, la valeur par défaut est **EndTime** moins une heure. Si vous n'incluez pas **EndTime**, la valeur par défaut est l'heure actuelle. Toutes les heures sont exprimées en heure UTC.


Obtenir les entrées de journal créées après une date particulière heure :

```powershell
Get-AzLog -StartTime 2016-03-01T10:30
```

Obtenir les entrées de journal entre une plage de date / heure :

```powershell
Get-AzLog -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

Obtenir les entrées de journal à partir d'un groupe de ressources spécifique :

```powershell
Get-AzLog -ResourceGroup 'myrg1'
```

Obtenir les entrées de journal à partir d’un fournisseur de ressources spécifique entre une plage de date / heure :

```powershell
Get-AzLog -ResourceProvider 'Microsoft.Web' -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

Obtenir les entrées de journal avec un appelant spécifique :

```powershell
Get-AzLog -Caller 'myname@company.com'
```

Obtenir les 1000 derniers événements :

```powershell
Get-AzLog -MaxEvents 1000
```


## <a name="cli"></a>Interface de ligne de commande
Utilisez [az moniteur-journal d’activité](cli-samples.md#view-activity-log-for-a-subscription) pour récupérer le journal d’activité à partir de l’interface CLI. Voici quelques exemples courants.


Afficher toutes les options disponibles.

```azurecli
az monitor activity-log list -h
```

Obtenir les entrées de journal à partir d'un groupe de ressources spécifique :

```azurecli
az monitor activity-log list --resource-group <group name>
```

Obtenir les entrées de journal avec un appelant spécifique :

```azurecli
az monitor activity-log list --caller myname@company.com
```

Obtenir les journaux par appelant sur un type de ressource, dans une plage de dates :

```azurecli
az monitor activity-log list --resource-provider Microsoft.Web \
    --caller myname@company.com \
    --start-time 2016-03-08T00:00:00Z \
    --end-time 2016-03-16T00:00:00Z
```

## <a name="rest-api"></a>API REST
Utilisez le [API REST Azure Monitor](https://docs.microsoft.com/rest/api/monitor/) pour récupérer le journal d’activité à partir d’un client REST. Voici quelques exemples courants.

Obtenir les journaux d’activité avec filtre :

``` HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '2018-01-21T20:00:00Z' and eventTimestamp le '2018-01-23T20:00:00Z' and resourceGroupName eq 'MSSupportGroup'
```

Obtenir les journaux d’activité avec un filtre, puis sélectionnez :

```HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '2015-01-21T20:00:00Z' and eventTimestamp le '2015-01-23T20:00:00Z' and resourceGroupName eq 'MSSupportGroup'&$select=eventName,id,resourceGroupName,resourceProviderName,operationName,status,eventTimestamp,correlationId,submissionTimestamp,level
```

Obtenir les journaux d’activité avec select :

```HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&$select=eventName,id,resourceGroupName,resourceProviderName,operationName,status,eventTimestamp,correlationId,submissionTimestamp,level
```

Obtenir les journaux d’activité sans filtre ou sélectionnez :

```HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01
```


## <a name="next-steps"></a>Étapes suivantes

* [Lire une vue d’ensemble du journal d’activité](activity-logs-overview.md)
* [Archiver le journal d’activité vers le stockage ou de diffuser en continu vers Event Hubs](activity-log-export.md)
* [Stream the Azure Activity Log to Event Hubs (Diffuser en continu le journal d’activités Azure vers Event Hubs)](activity-logs-stream-event-hubs.md)
* [Archiver le journal d’activité Azure vers le stockage](archive-activity-log.md)

