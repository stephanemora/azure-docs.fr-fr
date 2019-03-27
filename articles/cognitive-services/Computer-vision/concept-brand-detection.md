---
title: Détection des marques - Vision par ordinateur
titleSuffix: Azure Cognitive Services
description: Concepts liés à la détection des marques ou logos à l’aide de l’API Vision par ordinateur.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 01/26/2019
ms.author: pafarley
ms.openlocfilehash: abeca204296bcb3933013f2b7434b8c558f62e50
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2019
ms.locfileid: "58496585"
---
# <a name="brand-detection"></a>Détection des marques

La détection des marques est un mode spécialisé de la [détection des objets](concept-object-detection.md) qui utilise une base de données comprenant des milliers de logos du monde entier pour permettre d’identifier des marques commerciales dans les images et les vidéos. Vous pouvez utiliser cette fonctionnalité, par exemple, pour déterminer quelles marques sont les plus populaires sur les réseaux sociaux, et lesquelles prédominent dans les médias.

Le service Vision par ordinateur permet de détecter si une image donnée contient un logo de marque. Si c’est le cas, il retourne le nom de la marque, un indice de confiance ainsi que les coordonnées du rectangle englobant qui se trouve autour du logo.

La base de données de logos intégrée contient les marques les plus populaires d’appareils électroniques grand public, de vêtements, etc. Si la marque que vous recherchez n’est pas détectée par le service Vision par ordinateur, il peut être préférable de créer et d’entraîner votre propre détecteur de logos à l’aide du service [Custom Vision](https://docs.microsoft.com/azure/cognitive-services/Custom-Vision-Service/).

## <a name="brand-detection-example"></a>Exemple de détection de marque

Les réponses JSON suivantes illustrent ce que retourne l’API Vision par ordinateur lors de la détection de marques dans les exemples d’images.

![Sweatshirt gris avec une étiquette où figurent la marque et le logo Microsoft](./Images/gray-shirt-logo.jpg)

```json
{
   "brands":[
      {
         "name":"Microsoft",
         "confidence":0.706,
         "rectangle":{
            "x":470,
            "y":862,
            "w":338,
            "h":327
         }
      }
   ],
   "requestId":"5fda6b40-3f60-4584-bf23-911a0042aa13",
   "metadata":{
      "width":2286,
      "height":1715,
      "format":"Jpeg"
   }
}
```
Dans certains cas, le détecteur de marque considère l’image du logo et le nom stylisé de la marque comme constituant deux logos distincts.

![T-shirt rouge avec une étiquette où figurent la marque et le logo Microsoft](./Images/red-shirt-logo.jpg)

```json
{
   "brands":[
      {
         "name":"Microsoft",
         "confidence":0.657,
         "rectangle":{
            "x":436,
            "y":473,
            "w":568,
            "h":267
         }
      },
      {
         "name":"Microsoft",
         "confidence":0.85,
         "rectangle":{
            "x":101,
            "y":561,
            "w":273,
            "h":263
         }
      }
   ],
   "requestId":"10dcd2d6-0cf6-4a5e-9733-dc2e4b08ac8d",
   "metadata":{
      "width":1286,
      "height":1715,
      "format":"Jpeg"
   }
}
```

## <a name="use-the-api"></a>Utilisation de l’API

La fonctionnalité de détection de marque fait partie de l’API [Analyser l’image](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa). Vous pouvez appeler cette API via un SDK natif ou via des appels REST. Inclure `Brands` dans le **visualFeatures** paramètre de requête. Ensuite, lorsque vous obtenez la réponse JSON complète, simplement analyser la chaîne pour le contenu de la `"brands"` section.

* [Démarrage rapide : Analyser une image (SDK .NET)](./quickstarts-sdk/csharp-analyze-sdk.md)
* [Démarrage rapide : Analyser une image (API REST)](./quickstarts/csharp-analyze.md)
