---
title: Utiliser Azure Event Grid avec des événements dans le schéma CloudEvents
description: Décrit comment définir le schéma CloudEvents pour les événements dans Azure Event Grid.
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/09/2018
ms.author: babanisa
ms.openlocfilehash: 23187fbc230e384984085d330bfbfbc90cc9f945
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/18/2018
---
# <a name="use-cloudevents-schema-with-event-grid"></a>Utiliser le schéma CloudEvents avec Event Grid

En plus de son [schéma d’événement par défaut](event-schema.md), Azure Event Grid prend en charge en mode natif les événements dans le [schéma JSON CloudEvents](https://github.com/cloudevents/spec/blob/master/json-format.md). [CloudEvents](http://cloudevents.io/) est une [spécification standard ouverte](https://github.com/cloudevents/spec/blob/master/spec.md) qui décrit les données d’événement de manière commune.

CloudEvents simplifie l’interopérabilité grâce à un schéma d’événement commun pour la publication et la consommation des événements basés sur le cloud. Ce schéma permet l’utilisation d’outils uniformes, la mise en œuvre de méthodes de routage et de gestion des événements standard, ainsi que de méthodes universelles pour désérialiser le schéma d’événement externe. Avec un schéma commun, vous pouvez plus facilement intégrer le travail entre plusieurs plateformes.

Plusieurs [collaborateurs](https://github.com/cloudevents/spec/blob/master/community/contributors.md), dont Microsoft, travaillent à l’élaboration de CloudEvents par le biais de la [Cloud Native Compute Foundation](https://www.cncf.io/). Il est disponible en version 0.1.

Cet article décrit comment utiliser le schéma CloudEvents avec Event Grid.

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="cloudevent-schema"></a>Schéma CloudEvents

Voici un exemple d’événement Stockage Blob Azure dans le format CloudEvents :

``` JSON
{
    "cloudEventsVersion" : "0.1",
    "eventType" : "Microsoft.Storage.BlobCreated",
    "eventTypeVersion" : "",
    "source" : "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-account}#blobServices/default/containers/{storage-container}/blobs/{new-file}",
    "eventID" : "173d9985-401e-0075-2497-de268c06ff25",
    "eventTime" : "2018-04-28T02:18:47.1281675Z",
    "data" : {
      "api": "PutBlockList",
      "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
      "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
      "eTag": "0x8D4BCC2E4835CD0",
      "contentType": "application/octet-stream",
      "contentLength": 524288,
      "blobType": "BlockBlob",
      "url": "https://oc2d2817345i60006.blob.core.windows.net/oc2d2817345i200097container/oc2d2817345i20002296blob",
      "sequencer": "00000000000004420000000000028963",
      "storageDiagnostics": {
        "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
      }
    }
}
```

CloudEvents v0.1 dispose des propriétés suivantes :

| CloudEvents        | type     | Exemple de valeur JSON             | Description                                                        | Mappage Event Grid
|--------------------|----------|--------------------------------|--------------------------------------------------------------------|-------------------------
| eventType          | Chaîne   | "com.example.someevent"          | Type d’occurrence qui s’est produit                                   | eventType
| eventTypeVersion   | Chaîne   | "1.0"                            | Version d’eventType (facultatif)                            | dataVersion
| cloudEventsVersion | Chaîne   | "0.1"                            | Version de la spécification CloudEvents qu’utilise l’événement        | *transmise*
| source             | URI      | "/mycontext"                     | Décrit le producteur de l’événement                                       | topic#subject
| eventID            | Chaîne   | "1234-1234-1234"                 | ID de l’événement                                                    | id
| eventTime          | Timestamp| "2018-04-05T17:31:00Z"           | Horodatage qui indique quand l’événement s’est produit (facultatif)                    | eventTime
| schemaURL          | URI      | "https://myschema.com"           | Lien vers le schéma auquel adhère l’attribut de données (facultatif) | *non utilisée*
| contentType        | Chaîne   | "application/json"               | Décrit le format d’encodage des données (facultatif)                       | *non utilisée*
| extensions         | Mappage      | { "extA": "vA", "extB", "vB" }  | Toutes métadonnées supplémentaires (facultatif)                                 | *non utilisée*
| données               | Object   | { "objA": "vA", "objB", "vB" }  | Charge utile d’événement (facultatif)                                       | données

Pour plus d’informations, consultez la [spécification CloudEvents](https://github.com/cloudevents/spec/blob/master/spec.md#context-attributes).

## <a name="configure-event-grid-for-cloudevents"></a>Configurer Event Grid pour CloudEvents

Azure Event Grid prend en charge en préversion l’entrée et la sortie du format JSON CloudEvents dans les régions **États-Unis Centre-Ouest**, **Centre des États-Unis** et **Europe du Nord**.

Vous pouvez utiliser Event Grid pour l’entrée et la sortie des événements dans le schéma CloudEvents. Vous pouvez utiliser CloudEvents pour des événements système, tels que les événements Stockage Blob et les événements IoT Hub, et des événements personnalisés. Il peut aussi transformer ces événements dans un sens ou dans l’autre.


| Schéma d’entrée       | Schéma de sortie
|--------------------|---------------------
| Format CloudEvents | Format CloudEvents
| Format Event Grid  | Format CloudEvents
| Format CloudEvents | Format Event Grid
| Format Event Grid  | Format Event Grid

Pour tous les schémas d’événement, Event Grid requiert une validation au moment de la publication sur une rubrique de grille d’événement et de la création d’un abonnement aux événements. Pour en savoir plus, consultez la page [Sécurité et authentification pour Event Grid](security-authentication.md).

### <a name="input-schema"></a>Schéma d’entrée

Pour définir le schéma d’entrée d’une rubrique personnalisée sur CloudEvents, utilisez le paramètre suivant dans l’interface de ligne de commande Azure CLI quand vous créez votre rubrique `--input-schema cloudeventv01schema`. La rubrique personnalisée attend maintenant les événements entrants dans le format CloudEvents v0.1.

Pour créer une rubrique de grille d’événement, utilisez :

```azurecli
# if you have not already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid topic create \
  --name <topic_name> \
  -l westcentralus \
  -g gridResourceGroup \
  --input-schema cloudeventv01schema
```

La version actuelle de CloudEvents ne prend pas en charge le traitement par lots des événements. Pour publier des événements avec le schéma CloudEvents sur une rubrique, publiez chaque événement individuellement.

### <a name="output-schema"></a>Schéma de sortie

Pour définir le schéma de sortie d’un abonnement aux événements sur CloudEvents, utilisez le paramètre suivant dans l’interface de ligne de commande Azure CLI quand vous créez votre abonnement aux événements `--event-delivery-schema cloudeventv01schema`. Les événements liés à cet abonnement aux événements sont désormais remis dans le format CloudEvents v0.1.

Pour créer un abonnement aux événements, utilisez :

```azurecli
az eventgrid event-subscription create \
  --name <event_subscription_name> \
  --topic-name <topic_name> \
  -g gridResourceGroup \
  --endpoint <endpoint_URL> \
  --event-delivery-schema cloudeventv01schema
```

La version actuelle de CloudEvents ne prend pas en charge le traitement par lots des événements. Un abonnement aux événements qui est configuré pour le schéma CloudEvents reçoit chaque événement individuellement.

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’information sur la surveillance des remises des événements, consultez [Surveiller la remise des messages Event Grid](monitor-event-delivery.md).
* Nous vous encourageons à tester et à commenter CloudEvents, ainsi qu’à y [contribuer](https://github.com/cloudevents/spec/blob/master/CONTRIBUTING.md).
* Pour plus d’informations sur la création d’un abonnement Azure Event Grid, consultez [Schéma d’abonnement à Event Grid](subscription-creation-schema.md).
