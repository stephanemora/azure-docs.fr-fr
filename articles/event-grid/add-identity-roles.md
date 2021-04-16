---
title: Ajouter une identité managée à un rôle sur une destination Azure Event Grid
description: Cet article explique comment ajouter une identité managée aux rôles Azure sur des destinations telles que Azure Service Bus et Azure Event Hubs.
ms.topic: how-to
ms.date: 03/25/2021
ms.openlocfilehash: 1578e4c24201614ce89351b3c3cee52a09cadc30
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106280477"
---
# <a name="grant-managed-identity-the-access-to-event-grid-destination"></a>Accorder à une identité managée l’accès à une destination Event Grid
Cette section décrit comment ajouter l’identité pour votre rubrique système ou rubrique personnalisés à un rôle Azure. 

## <a name="prerequisites"></a>Prérequis
Affectez une identité managée affectée par le système à l’aide des instructions disponibles dans ces articles :

- [Rubriques ou domaines personnalisés](enable-identity-custom-topics-domains.md)
- [Rubriques sur le système](enable-identity-system-topics.md)

## <a name="supported-destinations-and-azure-roles"></a>Destinations et rôles Azure pris en charge
Une fois que vous avez activé l’identité pour votre rubrique ou votre domaine personnalisés Event Grid, Azure crée automatiquement une identité dans Azure Active Directory. Ajoutez cette identité aux rôles Azure appropriés afin que la rubrique ou le domaine personnalisés puissent transférer des événements vers les destinations prises en charge. Par exemple, ajoutez l’identité au rôle **Expéditeur de données Azure Event Hubs** pour un espace de noms Azure Event Hubs pour que la rubrique personnalisée Event Grid puisse transférer les événements aux hubs d’événements de cet espace de noms. 

Actuellement, Azure Event Grid prend en charge les rubriques et domaines personnalisés configurés avec une identité managée affectée par le système pour transférer les événements vers les destinations suivantes. Ce tableau indique également les rôles à associer à l’identité pour que la rubrique personnalisée puisse transférer les événements.

