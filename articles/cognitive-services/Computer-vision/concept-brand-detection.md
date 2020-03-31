---
title: Détection des marques - Vision par ordinateur
titleSuffix: Azure Cognitive Services
description: "Cet article présente un mode de détection d'objets particulier : la détection de marques et/ou de logos à l'aide de l'API Vision par ordinateur."
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: pafarley
ms.openlocfilehash: 50e4fe1e2573c8566bbdf5697bb81b025a00935c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131735"
---
# <a name="detect-popular-brands-in-images"></a>Détecter des marques populaires dans des images

La détection des marques est un mode spécialisé de la [détection des objets](concept-object-detection.md) qui utilise une base de données comprenant des milliers de logos du monde entier pour permettre d’identifier des marques commerciales dans les images et les vidéos. Vous pouvez utiliser cette fonctionnalité, par exemple, pour déterminer quelles marques sont les plus populaires sur les réseaux sociaux, et lesquelles prédominent dans les médias.

Le service Vision par ordinateur permet de détecter si une image donnée contient un logo de marque. Si c’est le cas, il retourne le nom de la marque, un indice de confiance ainsi que les coordonnées du rectangle englobant qui se trouve autour du logo.

La base de données de logos intégrée contient les marques les plus populaires d’appareils électroniques grand public, de vêtements, etc. Si la marque que vous recherchez n’est pas détectée par le service Vision par ordinateur, il peut être préférable de créer et d’entraîner votre propre détecteur de logos à l’aide du service [Custom Vision](https://docs.microsoft.com/azure/cognitive-services/Custom-Vision-Service/).

## <a name="brand-detection-example"></a>Exemple de détection de marque

Les réponses JSON suivantes illustrent ce que retourne l’API Vision par ordinateur lors de la détection de marques dans les exemples d’images.

![T-shirt rouge avec une étiquette où figurent la marque et le logo Microsoft](./Images/red-shirt-logo.jpg)

```json
"brands":[  
   {  
      "name":"Microsoft",
      "rectangle":{  
         "x":20,
         "y":97,
         "w":62,
         "h":52
      }
   }
]
```

Dans certains cas, le détecteur de marque considère l’image du logo et le nom stylisé de la marque comme constituant deux logos distincts.

![Sweatshirt gris avec une étiquette où figurent la marque et le logo Microsoft](./Images/gray-shirt-logo.jpg)

```json
"brands":[  
   {  
      "name":"Microsoft",
      "rectangle":{  
         "x":58,
         "y":106,
         "w":55,
         "h":46
      }
   },
   {  
      "name":"Microsoft",
      "rectangle":{  
         "x":58,
         "y":86,
         "w":202,
         "h":63
      }
   }
]
```

## <a name="use-the-api"></a>Utilisation de l’API

La fonctionnalité de détection de marque fait partie de l’API [Analyser l’image](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa). Vous pouvez appeler cette API via un SDK natif ou via des appels REST. Incluez `Brands` dans le paramètre de requête **visualFeatures**. Ensuite, lorsque vous obtenez la réponse JSON complète, analysez simplement la chaîne de contenu de la section `"brands"`.

* [Démarrage rapide : SDK .NET Vision par ordinateur](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
* [Démarrage rapide : Analyser une image (API REST)](./quickstarts/csharp-analyze.md)
