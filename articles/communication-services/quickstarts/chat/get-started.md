---
title: 'Démarrage rapide : Ajouter un système de conversation à votre application'
titleSuffix: An Azure Communication Services quickstart
description: Ce guide de démarrage rapide vous montre comment ajouter un système de conversation Communication Services à votre application.
author: fanche
manager: phans
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
zone_pivot_groups: acs-js-csharp-java-python-swift-android
ms.openlocfilehash: a62c25aa5fd9d25b7330dde89c8544e79785b9d0
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2021
ms.locfileid: "103495399"
---
# <a name="quickstart-add-chat-to-your-app"></a>Démarrage rapide : Ajouter un système de conversation à votre application

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]


Commencez avec Azure Communication Services en utilisant la bibliothèque de client Communication Services Chat pour ajouter un système de conversation en temps réel à votre application. Dans ce guide de démarrage rapide, nous allons utiliser la bibliothèque de client Chat pour créer des fils de conversation qui permettent aux utilisateurs de discuter entre eux. Pour en savoir plus sur les concepts de Chat, consultez la [documentation conceptuelle de Chat](../../concepts/chat/concepts.md).

::: zone pivot="programming-language-javascript"
[!INCLUDE [Chat with JavaScript client library](./includes/chat-js.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Chat with Python client library](./includes/chat-python.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Chat with Java client library](./includes/chat-java.md)]
::: zone-end

::: zone pivot="programming-language-android"
[!INCLUDE [Chat with Android client library](./includes/chat-android.md)]
::: zone-end

::: zone pivot="programming-language-csharp"
[!INCLUDE [Chat with C# client library](./includes/chat-csharp.md)]
::: zone-end

::: zone pivot="programming-language-swift"
[!INCLUDE [Chat with iOS client library](./includes/chat-swift.md)]
::: zone-end

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous voulez nettoyer et supprimer un abonnement Communication Services, vous pouvez supprimer la ressource ou le groupe de ressources. La suppression du groupe de ressources efface également les autres ressources qui y sont associées. Apprenez-en davantage sur le [nettoyage des ressources](../create-communication-resource.md#clean-up-resources).

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez découvert comment :

> [!div class="checklist"]
> * Créer un client de conversation
> * Créer un fil avec deux utilisateurs
> * Envoyer un message au fil
> * Recevoir des messages d’un fil
> * Supprimer des utilisateurs d’un fil

> [!div class="nextstepaction"]
> [Essayez l’application Chat Hero](../../samples/chat-hero-sample.md)

Vous voudrez peut-être aussi :

 - Découvrir les [concepts de Chat](../../concepts/chat/concepts.md)
 - Vous familiariser avec la [bibliothèque de client Chat](../../concepts/chat/sdk-features.md)
