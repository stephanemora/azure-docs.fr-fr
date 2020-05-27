---
title: Afficher les événements du journal d’activité Azure dans Azure Monitor
description: Affichez le journal d’activité Azure dans Azure Monitor et récupérez des éléments avec PowerShell, l'interface CLI et l'API REST.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/07/2019
ms.author: johnkem
ms.subservice: logs
ms.openlocfilehash: 3327f0bdaff641055cf84ab205d847f0fb73bfe8
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/25/2020
ms.locfileid: "83834609"
---
# <a name="view-and-retrieve-azure-activity-log-events"></a>Afficher et récupérer les événements du journal d’activité Azure

Le [journal d’activité Azure](platform-logs-overview.md) apporte des insights sur les événements liés aux abonnements qui se sont produits dans Azure. Cet article fournit des détails sur les différentes méthodes d'affichage et de récupération des événements du journal d’activité.

## <a name="azure-portal"></a>Portail Azure
Affichez le journal d’activité de toutes les ressources à partir du menu **Surveiller** dans le portail Azure. Affichez le journal d’activité d’une ressource particulière à partir de l’option **Journal d’activité** disponible dans le menu de cette ressource.

![Afficher le journal d’activité](./media/activity-logs-overview/view-activity-log.png)

Vous pouvez filtrer les événements du journal d’activité en fonction des champs suivants :

