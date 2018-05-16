---
title: Envoyer des événements personnalisés pour Azure Event Grid vers une connexion hybride | Microsoft Docs
description: Utilisez Azure Event Grid et Azure CLI pour publier une rubrique et pour vous abonner à cet événement. Une connexion hybride est utilisée pour le point de terminaison.
services: event-grid
keywords: ''
author: tfitzmac
ms.author: tomfitz
ms.date: 05/04/2018
ms.topic: article
ms.service: event-grid
ms.openlocfilehash: c95cfee787244367688b82959474e2a8028b7ff6
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/11/2018
---
# <a name="route-custom-events-to-azure-relay-hybrid-connections-with-azure-cli-and-event-grid"></a>Acheminer des événements personnalisés vers des connexions hybrides Azure Relay avec Azure CLI et Event Grid

Azure Event Grid est un service de gestion d’événements pour le cloud. Les connexions hybrides Azure Relay sont l’un des gestionnaires d’événements pris en charge. Vous utilisez des connexions hybrides comme gestionnaires d’événements lorsque vous devez traiter des événements à partir d’applications qui ne possèdent pas de point de terminaison public. Ces applications peuvent se trouver dans votre réseau d’entreprise. Dans cet article, vous utilisez Azure CLI pour créer une rubrique personnalisée, vous abonner à cette rubrique et déclencher l’événement pour afficher le résultat. Vous envoyez les événements à la connexion hybride.

## <a name="prerequisites"></a>Prérequis


Cet article suppose que vous disposez déjà d’une connexion hybride et d’une application d’écouteur. Pour aborder les connexions hybrides, consultez la section [Prise en main des connexions hybrides Relay : .NET](../service-bus-relay/relay-hybrid-connections-dotnet-get-started.md) ou [Prise en main des connexions hybrides Relay : nœud](../service-bus-relay/relay-hybrid-connections-node-get-started.md).

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Les rubriques Event Grid sont des ressources Azure et doivent être placées dans un groupe de ressources Azure. Un groupe de ressources est une collection logique dans laquelle des ressources Azure sont déployées et gérées.

Créez un groupe de ressources avec la commande [az group create](/cli/azure/group#az_group_create). 

L’exemple suivant crée un groupe de ressources nommé *gridResourceGroup* à l’emplacement *westus2*.

```azurecli-interactive
az group create --name gridResourceGroup --location westus2
```

## <a name="create-a-custom-topic"></a>Créer une rubrique personnalisée

Une rubrique de grille d’événement fournit un point de terminaison défini par l’utilisateur vers lequel vous envoyez vos événements. L’exemple suivant permet de créer la rubrique personnalisée dans votre groupe de ressources. Remplacez `<topic_name>` par un nom unique pour votre rubrique. Le nom de la rubrique doit être unique, car elle est représentée par une entrée DNS.

```azurecli-interactive
# if you have not already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid topic create --name <topic_name> -l westus2 -g gridResourceGroup
```

## <a name="subscribe-to-a-topic"></a>S’abonner à une rubrique

Vous vous abonnez à une rubrique pour communiquer à Event Grid les événements qui vous intéressent. L’exemple suivant renvoie à la rubrique que vous avez créée et transmet l’ID de ressource de la connexion hybride au point de terminaison. L’ID de connexion hybride est au format :

`/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Relay/namespaces/<relay-namespace>/hybridConnections/<hybrid-connection-name>`

Le script suivant obtient l’ID de ressource de l’espace de nom Relay. Il construit l’identifiant de la connexion hybride, puis s’abonne à une rubrique Event Grid. Il définit le type de point de terminaison sur `hybridconnection` et utilise l’ID de connexion hybride pour le point de terminaison.

```azurecli-interactive
relayname=<namespace-name>
relayrg=<resource-group-for-relay>
hybridname=<hybrid-name>

relayid=$(az resource show --name $relayname --resource-group $relayrg --resource-type Microsoft.Relay/namespaces --query id --output tsv)
hybridid="$relayid/hybridConnections/$hybridname"

az eventgrid event-subscription create \
  --topic-name <topic_name> \
  -g gridResourceGroup \
  --name <event_subscription_name> \
  --endpoint-type hybridconnection \
  --endpoint $hybridid
```

## <a name="send-an-event-to-your-topic"></a>Envoyer un événement à votre rubrique

Nous allons maintenant déclencher un événement pour voir comment Event Grid distribue le message à votre point de terminaison. Tout d’abord, il nous faut l’URL et la clé de la rubrique personnalisée. Utilisez de nouveau le nom de votre rubrique pour `<topic_name>`.

```azurecli-interactive
endpoint=$(az eventgrid topic show --name <topic_name> -g gridResourceGroup --query "endpoint" --output tsv)
key=$(az eventgrid topic key list --name <topic_name> -g gridResourceGroup --query "key1" --output tsv)
```

Pour simplifier cet article, vous allez utiliser des exemples de données d’événements à envoyer à la rubrique. En règle générale, une application ou un service Azure envoie les données d’événements. CURL est un utilitaire qui envoie des requêtes HTTP. Dans cet article, utilisez CURL pour envoyer l’événement à la rubrique.  L’exemple suivant envoie trois événements à la rubrique Event Grid :

```azurecli-interactive
body=$(eval echo "'$(curl https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/customevent.json)'")
curl -X POST -H "aeg-sas-key: $key" -d "$body" $endpoint
```

Votre application d’écouteur doit recevoir le message d’événement.

## <a name="clean-up-resources"></a>Supprimer des ressources
Si vous envisagez de continuer à utiliser cet événement, ne supprimez pas les ressources créées dans cet article. Sinon, utilisez la commande suivante pour supprimer les ressources créées avec cet article.

```azurecli-interactive
az group delete --name gridResourceGroup
```

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous savez créer des rubriques et des abonnements d’événements, vous pouvez en apprendre davantage sur Event Grid et ce qu’il peut vous offrir :

- [Event Grid](overview.md)
- [Acheminer des événements de stockage Blob Azure vers un point de terminaison Web personnalisé ](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)
- [Surveiller les modifications d’une machine virtuelle avec Azure Event Grid et Azure Logic Apps](monitor-virtual-machine-changes-event-grid-logic-app.md)
- [Diffuser en continu des Big Data dans un entrepôt de données](event-grid-event-hubs-integration.md)
