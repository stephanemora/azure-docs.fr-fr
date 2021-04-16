---
title: Remise d’événement, identité de service géré et liaison privée
description: Cet article explique comment activer une identité de service managé pour une rubrique Azure Event Grid. Utilisez-la pour transférer des événements vers des destinations prises en charge.
ms.topic: how-to
ms.date: 03/25/2021
ms.openlocfilehash: 76f10b4627dc9578b1e616a868eab03431b59b69
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105625272"
---
# <a name="event-delivery-with-a-managed-identity"></a>Remise d’événement avec une identité managée
Cet article explique comment utiliser une [identité de service managé](../active-directory/managed-identities-azure-resources/overview.md) pour un domaine, une rubrique personnalisée ou une rubrique système de grille d’événements Azure. Utilisez-le pour transférer des événements vers des destinations prises en charge, telles que des files d’attente et rubriques Service Bus, des concentrateurs d’événements et des comptes de stockage.



## <a name="prerequisites"></a>Prérequis
1. Assignez une identité affectée par le système à une rubrique système, à une rubrique personnalisée ou à un domaine. 
    - Pour les rubriques personnalisées et les domaines, consultez [Activer une identité managée pour les domaines et les rubriques personnalisées](enable-identity-custom-topics-domains.md). 
    - Pour les rubriques système, consultez [Activer une identité managée pour les rubriques système](enable-identity-system-topics.md).
