---
title: Guide pratique pour filtrer des événements pour Azure Event Grid
description: Cet article explique comment filtrer des événements (par type d’événement, par sujet, par opérateurs, par données, etc.) lors de la création d’un abonnement Event Grid.
ms.topic: conceptual
ms.date: 08/11/2021
ms.openlocfilehash: 666690fc7c0d21c7ae257a20b49949961d89cb60
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122524978"
---
# <a name="filter-events-for-event-grid"></a>Filtrer des événements pour Event Grid

Cet article montre comment filtrer des événements lors de la création d’un abonnement Event Grid. Pour en savoir plus sur les options de filtrage d’événements, consultez [Comprendre le filtrage d’événements pour les abonnements Event Grid](event-filtering.md).

## <a name="filter-by-event-type"></a>Filtrer par type d’événement

Quand vous créez un abonnement Event Grid, vous pouvez spécifier quels [types d’événements](event-schema.md) envoyer au point de terminaison. Les exemples de cette section créent des abonnements à des événements d’un groupe de ressources, mais limitent les événements qui sont envoyés à `Microsoft.Resources.ResourceWriteFailure` et `Microsoft.Resources.ResourceWriteSuccess`. Si vous avez besoin de davantage de souplesse lors du filtrage d’événements par type d’événement, consultez Filtrer avec des opérateurs avancés et sur des champs de données.

### <a name="azure-powershell"></a>Azure PowerShell
Pour PowerShell, utilisez le paramètre `-IncludedEventType` lors de la création de l’abonnement.

```powershell
$includedEventTypes = "Microsoft.Resources.ResourceWriteFailure", "Microsoft.Resources.ResourceWriteSuccess"

New-AzEventGridSubscription `
  -EventSubscriptionName demoSubToResourceGroup `
  -ResourceGroupName myResourceGroup `
  -Endpoint <endpoint-URL> `
  -IncludedEventType $includedEventTypes
```

### <a name="azure-cli"></a>Azure CLI
Pour Azure CLI, utilisez le paramètre `--included-event-types`. L’exemple suivant utilise Azure CLI dans un interpréteur de commandes Bash :

```azurecli
includedEventTypes="Microsoft.Resources.ResourceWriteFailure Microsoft.Resources.ResourceWriteSuccess"

az eventgrid event-subscription create \
  --name demoSubToResourceGroup \
  --resource-group myResourceGroup \
  --endpoint <endpoint-URL> \
  --included-event-types $includedEventTypes
```

### <a name="azure-portal"></a>Portail Azure

1. Dans la page **Abonnement aux événements**, basculez vers l’onglet **Filtres**. 
1. Sélectionnez **Ajouter un type d’événement** en regard de **Filtrer les types d’événements**. 

    :::image type="content" source="./media/how-to-filter-events/add-event-type-button.png" alt-text="Capture d’écran de la page Abonnement aux événements avec le bouton Ajouter un type d’événement sélectionné.":::    
1. Tapez le type d’événement, puis appuyez sur Entrée. Dans l’exemple suivant, le type d’événement est `Microsoft.Resources.ResourceWriteSuccess`. 

    :::image type="content" source="./media/how-to-filter-events/sample-event-type.png" alt-text="Capture d’écran de la page Abonnement aux événements avec un exemple de type d’événement.":::    
      
### <a name="azure-resource-manager-template"></a>Modèle Azure Resource Manager
Pour un modèle Resource Manager, utilisez la propriété `includedEventTypes`.

```json
"resources": [
  {
    "type": "Microsoft.EventGrid/eventSubscriptions",
    "name": "[parameters('eventSubName')]",
    "apiVersion": "2018-09-15-preview",
    "properties": {
      "destination": {
        "endpointType": "WebHook",
        "properties": {
          "endpointUrl": "[parameters('endpoint')]"
        }
      },
      "filter": {
        "subjectBeginsWith": "",
        "subjectEndsWith": "",
        "isSubjectCaseSensitive": false,
        "includedEventTypes": [
          "Microsoft.Resources.ResourceWriteFailure",
          "Microsoft.Resources.ResourceWriteSuccess"
        ]
      }
    }
  }
]
```

