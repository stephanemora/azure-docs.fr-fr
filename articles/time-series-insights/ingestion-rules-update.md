---
title: Modifications à venir des règles d’ingestion et de mise à plat dans Azure Time Series Insights | Microsoft Docs
description: Modifications de la règle d’ingestion
ms.service: time-series-insights
services: time-series-insights
author: lyrana
ms.author: lyhughes
manager: dpalled
ms.workload: big-data
ms.topic: conceptual
ms.date: 06/16/2020
ms.custom: lyhughes
ms.openlocfilehash: 067244aa40256e3cc76239343790974bc3c06481
ms.sourcegitcommit: dee7b84104741ddf74b660c3c0a291adf11ed349
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85919030"
---
# <a name="upcoming-changes-to-the-json-flattening-and-escaping-rules-for-new-environments"></a>Modifications à venir des règles JSON de mise à plat et d’échappement pour les nouveaux environnements

Ces modifications seront appliquées uniquement aux *nouveaux* environnements Azure Time Series Insights de paiement à l’utilisation (PAYG). Ces modifications ne s’appliquent pas aux environnements Standard (S).

Votre environnement Azure Time Series Insights crée de manière dynamique les colonnes de votre stockage, en suivant un ensemble particulier de conventions d’affectation de noms. Lorsqu’un événement est ingéré, un ensemble de règles est appliqué à la charge utile JSON et aux noms de propriétés. Les modifications apportées à la façon dont les données JSON sont aplaties et stockées entreront en vigueur pour les nouveaux environnements Azure Time Series Insights de paiement à l’utilisation en juillet 2020. Cette modification vous concerne dans les cas suivants :

* Si votre charge utile JSON contient des objets imbriqués
*  Si votre charge utile JSON contient des tableaux
*  Si vous utilisez l’un des quatre caractères spéciaux suivants dans un nom de propriété JSON : [ \ . '
*  Si une ou plusieurs de vos propriétés ID TS se trouvent dans un objet imbriqué

Si vous créez un nouvel environnement et qu’un ou plusieurs des cas ci-dessus s’appliquent à votre charge utile d’événement, vous verrez vos données aplaties et stockées différemment. Voici un récapitulatif des modifications :

| Règle actuelle | Nouvelle règle | Exemple JSON | Nom de colonne précédent | Nouveau nom de colonne
|---|---| ---| ---|  ---|
| Le JSON imbriqué est aplati à l’aide d’un trait de soulignement comme délimiteur |Le JSON imbriqué est aplati à l’aide d’un point comme délimiteur  | ``{"series" : { "value" : 19.338 }}`` | `series_value_double` |`series.value_double` |
| Les caractères spéciaux ne sont pas placés dans une séquence d’échappement | Les noms des propriétés JSON qui incluent les caractères spéciaux . [  \ et ' sont placés dans une séquence d’échappement [' et ']. Entre les caractères [' et '], des guillemets simples et des barres obliques inverses supplémentaires sont inclus. Un guillemet simple est écrit sous la forme \' et une barre oblique inverse est écrite sous la forme \\\  | ```"Foo's Law Value": "17.139999389648"``` | `Foo's Law Value_double` | `['Foo\'s Law Value']_double` | 
| Les tableaux de primitives sont stockés sous forme de chaîne | Les tableaux de types primitifs sont stockés sous un type dynamique  | `"values": [154, 149, 147]` | `values_string`  | `values_dynamic` |
Les tableaux d’objets sont toujours aplatis, ce qui génère plusieurs événements | Si les objets d’un tableau n’ont pas les propriétés ID TS ou timestamp, le tableau d’objets est stocké sous un type dynamique | `"values": [{"foo" : 140}, {"bar" : 149}]` | `values_foo_long | values_bar_long` | `values_dynamic` |

## <a name="recommended-changes-for-new-environments"></a>Modifications recommandées pour les nouveaux environnements

#### <a name="if-your-ts-id-andor-timestamp-property-is-nested-within-an-object"></a>Si votre propriété ID TS ou timestamp est imbriquée dans un objet :

*  Tous les nouveaux déploiements devront obéir aux nouvelles règles d’ingestion. Par exemple, si votre ID TS est `telemetry_tagId`, vous devez mettre à jour tous les modèles Resource Manager ou scripts de déploiement automatisés pour configurer `telemetry.tagId` comme ID TS d’environnement. Cette modification est également nécessaire pour les timestamps de la source d’événement dans le format JSON imbriqué.

 #### <a name="if-your-payload-contains-nested-json-or-special-characters-and-you-automate-authoring-time-series-model-variable-expressions"></a>Si votre charge utile contient des caractères spéciaux ou un JSON imbriqué et que vous automatisez la création des expressions de variable du[modèle de série chronologique](.\time-series-insights-update-tsm.md) :

*  Mettez à jour votre code client en exécutant [TypesBatchPut](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/timeseriestypes/executebatch#typesbatchput) pour qu’il respecte les nouvelles règles d’ingestion. Par exemple, une ancienne [expression de série chronologique](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax) `"value": {"tsx": "$event.series_value.Double"}` doit être mise à jour vers l’une des options ci-dessous :
    * `"value": {"tsx": "$event.series.value.Double"}`
    * `"value": {"tsx": "$event['series']['value'].Double"}`



## <a name="next-steps"></a>Étapes suivantes

- Lire [Ajout de la prise en charge du type de données Long](./time-series-insights-long-data-type.md).

- Lire [Azure Time Series Insights (préversion) – Stockage et entrée](./time-series-insights-update-storage-ingress.md).

