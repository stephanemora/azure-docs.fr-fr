---
title: Stratégies de lettres mortes et de nouvelles tentatives pour les abonnements Azure Event Grid
description: Explique comment personnaliser les options de remise des événements pour Event Grid. Définissez une destination de lettres mortes, et spécifiez la durée des nouvelles tentatives de remise.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/06/2019
ms.author: spelluru
ms.openlocfilehash: 8660f09c41cf6226f2ffb173508d37c260522b80
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/23/2019
ms.locfileid: "54474242"
---
# <a name="dead-letter-and-retry-policies"></a>Stratégies de lettres mortes et de nouvelles tentatives

Quand vous créez un abonnement aux événements, vous pouvez personnaliser les paramètres de remise des événements. Cet article vous montre comment configurer un emplacement de lettres mortes et personnaliser les paramètres de nouvelle tentative. Pour plus d’informations sur ces fonctionnalités, voir [Remise et nouvelle tentative de remise de messages Event Grid](delivery-and-retry.md).

## <a name="install-preview-feature"></a>Installer la fonctionnalité d'évaluation

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="set-dead-letter-location"></a>Définir l’emplacement des lettres mortes

Pour définir un emplacement de lettres mortes, vous avez besoin d’un compte de stockage. Ce dernier sert à contenir les événements qui ne peuvent pas être remis à un point de terminaison. Les exemples reçoivent l'ID de ressource d'un compte de stockage existant. Ils créent un abonnement aux événements qui utilise un conteneur de ce compte de stockage pour le point de terminaison des lettres mortes.

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# If you have not already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

containername=testcontainer

topicid=$(az eventgrid topic show --name demoTopic -g gridResourceGroup --query id --output tsv)
storageid=$(az storage account show --name demoStorage --resource-group gridResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --source-resource-id $topicid \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL> \
  --deadletter-endpoint $storageid/blobServices/default/containers/$containername
```

Pour désactiver les lettres mortes, réexécutez la commande pour créer l’abonnement aux événements, mais ne fournissez pas de valeur pour `deadletter-endpoint`. Vous n’avez pas besoin de supprimer l’abonnement aux événements.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# If you have not already installed the module, do it now.
# This module is required for preview features.
Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery

$containername = "testcontainer"

$topicid = (Get-AzureRmEventGridTopic -ResourceGroupName gridResourceGroup -Name demoTopic).Id
$storageid = (Get-AzureRmStorageAccount -ResourceGroupName gridResourceGroup -Name demostorage).Id

New-AzureRmEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint <endpoint_URL> `
  -DeadLetterEndpoint "$storageid/blobServices/default/containers/$containername"
```

Pour désactiver les lettres mortes, réexécutez la commande pour créer l’abonnement aux événements, mais ne fournissez pas de valeur pour `DeadLetterEndpoint`. Vous n’avez pas besoin de supprimer l’abonnement aux événements.

## <a name="set-retry-policy"></a>Définir une stratégie de nouvelle tentative

Au moment de créer un abonnement Event Grid, vous pouvez définir des valeurs fixant la durée pendant laquelle Event Grid doit tenter de remettre l’événement. Par défaut, Event Grid essaie pendant 24 heures (1 440 minutes) ou 30 fois. Vous pouvez définir chacune des ces valeurs pour votre abonnement Event Grid. La valeur de durée de vie d’un événement doit être un entier de 1 à 1440. La valeur du nombre maximum de nouvelles tentatives doit être un entier compris entre 1 et 30.

Vous ne pouvez pas configurer de [nouvelle tentative de planification](delivery-and-retry.md#retry-schedule-and-duration).

### <a name="azure-cli"></a>Azure CLI

Pour attribuer aux événements une durée de vie différente de 1 440 minutes, utilisez :

```azurecli-interactive
# if you have not already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid event-subscription create \
  -g gridResourceGroup \
  --topic-name <topic_name> \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL> \
  --event-ttl 720
```

Pour définir un nombre maximum de nouvelles tentatives différent de 30, utilisez :

```azurecli-interactive
az eventgrid event-subscription create \
  -g gridResourceGroup \
  --topic-name <topic_name> \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL> \
  --max-delivery-attempts 18
```

Si vous définissez `event-ttl` et `max-deliver-attempts`, Event Grid utilise la date de la première expiration pour déterminer quand arrêter la remise des événements.

### <a name="powershell"></a>PowerShell

Pour attribuer aux événements une durée de vie différente de 1 440 minutes, utilisez :

```azurepowershell-interactive
# If you have not already installed the module, do it now.
# This module is required for preview features.
Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery

$topicid = (Get-AzureRmEventGridTopic -ResourceGroupName gridResourceGroup -Name demoTopic).Id

New-AzureRmEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint <endpoint_URL> `
  -EventTtl 720
```

Pour définir un nombre maximum de nouvelles tentatives différent de 30, utilisez :

```azurepowershell-interactive
$topicid = (Get-AzureRmEventGridTopic -ResourceGroupName gridResourceGroup -Name demoTopic).Id

New-AzureRmEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint <endpoint_URL> `
  -MaxDeliveryAttempt 18
```

Si vous définissez `EventTtl` et `MaxDeliveryAttempt`, Event Grid utilise la date de la première expiration pour déterminer quand arrêter la remise des événements.

## <a name="next-steps"></a>Étapes suivantes

* Pour un exemple d’application utilisant une application de Azure Function pour traiter des événements de lettres mortes, voir [Exemples de lettres mortes Azure Event Grid pour .NET](https://azure.microsoft.com/resources/samples/event-grid-dotnet-handle-deadlettered-events/).
* Pour plus d’informations sur la remise d’événements et sur les nouvelles tentatives, consultez [Remise et nouvelle tentative de remise de messages avec Azure Grid](delivery-and-retry.md).
* Pour une présentation d’Event Grid, consultez [À propos d’Event Grid](overview.md).
* Pour une prise en main rapide d’Event Grid, consultez [Créer et acheminer des événements personnalisés avec Azure Event Grid](custom-event-quickstart.md).