> [!NOTE]
> Pour en savoir plus sur ces filtres (Types d’événements, Objet et Avancé), consultez [Comprendre le filtrage d’événements pour les abonnements Event Grid](event-filtering.md). 

## <a name="filter-by-subject"></a>Filtrer par objet

Vous pouvez filtrer des événements par l’objet indiqué dans les données d’événement. Vous pouvez spécifier une valeur à faire correspondre pour le début ou la fin de l’objet. Si vous avez besoin de davantage de souplesse lors du filtrage d’événements par objet, consultez Filtrer avec des opérateurs avancés et sur des champs de données.

Dans l’exemple PowerShell suivant, vous créez un abonnement à un événement qui filtre sur le début de l’objet. Vous utilisez le paramètre `-SubjectBeginsWith` pour limiter les événements à ceux d’une ressource spécifique. Vous transmettez l’ID de ressource d’un groupe de sécurité réseau.

### <a name="azure-powershell"></a>Azure PowerShell
```powershell
$resourceId = (Get-AzResource -ResourceName demoSecurityGroup -ResourceGroupName myResourceGroup).ResourceId

New-AzEventGridSubscription `
  -Endpoint <endpoint-URL> `
  -EventSubscriptionName demoSubscriptionToResourceGroup `
  -ResourceGroupName myResourceGroup `
  -SubjectBeginsWith $resourceId
```

L’exemple PowerShell suivant crée un abonnement pour un stockage d’objets blob. Il limite les événements à ceux dont l’objet se termine par `.jpg`.

```powershell
$storageId = (Get-AzStorageAccount -ResourceGroupName myResourceGroup -AccountName $storageName).Id

New-AzEventGridSubscription `
  -EventSubscriptionName demoSubToStorage `
  -Endpoint <endpoint-URL> `
  -ResourceId $storageId `
  -SubjectEndsWith ".jpg"
```

### <a name="azure-cli"></a>Azure CLI
Dans l’exemple Azure CLI suivant, vous créez un abonnement à un événement qui filtre sur le début de l’objet. Vous utilisez le paramètre `--subject-begins-with` pour limiter les événements à ceux d’une ressource spécifique. Vous transmettez l’ID de ressource d’un groupe de sécurité réseau.

```azurecli
resourceId=$(az resource show --name demoSecurityGroup --resource-group myResourceGroup --resource-type Microsoft.Network/networkSecurityGroups --query id --output tsv)

az eventgrid event-subscription create \
  --name demoSubscriptionToResourceGroup \
  --resource-group myResourceGroup \
  --endpoint <endpoint-URL> \
  --subject-begins-with $resourceId
```

L’exemple Azure CLI suivant crée un abonnement pour un stockage d’objets blob. Il limite les événements à ceux dont l’objet se termine par `.jpg`.

```azurecli
storageid=$(az storage account show --name $storageName --resource-group myResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --resource-id $storageid \
  --name demoSubToStorage \
  --endpoint <endpoint-URL> \
  --subject-ends-with ".jpg"
```

### <a name="azure-portal"></a>Portail Azure

1. Dans la page **Abonnement aux événements**, sélectionnez **Activer le filtrage d’objet**. 
1. Entrez des valeurs pour un ou plusieurs des champs suivants : **Le sujet commence par** et **Le sujet se termine par**. Dans la capture suivante, les deux options sont sélectionnées. 

    :::image type="content" source="./media/how-to-filter-events/subject-filter-example.png" alt-text="Capture d’écran de la page Abonnement aux événements avec un exemple de filtrage d’objet.":::
1. Sélectionnez l’option de **Correspondance sensible à la casse** si vous voulez que l’objet de l’événement corresponde à la casse des filtres spécifiés. 

### <a name="azure-resource-manager-template"></a>Modèle Azure Resource Manager
Dans l’exemple de modèle Resource Manager suivant, vous créez un abonnement à un événement qui filtre sur le début de l’objet. Vous utilisez la propriété `subjectBeginsWith` pour limiter les événements à ceux d’une ressource spécifique. Vous transmettez l’ID de ressource d’un groupe de sécurité réseau.