| Destination | Rôle Azure | 
| ----------- | --------- | 
| Files d’attente et rubriques Service Bus | [Expéditeur de données Azure Service Bus](../service-bus-messaging/authenticate-application.md#azure-built-in-roles-for-azure-service-bus) |
| Hubs d'événements Azure | [Expéditeur de données Azure Event Hubs](../event-hubs/authorize-access-azure-active-directory.md#azure-built-in-roles-for-azure-event-hubs) | 
| Stockage Blob Azure | [Contributeur aux données Blob du stockage](../storage/common/storage-auth-aad-rbac-portal.md#azure-roles-for-blobs-and-queues) |
| Stockage File d’attente Azure |[Expéditeur de messages de données en file d’attente du stockage](../storage/common/storage-auth-aad-rbac-portal.md#azure-roles-for-blobs-and-queues) | 


## <a name="use-the-azure-portal"></a>Utilisation du portail Azure
Vous pouvez utiliser le portail Azure pour affecter à l’identité de la rubrique ou du domaine personnalisés un rôle approprié afin que la rubrique ou le domaine personnalisés puissent transférer les événements vers la destination. 

L’exemple suivant ajoute une identité managée pour une rubrique personnalisée Event Grid nommée **msitesttopic** au rôle **Expéditeur de données Azure Service Bus** pour un espace de noms Service Bus contenant une ressource de file d’attente ou de rubrique. Lors de l’ajout au rôle au niveau de l’espace de noms, la rubrique personnalisée Event Grid peut transférer des événements à toutes les entités au sein de l’espace de noms. 

1. Accédez à votre **espace de noms Service Bus** sur le [Portail Azure](https://portal.azure.com). 
1. Sélectionnez **Contrôle d’accès** dans le volet gauche. 
1. Dans la section **Ajouter une attribution de rôle**, sélectionnez **Ajouter**. 
1. Dans la page **Ajouter une attribution de rôle**, effectuez les étapes suivantes :
    1. Sélectionnez le rôle. Dans ce cas, il s’agit du rôle **Expéditeur de données Azure Service Bus**. 
    1. Sélectionnez l’**identité** pour votre rubrique ou domaine personnalisés Event Grid. 
    1. Sélectionnez **Enregistrer** pour enregistrer la configuration.

Les étapes sont similaires pour l’ajout d’une identité à d’autres rôles mentionnés dans le tableau. 

## <a name="use-the-azure-cli"></a>Utilisation de l’interface de ligne de commande Microsoft Azure
L’exemple présenté dans cette section montre comment utiliser Azure CLI pour ajouter une identité à un rôle Azure. Les exemples de commandes sont destinés aux rubriques personnalisées Event Grid. Les commandes pour les domaines Event Grid sont similaires. 

### <a name="get-the-principal-id-for-the-custom-topics-system-identity"></a>Obtenir l’ID du principal pour l’identité système de la rubrique personnalisée 
Récupérez l’ID du principal de l’identité managée par le système de la rubrique personnalisée, puis attribuez l’identité aux rôles appropriés.

```azurecli-interactive
topic_pid=$(az ad sp list --display-name "$<TOPIC NAME>" --query [].objectId -o tsv)
```

### <a name="create-a-role-assignment-for-event-hubs-at-various-scopes"></a>Créer une attribution de rôle pour Event hubs à différentes étendues 
L’exemple d’interface CLI suivant montre comment ajouter une identité de rubrique personnalisée au rôle **Expéditeur de données Azure Event Hubs** au niveau de l’espace de noms ou de l’Event Hub. Si vous créez l’attribution de rôle au niveau de l’espace de noms, la rubrique personnalisée peut transférer des événements à tous les Event Hubs dans cet espace de noms. Si vous créez une attribution de rôle au niveau de l’Event Hub, la rubrique personnalisée ne peut transférer des événements qu’à cet Event Hub spécifique. 


```azurecli-interactive
role="Azure Event Hubs Data Sender" 
namespaceresourceid=$(az eventhubs namespace show -n $<EVENT HUBS NAMESPACE NAME> -g <RESOURCE GROUP of EVENT HUB> --query "{I:id}" -o tsv) 
eventhubresourceid=$(az eventhubs eventhub show -n <EVENT HUB NAME> --namespace-name <EVENT HUBS NAMESPACE NAME> -g <RESOURCE GROUP of EVENT HUB> --query "{I:id}" -o tsv) 

# create role assignment for the whole namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$namespaceresourceid" 

# create role assignment scoped to just one event hub inside the namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$eventhubresourceid" 
```

### <a name="create-a-role-assignment-for-a-service-bus-topic-at-various-scopes"></a>Créer une attribution de rôle pour une rubrique Service Bus dans différentes étendues 
L’exemple d’interface de ligne de commande suivant montre comment ajouter une identité de rubrique personnalisée Event Hub au rôle **Expéditeur de données Azure Service Bus** au niveau de l’espace de noms ou de la rubrique Service Bus. Si l’attribution de rôle est créée au niveau de l’espace de noms, la rubrique Event Grid peut transférer les événements à toutes les entités (files d’attente et rubriques Service Bus) de cet espace de noms. Si vous créez une attribution de rôle au niveau de la file d’attente ou de la rubrique Service Bus, la rubrique personnalisée Event Grid ne peut transférer des événements qu’à cette file d’attente ou à cette rubrique Service Bus spécifiques. 

```azurecli-interactive
role="Azure Service Bus Data Sender" 
namespaceresourceid=$(az servicebus namespace show -n $RG\SB -g "$RG" --query "{I:id}" -o tsv 
sbustopicresourceid=$(az servicebus topic show -n topic1 --namespace-name $RG\SB -g "$RG" --query "{I:id}" -o tsv) 

# create role assignment for the whole namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$namespaceresourceid" 

# create role assignment scoped to just one hub inside the namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$sbustopicresourceid" 
```

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous avez affecté une identité attribuée par le système à votre rubrique système, à une rubrique personnalisée ou à un domaine, et que vous avez ajouté l’identité aux rôles appropriés sur les destinations, consultez l’article [Remise d’événement avec une identité managée](managed-service-identity.md) pour savoir comment remettre des événements vers des destinations selon l’identité.


