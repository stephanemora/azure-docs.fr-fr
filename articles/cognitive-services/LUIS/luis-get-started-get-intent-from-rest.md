---
title: 'Démarrage rapide : Reconnaître une intention avec des API REST - LUIS'
description: Dans ce guide de démarrage rapide avec une API REST, utilisez une application LUIS publique disponible pour déterminer l’intention d’un utilisateur à partir d’un texte de conversation.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 05/18/2020
ms.custom: devx-track-python, devx-track-js, devx-track-csharp
zone_pivot_groups: programming-languages-set-one
ms.openlocfilehash: d03ebc1ec5730f1905ce1ee5b9c484312f5aeee1
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2020
ms.locfileid: "91316315"
---
# <a name="quickstart-get-intent-with-rest-apis"></a>Démarrage rapide : Reconnaître une intention avec des API REST

Dans ce guide de démarrage rapide, vous allez utiliser une application LUIS pour déterminer l’intention d’un utilisateur à partir du texte conversationnel. Envoyez l’intention de l’utilisateur sous forme de texte au point de terminaison de prédiction HTTP de l’application de pizza. Au niveau du point de terminaison, LUIS applique le modèle de l’application de pizza pour analyser le sens du texte en langage naturel, déterminer l’intention globale et extraire les données pertinentes pour le domaine de l’application.

Pour cet article, vous avez besoin d’un compte [LUIS](https://www.luis.ai) gratuit.

<a name="create-luis-subscription-key"></a>

::: zone pivot="programming-language-csharp"
[!INCLUDE [Get intent with C# and REST](./includes/get-started-get-intent-rest-csharp.md)]
::: zone-end

::: zone pivot="programming-language-go"
[!INCLUDE [Get intent with Go and REST](./includes/get-started-get-intent-rest-go.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Get intent with Java and REST](./includes/get-started-get-intent-rest-java.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [Get intent with Node.js and REST](./includes/get-started-get-intent-rest-nodejs.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Get intent with Python and REST](./includes/get-started-get-intent-rest-python.md)]
::: zone-end
