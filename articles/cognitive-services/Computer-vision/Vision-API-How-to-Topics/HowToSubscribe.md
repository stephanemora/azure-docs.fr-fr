---
title: Obtenir des clés d’abonnement - Vision par ordinateur
titlesuffix: Azure Cognitive Services
description: Découvrez comment obtenir des clés d’abonnement pour les appels vers l’API Vision par ordinateur dans Azure Cognitive Services.
services: cognitive-services
author: KellyDF
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: article
ms.date: 05/19/2017
ms.author: kefre
ms.custom: seodec18
ms.openlocfilehash: 03e519520d4a956a5c9690dc1327089505aafced
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/18/2019
ms.locfileid: "58120854"
---
# <a name="how-to-obtain-subscription-keys"></a>Obtenir des clés d’abonnement

Les services Vision par ordinateur nécessitent des clés d’abonnement spéciales. Chaque appel de l’API Vision par ordinateur nécessite une clé d’abonnement. Cette clé doit être transmise par le biais d’un paramètre de chaîne de requête ou spécifiée dans l’en-tête de requête.

Pour vous inscrire et obtenir des clés d’abonnement, consultez [Abonnements](https://azure.microsoft.com/try/cognitive-services/). L’inscription est gratuite. La tarification de ces services peut faire l’objet de modifications.

> [!NOTE]
> Vos clés d’abonnement ne sont valides que dans l’une de ces [régions Microsoft Azure](https://azure.microsoft.com/regions/). 

| Région | Adresse |
|---|---|
| USA Ouest | westus.api.cognitive.microsoft.com |
| USA Est 2 | eastus2.api.cognitive.microsoft.com |
| USA Centre-Ouest | westcentralus.api.cognitive.microsoft.com |
| Europe Ouest | westeurope.api.cognitive.microsoft.com |
| Asie Sud-Est | southeastasia.api.cognitive.microsoft.com |

Si vous vous inscrivez via l’essai gratuit du service de Vision par ordinateur, vos clés d’abonnement sont valides pour la région **westcentral** (`https://westcentralus.api.cognitive.microsoft.com/`). Il s’agit du cas le plus courant. Toutefois, si vous vous inscrivez au service de Vision par ordinateur avec votre compte Microsoft Azure via le site web [https://azure.microsoft.com/](https://azure.microsoft.com/), vous devez spécifier la région de votre essai à partir de la liste des régions ci-dessus.

Par exemple, si vous vous inscrivez au service de Vision par ordinateur avec votre compte Microsoft Azure et que vous spécifiez `westus` comme région, vous devez utiliser la région `westus` pour vos appels REST API (`https://westus.api.cognitive.microsoft.com/`).

Si vous oubliez la région de votre clé d’abonnement après l’obtention de votre clé d’essai, vous pouvez retrouver votre région sur [https://azure.microsoft.com/try/cognitive-services/my-apis/](https://azure.microsoft.com/try/cognitive-services/my-apis/).

### <a name="related-links"></a>Liens connexes

* [Options de tarification des API Azure Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/)
