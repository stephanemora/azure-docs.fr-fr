---
author: baanders
description: Fichier include pour les options de filtre de routage Azure Digital Twins
ms.service: digital-twins
ms.topic: include
ms.date: 12/04/2020
ms.author: baanders
ms.openlocfilehash: d93f484e318c10489eb1db3e9c65c6e0c7479c90
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98860124"
---
| Nom du filtre | Description | Schéma du texte du filtre | Valeurs prises en charge | 
| --- | --- | --- | --- |
| True / False | Autorise la création d'un itinéraire sans filtrage ou la désactivation d'un itinéraire afin qu'aucun événement ne soit envoyé | `<true/false>` | `true` = l’itinéraire est activé sans filtrage. <br> `false` = l’itinéraire est désactivé |
| Type | [Type d’événement](../articles/digital-twins/concepts-route-events.md#types-of-event-messages) circulant dans votre instance de jumeau numérique. | `type = '<eventType>'` | Les valeurs de type d'événement possibles sont les suivantes : <br>`Microsoft.DigitalTwins.Twin.Create` <br> `Microsoft.DigitalTwins.Twin.Delete` <br> `Microsoft.DigitalTwins.Twin.Update`<br>`Microsoft.DigitalTwins.Relationship.Create`<br>`Microsoft.DigitalTwins.Relationship.Update`<br> `Microsoft.DigitalTwins.Relationship.Delete` <br> `microsoft.iot.telemetry`  |
| Source | Nom de l’instance Azure Digital Twins. | `source = '<hostname>'`| Les valeurs de nom d'hôte possibles sont les suivantes : <br> **Pour les notifications** : `<yourDigitalTwinInstance>.api.<yourRegion>.digitaltwins.azure.net` <br> **Pour la télémétrie** : `<yourDigitalTwinInstance>.api.<yourRegion>.digitaltwins.azure.net/<twinId>`|
| Objet | Description de l’événement dans le contexte de la source de l’événement ci-dessus | `subject = '<subject>'` | Les valeurs d'objet sont les suivantes : <br>**Pour les notifications** : l’objet est `<twinid>` <br> ou un format d’URI pour les objets identifiés de façon unique par plusieurs éléments ou ID :<br>`<twinid>/relationships/<relationshipid>`<br> **Pour la télémétrie** : l’objet est le chemin d’accès du composant (si la télémétrie est émise à partir d’un composant jumeau), par exemple `comp1.comp2`. Si la télémétrie n’est pas émise à partir d’un composant, le champ d’objet est vide. |
| Schéma de données | ID de modèle DTDL | `dataschema = '<model-dtmi-ID>'` | **Pour la télémétrie** : le schéma de données est l'ID de modèle du jumeau ou du composant qui émet les données de télémétrie. Par exemple : `dtmi:example:com:floor4;2` <br>**Pour les notifications (création/suppression)**  : Le schéma de données est accessible dans le corps de la notification à `$body.$metadata.$model`. <br>**Pour les notifications (mise à jour)**  : Le schéma de données est accessible dans le corps de la notification à `$body.modelId`|
| Type de contenu | Type de contenu de la valeur de données | `datacontenttype = '<contentType>'` | Le type de contenu est `application/json` |
| Version de spécification | Version du schéma d’événement que vous utilisez | `specversion = '<version>'` | La version doit être `1.0`. Cela indique le schéma CloudEvents version 1.0. |
| Corps de la notification | Référencer n’importe quelle propriété dans le champ `data` d’une notification | `$body.<property>` | Consultez [*Guide pratique pour comprendre les données d’événements*](../articles/digital-twins/how-to-interpret-event-data.md) afin d’obtenir des exemples de notifications. Toute propriété du champ `data` peut être référencée à l’aide de `$body`

Les types de données suivants sont pris en charge en tant que valeurs retournées par les références aux données ci-dessus :

| Type de données | Exemple |
|-|-|-|
|**Chaîne**| `STARTS_WITH($body.$metadata.$model, 'dtmi:example:com:floor')` <br> `CONTAINS(subject, '<twinID>')`|
|**Integer**|`$body.errorCode > 200`|
|**Double**|`$body.temperature <= 5.5`|
|**Bool**|`$body.poweredOn = true`|
|**Null**|`$body.prop != null`|

Les opérateurs suivants sont pris en charge lors de la définition de filtres de route :

|Famille|Opérateurs|Exemple|
|-|-|-|
|Logical|AND, OR, ( )|`(type != 'microsoft.iot.telemetry' OR datacontenttype = 'application/json') OR (specversion != '1.0')`|
|Comparaison|<, <=, >, >=, =, !=|`$body.temperature <= 5.5`

Les fonctions suivantes sont prises en charge lors de la définition de filtres de route :

|Fonction|Description|Exemple|
|--|--|--|
|STARTS_WITH(x,y)|Retourne la valeur true si la valeur `x` commence par la chaîne `y`.|`STARTS_WITH($body.$metadata.$model, 'dtmi:example:com:floor')`|
|ENDS_WITH(x,y) | Retourne la valeur true si la valeur `x` se termine par la chaîne `y`.|`ENDS_WITH($body.$metadata.$model, 'floor;1')`|
|CONTAINS(x,y)| Retourne la valeur true si la valeur `x` contient la chaîne `y`.|`CONTAINS(subject, '<twinID>')`|

Lorsque vous implémentez ou mettez à jour un filtre, quelques minutes peuvent s’écouler avant que la modification apparaisse dans le pipeline de données.