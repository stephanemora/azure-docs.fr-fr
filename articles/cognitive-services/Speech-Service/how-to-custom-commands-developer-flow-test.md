---
title: Tester l’application Commandes personnalisées
titleSuffix: Azure Cognitive Services
description: Dans cet article, vous étudierez différentes approches pour tester une application Commandes personnalisées.
services: cognitive-services
author: xiaojul
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: xiaojul
ms.custom: devx-track-csharp
ms.openlocfilehash: fa9f9ec8d7a8f60d6c72cb6c4f669ef511cc0068
ms.sourcegitcommit: 30e3eaaa8852a2fe9c454c0dd1967d824e5d6f81
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/22/2021
ms.locfileid: "112456870"
---
# <a name="test-your-custom-commands-application"></a>Tester votre application Commandes personnalisées

Dans cet article, vous étudierez différentes approches pour tester une application Commandes personnalisées.

## <a name="test-in-the-portal"></a>Test dans le portail

Le test dans le portail est le moyen le plus simple et le plus rapide de vérifier si votre application Commandes personnalisées fonctionne comme prévu. Une fois l’apprentissage de l’application correctement effectué, cliquez sur le bouton `Test` pour démarrer le test.

> [!div class="mx-imgBorder"]
> ![Test dans le portail](media/custom-commands/create-basic-test-chat-no-mic.png)

## <a name="test-with-windows-voice-assistant-client"></a>Test avec le client Assistant vocal Windows

Le client Assistant vocal Windows est une application Windows Presentation Foundation (WPF) en C#, qui permet de tester facilement les interactions avec votre bot avant de créer une application cliente personnalisée.

L’outil peut accepter un ID d’application Commandes personnalisées. Il vous permet de tester l’achèvement de votre tâche ou le scénario de commande et de contrôle hébergé sur le service Commandes personnalisées.

Pour configurer le client, extrayez le [client Assistant vocal Windows](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/tree/master/clients/csharp-wpf).

> [!div class="mx-imgBorder"]
> ![Création d’un profil WVAC](media/custom-commands/conversation.png)

## <a name="test-programatically-with-the-cognitive-services-voice-assistant-test-tool"></a>Tester par programmation avec l’outil de test Assistant vocal Cognitive Services

L’outil de test Assistant vocal est une application console C# .NET Core configurable pour les tests de régression fonctionnelle de bout en bout de votre Assistant vocal Microsoft. 

L’outil peut s’exécuter manuellement en tant que commande de console ou être automatisé dans le cadre d’un pipeline Azure DevOps CI/CD pour empêcher les régressions dans votre bot.

Pour apprendre à configurer l’outil, consultez [Outil de test Assistant vocal](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/tree/main/clients/csharp-dotnet-core/voice-assistant-test).

## <a name="test-with-speech-sdk-enabled-client-applications"></a>Test avec les applications clientes compatibles avec le kit de développement logiciel (SDK) Speech

Le kit de développement logiciel (SDK) Speech expose de nombreuses fonctionnalités du service Speech pour vous permettre de développer des applications à reconnaissance vocale. Il est disponible dans de nombreux langages de programmation sur la plupart des plateformes.

Pour configurer une application cliente plateforme Universal Windows Platform (UWP) avec le kit de développement logiciel (SDK) Speech et l’intégrer à votre application Commandes personnalisées :  
- [Procédure : Intégration à une application cliente à l’aide du SDK Speech](./how-to-custom-commands-setup-speech-sdk.md)
- [Procédure : Envoyer une activité à l’application cliente](./how-to-custom-commands-send-activity-to-client.md)
- [Procédure : Configurer des points de terminaison Web](./how-to-custom-commands-setup-web-endpoints.md)

Pour d’autres plateformes et langages de programmation :
- [Langages de programmation du SDK Speech, plateformes, capacités de scénario](./speech-sdk.md)
- [Exemples de code d’assistant vocal](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant)

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Voir des exemples sur GitHub](https://aka.ms/speech/cc-samples)
