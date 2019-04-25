---
title: Scripts PowerShell à l’aide du module Az.Search - recherche Azure
description: Créez et configurez un service Azure Search avec PowerShell. Vous pouvez évoluer un service, gérer admin et clés api de requête et interroger les informations système.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.devlang: powershell
ms.topic: conceptual
ms.date: 03/28/2019
ms.author: heidist
ms.openlocfilehash: 8f07468ccff4431e1afdf66aedc72599ddc0c25b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60194272"
---
# <a name="manage-your-azure-search-service-with-powershell"></a>Gérer votre service Azure Search avec PowerShell
> [!div class="op_single_selector"]
> * [Portail](search-manage.md)
> * [PowerShell](search-manage-powershell.md)
> * [API REST](https://docs.microsoft.com/rest/api/searchmanagement/)
> * [Kit de développement logiciel (SDK) .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.search)
> * [Python](https://pypi.python.org/pypi/azure-mgmt-search/0.1.0)> 

Vous pouvez exécuter des scripts et des applets de commande PowerShell sur Windows, Linux, ou dans [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) pour créer et configurer la recherche Azure. Le **Az.Search** module étend Azure PowerShell] avec une parité complète pour le [API REST de gestion de recherche Azure](https://docs.microsoft.com/rest/api/searchmanagement). Avec Azure PowerShell et **Az.Search**, vous pouvez effectuer les tâches suivantes :

> [!div class="checklist"]
> * [Liste de tous les services de recherche dans votre abonnement](#list-search-services)
> * [Obtenir des informations sur un service de recherche spécifique](#get-search-service-information)
> * [Créer ou supprimer un service](#create-or-delete-a-service)
> * [Régénérer les clés de l’API d’administration](#regenerate-admin-keys)
> * [Créer ou supprimer des clés api de requête](#create-or-delete-query-keys)
> * [L’échelle d’un service en augmentant ou diminuant les réplicas et partitions](#scale-replicas-and-partitions)

PowerShell ne peut pas être utilisé pour modifier le nom, région ou niveau de votre service. Des ressources dédiées sont allouées lorsqu’un service est créé. Changer le matériel sous-jacent (emplacement ou type de nœud) nécessite un nouveau service. Il n’existe aucune API ou les outils de transfert de contenu à partir d’un service à un autre. Toute la gestion du contenu consiste à utiliser [REST](https://docs.microsoft.com/rest/api/searchservice/) ou [.NET](https://docs.microsoft.com/dotnet/api/?term=microsoft.azure.search) API, et si vous souhaitez déplacer les index, vous devrez recréer et de les recharger sur un nouveau service. 

Bien qu’il n’existe aucune commande PowerShell dédié pour la gestion de contenu, vous pouvez écrire le script PowerShell qui appelle REST ou .NET pour créer et charger des index. Le **Az.Search** module par lui-même ne fournit pas ces opérations.

Autres tâches non pris en charge via PowerShell ou toute autre API (portail uniquement) :
+ [Attacher une ressource cognitive services](cognitive-search-attach-cognitive-services.md) pour [enrichi d’intelligence artificielle indexation](cognitive-search-concept-intro.md). Un service COGNITIF est attaché à des compétences, pas un abonnement ou un service.
+ [Les solutions de surveillance de module complémentaire](search-monitor-usage.md#add-on-monitoring-solutions) ou [recherche analytique de trafic](search-traffic-analytics.md) utilisés pour la surveillance d’Azure Search.

<a name="check-versions-and-load"></a>

## <a name="check-versions-and-load-modules"></a>Vérifiez les versions et charger des modules

Les exemples de cet article sont interactifs et nécessitent des autorisations élevées. Azure PowerShell (le **Az** module) doit être installé. Pour plus d’informations, consultez l’article [Installation et configuration d’Azure PowerShell](/powershell/azure/overview).

### <a name="powershell-version-check-51-or-later"></a>Vérification de la version PowerShell (5.1 ou version ultérieure)

PowerShell local doit être 5.1 ou version ultérieure, sur n’importe quel système d’exploitation pris en charge.

```azurepowershell-interactive
$PSVersionTable.PSVersion
```

### <a name="load-azure-powershell"></a>Charger Azure PowerShell

Si vous ne savez pas si **Az** est installé, exécutez la commande suivante en tant qu’une étape de vérification. 

```azurepowershell-interactive
Get-InstalledModule -Name Az
```

Certains systèmes ne pas charger automatiquement les modules. Si vous obtenez une erreur sur la commande précédente, essayez de charger le module et si cette tentative échoue, revenir en arrière pour les instructions d’installation pour voir si vous avez manqué une étape.

```azurepowershell-interactive
Import-Module -Name Az
```

### <a name="connect-to-azure-with-a-browser-sign-in-token"></a>Se connecter à Azure avec un jeton de connexion de navigateur

Vous pouvez utiliser vos informations d’identification de connexion portail pour se connecter à un abonnement dans PowerShell. Vous pouvez également [s’authentifier de manière non interactive avec un principal de service](../active-directory/develop/howto-authenticate-service-principal-powershell.md).

```azurepowershell-interactive
Connect-AzAccount
```

Si vous maintenez plusieurs abonnements Azure, définissez votre abonnement Azure. Pour afficher une liste de vos abonnements en cours, exécutez la commande suivante.

```azurepowershell-interactive
Get-AzSubscription | sort SubscriptionName | Select SubscriptionName
```

Pour spécifier l’abonnement, exécutez la commande suivante. Dans l’exemple suivant, le nom de l’abonnement est `ContosoSubscription`.

```azurepowershell-interactive
Select-AzSubscription -SubscriptionName ContosoSubscription
```

<a name="list-search-services"></a>

## <a name="list-all-azure-search-services-in-your-subscription"></a>Liste de tous les services de recherche Azure dans votre abonnement

Les commandes suivantes sont à partir de [ **Az.Resources**](https://docs.microsoft.com/powershell/module/az.resources/?view=azps-1.4.0#resources), retour d’informations sur les ressources existantes et de services déjà configurés dans votre abonnement. Si vous ne savez pas combien de services de recherche sont déjà créées, ces commandes retournent plus d’informations, vous économisant un trajet vers le portail.

La première commande retourne tous les services de recherche.

```azurepowershell-interactive
Get-AzResource -ResourceType Microsoft.Search/searchServices | ft
```

Dans la liste des services, retourner des informations sur une ressource spécifique.

```azurepowershell-interactive
Get-AzResource -ResourceName <service-name>
```

Résultats doivent ressembler à la sortie suivante.

```
Name              : my-demo-searchapp
ResourceGroupName : demo-westus
ResourceType      : Microsoft.Search/searchServices
Location          : westus
ResourceId        : /subscriptions/<alpha-numeric-subscription-ID>/resourceGroups/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp
```

## <a name="import-azsearch"></a>Importation Az.Search

Commandes à partir de [ **Az.Search** ](https://docs.microsoft.com/powershell/module/az.search/?view=azps-1.4.0#search) ne sont pas disponibles tant que vous chargez le module.

```azurepowershell-interactive
Install-Module -Name Az.Search
```

### <a name="list-all-azsearch-commands"></a>Répertorier toutes les commandes Az.Search

En guise de vérification, renvoyer une liste de commandes fournies dans le module.

```azurepowershell-interactive
Get-Command -Module Az.Search
```

Résultats doivent ressembler à la sortie suivante.

```
CommandType     Name                                Version    Source
-----------     ----                                -------    ------
Cmdlet          Get-AzSearchAdminKeyPair            0.7.1      Az.Search
Cmdlet          Get-AzSearchQueryKey                0.7.1      Az.Search
Cmdlet          Get-AzSearchService                 0.7.1      Az.Search
Cmdlet          New-AzSearchAdminKey                0.7.1      Az.Search
Cmdlet          New-AzSearchQueryKey                0.7.1      Az.Search
Cmdlet          New-AzSearchService                 0.7.1      Az.Search
Cmdlet          Remove-AzSearchQueryKey             0.7.1      Az.Search
Cmdlet          Remove-AzSearchService              0.7.1      Az.Search
Cmdlet          Set-AzSearchService                 0.7.1      Az.Search
```

## <a name="get-search-service-information"></a>Obtenir des informations de service de recherche

Après avoir **Az.Search** est importé et que vous connaissez le groupe de ressources contenant votre service de recherche, exécutez [Get-AzSearchService](https://docs.microsoft.com/powershell/module/az.search/get-azsearchservice?view=azps-1.4.0) pour retourner la définition de service, notamment le nom, région, niveau et réplica et nombre de partitions.

```azurepowershell-interactive
Get-AzSearchService -ResourceGroupName <resource-group-name>
```

Résultats doivent ressembler à la sortie suivante.

```
Name              : my-demo-searchapp
ResourceGroupName : demo-westus
ResourceType      : Microsoft.Search/searchServices
Location          : West US
Sku               : Standard
ReplicaCount      : 1
PartitionCount    : 1
HostingMode       : Default
ResourceId        : /subscriptions/<alphanumeric-subscription-ID>/resourceGroups/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp
```

## <a name="create-or-delete-a-service"></a>Créer ou supprimer un service

[**Nouvelle AzSearchService** ](https://docs.microsoft.com/powershell/module/az.search/new-azsearchadminkey?view=azps-1.4.0) est utilisé pour [créer un nouveau service de recherche](search-create-service-portal.md).

```azurepowershell-interactive
New-AzSearchService -ResourceGroupName "demo-westus" -Name "my-demo-searchapp" -Sku "Standard" -Location "West US" -PartitionCount 3 -ReplicaCount 3
``` 
Résultats doivent ressembler à la sortie suivante.

```
ResourceGroupName : demo-westus
Name              : my-demo-searchapp
Id                : /subscriptions/<alphanumeric-subscription-ID>/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp
Location          : West US
Sku               : Standard
ReplicaCount      : 3
PartitionCount    : 3
HostingMode       : Default
Tags
```     

## <a name="regenerate-admin-keys"></a>Régénération des clés d’administration

[**Nouvelle AzSearchAdminKey** ](https://docs.microsoft.com/powershell/module/az.search/new-azsearchadminkey?view=azps-1.4.0) est utilisé pour substituer admin [clés API](search-security-api-keys.md). Deux clés admin sont créées avec chaque service pour un accès authentifié. Clés sont nécessaires sur chaque demande. Les deux clés d’administration sont fonctionnellement équivalents, en lui accordant l’accès en écriture complète à un service de recherche avec la possibilité de récupérer toutes les informations, ou de créer et de supprimer n’importe quel objet. Deux clés existent afin que vous pouvez utiliser une lors du remplacement de l’autre. 

Vous ne pouvez régénérer une à la fois, spécifiée comme le `primary` ou `secondary` clé. Pour un service ininterrompu, pensez à mettre à jour de tout le code client pour utiliser une clé secondaire lors de la régénération de la clé primaire. Évitez de modifier les clés pendant des opérations en cours.

Comme vous pouvez l’imaginer, si vous régénérez les clés sans mettre à jour le code client, les demandes à l’aide de l’ancienne clé échouera. La régénération de toutes les nouvelles clés de ne pas bloquer de manière permanente vous en dehors de votre service, et vous pouvez toujours accéder au service via le portail. Après avoir régénéré les clés primaires et secondaires, vous pouvez mettre à jour le code client pour utiliser les nouvelles clés et les opérations reprennent en conséquence.

Valeurs pour les clés API sont générées par le service. Vous ne peut pas fournir une clé personnalisée pour Azure Search à utiliser. De même, il n’est aucun nom défini par l’utilisateur pour l’administration des clés API. Références à la clé sont des chaînes fixe, soit `primary` ou `secondary`. 

```azurepowershell-interactive
New-AzSearchAdminKey -ResourceGroupName <resource-group-name> -ServiceName <search-service-name> -KeyKind Primary
```

Résultats doivent ressembler à la sortie suivante. Les deux clés sont retournées, même si vous modifiez uniquement l’une à la fois.

```
Primary                    Secondary
-------                    ---------
<alphanumeric-guid>        <alphanumeric-guid>  
```

## <a name="create-or-delete-query-keys"></a>Créer ou supprimer des clés de requête

[**Nouvelle AzSearchQueryKey** ](https://docs.microsoft.com/powershell/module/az.search/new-azsearchquerykey?view=azps-1.4.0) est utilisé pour créer la requête [clés API](search-security-api-keys.md) pour l’accès en lecture seule à partir d’applications clientes à un index Azure Search. Clés de requête sont utilisées pour s’authentifier auprès d’un index spécifique afin de récupérer les résultats de la recherche. Clés de requête n’accordent pas d’accès en lecture seule à d’autres éléments sur le service, tel qu’un index, une source de données ou un indexeur.

Vous ne pouvez pas fournir une clé pour la recherche Azure à utiliser. Clés API sont générées par le service.

```azurepowershell-interactive
New-AzSearchQueryKey -ResourceGroupName <resource-group-name> -ServiceName <search-service-name> -Name <query-key-name> 
```

## <a name="scale-replicas-and-partitions"></a>Réplicas de mise à l’échelle et des partitions

[**Set-AzSearchService** ](https://docs.microsoft.com/powershell/module/az.search/set-azsearchservice?view=azps-1.4.0) est utilisé pour [augmenter ou diminuer les réplicas et partitions](search-capacity-planning.md) de réajuster les ressources facturables au sein de votre service. Augmenter les réplicas ou des partitions ajoute à votre facture, qui possède à la fois des frais fixes et variables. Si vous avez un besoin temporaire pour la puissance de traitement supplémentaire, vous pouvez augmenter les réplicas et des partitions pour gérer la charge de travail. La zone de surveillance dans la page Vue d’ensemble du portail comporte des vignettes sur la latence de requête, les requêtes par seconde et la limitation, qui indique si la capacité actuelle est suffisante.

Il peut prendre un certain temps pour ajouter ou supprimer des ressources. Réglages de capacité se produisent en arrière-plan, ce qui permet des charges de travail existantes continuer. Une capacité supplémentaire est utilisée pour les demandes entrantes, dès qu’il est prêt, sans aucune configuration supplémentaire. 

Suppression de capacité peut entraîner des perturbations. Arrêter tous les travaux d’indexation et de l’indexeur avant la réduction de la capacité est recommandé d’éviter les demandes supprimées. Si ce n’est pas possible, vous pouvez envisager de réduction de la capacité de façon incrémentielle, un seul réplica et partition à la fois, jusqu'à ce que vos nouveaux niveaux de cible est atteintes.

Une fois que vous envoyez la commande, il n’existe aucun moyen pour y mettre fin à mi-chemin via. Vous devrez attendre la fin de la commande avant de revoir les nombres.

```azurepowershell-interactive
Set-AzSearchService -ResourceGroupName <resource-group-name> -Name <search-service-name> -PartitionCount 6 -ReplicaCount 6
```

Résultats doivent ressembler à la sortie suivante.

```
ResourceGroupName : demo-westus
Name              : my-demo-searchapp
Location          : West US
Sku               : Standard
ReplicaCount      : 6
PartitionCount    : 6
HostingMode       : Default
Id                : /subscriptions/65a1016d-0f67-45d2-b838-b8f373d6d52e/resourceGroups/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp
```


## <a name="next-steps"></a>Étapes suivantes

Générer un [index](search-what-is-an-index.md), [interroger un index](search-query-overview.md) à l’aide du portail, API REST ou le kit SDK .NET.

* [Création d’un index Azure Search dans le portail Azure](search-create-index-portal.md)
* [Configurer un indexeur pour charger des données provenant d’autres services](search-indexer-overview.md)
* [Interrogation d’un index de Recherche Azure à l’aide de l’Explorateur de recherche dans le Portail Azure](search-explorer.md)
* [Utilisation d'Azure Search dans .NET](search-howto-dotnet-sdk.md)