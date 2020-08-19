---
author: baanders
description: Fichier include pour les options de filtre de routage Azure Digital Twins
ms.service: digital-twins
ms.topic: include
ms.date: 8/3/2020
ms.author: baanders
ms.openlocfilehash: c5b78d80b1aa958af50b81dc152b4a746ce85f70
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/06/2020
ms.locfileid: "87902190"
---
| Nom du filtre | Description | Schéma du texte du filtre | Valeurs prises en charge | 
| --- | --- | --- | --- |
| True / False | Autorise la création d'un itinéraire sans filtrage ou la désactivation d'un itinéraire afin qu'aucun événement ne soit envoyé | `<true/false>` | `true` = l’itinéraire est activé sans filtrage. <br> `false` = l’itinéraire est désactivé |
| Type | [Type d’événement](../articles/digital-twins/concepts-route-events.md#types-of-event-messages) circulant dans votre instance de jumeau numérique. | `type = '<eventType>'` | Les valeurs de type d'événement possibles sont les suivantes : <br>`Microsoft.DigitalTwins.Twin.Create` <br> `Microsoft.DigitalTwins.Twin.Delete` <br> `Microsoft.DigitalTwins.Twin.Update`<br>`Microsoft.DigitalTwins.Relationship.Create`<br>`Microsoft.DigitalTwins.Relationship.Update`<br> `Microsoft.DigitalTwins.Relationship.Delete` <br> `microsoft.iot.telemetry`  |
| Source | Nom de l’instance Azure Digital Twins. | `source = '<hostname>'`| Les valeurs de nom d'hôte possibles sont les suivantes : <br> **Pour les notifications** : `<yourDigitalTwinInstance>.<yourRegion>.azuredigitaltwins.net` <br> **Pour la télémétrie** : `<yourDigitalTwinInstance>.<yourRegion>.azuredigitaltwins.net/digitaltwins/<twinId>`|
| Objet | Description de l’événement dans le contexte de la source de l’événement ci-dessus | `subject = '<subject>'` | Les valeurs d'objet sont les suivantes : <br>**Pour les notifications** : l’objet est `<twinid>` <br> ou un format d’URI pour les objets identifiés de façon unique par plusieurs éléments ou ID :<br>`<twinid>/relationships/<relationshipid>`<br> **Pour la télémétrie** : l’objet est le chemin d’accès du composant (si la télémétrie est émise à partir d’un composant jumeau), par exemple `comp1.comp2`. Si la télémétrie n’est pas émise à partir d’un composant, le champ d’objet est vide. |
| Schéma de données | ID de modèle DTDL | `dataschema = '<model-dtmi-ID>'` | **Pour la télémétrie** : le schéma de données est l'ID de modèle du jumeau ou du composant qui émet les données de télémétrie. Par exemple : `dtmi:example:com:floor4;2` <br>**Pour les notifications** : le schéma de données n’est pas pris en charge.|
| Type de contenu | Type de contenu de la valeur de données | `datacontenttype = '<contentType>'` | Le type de contenu est `application/json` |
| Version de spécification | Version du schéma d’événement que vous utilisez | `specversion = '<version>'` | La version doit être `1.0`. Cela indique le schéma CloudEvents version 1.0. |

Il est également possible de combiner des filtres à l’aide des opérations suivantes :

| Nom du filtre | Schéma du texte du filtre | Exemple | 
| --- | --- | --- |
| AND / OR | `<filter1> AND <filter2>` | `type != 'microsoft.iot.telemetry' AND datacontenttype = 'application/json'` |
| AND / OR | `<filter1> OR <filter2>` | `type != 'microsoft.iot.telemetry' OR datacontenttype = 'application/json'` |
| Opérations imbriquées | `(<Comparison1>) AND (<Comparison2>)` | `(type != 'microsoft.iot.telemetry' OR datacontenttype = 'application/json') OR (specversion != '1.0')` |

> [!NOTE]
> Pendant la période de préversion, seule l’égalité de chaîne est prise en charge (=, ! =).

Lorsque vous implémentez ou mettez à jour un filtre, quelques minutes peuvent s’écouler avant que la modification apparaisse dans le pipeline de données.