```json
"resources": [
  {
    "type": "Microsoft.EventGrid/eventSubscriptions",
    "name": "[parameters('eventSubName')]",
    "apiVersion": "2018-09-15-preview",
    "properties": {
      "destination": {
        "endpointType": "WebHook",
        "properties": {
          "endpointUrl": "[parameters('endpoint')]"
        }
      },
      "filter": {
        "subjectBeginsWith": "[resourceId('Microsoft.Network/networkSecurityGroups','demoSecurityGroup')]",
        "subjectEndsWith": "",
        "isSubjectCaseSensitive": false,
        "includedEventTypes": [ "All" ]
      }
    }
  }
]
```

L’exemple de modèle Resource Manager suivant crée un abonnement pour un stockage d’objets blob. Il limite les événements à ceux dont l’objet se termine par `.jpg`.

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts/providers/eventSubscriptions",
    "name": "[concat(parameters('storageName'), '/Microsoft.EventGrid/', parameters('eventSubName'))]",
    "apiVersion": "2018-09-15-preview",
    "properties": {
      "destination": {
        "endpointType": "WebHook",
        "properties": {
          "endpointUrl": "[parameters('endpoint')]"
        }
      },
      "filter": {
        "subjectEndsWith": ".jpg",
        "subjectBeginsWith": "",
        "isSubjectCaseSensitive": false,
        "includedEventTypes": [ "All" ]
      }
    }
  }
]
```

> [!NOTE]
> Pour en savoir plus sur ces filtres (Types d’événements, Objet et Avancé), consultez [Comprendre le filtrage d’événements pour les abonnements Event Grid](event-filtering.md). 

## <a name="filter-by-operators-and-data"></a>Filtrer avec des opérateurs et sur des données

Pour plus de flexibilité, vous pouvez utiliser des opérateurs et des propriétés de données lors du filtrage des événements.

### <a name="subscribe-with-advanced-filters"></a>S’abonner avec des filtres avancés

Pour en savoir plus sur les opérateurs et les clés que vous pouvez utiliser pour le filtrage avancé, consultez [Filtrage avancé](event-filtering.md#advanced-filtering).

Ces exemples créent une rubrique personnalisée. Ils s’abonnent à la rubrique personnalisée et filtrent selon une valeur de l'objet de données. Les événements dont la propriété color est définie sur blue, red ou green sont envoyés à l’abonnement.

### <a name="azure-powershell"></a>Azure PowerShell

Pour PowerShell, utilisez la commande suivante :

```powershell
$topicName = <your-topic-name>
$endpointURL = <endpoint-URL>

New-AzResourceGroup -Name gridResourceGroup -Location eastus2
New-AzEventGridTopic -ResourceGroupName gridResourceGroup -Location eastus2 -Name $topicName

$topicid = (Get-AzEventGridTopic -ResourceGroupName gridResourceGroup -Name $topicName).Id

$expDate = '<mm/dd/yyyy hh:mm:ss>' | Get-Date
$AdvFilter1=@{operator="StringIn"; key="Data.color"; Values=@('blue', 'red', 'green')}

New-AzEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint $endpointURL `
  -ExpirationDate $expDate `
  -AdvancedFilter @($AdvFilter1)
```

### <a name="azure-cli"></a>Azure CLI

Pour l’interface de ligne de commande Azure, consultez :

```azurecli
topicName=<your-topic-name>
endpointURL=<endpoint-URL>

az group create -n gridResourceGroup -l eastus2
az eventgrid topic create --name $topicName -l eastus2 -g gridResourceGroup

