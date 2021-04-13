---
title: 'Démarrage rapide : Bibliothèques de client et API REST du SDK Language Understanding (LUIS)'
description: Créez et interrogez une application LUIS avec les bibliothèques de client et l’API REST du SDK LUIS.
ms.topic: quickstart
ms.date: 03/29/2021
ms.service: cognitive-services
ms.author: aahi
manager: nitinme
ms.subservice: language-understanding
author: aahill
keywords: azure, intelligence artificielle, ia, traitement en langage naturel, tln, LUIS azure luis, compréhension du langage naturel, chatbot ia, créateur de chatbot, comprendre le langage naturel
ms.custom: devx-track-python, devx-track-js, devx-track-csharp, cog-serv-seo-aug-2020
zone_pivot_groups: programming-languages-set-luis
ms.openlocfilehash: ca45266ce4b8ca784c3d54aafb80a66efaf2a1da
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106278913"
---
# <a name="quickstart-language-understanding-luis-client-libraries-and-rest-api"></a>Démarrage rapide : Bibliothèques de client et API REST Language Understanding (LUIS)

Créez et interrogez une application d’IA Azure LUIS avec les bibliothèques de client du SDK LUIS en utilisant ce guide de démarrage rapide avec C#, Python ou JavaScript. Vous pouvez également utiliser cURL pour envoyer des requêtes à l’aide de l’API REST.

Language Understanding (LUIS) vous permet d’appliquer un traitement en langage naturel au texte en langage naturel des conversations d’un utilisateur afin d’en prédire le sens général, et d’en extraire des informations détaillées et pertinentes.

* La bibliothèque de client et l’API REST **Authoring** vous permettent de créer, modifier, entraîner et publier votre application LUIS.
* La bibliothèque de client et l’API REST **Prediction Runtime** vous permettent d’interroger l’application publiée.

::: zone pivot="programming-language-csharp"
[!INCLUDE [LUIS development with C# SDK](./includes/sdk-csharp.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [LUIS development with Node.js SDK](./includes/sdk-nodejs.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [LUIS development with Python SDK](./includes/sdk-python.md)]
::: zone-end

::: zone pivot="rest-api"
[!INCLUDE [LUIS development with REST API](./includes/rest-api.md)]
::: zone-end

## <a name="clean-up-resources"></a>Nettoyer les ressources

Vous pouvez supprimer l’application à partir du [portail LUIS](https://www.luis.ai) et supprimer les ressources Azure du [portail Azure](https://portal.azure.com/).

Si vous utilisez l’API REST, vous devrez supprimer le fichier `ExampleUtterances.JSON` du système de fichiers à la fin de ce guide de démarrage rapide.

## <a name="troubleshooting"></a>Dépannage

* Authentification auprès de la bibliothèque de client : les erreurs d’authentification indiquent généralement qu’une clé et un point de terminaison incorrects ont été utilisés. Pour des raisons pratiques, ce guide de démarrage rapide utilise la clé de création et le point de terminaison de création pour le runtime de prédiction, mais cela fonctionnera seulement si vous n’avez pas déjà utilisé le quota mensuel. Si vous ne pouvez pas utiliser la clé et le point de terminaison de création, vous devez utiliser la clé et le point de terminaison du runtime de prédiction lors de l’accès à la bibliothèque de client du SDK du runtime de prédiction.
* Création d’entités : si vous obtenez une erreur lors de la création de l’entité de machine learning imbriquée utilisée dans ce tutoriel, vérifiez que vous avez copié le code et que vous ne l’avez pas modifié pour créer une entité différente.
* Création d’exemples d’énoncés : si vous obtenez une erreur lors de la création de l’exemple d’énoncé étiqueté utilisé dans ce tutoriel, vérifiez que vous avez copié le code et que vous ne l’avez pas modifié pour créer un exemple étiqueté différent.
* Entraînement : si vous obtenez une erreur d’entraînement, cela indique généralement une application vide (sans intentions avec les exemples d’énoncés) ou une application avec des intentions ou des entités mal formées.
* Erreurs diverses : étant donné que le code appelle les bibliothèques de client avec du texte et des objets JSON, vérifiez que vous n’avez pas modifié le code.

Autres erreurs : si une erreur non couverte dans la liste précédente s’affiche, faites-le nous savoir en indiquant vos commentaires en bas de cette page. Incluez le langage de programmation et la version des bibliothèques de client que vous avez installés.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Développement itératif d'applications pour LUIS](./luis-concept-app-iteration.md)