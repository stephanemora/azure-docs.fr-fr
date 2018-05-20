---
title: Mapper un champ personnalisé au schéma Azure Event Grid
description: Décrit comment convertir votre schéma personnalisé en schéma Azure Event Grid.
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 05/09/2018
ms.author: tomfitz
ms.openlocfilehash: 1e809f83b43c32031b66c8f470575da6e9fcdc56
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/11/2018
---
# <a name="map-custom-fields-to-event-grid-schema"></a>Mapper des champs personnalisés au schéma Event Grid

Si vos données d’événement ne correspondent pas au [schéma Event Grid](event-schema.md) attendu, vous pouvez toujours utiliser Event Grid pour acheminer l’événement vers les abonnés. Cet article décrit comment mapper votre schéma au schéma Event Grid.

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="original-event-schema"></a>Schéma d’événement d’origine

Supposons que vous avez une application qui envoie des événements dans le format suivant :

```json
[
  {
    "myEventTypeField":"Created",
    "resource":"Users/example/Messages/1000",
    "resourceData":{"someDataField1":"SomeDataFieldValue"}
  }
]
```

Bien que ce format ne corresponde pas au schéma requis, Event Grid vous permet de mapper vos champs au schéma. Ou bien, vous pouvez recevoir les valeurs dans le schéma d’origine.

## <a name="create-custom-topic-with-mapped-fields"></a>Créer une rubrique personnalisée avec des champs mappés

Quand vous créez une rubrique personnalisée, spécifiez comment mapper les champs à partir de l’événement d’origine au schéma de grille d’événement. Vous disposez de trois propriétés pour personnaliser le mappage :

* Le paramètre `--input-schema` spécifie le type de schéma. Les options disponibles sont *cloudeventv01schema*, *customeventschema* et *eventgridschema*. La valeur par défaut est eventgridschema. Quand vous créez un mappage personnalisé entre votre schéma et le schéma de grille d’événement, utilisez customeventschema. Quand les événements se trouvent dans le schéma CloudEvents, utilisez cloudeventv01schema.

* Le paramètre `--input-mapping-default-values` spécifie les valeurs par défaut des champs dans le schéma Event Grid. Vous pouvez définir des valeurs par défaut pour *subject*, *eventtype* et *dataversion*. En règle générale, vous utilisez ce paramètre quand votre schéma personnalisé ne contient aucun champ correspondant à l’un de ces trois champs. Par exemple, vous pouvez spécifier que dataversion est toujours défini sur **1.0**.

* Le paramètre `--input-mapping-fields` mappe les champs à partir de votre schéma au schéma de grille d’événement. Vous spécifiez les valeurs sous la forme de paires clé/valeur séparées par des espaces. En guise de nom de clé, utilisez le nom du champ de la grille d’événement. En guise de valeur, utilisez le nom de votre champ. Vous pouvez utiliser des noms de clé pour *id*, *topic*, *eventtime*, *subject*, *eventtype* et *dataversion*.

L’exemple suivant crée une rubrique personnalisée avec certains champs mappés et par défaut :

```azurecli-interactive
# if you have not already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid topic create \
  -n demotopic \
  -l eastus2 \
  -g myResourceGroup \
  --input-schema customeventschema
  --input-mapping-fields eventType=myEventTypeField \
  --input-mapping-default-values subject=DefaultSubject dataVersion=1.0
```

## <a name="subscribe-to-event-grid-topic"></a>S’abonner à une rubrique de grille d’événement

Durant l’abonnement à la rubrique personnalisée, vous spécifiez le schéma que vous souhaitez utiliser pour recevoir les événements. Vous utilisez le paramètre `--event-delivery-schema` et le définissez sur *cloudeventv01schema*, *eventgridschema* ou *inputeventschema*. La valeur par défaut est eventgridschema.

Les exemples de cette section utilisent un stockage File d’attente pour le gestionnaire d’événements. Pour plus d’informations, consultez [Acheminer des événements personnalisés vers le stockage File d’attente Azure](custom-event-to-queue-storage.md).

L’exemple suivant s’abonne à une rubrique de grille d’événement et utilise le schéma de grille d’événement par défaut :

```azurecli-interactive
az eventgrid event-subscription create \
  --topic-name demotopic \
  -g myResourceGroup \
  --name eventsub1 \
  --endpoint-type storagequeue \
  --endpoint <storage-queue-url>
```

L’exemple suivant utilise le schéma d’entrée de l’événement :

```azurecli-interactive
az eventgrid event-subscription create \
  --topic-name demotopic \
  -g myResourceGroup \
  --name eventsub2 \
  --event-delivery-schema inputeventschema \
  --endpoint-type storagequeue \
  --endpoint <storage-queue-url>
```

## <a name="publish-event-to-topic"></a>Publier un événement sur la rubrique

Vous êtes maintenant prêt à envoyer un événement à la rubrique personnalisée et à voir le résultat du mappage. Le script suivant permet de publier un événement dans [l’exemple de schéma](#original-event-schema) :

```azurecli-interactive
endpoint=$(az eventgrid topic show --name demotopic -g myResourceGroup --query "endpoint" --output tsv)
key=$(az eventgrid topic key list --name demotopic -g myResourceGroup --query "key1" --output tsv)

body=$(eval echo "'$(curl https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/mapeventfields.json)'")

curl -X POST -H "aeg-sas-key: $key" -d "$body" $endpoint
```

À présent, examinez votre stockage File d’attente. Les deux abonnements ont remis des événements dans des schémas différents.

Le premier abonnement a utilisé le schéma de grille d’événement. Le format de l’événement remis est :

```json
{
  "Id": "016b3d68-881f-4ea3-8a9c-ed9246582abe",
  "EventTime": "2018-05-01T20:00:25.2606434Z",
  "EventType": "Created",
  "DataVersion": "1.0",
  "MetadataVersion": "1",
  "Topic": "/subscriptions/<subscription-id>/resourceGroups/myResourceGroup/providers/Microsoft.EventGrid/topics/demotopic",
  "Subject": "DefaultSubject",
  "Data": {
    "myEventTypeField": "Created",
    "resource": "Users/example/Messages/1000",
    "resourceData": { "someDataField1": "SomeDataFieldValue" } 
  }
}
```

Ces champs contiennent les mappages à partir de la rubrique personnalisée. **myEventTypeField** est mappé à **EventType**. Les valeurs par défaut pour **DataVersion** et **Subject** sont utilisées. L’objet **Data** contient les champs de schéma d’événement d’origine.

Le second abonnement a utilisé le schéma d’événement d’entrée. Le format de l’événement remis est :

```json
{
  "myEventTypeField": "Created",
  "resource": "Users/example/Messages/1000",
  "resourceData": { "someDataField1": "SomeDataFieldValue" }
}
```

Notez que les champs d’origine ont été remis.

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur la remise d’événements et sur les nouvelles tentatives, consultez [Remise et nouvelle tentative de remise de messages avec Azure Grid](delivery-and-retry.md).
* Pour une présentation d’Event Grid, consultez [À propos d’Event Grid](overview.md).
* Pour une prise en main rapide d’Event Grid, consultez [Créer et acheminer des événements personnalisés avec Azure Event Grid](custom-event-quickstart.md).
