---
title: Stratégies de lettres mortes et de nouvelles tentatives pour les abonnements Azure Event Grid
description: Explique comment personnaliser les options de remise des événements pour Event Grid. Définissez une destination de lettres mortes, et spécifiez la durée des nouvelles tentatives de remise.
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: conceptual
ms.date: 10/10/2018
ms.author: tomfitz
ms.openlocfilehash: fcf3ecaff6e8ba1421496a96d01428946cf8ab8e
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/10/2018
ms.locfileid: "49077780"
---
# <a name="dead-letter-and-retry-policies"></a>Stratégies de lettres mortes et de nouvelles tentatives

Quand vous créez un abonnement aux événements, vous pouvez personnaliser les paramètres de remise des événements. Cet article vous montre comment configurer un emplacement de lettres mortes et personnaliser les paramètres de nouvelle tentative. Pour plus d’informations sur ces fonctionnalités, voir [Remise et nouvelle tentative de remise de messages Event Grid](delivery-and-retry.md).

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="set-dead-letter-location"></a>Définir l’emplacement des lettres mortes

Pour définir un emplacement de lettres mortes, vous avez besoin d’un compte de stockage. Ce dernier sert à contenir les événements qui ne peuvent pas être remis à un point de terminaison. Le script suivant obtient l’ID de ressource d’un compte de stockage existant et crée un abonnement aux événements qui utilise un conteneur dans ce compte de stockage pour le point de terminaison des lettres mortes.

```azurecli-interactive
# if you have not already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

storagename=demostorage
containername=testcontainer

storageid=$(az storage account show --name $storagename --resource-group gridResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  -g gridResourceGroup \
  --topic-name <topic_name> \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL> \
  --deadletter-endpoint $storageid/blobServices/default/containers/$containername
```

Pour désactiver les lettres mortes, réexécutez la commande pour créer l’abonnement aux événements, mais ne fournissez pas de valeur pour `deadletter-endpoint`. Vous n’avez pas besoin de supprimer l’abonnement aux événements.

## <a name="set-retry-policy"></a>Définir une stratégie de nouvelle tentative

Au moment de créer un abonnement Event Grid, vous pouvez définir des valeurs fixant la durée pendant laquelle Event Grid doit tenter de remettre l’événement. Par défaut, Event Grid renouvelle les tentatives pendant 24 heures (1 440 minutes) et les répète au maximum 30 fois. Vous pouvez définir chacune des ces valeurs pour votre abonnement Event Grid. La valeur de durée de vie d’un événement doit être un entier de 1 à 1440. La valeur de nombre maximal de tentatives de remise doit être un entier de 1 à 30.

Vous ne pouvez pas configurer de [nouvelle tentative de planification](delivery-and-retry.md#retry-schedule-and-duration).

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

Pour définir un nombre maximal de tentatives différent de 30, utilisez :

```azurecli-interactive
az eventgrid event-subscription create \
  -g gridResourceGroup \
  --topic-name <topic_name> \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL> \
  --max-delivery-attempts 18
```

Si vous définissez à la fois `event-ttl` et `max-deliver-attempts`, Event Grid utilise le premier paramètre à expirer pour les nouvelles tentatives.

## <a name="next-steps"></a>Étapes suivantes

* Pour un exemple d’application utilisant une application de Azure Function pour traiter des événements de lettres mortes, voir [Exemples de lettres mortes Azure Event Grid pour .NET](https://azure.microsoft.com/resources/samples/event-grid-dotnet-handle-deadlettered-events/).
* Pour plus d’informations sur la remise d’événements et sur les nouvelles tentatives, consultez [Remise et nouvelle tentative de remise de messages avec Azure Grid](delivery-and-retry.md).
* Pour une présentation d’Event Grid, consultez [À propos d’Event Grid](overview.md).
* Pour une prise en main rapide d’Event Grid, consultez [Créer et acheminer des événements personnalisés avec Azure Event Grid](custom-event-quickstart.md).
