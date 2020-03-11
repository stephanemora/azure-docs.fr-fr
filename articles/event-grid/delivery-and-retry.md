---
title: Distribution et nouvelle tentative de distribution avec Azure Event Grid
description: Décrit comment Azure Event Grid distribue des événements et gère les messages qui n’ont pas été distribués.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 02/27/2020
ms.author: spelluru
ms.openlocfilehash: dda2fd98c4c0d330059156a5ec00baa97ffaf627
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/28/2020
ms.locfileid: "77921060"
---
# <a name="event-grid-message-delivery-and-retry"></a>Distribution et nouvelle tentative de distribution de messages avec Azure Grid

Cet article décrit comment Azure Event Grid gère les événements en l’absence d’accusé de réception d’une distribution.

Event Grid assure une distribution fiable. Il distribue chaque message au moins une fois pour chaque abonnement. Les événements sont envoyés immédiatement au point de terminaison inscrit de chaque abonnement. Si un point de terminaison n’accuse pas réception d’un événement, Event Grid effectue une nouvelle tentative de distribution.

## <a name="batched-event-delivery"></a>Livraison d’événements par lot

Par défaut, Event Grid envoie chaque événement individuellement aux abonnés. L’abonné reçoit un tableau ne comprenant qu’un seul événement. Vous pouvez configurer Event Grid pour que les événements soient livrés par lot afin d’améliorer les performances HTTP dans les scénarios à débit élevé.

La livraison par lot a deux paramètres :

* **Nombre maximum d’événements par lot** : nombre maximum d’événements qu’Event Grid livrera par lot. Ce nombre ne sera jamais dépassé, mais moins d’événements peuvent être livrés si aucun autre événement n’est disponible au moment de la publication. Event Grid ne retarde pas la livraison des événements pour créer un lot si moins d’événements sont disponibles. Doit être compris entre 1 et 5 000.
* **Taille de lot préférée en kilo-octets** : plafond cible pour la taille de lot en kilo-octets. Comme pour le nombre maximum d’événements, la taille du lot peut être plus petite si aucun autre événement n’est disponible au moment de la publication. Il est possible qu’un lot soit plus grand que la taille de lot préférée *si* un événement unique est plus volumineux que la taille préférée. Par exemple, si la taille préférée est de 4 Ko et qu’un événement de 10 Ko est envoyé (push) à Event Grid, l’événement de 10 Ko sera tout de même livré dans son propre lot plutôt que d’être abandonné.

La livraison en lot est configurée sur la base d’un abonnement par événement via le portail, l’interface CLI, PowerShell ou les Kits de développement logiciel (SDK).

### <a name="azure-portal"></a>Portail Azure : 
![Paramètres de livraison en lot](./media/delivery-and-retry/batch-settings.png)

### <a name="azure-cli"></a>Azure CLI
Lorsque vous créez un abonnement aux événements, utilisez les paramètres suivants : 

- **max-events-per-batch** : nombre maximum d’événements dans un lot. Doit être un nombre compris entre 1 et 5000.
- **preferred-batch-size-in-kilobytes**  taille de lot préférée en kilo-octets. Doit être un nombre compris entre 1 et 1024.

```azurecli
storageid=$(az storage account show --name <storage_account_name> --resource-group <resource_group_name> --query id --output tsv)
endpoint=https://$sitename.azurewebsites.net/api/updates

az eventgrid event-subscription create \
  --resource-id $storageid \
  --name <event_subscription_name> \
  --endpoint $endpoint \
  --max-events-per-batch 1000 \
  --preferred-batch-size-in-kilobytes 512
```

Pour plus d’informations sur l’utilisation d’Azure CLI avec Event Grid, consultez [Acheminer des événements de stockage vers un point de terminaison web avec Azure CLI](../storage/blobs/storage-blob-event-quickstart.md).

## <a name="retry-schedule-and-duration"></a>Planification d’un nouvel essai et durée

Event Grid attend une réponse pendant 30 secondes après la distribution d’un message. Après 30 secondes, si le point de terminaison n’a pas répondu, le message est mis en file d’attente en vue d’une nouvelle tentative. Event Grid utilise une stratégie de nouvelle tentative d’interruption exponentielle pour la distribution des événements. Dans la mesure du possible, Event Grid tente une nouvelle livraison selon la planification suivante :

- 10 secondes
- 30 secondes
- 1 minute
- 5 minutes
- 10 minutes
- 30 minutes
- 1 heure
- Toutes les heures jusqu’à 24 heures

Si le point de terminaison répond dans les 3 minutes, Event Grid tente de supprimer l’événement de la file d’attente de nouvelle tentative dans la mesure du possible, mais des doublons peuvent toujours être reçus.

Event Grid ajoute une légère randomisation à toutes les étapes de nouvelle tentative et peut ignorer de façon opportuniste certaines nouvelles tentatives si un point de terminaison est systématiquement non sain, inactif pendant une longue période ou semble être surchargé.

Pour un comportement déterministe, définissez la durée de l’événement de durée de vie et de nombre maximum de tentatives de remise dans les [stratégies de nouvelle tentative d’abonnement](manage-event-delivery.md).

Par défaut, Event Grid fait expirer tous les événements qui ne sont pas distribués dans les 24 heures. Vous pouvez [personnaliser la stratégie de nouvelle tentative](manage-event-delivery.md) lors de la création d’un abonnement à un événement. Vous fournissez le nombre maximal de tentatives de remise (par défaut, 30) et la durée de vie de l’événement (par défaut, 1 440 minutes).

