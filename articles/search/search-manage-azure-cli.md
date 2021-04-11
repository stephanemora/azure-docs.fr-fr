---
title: Scripts Azure CLI utilisant le module az search
titleSuffix: Azure Cognitive Search
description: Créez et configurez un service Recherche cognitive Azure avec Azure CLI. Vous pouvez mettre à l'échelle un service, gérer les clés API d'administration et de requête et interroger des informations système.
manager: luisca
author: DerekLegenzoff
ms.author: delegenz
ms.service: cognitive-search
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 02/17/2021
ms.openlocfilehash: 456aaf20c0b6d198ae353490d961a69a319b6601
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105045108"
---
# <a name="manage-your-azure-cognitive-search-service-with-the-azure-cli"></a>Gérer votre service Recherche cognitive Azure avec Azure CLI
> [!div class="op_single_selector"]
> * [Portail](search-manage.md)
> * [PowerShell](search-manage-powershell.md)
> * [Azure CLI](search-manage-azure-cli.md)
> * [REST API](/rest/api/searchmanagement/)
> * [Kit de développement logiciel (SDK) .NET](/dotnet/api/microsoft.azure.management.search)
> * [Python](https://pypi.python.org/pypi/azure-mgmt-search/0.1.0)

Vous pouvez exécuter des scripts et des commandes Azure CLI sur Windows, macOS, Linux ou dans [Azure Cloud Shell](../cloud-shell/overview.md) pour créer et configurer la Recherche cognitive Azure. Le module [**az search**](/cli/azure/search) étend [Azure CLI](/cli/) avec une parité complète pour les [API REST de gestion de la recherche](/rest/api/searchmanagement) et la capacité à réaliser les tâches suivantes :

> [!div class="checklist"]
> * [Lister les services de recherche dans un abonnement](#list-search-services)
> * [Retourner les informations sur les services](#get-search-service-information)
> * [Créer ou supprimer un service](#create-or-delete-a-service)
> * [Créer un service avec un point de terminaison privé](#create-a-service-with-a-private-endpoint)
> * [Régénérer des clés API d’administration](#regenerate-admin-keys)
> * [Créer ou supprimer des clés API de requête](#create-or-delete-query-keys)
> * [Effectuer un scale-up ou un scale-down avec les réplicas et les partitions](#scale-replicas-and-partitions)
> * [Créer une ressource de liaison privée partagée](#create-a-shared-private-link-resource)

Parfois, des questions sont posées sur des tâches qui ne figurent *pas* dans la liste ci-dessus. Vous ne pouvez pas utiliser le module **az search**  ou l’API REST de gestion pour changer un nom de serveur, une région ou un niveau. Des ressources dédiées sont allouées lorsqu’un service est créé. Ainsi, toute modification du matériel sous-jacent (emplacement ou type de nœud) nécessite un nouveau service. De même, il n’existe pas d’API ou d’outils pour transférer du contenu, tel qu’un index, d’un service à un autre.

Au sein d’un service, la création et la gestion de contenus s’effectuent par le biais de l’[API REST du service de recherche](/rest/api/searchservice/) ou du [SDK .NET](/dotnet/api/overview/azure/search.documents-readme). Bien qu’il n’existe aucune commande PowerShell dédiée au contenu, vous pouvez écrire des scripts qui appellent des API REST ou .NET afin de créer et de charger des index.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

<a name="list-search-services"></a>

## <a name="list-services-in-a-subscription"></a>Répertorier les services dans un abonnement

Les commandes suivantes proviennent d’[**az resources**](/cli/azure/resource) et renvoient des informations sur les ressources et services existants déjà approvisionnés dans votre abonnement. Si vous ne savez pas combien de services de recherche sont déjà créés, ces commandes vous en informent et vous évitent d'accéder au portail.

La première commande renvoie tous les services de recherche.

```azurecli-interactive
az resource list --resource-type Microsoft.Search/searchServices --output table
```

Dans la liste des services, elle permet d'obtenir des informations sur une ressource spécifique.

```azurecli-interactive
az resource list --name <service-name>
```

## <a name="list-all-az-search-commands"></a>Répertorier toutes les commandes az search

Vous pouvez afficher des informations sur les sous-groupes et les commandes disponibles dans [**az search**](/cli/azure/search) à partir de l’interface CLI. Vous pouvez également consulter la [documentation](/cli/azure/search).

Pour afficher les sous-groupes disponibles dans `az search`, exécutez la commande suivante.

```azurecli-interactive
az search --help
```

La réponse doit être semblable à ma sortie suivante.

```
Group
    az search : Manage Azure Search services, admin keys and query keys.
        WARNING: This command group is in preview and under development. Reference and support
        levels: https://aka.ms/CLI_refstatus
Subgroups:
    admin-key                    : Manage Azure Search admin keys.
    private-endpoint-connection  : Manage Azure Search private endpoint connections.
    private-link-resource        : Manage Azure Search private link resources.
    query-key                    : Manage Azure Search query keys.
    service                      : Manage Azure Search services.
    shared-private-link-resource : Manage Azure Search shared private link resources.

For more specific examples, use: az find "az search"
```

Dans chaque sous-groupe, plusieurs commandes sont disponibles. Vous pouvez voir les commandes disponibles pour le sous-groupes `service` en exécutant la ligne suivante.

```azurecli-interactive
az search service --help
```

Vous pouvez également voir les arguments disponibles pour une commande particulière.

```azurecli-interactive
az search service create --help
```

## <a name="get-search-service-information"></a>Obtenir des informations sur le service de recherche

Si vous connaissez le groupe de ressources qui contient votre service de recherche, exécuter [**az search service show**](/cli/azure/search/service#az_search_service_show) pour renvoyer la définition du service, avec le nom, la région, le niveau ainsi que le nombre de partitions et de réplicas.

```azurecli-interactive
az search service show --name <service-name> --resource-group <resource-group-name>
```

## <a name="create-or-delete-a-service"></a>Créer ou supprimer un service

Pour [créer un service de recherche](search-create-service-portal.md), utilisez la commande [**az search service create**](/cli/azure/search/service#az_search_service_show).

```azurecli-interactive
az search service create \
    --name <service-name> \
    --resource-group <resource-group-name> \
    --sku Standard \
    --partition-count 1 \
    --replica-count 1
``` 

Les résultats se présentent comme suit :

```
{
  "hostingMode": "default",
  "id": "/subscriptions/<alphanumeric-subscription-ID>/resourceGroups/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp",
  "identity": null,
  "location": "West US",
  "name": "my-demo-searchapp",
  "networkRuleSet": {
    "bypass": "None",
    "ipRules": []
  },
  "partitionCount": 1,
  "privateEndpointConnections": [],
  "provisioningState": "succeeded",
  "publicNetworkAccess": "Enabled",
  "replicaCount": 1,
  "resourceGroup": "demo-westus",
  "sharedPrivateLinkResources": [],
  "sku": {
    "name": "standard"
  },
  "status": "running",
  "statusDetails": "",
  "tags": null,
  "type": "Microsoft.Search/searchServices"
}
```

### <a name="create-a-service-with-ip-rules"></a>Créer un service avec des règles IP

En fonction de vos exigences de sécurité, vous pouvez créer un service de recherche avec un [pare-feu IP configuré](service-configure-firewall.md). Pour ce faire, transmettez les adresses IP publiques (v4) ou les plages CIDR à l’argument `ip-rules` comme indiqué ci-dessous. Les règles doivent être séparées par une virgule (`,`) ou un point-virgule (`;`).

```azurecli-interactive
az search service create \
    --name <service-name> \
    --resource-group <resource-group-name> \
    --sku Standard \
    --partition-count 1 \
    --replica-count 1 \
    --ip-rules "55.5.63.73;52.228.215.197;101.37.221.205"
```

### <a name="create-a-service-with-a-system-assigned-managed-identity"></a>Créer un service avec une identité managée affectée par le système

Dans certains cas, par exemple quand [vous utilisez une identité managée pour vous connecter à une source de données](search-howto-managed-identities-storage.md), vous devez activer l’[identité managée affectée par le système](../active-directory/managed-identities-azure-resources/overview.md). Pour ce faire, ajoutez `--identity-type SystemAssigned` à la commande.

```azurecli-interactive
az search service create \
    --name <service-name> \
    --resource-group <resource-group-name> \
    --sku Standard \
    --partition-count 1 \
    --replica-count 1 \
    --identity-type SystemAssigned
```

## <a name="create-a-service-with-a-private-endpoint"></a>Créer un service avec un point de terminaison privé

Les [points de terminaison privés](../private-link/private-endpoint-overview.md)de Recherche cognitive Azure permettent à un client d’un réseau virtuel d’accéder en toute sécurité aux données d’un index de recherche grâce à une [liaison privée](../private-link/private-link-overview.md). Ils utilisent une adresse IP de [l’espace d’adressage du réseau virtuel](../virtual-network/private-ip-addresses.md) pour votre service Search. Le trafic entre le client et le service Search traverse le réseau virtuel et une liaison privée sur le réseau principal de Microsoft, ce qui élimine l’exposition sur l’Internet public. Pour plus d’informations, consultez la documentation qui explique [comment créer un point de terminaison privé pour la Recherche cognitive Azure](service-create-private-endpoint.md).

L’exemple suivant montre comment créer un service de recherche avec un point de terminaison privé.

Tout d’abord, déployez un service de recherche avec `PublicNetworkAccess` défini sur `Disabled`.

```azurecli-interactive
az search service create \
    --name <service-name> \
    --resource-group <resource-group-name> \
    --sku Standard \
    --partition-count 1 \
    --replica-count 1 \
    --public-access Disabled
```

Ensuite, créez un réseau virtuel et le point de terminaison privé.

```azurecli-interactive
# Create the virtual network
az network vnet create \
    --resource-group <resource-group-name> \
    --location "West US" \
    --name <virtual-network-name> \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name <subnet-name> \
    --subnet-prefixes 10.1.0.0/24

# Update the subnet to disable private endpoint network policies
az network vnet subnet update \
    --name <subnet-name> \
    --resource-group <resource-group-name> \
    --vnet-name <virtual-network-name> \
    --disable-private-endpoint-network-policies true

# Get the id of the search service
id=$(az search service show \
    --resource-group <resource-group-name> \
    --name <service-name> \
    --query [id] \
    --output tsv)

# Create the private endpoint
az network private-endpoint create \
    --name <private-endpoint-name> \
    --resource-group <resource-group-name> \
    --vnet-name <virtual-network-name> \
    --subnet <subnet-name> \
    --private-connection-resource-id $id \
    --group-id searchService \
    --connection-name <private-link-connection-name>  
```

Enfin, créez une zone DNS privée. 

```azurecli-interactive
## Create private DNS zone
az network private-dns zone create \
    --resource-group <resource-group-name> \
    --name "privatelink.search.windows.net"

## Create DNS network link
az network private-dns link vnet create \
    --resource-group <resource-group-name> \
    --zone-name "privatelink.search.windows.net" \
    --name "myLink" \
    --virtual-network <virtual-network-name> \
    --registration-enabled false

## Create DNS zone group
az network private-endpoint dns-zone-group create \
   --resource-group <resource-group-name>\
   --endpoint-name <private-endpoint-name> \
   --name "myZoneGroup" \
   --private-dns-zone "privatelink.search.windows.net" \
   --zone-name "searchServiceZone"
```

Pour plus d’informations sur la création de points de terminaison privés dans PowerShell, consultez ce [Guide de démarrage rapide sur les liaisons privées](../private-link/create-private-endpoint-cli.md)

### <a name="manage-private-endpoint-connections"></a>Gérer les connexions de point de terminaison privé

Vous pouvez créer une connexion de point de terminaison privé, mais également récupérer (`show`), définir (`update`) et supprimer (`delete`) cette connexion.

Pour récupérer une connexion de point de terminaison privé et afficher son état, utilisez la commande [**az search private-endpoint-connection show**](/cli/azure/search/private-endpoint-connection#az_search_private_endpoint_connection_show).

```azurecli-interactive
az search private-endpoint-connection show \
    --name <pe-connection-name> \
    --service-name <search-service-name> \
    --resource-group <resource-group-name> 
```

Pour mettre à jour la connexion, utilisez la commande [**az search private-endpoint-connection update**](/cli/azure/search/private-endpoint-connection#az_search_private_endpoint_connection_update). L’exemple suivant montre comment rejeter une connexion de point de terminaison privé :

```azurecli-interactive
az search private-endpoint-connection show \
    --name <pe-connection-name> \
    --service-name <search-service-name> \
    --resource-group <resource-group-name> 
    --status Rejected \
    --description "Rejected" \
    --actions-required "Please fix XYZ"
```

Pour supprimer la connexion au point de terminaison privé, utilisez la commande [**az search private-endpoint-connection delet**](/cli/azure/search/private-endpoint-connection#az_search_private_endpoint_connection_delete).

```azurecli-interactive
az search private-endpoint-connection delete \
    --name <pe-connection-name> \
    --service-name <search-service-name> \
    --resource-group <resource-group-name> 
```

## <a name="regenerate-admin-keys"></a>Régénération des clés d’administration

Pour remplacer les [clés de l’API](search-security-api-keys.md) Administrateur, utilisez la commande [**az search admin-key renew**](/cli/azure/search/admin-key#az_search_admin_key_renew). Deux clés d'administration sont créées avec chaque service à des fins d'accès authentifié. Ces clés sont nécessaires pour chaque requête. Les deux clés d’administration fonctionnement de la même manière, et accorde au service de recherche un accès total en écriture, avec possibilité de récupérer des informations ou de créer et de supprimer des objets. Deux clés sont disponibles pour vous permettre d'en utiliser une lorsque vous remplacez l'autre. 

Vous ne pouvez régénérer qu'une clé à la fois, spécifiée en tant que clé `primary` ou `secondary`. Pour ne pas interrompre le service, pensez à mettre à jour la totalité du code client de manière à utiliser une clé secondaire lors de la substitution de la clé primaire. Évitez tout changement de clés lorsque des opérations sont en cours.

En effet, si vous régénérez les clés sans mettre à jour le code client, les requêtes utilisant l'ancienne clé n'aboutiront pas. La régénération de toutes les nouvelles clés ne bloque indéfiniment votre service, et vous en conservez l'accès via le portail. Une fois les clés primaire et secondaire régénérées, vous pouvez mettre à jour le code client pour utiliser les nouvelles clés, après quoi les opérations reprennent.

Les valeurs correspondant aux clés API sont générées par le service. Vous ne pouvez pas fournir de clé personnalisée à des fins d'utilisation par Recherche cognitive Azure. De même, il n'existe aucun nom défini par l'utilisateur pour les clés API d'administration. Les références à la clé correspondent à des chaînes fixes, `primary` ou `secondary`. 

```azurecli-interactive
az search admin-key renew \
    --resource-group <resource-group-name> \
    --service-name <search-service-name> \
    --key-kind primary
```

Les résultats se présentent comme suit. Les deux clés sont renvoyées, même si vous n'en modifiez qu'une.

```   
{
  "primaryKey": <alphanumeric-guid>,
  "secondaryKey": <alphanumeric-guid>  
}
```

## <a name="create-or-delete-query-keys"></a>Créer ou supprimer des clés de requête

Pour créer des [clés d’API](search-security-api-keys.md) de requête à des fins d’accès en lecture seule à partir d’applications clientes vers un index Recherche cognitive Azure, utilisez la commande [**az search query-key create**](/cli/azure/search/query-key#az_search_query_key_create). Les clés de requête sont utilisées pour s’authentifier auprès d’un index spécifique et récupérer les résultats de la recherche. Les clés de requête n’accordent pas d’accès en lecture seule à d’autres éléments du service, comme un index, une source de données ou un indexeur.

Vous ne pouvez pas fournir de clé à des fins d'utilisation par Recherche cognitive Azure. Les clés API sont générées par le service.

```azurecli-interactive
az search query-key create \
    --name myQueryKey \
    --resource-group <resource-group-name> \
    --service-name <search-service-name>
```

## <a name="scale-replicas-and-partitions"></a>Mettre à l'échelle des réplicas et des partitions

Pour [augmenter ou diminuer les réplicas et les partitions,](search-capacity-planning.md) utilisez la commande [**az search service update**](/cli/azure/search/service#az_search_service_update). Augmenter les réplicas ou les partitions se répercute sur votre facture, qui présente des frais fixes et variables. En cas de besoin temporaire de plus de puissance de traitement, vous pouvez augmenter les réplicas et les partitions pour gérer la charge de travail. La zone de surveillance de la page Vue d’ensemble du portail présente des vignettes sur la latence de requête, les requêtes par seconde et les limitations, indiquant si la capacité actuelle est suffisante.

L'ajout ou la suppression de ressources peut prendre un certain temps. Des ajustements de la capacité interviennent en arrière-plan, ce qui permet la poursuite des charges de travail existantes. Une capacité supplémentaire est utilisée pour les demandes entrantes, dès qu’elle est prête, sans configuration supplémentaire. 

La suppression de la capacité peut entraîner des perturbations. Il est recommandé d'arrêter tous les travaux d'indexation avant de réduire la capacité pour éviter la suppression de requêtes. Si ce n’est pas possible, essayez de réduire la capacité de façon incrémentielle, un seul réplica et une seule partition à la fois, jusqu'à ce que vos nouveaux niveaux cibles soient atteints.

Il n'est pas possible d'arrêter la commande une fois celle-ci en cours d'exécution. Vous devez attendre qu'elle prenne fin avant d'examiner les résultats.

```azurecli-interactive
az search service update \
    --name <service-name> \
    --resource-group <resource-group-name> \
    --partition-count 6 \
    --replica-count 6
```

Outre la mise à jour des nombres de réplicas et de partitions, vous pouvez également mettre à jour `ip-rules`, `public-access` et `identity-type`.

## <a name="create-a-shared-private-link-resource"></a>Créer une ressource de liaison privée partagée

Les points de terminaison privés des ressources privées créées par les API Recherche cognitive Azure sont appelés *ressources de liaison privée partagées*. En effet, vous « partagez » l’accès à une ressource, un compte de stockage par exemple, qui a été intégrée au [service Azure Private Link](https://azure.microsoft.com/services/private-link/).

Si vous utilisez un indexeur pour indexer des données dans Recherche cognitive Azure et que votre source de données se trouve sur un réseau privé, vous pouvez créer une [connexion de point de terminaison privée](../private-link/private-endpoint-overview.md) sortante pour atteindre les données.

Si vous souhaitez obtenir la liste complète des ressources Azure pour lesquelles il est possible de créer des points de terminaison privés sortants à partir de la Recherche cognitive Azure, ainsi que les valeurs d’**ID de groupe** associées, [cliquez ici](search-indexer-howto-access-private.md#shared-private-link-resources-management-apis).

Pour créer la ressource de liaison privée partagée, utilisez la commande [**az search shared-private-link-resource create**](/cli/azure/search/shared-private-link-resource#az_search_shared_private_link_resource_list). Notez que vous devrez peut-être configurer la source de données avant d’exécuter cette commande.

```azurecli-interactive
az search shared-private-link-resource create \
    --name <spl-name> \
    --service-name <search-service-name> \
    --resource-group <resource-group-name> \
    --group-id blob \
    --resource-id "/subscriptions/<alphanumeric-subscription-ID>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/myBlobStorage"  \
    --request-message "Please approve" 
```


Pour récupérer les ressources de liaison privée partagée et afficher leur état, utilisez la commande [**az search shared-private-link-resource list**](/cli/azure/search/shared-private-link-resource#az_search_shared_private_link_resource_list).

```azurecli-interactive
az search shared-private-link-resource list \
    --service-name <search-service-name> \
    --resource-group <resource-group-name> 
```

Vous devrez approuver la connexion à l’aide de la commande suivante avant de pouvoir l’utiliser. L’ID de connexion au point de terminaison privé doit être récupéré à partir de la ressource enfant. Dans ce cas, nous obtenons l’ID de connexion à partir de la commande az storage.

```azurecli-interactive
id = (az storage account show -n myBlobStorage --query "privateEndpointConnections[0].id")

az network private-endpoint-connection approve --id $id
```

Pour supprimer la ressource de liaison privée partagée, utilisez la commande [**az search shared-private-link-resource delete**](/cli/azure/search/shared-private-link-resource#az_search_shared_private_link_resource_delete).

```azurecli-interactive
az search shared-private-link-resource delete \
    --name <spl-name> \
    --service-name <search-service-name> \
    --resource-group <resource-group-name> 
```

Pour plus d’informations sur la configuration des ressources de liaisons privées partagées, consultez la documentation relative à la [création de connexions d’indexeur via un point de terminaison privé](search-indexer-howto-access-private.md).

## <a name="next-steps"></a>Étapes suivantes

Génération d'un [index](search-what-is-an-index.md), [interrogation d'un index](search-query-overview.md) à l’aide du portail, des API REST ou du kit de développement logiciel (SDK) .NET.

* [Créer un index Recherche cognitive Azure dans le portail Azure](search-get-started-portal.md)
* [Configuration d’un indexeur pour charger des données provenant d’autres services](search-indexer-overview.md)
* [Interrogation d’un index de Recherche cognitive Azure à l’aide de l’Explorateur de recherche dans le Portail Azure](search-explorer.md)
* [Utilisation de la Recherche cognitive Azure dans .NET](search-howto-dotnet-sdk.md)