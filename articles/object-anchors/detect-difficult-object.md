---
title: Procédure de détection d’un objet difficile
description: Décrivez les mécanismes qui peuvent être configurés pour détecter des objets difficiles.
author: rgarcia
manager: vrivera
ms.author: rgarcia
ms.date: 05/28/2021
ms.topic: overview
ms.service: azure-object-anchors
ms.openlocfilehash: f7ebff2a9fb45831842b9390e3e72cdbfe58e794
ms.sourcegitcommit: e39ad7e8db27c97c8fb0d6afa322d4d135fd2066
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111987828"
---
# <a name="how-to-detect-a-difficult-object"></a>Procédure de détection d’un objet difficile

Parfois, un objet peut s’avérer plus difficile à détecter. Par exemple :

- Lorsqu’une grande surface d’exposition n’est pas accessible parce que l’objet est adossé à un mur
- Lorsqu’un objet est trop volumineux et qu’il faut trop de temps pour le contourner
- Lorsque la surface de l’objet n’est pas détectée par les capteurs d’appareil

Voici quelques mécanismes offerts par le kit de développement logiciel (SDK) Azure Object Anchors qui peuvent être utiles dans ces cas :

- La propriété `ObjectQuery.MinSurfaceCoverage`. Elle représente le ratio minimal de couverture de surface requis pour considérer une instance d’objet comme un vrai positif. Elle autorise une plage comprise entre 0 et 1 (qui représente de 0 % à 100 %). Le paramètre par défaut varie d’un objet à l’autre (plus la surface d’exposition est grande, plus la couverture minimale requise est petite). Elle fonctionne telle quelle dans la plupart des situations. Toutefois, lorsqu’il s’agit d’objets difficiles, il est recommandé de réduire la valeur de cette propriété afin qu’une couverture de surface plus petite soit requise pour détecter l’objet.

- La propriété `ObjectQuery.MaxScaleChange`. Si le modèle d’origine n’a pas de mise à l’échelle `1:1` vers l’objet en cours de détection, ce paramètre peut être ajusté. Elle autorise une plage comprise entre 0 et 1 (qui représente de 0 % à 100 %). La valeur par défaut, à savoir 0, désactive l’estimation de l’échelle, ce qui nécessite un mappage de l'échelle `1:1`. L’affectation de la valeur 10 % à cette propriété peut, par exemple, activer l’estimation de l’échelle et permettre une certaine flexibilité dans les cas où l’échelle du modèle n’a pas de correspondance `1:1` avec l’objet.

- La propriété `ObjectQuery.ExpectedMaxVerticalOrientationInDegrees`. Elle représente l’angle maximal, en degrés, entre la direction vers le haut de l’objet et la gravité. Elle est comprise entre 0 et 180. En d’autres termes, elle représente l’inclinaison de l’objet par rapport au modèle d’origine. Le paramètre par défaut, à 3 degrés, peut être augmenté pour offrir une plus grande flexibilité dans les cas où l’inclinaison de l’objet ne correspond pas au modèle d’origine.

- La propriété `ObjectQuery.IsExpectedToBeStandingOnGroundPlane`. Il s’agit d’une valeur booléenne qui indique si l’objet est supposé être debout au niveau du sol ou non. La valeur par défaut est « False ». Vous pouvez basculer vers la valeur True pour accélérer la détection des cas où l’objet est au niveau du sol.

- La propriété `ObjectQuery.SearchAreas`. Elle représente une série de régions dans laquelle rechercher des objets. L’indication de zones de recherche étroites, couvrant toutefois la totalité ou une partie de l’objet, améliore la vitesse et la précision de la détection. Vous pouvez utiliser, au choix :

  - Un cadre englobant orienté, à l’aide de `ObjectSearchArea.FromOriented`.
  - Un champ de vue, à l’aide de `ObjectSearchArea.FromFieldOfView`.
  - Un emplacement, à l’aide de `ObjectSearchArea.FromLocation`.
  - Une sphère, à l’aide de `ObjectSearchArea.FromSphere`.

Pour plus d’informations, consultez la classe `ObjectQuery` pour [Unity](/dotnet/api/microsoft.azure.objectanchors.objectquery) ou [HoloLens C++/WinRT](/cpp/api/object-anchors/winrt/objectquery).
