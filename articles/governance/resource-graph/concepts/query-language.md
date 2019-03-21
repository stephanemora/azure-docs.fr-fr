---
title: Comprendre le langage de requête
description: Décrit les opérateurs Kusto disponibles et les fonctions utilisables avec le graphique des ressources Azure.
services: resource-graph
author: DCtheGeek
ms.author: dacoulte
ms.date: 12/11/2018
ms.topic: conceptual
ms.service: resource-graph
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 618445c5b792317d4de4b668e7ea1a186707007c
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/04/2019
ms.locfileid: "57317915"
---
# <a name="understanding-the-azure-resource-graph-query-language"></a>Présentation du langage de requête Azure Resource Graph

Le langage de requête pour Azure Resource Graph prend en charge un certain nombre d’opérateurs et de fonctions. Chacun fonctionne selon l’[explorateur de données Azure](../../../data-explorer/data-explorer-overview.md).

La meilleure façon d’en savoir plus sur le langage de requête utilisé par Resource Graph consiste à consulter la documentation sur le [langage de requête](/azure/kusto/query/index) de l’explorateur de données Azure. Elle fournit des informations sur la structure du langage et sur la façon de combiner les différents opérateurs et fonctions pris en charge.

## <a name="supported-tabular-operators"></a>Opérateurs tabulaires pris en charge

Voici la liste des opérateurs tabulaires pris en charge dans Resource Graph :

- [count](/azure/kusto/query/countoperator)
- [distinct](/azure/kusto/query/distinctoperator)
- [extend](/azure/kusto/query/extendoperator)
- [limit](/azure/kusto/query/limitoperator)
- [order by](/azure/kusto/query/orderoperator)
- [project](/azure/kusto/query/projectoperator)
- [project-away](/azure/kusto/query/projectawayoperator)
- [sample](/azure/kusto/query/sampleoperator)
- [sample-distinct](/azure/kusto/query/sampledistinctoperator)
- [sort by](/azure/kusto/query/sortoperator)
- [summarize](/azure/kusto/query/summarizeoperator)
- [take](/azure/kusto/query/takeoperator)
- [top](/azure/kusto/query/topoperator)
- [top-nested](/azure/kusto/query/topnestedoperator)
- [top-hitters](/azure/kusto/query/tophittersoperator)
- [where](/azure/kusto/query/whereoperator)

## <a name="supported-functions"></a>Fonctions prises en charge

Voici la liste des fonctions prises en charge dans Resource Graph :

- [ago()](/azure/kusto/query/agofunction)
- [buildschema()](/azure/kusto/query/buildschema-aggfunction)
- [strcat()](/azure/kusto/query/strcatfunction)
- [isnotempty()](/azure/kusto/query/isnotemptyfunction)
- [tostring()](/azure/kusto/query/tostringfunction)
- [zip()](/azure/kusto/query/zipfunction)

## <a name="next-steps"></a>Étapes suivantes

- Voir l’utilisation du langage dans les [requêtes de démarrage](../samples/starter.md)
- Voir les utilisations avancées dans les [Requêtes avancées](../samples/advanced.md)
- Apprendre à [explorer les ressources](explore-resources.md)