topicid=$(az eventgrid topic show --name $topicName -g gridResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --source-resource-id $topicid \
  -n demoAdvancedSub \
  --advanced-filter data.color stringin blue red green \
  --endpoint $endpointURL \
  --expiration-date "<yyyy-mm-dd>"
```

Notez qu’une [date d’expiration](concepts.md#event-subscription-expiration) est définie pour l’abonnement.


### <a name="azure-portal"></a>Portail Azure

1. Dans la page **Abonnement aux événements**, sélectionnez **Ajouter un nouveau filtre** dans la section **FILTRES AVANCÉS**. 

    :::image type="content" source="./media/how-to-filter-events/add-new-filter-button.png" alt-text="Capture d’écran montrant la page Abonnement aux événements avec le lien Ajouter un nouveau filtre mis en évidence.":::    
2. Spécifiez une clé, un opérateur et une ou plusieurs valeurs à comparer. Dans l’exemple suivant, **data.color** est utilisé comme clé, **La chaîne est dans** comme opérateur, et les valeurs **blue**, **red** et **green** sont spécifiées comme valeurs. 

    :::image type="content" source="./media/how-to-filter-events/advanced-filter-example.png" alt-text="Capture d’écran montrant un exemple de filtre avancé."::: 

    > [!NOTE]
    > Pour en savoir plus sur les filtres avancés, consultez [Comprendre le filtrage d’événements pour les abonnements Event Grid](event-filtering.md). 


### <a name="test-the-filter"></a>Tester le filtre
Pour tester le filtre, envoyez un événement avec le champ color défini sur green. Comme green est l'une des valeurs du filtre, l'événement est transmis au point de terminaison.

### <a name="azure-powershell"></a>Azure PowerShell
Pour PowerShell, utilisez la commande suivante :

```powershell
$endpoint = (Get-AzEventGridTopic -ResourceGroupName gridResourceGroup -Name $topicName).Endpoint
$keys = Get-AzEventGridTopicKey -ResourceGroupName gridResourceGroup -Name $topicName

$eventID = Get-Random 99999
$eventDate = Get-Date -Format s

$htbody = @{
    id= $eventID
    eventType="recordInserted"
    subject="myapp/vehicles/cars"
    eventTime= $eventDate
    data= @{
        model="SUV"
        color="green"
    }
    dataVersion="1.0"
}

$body = "["+(ConvertTo-Json $htbody)+"]"

Invoke-WebRequest -Uri $endpoint -Method POST -Body $body -Headers @{"aeg-sas-key" = $keys.Key1}
```

Pour tester un scénario dans lequel l’événement n’est pas envoyé, envoyez un événement avec le champ color défini sur yellow. Comme yellow n’est pas l’une des valeurs spécifiées dans l’abonnement, l’événement n’est pas remis à votre abonnement.

```powershell
$htbody = @{
    id= $eventID
    eventType="recordInserted"
    subject="myapp/vehicles/cars"
    eventTime= $eventDate
    data= @{
        model="SUV"
        color="yellow"
    }
    dataVersion="1.0"
}

$body = "["+(ConvertTo-Json $htbody)+"]"

Invoke-WebRequest -Uri $endpoint -Method POST -Body $body -Headers @{"aeg-sas-key" = $keys.Key1}
```


### <a name="azure-cli"></a>Azure CLI
Pour l’interface de ligne de commande Azure, consultez :

```azurecli
topicEndpoint=$(az eventgrid topic show --name $topicName -g gridResourceGroup --query "endpoint" --output tsv)
key=$(az eventgrid topic key list --name $topicName -g gridResourceGroup --query "key1" --output tsv)

event='[ {"id": "'"$RANDOM"'", "eventType": "recordInserted", "subject": "myapp/vehicles/cars", "eventTime": "'`date +%Y-%m-%dT%H:%M:%S%z`'", "data":{ "model": "SUV", "color": "green"},"dataVersion": "1.0"} ]'

curl -X POST -H "aeg-sas-key: $key" -d "$event" $topicEndpoint
```

Pour tester un scénario dans lequel l’événement n’est pas envoyé, envoyez un événement avec le champ color défini sur yellow. Comme yellow n’est pas l’une des valeurs spécifiées dans l’abonnement, l’événement n’est pas remis à votre abonnement.

Pour l’interface de ligne de commande Azure, consultez :

```azurecli
event='[ {"id": "'"$RANDOM"'", "eventType": "recordInserted", "subject": "myapp/vehicles/cars", "eventTime": "'`date +%Y-%m-%dT%H:%M:%S%z`'", "data":{ "model": "SUV", "color": "yellow"},"dataVersion": "1.0"} ]'

curl -X POST -H "aeg-sas-key: $key" -d "$event" $topicEndpoint
```

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur les filtres (Types d’événements, Objet et Avancé), consultez [Comprendre le filtrage d’événements pour les abonnements Event Grid](event-filtering.md). 