* **Intervalle de temps** : heure de début et de fin des événements.
* **Catégorie** : catégorie d’événement comme décrit dans [Catégories du journal d’activité](activity-log-view.md#categories-in-the-activity-log).
* **Abonnement**: un ou plusieurs noms d’abonnements Azure.
* **Groupe de ressources** : un ou plusieurs groupes de ressources au sein de ces abonnements.
* **Ressource (nom)**  : nom d’une ressource spécifique.
* **Type de ressource** : type de ressource, par exemple, _Microsoft.Compute/virtualmachines_.
* **Nom de l’opération** : nom d’une opération Azure Resource Manager, par exemple, _Microsoft.SQL/servers/Write_.
* **Gravité** : Niveau de gravité de l’événement. Les valeurs disponibles sont _Information_, _Avertissement_, _Erreur_, _Critique_.
* **Événement lancé par** : utilisateur qui a effectué l’opération.
* **Recherche libre** : zone de recherche textuelle libre qui permet de rechercher une chaîne dans les champs de tous les événements.

## <a name="categories-in-the-activity-log"></a>Catégories dans le journal d’activité
Chaque événement dans le journal d’activité est associé à l’une des catégories décrites dans le tableau suivant. Pour plus d’informations sur le schéma de ces catégories, consultez [Schéma d’événements du journal d’activité](activity-log-schema.md). 

| Category | Description |
|:---|:---|
| Administratif | Contient l’enregistrement de toutes les opérations de création, mise à jour, suppression et action effectuées par le biais de Resource Manager. Les événements _créer une machine virtuelle_ et _supprimer un groupe de sécurité réseau_ sont deux exemples d’événements Administratif.<br><br>Chaque action effectuée par un utilisateur ou une application avec Resource Manager est modélisée comme une opération effectuée sur un type de ressource spécifique. Si le type d’opération est _Écrire_, _Supprimer_ ou _Action_, les enregistrements de début et de réussite ou d’échec de cette opération sont enregistrés dans la catégorie Administratif. Les événements de la catégorie Administratif incluent également les changements apportés au contrôle d’accès en fonction du rôle dans un abonnement. |
| Service Health | Contient l’enregistrement de tous les incidents d’intégrité du service qui se sont produits dans Azure. L’événement _SQL Azure dans la région USA Est rencontre des temps d’arrêt_ est un exemple d’événement d’intégrité du service. <br><br>Les événements Service Health se présentent sous six types : _Action requise_, _Récupération assistée_, _Incident_, _Maintenance_, _Information_ ou _Sécurité_. Ces événements sont créés uniquement si une ressource dans l’abonnement est impactée par l’événement.
| Intégrité des ressources | Contient l’enregistrement de tous les événements d’intégrité de ressource survenus dans vos ressources Azure. L’événement _L’état d’intégrité de la machine virtuelle est passé à Indisponible_ est un exemple d’événement d’intégrité de ressource.<br><br>Les événements d’intégrité de ressource peuvent représenter l’un de ces quatre états d’intégrité : _Disponible_, _Indisponible_, _Détérioré_ et _Inconnu_. En outre, les événements d’intégrité de ressource peuvent être classés dans la catégorie _Lancé par la plateforme_ ou _Lancé par l’utilisateur_. |
| Alerte | Contient l’enregistrement des activations d’alertes Azure. L’événement _Le % processeur sur ma machine virtuelle a dépassé 80 au cours des 5 dernières minutes_.|
| Mise à l’échelle automatique | Contient l’enregistrement de tous les événements liés au fonctionnement du moteur de mise à l’échelle automatique selon les paramètres d’échelle automatique définis dans votre abonnement. L’événement _Échec de l’action de scale-up de la mise à l’échelle automatique_ est un exemple d’événement de mise à l’échelle automatique. |
| Recommandation | Contient les événements de recommandation d’Azure Advisor. |
| Sécurité | Contient l’enregistrement de toutes les alertes générées par Azure Security Center. L’événement _Fichier à extension double suspect exécuté_ est un exemple d’événement de sécurité. |
| Stratégie | Contient les enregistrements de toutes les opérations d’action à effet effectuées par Azure Policy. Les événements _Audit_ et _Refuser_ sont des exemples d’événements de stratégie. Chaque action effectuée par Policy est modélisée en tant qu’opération sur une ressource. |

## <a name="view-change-history"></a>Afficher l'historique des modifications

Lorsque vous examinez le journal d’activité, il peut être utile de voir les modifications apportées au moment de l'événement. L'**historique des modifications** vous permet d'afficher ces informations. Sélectionnez un événement du journal d’activité sur lequel vous souhaitez vous pencher. Sélectionnez l'onglet **Historique des modifications (préversion)** pour afficher les modifications associées à cet événement.

![Historique des modifications pour un événement](media/activity-logs-overview/change-history-event.png)

Si des modifications ont été apportées à l’événement, la liste des modifications que vous pouvez sélectionner s'affiche. Vous accédez à la page **Historique des modifications (préversion)** . Les modifications apportées à la ressource s'affichent sur cette page. Comme vous pouvez le constater dans l'exemple suivant, nous sommes en mesure de voir que la taille de la machine virtuelle a changé, ainsi que sa taille avant et après la modification.

![Page Historique des modifications affichant les différences](media/activity-logs-overview/change-history-event-details.png)

Pour en savoir plus sur l’historique des modifications, consultez [Obtenir les modifications des ressources](../../governance/resource-graph/how-to/get-resource-changes.md).






## <a name="powershell"></a>PowerShell
Utilisez la cmdlet [Get-AzLog](https://docs.microsoft.com/powershell/module/az.monitor/get-azlog) pour récupérer le journal d’activité à partir de PowerShell. Voici quelques exemples courants.

> [!NOTE]
> `Get-AzLog` fournit uniquement 15 jours d'historique. Utilisez le paramètre **-MaxRecord** pour interroger les N derniers événements, au-delà de 15 jours. Pour accéder aux événements antérieurs à 15 jours, utilisez l'API REST ou le kit de développement logiciel (SDK). Si vous n'incluez pas **StartTime**, la valeur par défaut est **EndTime** moins une heure. Si vous n'incluez pas **EndTime**, la valeur par défaut est l'heure actuelle. Toutes les heures sont exprimées en heure UTC.


Obtenir les entrées de journal créées après une date/heure donnée :

```powershell
Get-AzLog -StartTime 2016-03-01T10:30
```

Obtenir les entrées de journal entre une plage de dates/heures :

```powershell
Get-AzLog -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

Obtenir les entrées de journal à partir d'un groupe de ressources spécifique :

```powershell
Get-AzLog -ResourceGroup 'myrg1'
```

Obtenir les entrées de journal à partir d'un fournisseur de ressources spécifique entre une plage de dates/heures :

```powershell
Get-AzLog -ResourceProvider 'Microsoft.Web' -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

Obtenir les entrées de journal avec un appelant spécifique :

```powershell
Get-AzLog -Caller 'myname@company.com'
```

Obtenir les 1 000 derniers événements :

```powershell
Get-AzLog -MaxRecord 1000
```


## <a name="cli"></a>Interface de ligne de commande
Utilisez [az monitor activity-log](../samples/cli-samples.md#view-activity-log-for-a-subscription) pour récupérer le journal d’activité à partir de l’interface CLI. Voici quelques exemples courants.


Affichez toutes les options disponibles.

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

Obtenir les journaux par appelant, sur un type de ressource, dans une plage de dates :

```azurecli
az monitor activity-log list --resource-provider Microsoft.Web \
    --caller myname@company.com \
    --start-time 2016-03-08T00:00:00Z \
    --end-time 2016-03-16T00:00:00Z
```

## <a name="rest-api"></a>API REST
Utilisez l'[API REST Azure Monitor](https://docs.microsoft.com/rest/api/monitor/) pour récupérer le journal d’activité à partir d’un client REST. Voici quelques exemples courants.

Obtenir les journaux d’activité avec filter :

``` HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '2018-01-21T20:00:00Z' and eventTimestamp le '2018-01-23T20:00:00Z' and resourceGroupName eq 'MSSupportGroup'
```

Obtenir les journaux d’activité avec filter et select :

```HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '2015-01-21T20:00:00Z' and eventTimestamp le '2015-01-23T20:00:00Z' and resourceGroupName eq 'MSSupportGroup'&$select=eventName,id,resourceGroupName,resourceProviderName,operationName,status,eventTimestamp,correlationId,submissionTimestamp,level
```

Obtenir les journaux d’activité avec select :

```HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&$select=eventName,id,resourceGroupName,resourceProviderName,operationName,status,eventTimestamp,correlationId,submissionTimestamp,level
```

Obtenir les journaux d’activité sans filter ou select :

```HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01
```


## <a name="next-steps"></a>Étapes suivantes

* [Lire une vue d’ensemble des journaux de plateforme](platform-logs-overview.md)
* [Créer un paramètre de diagnostic pour envoyer des journaux d’activité à d’autres destinations](diagnostic-settings.md)