## <a name="delayed-delivery"></a>Livraison retardée

En cas d'échec de livraison d'un point de terminaison, Event Grid commence à retarder la livraison et retente les événements sur ce point de terminaison. Par exemple, si les dix premiers événements publiés sur un point de terminaison échouent, Event Grid supposera que le point de terminaison rencontre des problèmes et retardera toutes les tentatives suivantes *et les nouvelles* livraisons pendant un certain laps de temps, parfois même pendant plusieurs heures.

La livraison retardée a pour objectif de protéger les points de terminaison non sains, ainsi que le système Event Grid. Sans temporisation et retard de livraison sur les points de terminaison non sains, la stratégie de nouvelle tentative d'Event Grid peut aisément saturer un système.

## <a name="dead-letter-events"></a>Événements de lettres mortes

Quand Event Grid ne parvient pas à remettre un événement, il peut envoyer cet événement non remis à un compte de stockage. Ce processus est appelé mise en file d’attente de lettres mortes. Par défaut, Event Grid n’active pas cette fonctionnalité. Pour l’activer, vous devez spécifier le compte de stockage dans lequel les événements non remis seront conservés au moment de créer l’abonnement aux événements. Les événements sont extraits de ce compte de stockage pour résoudre les remises.

Event Grid envoie un événement à l’emplacement des lettres mortes lorsqu’il a effectué toutes ses nouvelles tentatives. Si Event Grid reçoit un code de réponse 400 (requête incorrecte) ou 413 (entité de requête trop grande), il envoie immédiatement l’événement au point de terminaison des lettres mortes. Ces codes de réponse indiquent que la diffusion de l’événement va échouer.

Un délai de cinq minutes s’écoule entre la dernière tentative de remise d’un événement et le moment de sa remise à l’emplacement des lettres mortes. Ce décalage est destiné à réduire le nombre d’opérations de stockage d’objets blob. Si l’emplacement des lettres mortes est indisponible pendant quatre heures, l’événement est abandonné.

Avant de définir l’emplacement des lettres mortes, vous devez disposer d’un compte de stockage avec un conteneur. Vous devez indiquer le point de terminaison de ce conteneur au moment de créer l’abonnement aux événements. Le point de terminaison se présente sous la forme suivante : `/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-name>/blobServices/default/containers/<container-name>`

Vous souhaiterez peut-être être averti lorsqu’un événement a été envoyé à l’emplacement des lettres mortes. Pour répondre aux événements non remis à l’aide d’Event Grid, [créez un abonnement aux événements](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) pour le stockage Blob de lettres mortes. Chaque fois que votre stockage Blob de lettres mortes reçoit un événement non remis, Event Grid notifie votre gestionnaire. Le gestionnaire répond par les mesures que vous voulez prendre pour réconcilier les événements non remis.

Pour savoir comment configurer un emplacement de lettres mortes, voir [Stratégies de lettres mortes et de nouvelles tentatives](manage-event-delivery.md).

## <a name="message-delivery-status"></a>État de distribution du message

Event Grid utilise les codes de réponse HTTP pour accuser réception des événements. 

### <a name="success-codes"></a>Codes de réussite

Event Grid considère **uniquement** les codes de réponse HTTP suivants en tant que livraisons réussies. Tous les autres codes d’état sont considérés en tant que livraisons ayant échoué et font l'objet de nouvelles tentatives ou de lettres mortes, le cas échéant. Lorsqu'il reçoit un code d'état réussi, Event Grid considère la livraison comme terminée.

- 200 OK
- 201 Créé
- 202 Accepté
- 203 Informations ne faisant pas autorité
- 204 Pas de contenu

### <a name="failure-codes"></a>Codes d’échec

Tous les codes ne figurant par dans les codes ci-dessus (200 à 204) sont considérés comme ayant échoué et feront l'objet de nouvelles tentatives. Certains sont associés à des stratégies de nouvelle tentative spécifiques (voir ci-dessous). Toutes les autres suivent le modèle de temporisation exponentielle standard. Il est important de garder à l’esprit qu’en raison de la nature hautement parallélisée de l’architecture d'Event Grid, le comportement d'une nouvelle tentative n'est pas déterministe. 

| Code d’état | Comportement pour les nouvelles tentatives |
| ------------|----------------|
| 400 Demande incorrecte | Nouvelle tentative après 5 minutes (lettre morte immédiatement, si configurée) |
| 401 Non autorisé | Nouvelle tentative après 5 minutes ou plus |
| 403 Interdit | Nouvelle tentative après 5 minutes ou plus |
| 404 Introuvable | Nouvelle tentative après 5 minutes ou plus |
| 408 Délai d’expiration de la requête | Nouvelle tentative après 2 minutes ou plus |
| 413 Entité de demande trop grande | Nouvelle tentative après 10 secondes ou plus (lettre morte immédiatement, si configurée) |
| 503 Service indisponible | Nouvelle tentatives après 30 secondes ou plus |
| Tous les autres | Nouvelle tentatives après 10 secondes ou plus |


## <a name="next-steps"></a>Étapes suivantes

* Pour afficher l’état des remises des événements, consultez [Surveiller la remise des messages Event Grid](monitor-event-delivery.md).
* Pour personnaliser les options de diffusion d’événements, consultez [Stratégies de lettres mortes et de nouvelles tentatives](manage-event-delivery.md).
