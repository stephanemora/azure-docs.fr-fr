---
title: Stratégie de localisation d’ancre
description: En savoir plus sur les différentes stratégies lors de l’appel de l’API de localisation
author: pamistel
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: pamistel
ms.date: 02/11/2021
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: 13aa12be5a336363bbe3bcbf3e3fb354a8fa3074
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105048474"
---
# <a name="understanding-the-anchorlocatecriteria-class"></a>Fonctionnement de la classe AnchorLocateCriteria
Dans cet article, vous allez découvrir les différentes options que vous pouvez utiliser lors de l’interrogation d’une ancre. Nous allons passer en revue la classe AnchorLocateCriteria, ses options et les combinaisons d’options valides.

## <a name="anchor-locate-criteria"></a>Critères de localisation d’une ancre
La [classe AnchorLocateCriteria](/dotnet/api/microsoft.azure.spatialanchors.anchorlocatecriteria) vous permet d’interroger le service pour trouver des ancres créées précédemment. Un objet AnchorLocateCriteria peut être utilisé par observateur à tout moment. Chaque objet AnchorLocateCriteria doit inclure **exactement une** des propriétés suivantes : [Identifiers](#identifiers), [NearAnchor](#nearanchor) ou [NearDevice](#neardevice). Des propriétés supplémentaires telles que [Strategy](#strategy), [BypassCache](#bypasscache) et [RequestedCategories](#requestedcategories) peuvent être définies si vous le souhaitez. 

### <a name="properties"></a>Propriétés
Définissez **une seule** des propriétés suivantes dans votre observateur :
#### <a name="identifiers"></a>Identificateurs
*Valeur par défaut : tableau de chaînes vide*

En utilisant Identifiers, vous pouvez définir une liste d’ID d’ancre pour les ancres que vous souhaitez localiser. Les ID d’ancre vous sont initialement renvoyés après la création réussie d’une ancre. Lorsque la propriété Identifiers est spécifiée, AnchorLocateCriteria restreint l’ensemble des ancres demandées aux ancres dont les ID d’ancre correspondent. Cette propriété est spécifiée à l’aide d’un tableau de chaînes. 

#### <a name="nearanchor"></a>NearAnchor
*Valeur par défaut : non définie*

À l’aide de NearAnchor, vous pouvez spécifier qu’AnchorLocateCriteria restreint l’ensemble des ancres demandées à celles qui se trouvent à une distance donnée d’une ancre choisie. Vous devez fournir cette ancre choisie comme ancre source. Vous pouvez également définir la distance souhaitée par rapport à l’ancre source, ainsi que le nombre maximum d’ancres renvoyées, afin de limiter davantage la recherche.
Cette propriété est spécifiée à l’aide d’un objet NearAnchorCriteria.

#### <a name="neardevice"></a>NearDevice
*Valeur par défaut : non définie*

À l’aide de NearDevice, vous pouvez spécifier qu’AnchorLocateCriteria restreint l’ensemble des ancres demandées à celles proches de l’emplacement physique de l’appareil. Tout capteur activé sera utilisé pour détecter les ancres autour de votre appareil. Pour obtenir les meilleures chances de trouver des ancres, vous devez configurer SensorCapabilities pour permettre à la session d’accéder à tous les capteurs appropriés. Pour plus d’informations sur la configuration et l’utilisation de cette propriété, consultez [Relocalisation grossière – Azure Spatial Anchors | Microsoft Docs](./coarse-reloc.md) et *Comment créer et localiser des ancres à l’aide de la relocalisation grossière* en [C#](../how-tos/set-up-coarse-reloc-unity.md), [Objective-C](../how-tos/set-up-coarse-reloc-unity.md), [Swift](../how-tos/set-up-coarse-reloc-swift.md), [Java](../how-tos/set-up-coarse-reloc-java.md), [C++/NDK](../how-tos/set-up-coarse-reloc-cpp-ndk.md) et [C++/WinRT](../how-tos/set-up-coarse-reloc-cpp-winrt.md).
Cette propriété est spécifiée à l’aide d’un objet NearDeviceCriteria.

### <a name="additional-properties"></a>Propriétés supplémentaires
#### <a name="bypasscache"></a>BypassCache
*Valeur par défaut : false*

Lorsqu’une ancre a été créée ou trouvée dans une session, elle est également stockée dans le cache.  Si cette propriété a la valeur false, toute requête ultérieure dans la même session renverra la valeur mise en cache. Aucune demande au service ASA n’est effectuée.

#### <a name="requestedcategories"></a>RequestedCategories
*Valeur par défaut : Propriétés | Spatial*

Cette propriété est utilisée pour déterminer quelles données sont renvoyées par une requête utilisant AnchorLocateCriteria. La valeur par défaut renvoie à la fois les propriétés et les données spatiales ; elle ne doit pas être modifiée si les propriétés et les données spatiales sont toutes deux souhaitées. Cette propriété peut être spécifiée à l’aide de l’énumération AnchorDataCategory.

Valeur d’énumération AnchorDataCategory | Données renvoyées
-----|------------
None | Aucune donnée n’est renvoyée.
Propriétés| Les propriétés de l’ancre, y compris AppProperties, sont renvoyées.
Spatial| Les informations spatiales relatives à une ancre sont renvoyées.

#### <a name="strategy"></a>Stratégie
*Valeur par défaut : AnyStrategy*

La stratégie définit plus en détail le mode de localisation des ancres. La propriété Strategy peut être spécifiée à l’aide d’une énumération LocateStrategy.

Valeur d’énumération LocateStrategy | Description
---------------|------------
AnyStrategy | Cette stratégie permet au système d’utiliser des combinaisons de stratégies VisualInformation et Relationship pour rechercher des ancres. 
VisualInformation|Cette stratégie tente de trouver des ancres en faisant correspondre les informations visuelles de l’environnement actuel à celles de l’empreinte visuelle de l’ancre. L’empreinte visuelle d’une ancre fait référence aux informations visuelles actuellement associées à l’ancre. Ces informations visuelles sont généralement recueillies lors de la création de l’ancre. Actuellement, cette stratégie est autorisée uniquement conjointement avec les propriétés NearDevice ou Identifiers.
Relationship|Cette stratégie tente de trouver des ancres en utilisant les [ancres connectées](./anchor-relationships-way-finding.md#connect-anchors) existantes. Actuellement, cette stratégie est autorisée uniquement conjointement avec les propriétés NearAnchor ou Identifiers. Lorsqu’elle est utilisée avec la propriété Identifiers, il faut que, dans la même session, l’utilisateur ait déjà localisé une ou plusieurs ancres ayant des relations de connexion déjà établies avec les ancres dont les ID sont spécifiés dans le tableau Identifiers. 


### <a name="valid-combinations-of-locatestrategy-and-anchorlocatecriteria-properties"></a>Combinaisons valides des propriétés LocateStrategy et AnchorLocateCriteria 

Toutes les combinaisons des propriétés Strategy et AnchorLocateCriteria ne sont pas actuellement autorisées par le système. Le tableau suivant indique les combinaisons autorisées :



Propriété | AnyStrategy | Relationship | VisualInformation
-------- | ------------|--------------|-------------------
Identificateurs | &check;    | &check;     | &check;
NearAnchor  | &check;   (la valeur par défaut sera Relationship) | &check;    | 
NearDevice  | &check;    |   | &check;




## <a name="next-steps"></a>Étapes suivantes

Pour obtenir d’autres exemples d’utilisation de la classe AnchorLocateCriteria, consultez [Guide pratique pour créer et localiser des ancres à l’aide d’Azure Spatial Anchors](../create-locate-anchors-overview.md).