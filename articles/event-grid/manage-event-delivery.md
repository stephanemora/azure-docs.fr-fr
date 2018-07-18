---
title: Gérer les paramètres de remise des abonnements Azure Event Grid
description: Explique comment personnaliser les options de remise des événements pour Event Grid.
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 06/26/2018
ms.author: tomfitz
ms.openlocfilehash: 65e79f492e96c418502e096b60992ba992868dd7
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37034933"
---
# <a name="manage-event-grid-delivery-settings"></a>Gérer les paramètres de remise Event Grid

Quand vous créez un abonnement aux événements, vous pouvez personnaliser les paramètres de remise des événements. Vous pouvez définir pendant combien de temps Event Grid tente de remettre le message. Vous pouvez définir le compte de stockage à utiliser pour stocker les événements qui ne peuvent pas être remis à un point de terminaison.

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="set-retry-policy"></a>Définir une stratégie de nouvelle tentative

Au moment de créer un abonnement Event Grid, vous pouvez définir des valeurs fixant la durée pendant laquelle Event Grid doit tenter de remettre l’événement. Par défaut, Event Grid renouvelle les tentatives pendant 24 heures (1 440 minutes) et les répète au maximum 30 fois. Vous pouvez définir chacune des ces valeurs pour votre abonnement Event Grid.

Pour attribuer aux événements une durée de vie différente de 1 440 minutes, utilisez :

```azurecli-interactive
# if you have not already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid event-subscription create \
  -g gridResourceGroup \
  --topic-name <topic_name> \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL>
  --event-ttl 720
```

Pour définir un nombre maximal de tentatives différent de 30, utilisez :

```azurecli-interactive
az eventgrid event-subscription create \
  -g gridResourceGroup \
  --topic-name <topic_name> \
  --name <event_subscription_name> \
  --endpoint <endpoint_URL>
  --max-delivery-attempts 18
```

Si vous définissez à la fois `event-ttl` et `max-deliver-attempts`, Event Grid utilise le premier paramètre à expirer pour les nouvelles tentatives.

## <a name="set-dead-letter-location"></a>Définir l’emplacement des lettres mortes

Quand Event Grid ne parvient pas à remettre un événement, il peut envoyer cet événement non remis à un compte de stockage. Ce processus est appelé mise en file d’attente de lettres mortes. Par défaut, Event Grid n’active pas cette fonctionnalité. Pour l’activer, vous devez spécifier le compte de stockage dans lequel les événements non remis seront conservés au moment de créer l’abonnement aux événements. Les événements sont extraits de ce compte de stockage pour résoudre les remises.

Event Grid envoie un événement à l’emplacement des lettres mortes s’il a épuisé toutes ses tentatives ou s’il reçoit un message d’erreur indiquant que la remise n’aboutira jamais. Par exemple, si Event Grid reçoit une erreur de format incorrect au moment de remettre un événement, il envoie immédiatement cet événement à l’emplacement des lettres mortes.

Avant de définir l’emplacement des lettres mortes, vous devez disposer d’un compte de stockage avec un conteneur. Vous devez indiquer le point de terminaison de ce conteneur au moment de créer l’abonnement aux événements. Le point de terminaison se présente sous la forme suivante : `/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-name>/blobServices/default/containers/<container-name>`

Le script suivant obtient l’ID de ressource d’un compte de stockage existant et crée un abonnement aux événements qui utilise un conteneur dans ce compte de stockage pour le point de terminaison des lettres mortes.

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
  --endpoint <endpoint_URL>
  --deadletter-endpoint $storageid/blobServices/default/containers/$containername
```

Pour répondre aux événements non remis à l’aide d’Event Grid, [créez un abonnement aux événements](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) pour le stockage Blob de lettres mortes. Chaque fois que votre stockage Blob de lettres mortes reçoit un événement non remis, Event Grid notifie votre gestionnaire. Le gestionnaire répond par les mesures que vous voulez prendre pour réconcilier les événements non remis. 

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur la remise d’événements et sur les nouvelles tentatives, consultez [Remise et nouvelle tentative de remise de messages avec Azure Grid](delivery-and-retry.md).
* Pour une présentation d’Event Grid, consultez [À propos d’Event Grid](overview.md).
* Pour une prise en main rapide d’Event Grid, consultez [Créer et acheminer des événements personnalisés avec Azure Event Grid](custom-event-quickstart.md).
