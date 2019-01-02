---
title: Obtenir une chaîne de connexion - Azure Event Hubs | Microsoft Docs
description: Cet article fournit des instructions pour obtenir une chaîne de connexion utilisable par les clients pour se connecter à Azure Event Hubs.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 263dba970b015d466b3fd46f390a063e87de2e2c
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2018
ms.locfileid: "53093304"
---
# <a name="get-an-event-hubs-connection-string"></a>Obtenir une chaîne de connexion Event Hubs

Pour utiliser Event Hubs, vous devez créer un espace de noms Event Hubs. Un espace de noms est un conteneur d’étendue qui peut héberger plusieurs Event Hubs/rubriques Kafka. Cet espace de noms vous donne une valeur unique de [nom de domaine complet](https://en.wikipedia.org/wiki/Fully_qualified_domain_name). Une fois l’espace de noms créé, vous pouvez obtenir la chaîne de connexion requise pour communiquer avec Event Hubs.

La chaîne de connexion pour Azure Event Hubs se compose des éléments suivants :

* FQDN = nom de domaine complet de l’espace de noms Event Hubs que vous avez créé. (Comprend l’espace de noms Event Hubs suivi de servicebus.windows.net)
* SharedAccessKeyName = le nom que vous avez choisi pour les clés de signature d’accès partagé de votre application
* SharedAccessKey = la valeur de la clé générée.

Le modèle de chaîne de connexion ressemble à ce qui suit :
```
Endpoint=sb://<FQDN>/;SharedAccessKeyName=<KeyName>;SharedAccessKey=<KeyValue>
```

Un exemple de chaîne de connexion peut se présenter comme `Endpoint=sb://dummynamespace.servicebus.windows.net/;SharedAccessKeyName=DummyAccessKeyName;SharedAccessKey=5dOntTRytoC24opYThisAsit3is2B+OGY1US/fuL3ly=`

Cet article vous guide dans les différentes façons d’obtenir la chaîne de connexion.

## <a name="get-connection-string-from-the-portal"></a>Obtenir la chaîne de connexion dans le portail

Une fois que vous avez l’espace de noms Event Hubs, la section de la vue d’ensemble du portail peut vous donner la chaîne de connexion, comme illustré ci-dessous :

![Chaîne de connexion Event Hubs](./media/event-hubs-get-connection-string/event-hubs-get-connection-string1.png)

Lorsque vous cliquez sur le lien de chaîne de connexion dans la section vue d’ensemble, il ouvre l’onglet des stratégies SAS comme indiqué dans la figure ci-dessous :

![Stratégies SAS d’Event Hubs](./media/event-hubs-get-connection-string/event-hubs-get-connection-string2.png)

Vous pouvez ajouter une nouvelle stratégie SAS et obtenir la chaîne de connexion ou utiliser la stratégie par défaut déjà créée. Lorsque la stratégie est ouverte, la chaîne de connexion est obtenue comme indiqué dans la figure ci-dessous :

![Obtenir la chaîne de connexion Event Hubs](./media/event-hubs-get-connection-string/event-hubs-get-connection-string3.png)

## <a name="getting-the-connection-string-with-azure-powershell"></a>Obtention de la chaîne de connexion avec Azure PowerShell
Vous pouvez utiliser Get-AzureRmEventHubNamespaceKey pour obtenir la chaîne de connexion pour la stratégie ou le nom de règle spécifié comme indiqué ci-dessous :

```azurepowershell-interactive
Get-AzureRmEventHubKey -ResourceGroupName dummyresourcegroup -NamespaceName dummynamespace -AuthorizationRuleName RootManageSharedAccessKey
```

Reportez-vous à [Module PowerShell Azure Event Hubs](https://docs.microsoft.com/powershell/module/azurerm.eventhub/get-azurermeventhubkey) pour plus d’informations.

## <a name="getting-the-connection-string-with-azure-cli"></a>Obtention de la chaîne de connexion avec Azure CLI
Vous pouvez utiliser ce qui suit pour obtenir la chaîne de connexion de l’espace de noms :

```azurecli-interactive
az eventhubs namespace authorization-rule keys list --resource-group dummyresourcegroup --namespace-name dummynamespace --name RootManageSharedAccessKey
```

Reportez-vous à [CLI Azure pour Event Hubs](https://docs.microsoft.com/cli/azure/eventhubs) pour plus d’informations.

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez en apprendre plus sur Event Hubs en consultant les liens suivants :

* [Vue d’ensemble d’Event Hubs](event-hubs-what-is-event-hubs.md)
* [Créer un concentrateur d’événements](event-hubs-create.md)
