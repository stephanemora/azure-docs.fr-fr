---
title: Remise d’événement avec une identité de service managé
description: Cet article explique comment activer une identité de service managé pour une rubrique Azure Event Grid. Utilisez-la pour transférer des événements vers des destinations prises en charge.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: how-to
ms.date: 04/24/2020
ms.author: spelluru
ms.openlocfilehash: 4d96f28b98cccada2ac5c77589acc6df1430bb02
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/20/2020
ms.locfileid: "83700661"
---
# <a name="event-delivery-with-managed-identity"></a>Remise d’événement avec une identité managée
Cet article explique comment activer une [identité de service managé](../active-directory/managed-identities-azure-resources/overview.md) pour une rubrique ou un domaine Event Grid. Utilisez-le pour transférer des événements vers des destinations prises en charge, telles que des files d’attente et rubriques Service Bus, des concentrateurs d’événements et des comptes de stockage.

Les étapes décrites en détail dans cet article sont les suivantes :
1. Créer une rubrique ou un domaine avec une identité attribuée par le système (ou) mettre à jour une rubrique ou un domaine existants pour activer une identité. 
2. Ajouter l’identité à un rôle approprié (par exemple, expéditeur de données Service Bus) sur la destination (par exemple, une file d’attente Service Bus).
3. Lors de la création d’abonnements à des événements, activer l’utilisation de l’identité pour remettre des événements à la destination. 

## <a name="create-a-topic-or-domain-with-an-identity"></a>Créer une rubrique ou un domaine avec une identité
Tout d’abord, voyons comment créer une rubrique ou un domaine avec une identité managée par le système.

### <a name="using-azure-portal"></a>En passant par le portail Azure
Vous pouvez activer une identité attribuée par le système pour une rubrique ou un domaine lors de sa création dans le portail Azure. L’image suivante montre comment activer une identité managée par le système pour une rubrique. Fondamentalement, dans la page **Avancée** de l’Assistant Création de rubrique, vous sélectionnez l’option **Activer l’identité attribuée par le système**. Cette option apparaît également dans la page **Avancée** de l’Assistant Création de domaine. 

![Activer une identité lors de la création d’une rubrique](./media/managed-service-identity/create-topic-identity.png)

### <a name="using-azure-cli"></a>Utilisation de l’interface de ligne de commande Azure
Vous pouvez également utiliser Azure CLI pour créer une rubrique ou un domaine avec une identité attribuée par le système. Utilisez la commande `az eventgrid topic create` avec le paramètre `--identity` défini sur `systemassigned`. Si vous ne spécifiez pas de valeur pour ce paramètre, la valeur par défaut `noidentity` est utilisée. 

```azurecli-interactive
# create a topic with a system-assigned identity
az eventgrid topic create -g <RESOURCE GROUP NAME> --name <TOPIC NAME> -l <LOCATION>  --identity systemassigned
```

Vous pouvez également utiliser la commande `az eventgrid domain create` pour créer un domaine avec une identité managée par le système.

## <a name="enable-identity-for-an-existing-topic-or-domain"></a>Activer une identité pour une rubrique ou un domaine existant
Dans la dernière section, vous avez appris à activer une identité managée par le système lors de la création d’une rubrique ou d’un domaine. Dans cette section, vous allez apprendre à activer une identité managée par le système pour une rubrique ou un domaine existants. 

