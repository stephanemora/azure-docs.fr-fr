---
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: include
ms.date: 11/02/2021
ms.author: aahi
ms.custom: ignite-fall-2021
ms.openlocfilehash: 2502698a95671bf2b6ca9960a7f006a12d3a7ce8
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131028897"
---
## <a name="typical-workflow"></a>Flux de travail classique

Pour utiliser cette fonctionnalité, vous envoyez des données à des fins d’analyse et gérez la sortie de l’API dans votre application. L’analyse est effectuée telle quelle, sans aucune personnalisation supplémentaire du modèle utilisé sur vos données.

1. Créez une ressource Azure Language, qui vous permet d’accéder aux fonctionnalités offertes par Azure Cognitive Service for Language. Cela génère un mot de passe (appelé clé) et une URL de point de terminaison que vous utiliserez pour authentifier les requêtes d’API.

2. Créez une requête à l’aide de l’API REST ou de la bibliothèque de client pour C#, Java, JavaScript et Python. Vous pouvez également envoyer des appels asynchrones avec une requête de lot afin de combiner des requêtes d’API pour plusieurs fonctionnalités en un seul appel.

3. Envoyez la requête contenant vos données sous forme de texte brut non structuré. Votre clé et votre point de terminaison seront utilisés pour l’authentification.

4. Diffusez ou stockez la réponse localement. 
