---
title: Questions fréquentes sur les assistants vocaux
titleSuffix: Azure Cognitive Services
description: Obtenez des réponses aux questions les plus récurrentes sur les assistants vocaux en utilisant les Commandes personnalisées ou le canal Direct Line Speech.
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: travisw
ms.openlocfilehash: 511eb12df511fd037fc0b5bec701c0cc5c29bad2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102617775"
---
# <a name="voice-assistants-frequently-asked-questions"></a>Questions fréquentes sur les assistants vocaux

Si vous ne trouvez pas de réponses à vos questions dans ce document, consultez les [autres options de support](../cognitive-services-support-options.md?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext%253fcontext%253d%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext).

## <a name="general"></a>Général

**Q : Qu’est-ce qu’un assistant vocal ?**

**R :** Comme Cortana, un assistant vocal est une solution qui écoute les énoncés prononcés par un utilisateur, analyse le contenu de ces énoncés à la recherche de significations, effectue une ou plusieurs actions en réponse à l’intention de l’énoncé, puis fournit une réponse à l’utilisateur, comprenant souvent un composant parlé. Il s’agit d’une expérience de type « entrée et sortie de voix » permettant d’interagir avec un système. Les concepteurs d’assistants vocaux créent une application sur l’appareil à l’aide du `DialogServiceConnector` dans le SDK Speech pour communiquer avec un assistant créé à l’aide de la solution [Commandes personnalisées](custom-commands.md) ou du canal [Direct Line Speech](direct-line-speech.md) du Bot Framework. Ces assistants peuvent utiliser des mots clés, une reconnaissance vocale et une voix personnalisés pour offrir une expérience adaptée à votre marque ou produit.

**Q : Dois-je utiliser la solution Commandes personnalisées ou Direct Line Speech ? Quelle est la différence ?**

**R :** La solution [Commandes personnalisées](custom-commands.md) est un ensemble d’outils moins complexe permettant de créer et d’héberger facilement un assistant adapté aux scénarios d’achèvement des tâches. [Direct Line Speech](direct-line-speech.md) fournit des fonctionnalités plus riches et plus sophistiquées qui peuvent permettre des scénarios de conversation robustes. Pour plus d’informations, consultez la [comparaison des solutions d’assistant](voice-assistants.md#choosing-an-assistant-solution).

**Q : Comment faire pour démarrer ?**

**R :** La meilleure façon de commencer à créer une application de Commandes personnalisées (préversion) ou un bot Bot Framework de base.

- [Créer une application Commandes personnalisées (préversion)](./quickstart-custom-commands-application.md)
- [Créer un bot Bot Framework de base](/azure/bot-service/bot-builder-tutorial-basic-deploy)
- [Connecter un bot au canal Direct Line Speech](/azure/bot-service/bot-service-channel-connect-directlinespeech)

## <a name="debugging"></a>Débogage

**Q : Où est le secret de mon canal ?**

**R :** Si vous avez utilisé la préversion de Direct Line Speech ou que vous lisez la documentation associée, vous pouvez vous attendre à trouver une clé secrète sur la page d’inscription du canal Direct Line Speech. La méthode `FromBotSecret` de la fabrique `DialogServiceConfig` v1.7 du SDK Speech attend également cette valeur.

La dernière version de Direct Line Speech simplifie le processus de contact de votre bot à partir d’un appareil. Dans la page d’inscription du canal, la liste déroulante située en haut associe votre inscription du canal Direct Line Speech à une ressource de reconnaissance vocale. Une fois l’association effectuée, le SDK Speech v1.8 inclut une méthode de fabrique `BotFrameworkConfig::FromSubscription` qui configure un `DialogServiceConnector` pour contacter le bot que vous avez associé à votre abonnement.

Si vous migrez néanmoins votre application cliente de v1.7 vers v1.8, `DialogServiceConfig::FromBotSecret` peut continuer à utiliser une valeur non vide et non null pour son paramètre de secret du canal, par exemple le secret précédent que vous avez utilisé. Elle sera tout simplement ignorée lors de l’utilisation d’un abonnement de reconnaissance vocale associé à une inscription de canal plus récente. Notez que la valeur _doit_ être non null et non vide, car elle est vérifiée sur l’appareil avant que l’association côté service soit pertinente.

Pour obtenir un guide plus détaillé, consultez la [section du tutoriel](tutorial-voice-enable-your-bot-speech-sdk.md#register-the-direct-line-speech-channel) qui vous guide dans l’inscription du canal.

**Q : J’obtiens une erreur 401 lors de la connexion et rien ne fonctionne. Je sais que ma clé d’abonnement Speech est valide. Que se passe-t-il ?**

**R :** Lors de la gestion de votre abonnement sur le Portail Azure, vérifiez que vous utilisez la ressource **Speech** (Microsoft.CognitiveServicesSpeechServices, « Speech ») et _pas_ la ressource **Cognitive Services** (Microsoft.CognitiveServicesAllInOne, « Tous les services Cognitive Services »). Vérifiez également [la prise en charge de la région du service de reconnaissance vocale pour les assistants vocaux](regions.md#voice-assistants).

![abonnement approprié pour Direct Line Speech](media/voice-assistants/faq-supported-subscription.png "exemple d’abonnement vocal compatible")

**Q : J’obtiens le texte de la reconnaissance à partir de mon `DialogServiceConnector`, mais je vois une erreur « 1011 » et rien de mon bot. Pourquoi ?**

**R :** Cette erreur indique un problème de communication entre votre assistant et le service d’assistant vocal.

- Pour la solution Commandes personnalisées, assurez-vous que votre application Commandes personnalisées est publiée
- Pour Direct Line Speech, vérifiez que vous avez [connecté votre bot au canal Direct Line Speech](/azure/bot-service/bot-service-channel-connect-directlinespeech), [ajouté la prise en charge du protocole de streaming](/azure/bot-service/directline-speech-bot) à votre bot (avec la prise en charge WebSocket connexe), puis vérifiez que votre bot répond aux demandes entrantes provenant du canal.

**Q : Ce code ne fonctionne toujours pas et/ou j’obtiens une erreur différente lorsque j’utilise un `DialogServiceConnector`. Que dois-je faire ?**

**R :** La journalisation basée sur fichier fournit beaucoup plus de détails et peut accélérer le traitement des demandes de support. Pour activer cette fonctionnalité, consultez [comment utiliser la journalisation basée sur fichier](how-to-use-logging.md).

## <a name="next-steps"></a>Étapes suivantes

- [Dépannage](troubleshooting.md)
- [Notes de publication](releasenotes.md)