1. Ajouter l’identité à un rôle approprié (par exemple, expéditeur de données Service Bus) sur la destination (par exemple, une file d’attente Service Bus). Pour obtenir des instructions détaillées, consultez [Ajouter une identité à des rôles Azure sur des destinations](add-identity-roles.md).

    > [!NOTE]
    > Actuellement, il n’est pas possible de remettre des événements à l’aide de [points de terminaison privés](../private-link/private-endpoint-overview.md). Pour plus d’informations, consultez la section [Points de terminaison privés](#private-endpoints) à la fin de cet article. 

## <a name="create-event-subscriptions-that-use-an-identity"></a>Créer des abonnements à des événements qui utilisent une identité
Quand vous disposez d’un domaine, d’une rubrique système ou d’une rubrique personnalisée de grille d’événements avec une identité managée par le système et que vous avez ajouté l’identité au rôle approprié sur la destination, vous êtes prêt à créer des abonnements qui utilisent l’identité. 

### <a name="use-the-azure-portal"></a>Utilisation du portail Azure
Lors de la création d’un abonnement à des événements, une option permet d’activer l’utilisation d’une identité affectée par le système pour un point de terminaison dans la section **DÉTAILS DES POINTS DE TERMINAISON**. 

![Activer une identité lors de la création d’un abonnement à des événements pour une file d’attente Service Bus](./media/managed-service-identity/service-bus-queue-subscription-identity.png)

Vous pouvez également activer l’utilisation d’une identité affectée par le système pour la mise en file d’attente de lettres mortes sous l’onglet **Fonctionnalités supplémentaires**. 

![Activer une identité attribuée par le système pour la mise en file d'attente de lettres mortes](./media/managed-service-identity/enable-deadletter-identity.png)

### <a name="use-the-azure-cli---service-bus-queue"></a>Utilisation d’Azure CLI – File d’attente Service Bus 
Cette section explique comment utiliser Azure CLI afin d’activer l’utilisation d’une identité affectée par le système pour remettre des événements à une file d’attente Service Bus. L’identité doit être un membre du rôle **Expéditeur de données Azure Service Bus**. Elle doit également être membre du rôle **Contributeur aux données Blob du stockage** sur le compte de stockage utilisé pour la mise en file d'attente de lettres mortes. 

#### <a name="define-variables"></a>Définir des variables
Tout d’abord, spécifiez des valeurs pour les variables suivantes à utiliser dans la commande CLI. 

```azurecli-interactive
subid="<AZURE SUBSCRIPTION ID>"
rg = "<RESOURCE GROUP of EVENT GRID CUSTOM TOPIC>"
topicname = "<EVENT GRID TOPIC NAME>"

# get the service bus queue resource id
queueid=$(az servicebus queue show --namespace-name <SERVICE BUS NAMESPACE NAME> --name <QUEUE NAME> --resource-group <RESOURCE GROUP NAME> --query id --output tsv)
sb_esname = "<Specify a name for the event subscription>" 
```

#### <a name="create-an-event-subscription-by-using-a-managed-identity-for-delivery"></a>Créer un abonnement à des événements en utilisant une identité managée pour la remise 
Cet exemple de commande crée un abonnement à un événement pour une rubrique personnalisée Event Grid dont le type de point de terminaison est défini sur **File d’attente Service Bus**. 

```azurecli-interactive
az eventgrid event-subscription create  
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname
    --delivery-identity-endpoint-type servicebusqueue  
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $queueid
    -n $sb_esname 
```

#### <a name="create-an-event-subscription-by-using-a-managed-identity-for-delivery-and-dead-lettering"></a>Créer un abonnement à des événements en utilisant une identité managée pour la remise et la mise en file d’attente de lettres mortes
Cet exemple de commande crée un abonnement à un événement pour une rubrique personnalisée Event Grid dont le type de point de terminaison est défini sur **File d’attente Service Bus**. Il spécifie également que l’identité managée par le système doit être utilisée pour la mise en file d’attente de lettres mortes. 

```azurecli-interactive
storageid=$(az storage account show --name demoStorage --resource-group gridResourceGroup --query id --output tsv)
deadletterendpoint="$storageid/blobServices/default/containers/<BLOB CONTAINER NAME>"

az eventgrid event-subscription create  
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type servicebusqueue
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $queueid
    --deadletter-identity-endpoint $deadletterendpoint 
    --deadletter-identity systemassigned 
    -n $sb_esnameq 
```

### <a name="use-the-azure-cli---event-hubs"></a>Utilisation d’Azure CLI – Event Hubs 
Cette section explique comment utiliser Azure CLI afin d’activer l’utilisation d’une identité affectée par le système pour remettre des événements à un hub d’événements. L’identité doit être un membre du rôle **Expéditeur de données Azure Event Hubs**. Elle doit également être membre du rôle **Contributeur aux données Blob du stockage** sur le compte de stockage utilisé pour la mise en file d'attente de lettres mortes. 

#### <a name="define-variables"></a>Définir des variables
```azurecli-interactive
subid="<AZURE SUBSCRIPTION ID>"
rg = "<RESOURCE GROUP of EVENT GRID CUSTOM TOPIC>"
topicname = "<EVENT GRID CUSTOM TOPIC NAME>"

hubid=$(az eventhubs eventhub show --name <EVENT HUB NAME> --namespace-name <NAMESPACE NAME> --resource-group <RESOURCE GROUP NAME> --query id --output tsv)
eh_esname = "<SPECIFY EVENT SUBSCRIPTION NAME>" 
```

#### <a name="create-an-event-subscription-by-using-a-managed-identity-for-delivery"></a>Créer un abonnement à des événements en utilisant une identité managée pour la remise 
Cet exemple de commande crée un abonnement à un événement pour une rubrique personnalisée Event Grid dont le type de point de terminaison est défini sur **Event Hubs**. 

```azurecli-interactive
az eventgrid event-subscription create  
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type eventhub 
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $hubid
    -n $sbq_esname 
```

#### <a name="create-an-event-subscription-by-using-a-managed-identity-for-delivery--deadletter"></a>Créer un abonnement à des événements en utilisant une identité managée pour la remise et la mise en file d’attente de lettres mortes 
Cet exemple de commande crée un abonnement à un événement pour une rubrique personnalisée Event Grid dont le type de point de terminaison est défini sur **Event Hubs**. Il spécifie également que l’identité managée par le système doit être utilisée pour la mise en file d’attente de lettres mortes. 

```azurecli-interactive
storageid=$(az storage account show --name demoStorage --resource-group gridResourceGroup --query id --output tsv)
deadletterendpoint="$storageid/blobServices/default/containers/<BLOB CONTAINER NAME>"

az eventgrid event-subscription create
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type servicebusqueue  
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $hubid
    --deadletter-identity-endpoint $eh_deadletterendpoint
    --deadletter-identity systemassigned 
    -n $eh_esname 
```

### <a name="use-the-azure-cli---azure-storage-queue"></a>Utilisation d’Azure CLI – File d’attente de stockage Azure 
Cette section explique comment utiliser Azure CLI afin d’activer l’utilisation d’une identité affectée par le système pour remettre des événements à une file d’attente de Stockage Azure. L’identité doit être membre du rôle **Expéditeur de messages de données en file d’attente du stockage** sur le compte de stockage. Elle doit également être membre du rôle **Contributeur aux données Blob du stockage** sur le compte de stockage utilisé pour la mise en file d'attente de lettres mortes.

#### <a name="define-variables"></a>Définir des variables  

```azurecli-interactive
subid="<AZURE SUBSCRIPTION ID>"
rg = "<RESOURCE GROUP of EVENT GRID CUSTOM TOPIC>"
topicname = "<EVENT GRID CUSTOM TOPIC NAME>"

# get the storage account resource id
storageid=$(az storage account show --name <STORAGE ACCOUNT NAME> --resource-group <RESOURCE GROUP NAME> --query id --output tsv)

# build the resource id for the queue
queueid="$storageid/queueservices/default/queues/<QUEUE NAME>" 

sa_esname = "<SPECIFY EVENT SUBSCRIPTION NAME>" 
```

#### <a name="create-an-event-subscription-by-using-a-managed-identity-for-delivery"></a>Créer un abonnement à des événements en utilisant une identité managée pour la remise 

```azurecli-interactive
az eventgrid event-subscription create 
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type storagequeue  
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $queueid
    -n $sa_esname 
```

#### <a name="create-an-event-subscription-by-using-a-managed-identity-for-delivery--deadletter"></a>Créer un abonnement à des événements en utilisant une identité managée pour la remise et la mise en file d’attente de lettres mortes 

```azurecli-interactive
storageid=$(az storage account show --name demoStorage --resource-group gridResourceGroup --query id --output tsv)
deadletterendpoint="$storageid/blobServices/default/containers/<BLOB CONTAINER NAME>"

az eventgrid event-subscription create  
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type storagequeue  
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $queueid
    --deadletter-identity-endpoint $deadletterendpoint 
    --deadletter-identity systemassigned 
    -n $sa_esname 
```

## <a name="private-endpoints"></a>Instances Private Endpoint
Actuellement, il n’est pas possible de remettre des événements à l’aide de [points de terminaison privés](../private-link/private-endpoint-overview.md). Autrement dit, il n’y a pas de prise en charge si vous avez des exigences strictes en matière d’isolement réseau lorsque le trafic des événements remis ne doit pas sortir de l’espace IP privé. 

Toutefois, si vos exigences comprennent une méthode sécurisée pour envoyer des événements à l’aide d’un canal chiffré et une identité connue de l’expéditeur (dans ce cas, Event Grid) utilisant un espace d’adressage IP public, vous pouvez remettre des événements au service Event Hubs, Service Bus ou Stockage Azure à l’aide d’une rubrique ou d’un domaine personnalisés Azure Event Grid avec une identité managée par le système configurée comme dans cet article. Ensuite, vous pouvez utiliser une liaison privée configurée dans Azure Functions ou votre webhook déployé sur votre réseau virtuel pour extraire des événements. Voir l’exemple : [Connectez-vous à des points de terminaison privés avec Azure Functions](/samples/azure-samples/azure-functions-private-endpoints/connect-to-private-endpoints-with-azure-functions/).

Dans cette configuration, le trafic transite par l’adresse IP publique ou Internet d’Event Grid vers le service Event Hubs, Service Bus ou Stockage Azure, mais le canal peut être chiffré et une identité managée d’Event Grid est utilisée. Si vous configurez votre Azure Functions ou webhook déployé sur votre réseau virtuel pour utiliser un stockage Event Hubs, Service Bus ou stockage Azure via une liaison privée, cette section du trafic restera évidemment dans Azure.


## <a name="next-steps"></a>Étapes suivantes
Pour découvrir les identités managées, consultez [Que sont les identités managées pour les ressources Azure ?](../active-directory/managed-identities-azure-resources/overview.md).