### <a name="using-azure-portal"></a>En passant par le portail Azure
1. Accéder au [portail Azure](https://portal.azure.com)
2. Dans la barre de recherche, recherchez **rubriques Event Grid**.
3. Sélectionnez la **rubrique** pour laquelle vous souhaitez activer l’identité managée. 
4. Basculez vers l’onglet **Identité**. 
5. Positionnez le commutateur pour activer l’identité. 

    Vous pouvez utiliser des étapes similaires pour activer une identité pour un domaine Event Grid.

### <a name="using-azure-cli"></a>Utilisation de l’interface de ligne de commande Azure
Utilisez la commande `az eventgrid topic update` avec `--identity` défini sur `systemassigned` pour activer une identité attribuée par le système pour une rubrique existante. Si vous souhaitez désactiver l’identité, spécifiez la valeur `noidentity`. 

```azurecli-interactive
# Update the topic to assign a system-assigned identity. 
az eventgrid topic update -g $rg --name $topicname --identity systemassigned --sku basic 
```

La commande de mise à jour d’un domaine existant est similaire (`az eventgrid domain update`).

## <a name="supported-destinations-and-role-based-access-check-rbac-roles"></a>Destinations prises en charge et rôles de contrôle d’accès en fonction du rôle (RBAC)
Une fois que vous avez activé l’identité pour votre domaine ou rubrique Event Grid, Azure crée automatiquement une identité dans l’Azure Active Directory (Azure AD). Ajoutez cette identité aux rôles RBAC appropriés afin que la rubrique ou le domaine puisse transférer des événements vers les destinations prises en charge. Par exemple, ajoutez l’identité au rôle **Expéditeur de données Azure Event Hubs** pour un espace de noms Event Hubs afin que la rubrique Event Grid puisse transférer des événements aux Event Hubs dans cet espace de noms.  

Actuellement, Azure Event Grid prend en charge des rubriques ou domaines configurés avec une identité managée attribuée par le système pour transférer des événements aux destinations suivantes. Ce tableau indique également les rôles à associer à l’identité pour que la rubrique puisse transférer les événements.

| Destination | Rôle RBAC | 
| ----------- | --------- | 
| Files d’attente et rubriques Service Bus | [Expéditeur de données Azure Service Bus](../service-bus-messaging/authenticate-application.md#built-in-rbac-roles-for-azure-service-bus) |
| Event Hub | [Expéditeur de données Azure Event Hubs](../event-hubs/authorize-access-azure-active-directory.md#built-in-rbac-roles-for-azure-event-hubs) | 
| Stockage d'objets blob | [Contributeur aux données Blob du stockage](../storage/common/storage-auth-aad-rbac-portal.md#rbac-roles-for-blobs-and-queues) |
| Stockage de files d'attente |[Expéditeur de messages de données en file d’attente du stockage](../storage/common/storage-auth-aad-rbac-portal.md#rbac-roles-for-blobs-and-queues) | 

## <a name="add-identity-to-rbac-roles-on-destinations"></a>Ajouter une identité à des rôles RBAC sur des destinations
Cette section décrit comment ajouter l’identité pour votre rubrique ou domaine à un rôle RBAC. 

### <a name="using-azure-portal"></a>En passant par le portail Azure
Vous pouvez utiliser le **portail Azure** pour attribuer l’identité de rubrique ou de domaine à un rôle approprié afin que la rubrique ou le domaine puissent transférer des événements vers la destination. 

L’exemple suivant ajoute une identité managée pour une rubrique Event Grid nommée **msitesttopic** au rôle **Expéditeur de données Azure Service Bus** pour un **espace de noms** Service Bus contenant une ressource de file d’attente ou de rubrique. Lorsque vous faites l’ajout au rôle au niveau de l’espace de noms, la rubrique peut transférer des événements à toutes les entités au sein de l’espace de noms. 

1. Accédez à votre **espace de noms Service Bus** dans le [portail Azure](https://portal.azure.com). 
2. Dans le menu de gauche, sélectionnez **Contrôle d’accès**. 
3. Dans la section **Ajouter une attribution de rôle**, sélectionnez **Ajouter**. 
4. Dans la page **Ajouter une attribution de rôle**, effectuez les étapes suivantes :
    1. Sélectionnez le rôle. Dans ce cas, il s’agit du rôle **Expéditeur de données Azure Service Bus**. 
    2. Sélectionnez l’**identité** pour votre rubrique ou domaine. 
    3. Sélectionnez **Enregistrer** pour enregistrer la configuration.

Les étapes sont similaires pour l’ajout d’une identité à d’autres rôles mentionnés dans le tableau. 

### <a name="using-azure-cli"></a>Utilisation de l’interface de ligne de commande Azure
L’exemple présenté dans cette section montre comment utiliser **Azure CLI** pour ajouter une identité à un rôle RBAC. Les exemples de commandes sont destinés aux rubriques Event Grid. Les commandes pour les domaines Event Grid sont similaires. 

#### <a name="get-principal-id-for-the-topics-system-identity"></a>Obtenir l’ID du principal pour l’identité système de la rubrique 
Tout d’abord, récupérez l’ID du principal de l’identité managée par le système de la rubrique et attribuez l’identité aux rôles appropriés.

```azurecli-interactive
topic_pid=$(az ad sp list --display-name "$<TOPIC NAME>" --query [].objectId -o tsv)
```

#### <a name="create-a-role-assignment-for-event-hubs-at-various-scopes"></a>Créer une attribution de rôle pour Event hubs à différentes étendues 
L’exemple d’interface CLI suivant montre comment ajouter l’identité d’une rubrique au rôle **Expéditeur de données Azure Event Hubs** au niveau de l’espace de noms ou du concentrateur d’événements. Si vous créez l’attribution de rôle au niveau de l’espace de noms, la rubrique peut transférer des événements à tous les concentrateurs d’événements dans cet espace de noms. Si vous créez l’attribution de rôle au niveau du concentrateur d’événements, la rubrique peut transférer des événements uniquement à ce concentrateur d’événements spécifique. 


```azurecli-interactive
role="Azure Event Hubs Data Sender" 
namespaceresourceid=$(az eventhubs namespace show -n $<EVENT HUBS NAMESPACE NAME> -g <RESOURCE GROUP of EVENT HUB> --query "{I:id}" -o tsv) 
eventhubresourceid=$(az eventhubs eventhub show -n <EVENT HUB NAME> --namespace-name <EVENT HUBS NAMESPACE NAME> -g <RESOURCE GROUP of EVENT HUB> --query "{I:id}" -o tsv) 

# create role assignment for the whole namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$namespaceresourceid" 

# create role assignment scoped to just one event hub inside the namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$eventhubresourceid" 
```

#### <a name="create-a-role-assignment-for-service-bus-topic-at-various-scopes"></a>Créer une attribution de rôle pour une rubrique Service Bus dans différentes étendues 
L’exemple d’interface CLI suivant montre comment ajouter une identité de rubrique au rôle **Expéditeur de données Azure Service Bus** au niveau de l’espace de noms ou de la rubrique Service Bus. Si vous créez l’attribution de rôle au niveau de l’espace de noms, la rubrique Event Grid peut transférer des événements à toutes les entités (files d’attente ou rubriques Service Bus) au sein de cet espace de noms. Si vous créez l’attribution de rôle au niveau de la file d’attente ou de la rubrique Service Bus, la rubrique Event Grid peut transférer des événements uniquement à ces file d’attente ou rubrique Service Bus spécifiques. 

```azurecli-interactive
role="Azure Service Bus Data Sender" 
namespaceresourceid=$(az servicebus namespace show -n $RG\SB -g "$RG" --query "{I:id}" -o tsv 
sbustopicresourceid=$(az servicebus topic show -n topic1 --namespace-name $RG\SB -g "$RG" --query "{I:id}" -o tsv) 

# create role assignment for the whole namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$namespaceresourceid" 

# create role assignment scoped to just one hub inside the namespace 
az role assignment create --role "$role" --assignee "$topic_pid" --scope "$sbustopicresourceid" 
```

## <a name="create-event-subscriptions-that-use-identity"></a>Créer des abonnements à des événements qui utilisent une identité
Une fois que vous disposez d’une rubrique ou d’un domaine avec une identité managée par le système et que vous avez ajouté l’identité au rôle approprié sur la destination, vous êtes prêt à créer des abonnements qui utilisent l’identité. 

### <a name="using-azure-portal"></a>En passant par le portail Azure
Lorsque vous créez un abonnement à des événements, vous voyez une option permettant d’activer l’utilisation d’une identité attribuée par le système pour un point de terminaison dans la section **DÉTAILS DES POINTS DE TERMINAISON**. 

![Activer une identité lors de la création d’un abonnement à un événement pour une file d’attente Service Bus](./media/managed-service-identity/service-bus-queue-subscription-identity.png)

Vous pouvez également activer l’utilisation d’une identité attribuée par le système pour la mise en file d’attente de lettres mortes sous l’onglet **Fonctionnalités supplémentaires**. 

![Activer une identité attribuée par le système pour la mise en file d'attente de lettres mortes](./media/managed-service-identity/enable-deadletter-identity.png)

### <a name="using-azure-cli---service-bus-queue"></a>Utilisation d’Azure CLI – File d'attente Service Bus 
Cette section explique comment utiliser **Azure CLI** afin, d’activer l’utilisation d’une identité attribuée par le système pour remettre des événements à une file d’attente Service Bus. L’identité doit être un membre du rôle **Expéditeur de données Azure Service Bus**. Elle doit également être membre du rôle **Contributeur aux données Blob du stockage** sur le compte de stockage utilisé pour la mise en file d'attente de lettres mortes. 

#### <a name="define-variables"></a>Définir des variables
Tout d’abord, spécifiez des valeurs pour les variables suivantes à utiliser dans la commande CLI. 

```azurecli-interactive
subid="<AZURE SUBSCRIPTION ID>"
rg = "<RESOURCE GROUP of EVENT GRID TOPIC>"
topicname = "<EVENT GRID TOPIC NAME>"

# get the service bus queue resource id
queueid=$(az servicebus queue show --namespace-name <SERVICE BUS NAMESPACE NAME> --name <QUEUE NAME> --resource-group <RESOURCE GROUP NAME> --query id --output tsv)
sb_esname = "<Specify a name for the event subscription>" 
```

#### <a name="create-an-event-subscription-using-managed-identity-for-delivery"></a>créer un abonnement à un événement à l’aide d’une identité managée pour la remise 
Cet exemple de commande crée un abonnement à un événement pour une rubrique Event Grid avec le type de point de terminaison défini sur **File d’attente Service Bus**. 

```azurecli-interactive
az eventgrid event-subscription create  
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname
    --delivery-identity-endpoint-type servicebusqueue  
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $queueid
    -n $sb_esname 
```

#### <a name="create-an-event-subscription-using-managed-identity-for-delivery-and-dead-lettering"></a>créer un abonnement à un événement à l’aide d’une identité managée pour la remise et la mise en file d’attente de lettres mortes
Cet exemple de commande crée un abonnement à un événement pour une rubrique Event Grid avec le type de point de terminaison défini sur **File d’attente Service Bus**. Il spécifie également que l’identité managée par le système doit être utilisée pour la mise en file d’attente de lettres mortes. 

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

### <a name="azure-cli---event-hubs"></a>Azure CLI – Event Hubs 
Cette section explique comment utiliser **Azure CLI** afin, d’activer l’utilisation d’une identité attribuée par le système pour remettre des événements à un concentrateur d’événements. L’identité doit être un membre du rôle **Expéditeur de données Azure Event Hubs**. Elle doit également être membre du rôle **Contributeur aux données Blob du stockage** sur le compte de stockage utilisé pour la mise en file d'attente de lettres mortes. 

#### <a name="define-variables"></a>Définir des variables
```azurecli-interactive
subid="<AZURE SUBSCRIPTION ID>"
rg = "<RESOURCE GROUP of EVENT GRID TOPIC>"
topicname = "<EVENT GRID TOPIC NAME>"

hubid=$(az eventhubs eventhub show --name <EVENT HUB NAME> --namespace-name <NAMESPACE NAME> --resource-group <RESOURCE GROUP NAME> --query id --output tsv)
eh_esname = "<SPECIFY EVENT SUBSCRIPTION NAME>" 
```

#### <a name="create-event-subscription-using-managed-identity-for-delivery"></a>créer un abonnement à un événement à l’aide d’une identité managée pour la remise 
Cet exemple de commande crée un abonnement à un événement pour une rubrique Event Grid avec le type de point de terminaison défini sur **Event Hubs**. 

```azurecli-interactive
az eventgrid event-subscription create  
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type eventhub 
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $hubid
    -n $sbq_esname 
```

#### <a name="create-event-subscription-using-managed-identity-for-delivery--deadletter"></a>Créer un abonnement à un événement à l’aide d’une identité managée pour la remise et la mise en file d'attente de lettres mortes 
Cet exemple de commande crée un abonnement à un événement pour une rubrique Event Grid avec le type de point de terminaison défini sur **Event Hubs**. Il spécifie également que l’identité managée par le système doit être utilisée pour la mise en file d’attente de lettres mortes. 

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

### <a name="azure-cli---azure-storage-queue"></a>Azure CLI – File d’attente de stockage Azure 
Cette section explique comment utiliser **Azure CLI** afin, d’activer l’utilisation d’une identité attribuée par le système pour remettre des événements à une file d'attente de stockage Azure. L’identité doit être membre du rôle **Contributeur aux données Blob du stockage** sur le compte de stockage.

#### <a name="define-variables"></a>Définir des variables  

```azurecli-interactive
subid="<AZURE SUBSCRIPTION ID>"
rg = "<RESOURCE GROUP of EVENT GRID TOPIC>"
topicname = "<EVENT GRID TOPIC NAME>"

# get the storage account resource id
storageid=$(az storage account show --name <STORAGE ACCOUNT NAME> --resource-group <RESOURCE GROUP NAME> --query id --output tsv)

# build the resource id for the queue
queueid="$storageid/queueservices/default/queues/<QUEUE NAME>" 

sa_esname = "<SPECIFY EVENT SUBSCRIPTION NAME>" 
```

#### <a name="create-event-subscription-using-managed-identity-for-delivery"></a>créer un abonnement à un événement à l’aide d’une identité managée pour la remise 

```azurecli-interactive
az eventgrid event-subscription create 
    --source-resource-id /subscriptions/$subid/resourceGroups/$rg/providers/Microsoft.EventGrid/topics/$topicname 
    --delivery-identity-endpoint-type storagequeue  
    --delivery-identity systemassigned 
    --delivery-identity-endpoint $queueid
    -n $sa_esname 
```

#### <a name="create-event-subscription-using-managed-identity-for-delivery--deadletter"></a>Créer un abonnement à un événement à l’aide d’une identité managée pour la remise et la mise en file d'attente de lettres mortes 

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



## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur les identités de service managé, consultez [Que sont les identités managées pour les ressources Azure ?](../active-directory/managed-identities-azure-resources/overview.md) 