---
title: Forum Aux Questions (FAQ) sur Direct Line Speech
titleSuffix: Azure Cognitive Services
description: Obtenez des réponses aux questions plus populaires sur les assistants virtuels « voice first » en utilisant le canal Direct Line Speech.
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: travisw
ms.openlocfilehash: 8427417c9b579c7dfa21f834ce1ca77099159eb2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65072619"
---
# <a name="voice-first-virtual-assistants-preview-frequently-asked-questions"></a>Aperçu des assistants virtuels « voice-first » : Questions fréquentes (FAQ)

Si vous ne trouvez pas de réponses à vos questions dans ce document, consultez les [autres options de support](support.md).

## <a name="general"></a>Généralités

**Q : Qu’est-ce que Direct Line Speech ?**

**R :** Le `SpeechBotConnector` du SDK Speech met fournit une communication bidirectionnelle et asynchrone aux bots qui sont connectés au canal Direct Line Speech sur le Bot Framework. Ce canal fournit un accès coordonné à la reconnaissance vocale et à la synthèse vocale à partir des services Speech Azure qui permettent aux bots de se transformer en expériences de conversation vocales entrantes et sortantes. Avec la prise en charge des mots déclencheurs et la Vérification de mot déclencheur, cette solution permet de créer des assistants virtuels « voice first » hautement personnalisables pour votre marque ou produit.

**Q : Comment faire pour démarrer ?**

**R :** Le meilleur moyen pour commencer la création d’un assistant virtuel « voice first » est de commencer par la [création d’un Bot Framework de base](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0). Ensuite, connectez votre bot au [canal Direct Line Speech](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech).

## <a name="debugging"></a>Débogage

**Q : Je reçois une erreur 401 lors de la connexion et rien ne fonctionne. Je sais que ma clé d’abonnement Speech est valide. Que se passe-t-il ?**

**R :** Dans la préversion, Direct Line Speech présente des limitations très spécifiques sur l’abonnement utilisé. Vérifiez que vous utilisez la ressource **Speech** (Microsoft.CognitiveServicesSpeechServices, « Speech ») et *pas* la ressource **Cognitive Services** (Microsoft.CognitiveServicesAllInOne, « Tous les services Cognitive Services »). En outre, notez que seule la région **westus2** est actuellement prise en charge.

![abonnement correct pour direct line speech](media/voice-first-virtual-assistants/faq-supported-subscription.png "exemple d’un abonnement Speech compatible")

**Q : J’obtiens le texte de la reconnaissance à partir Direct Line Speech, mais je vois une erreur « 1011 » et rien de mon bot. Pourquoi ?**

**R :** Cette erreur indique un problème de communication entre le bot et Direct Line Speech. Vérifiez que vous avez [connecté le canal Direct Line Speech](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech), [ajouté la prise en charge du protocole de streaming](https://aka.ms/botframework/addstreamingprotocolsupport) à votre bot (avec la prise en charge WebSocket connexe), puis vérifiez que votre bot répond aux demandes entrantes provenant du canal.

**Q : Cela ne fonctionne toujours pas et/ou j’obtiens une erreur différente lorsque j’utilise SpeechBotConnector et je ne sais pas quoi faire. Que *dois-je* faire ?**

**R :** La journalisation basée sur fichier fournit beaucoup plus de détails et peut accélérer le traitement des demandes de support. Pour l’activer, consultez [comment utiliser la journalisation basée sur fichier](how-to-use-logging.md).

## <a name="next-steps"></a>Étapes suivantes

* [Dépannage](troubleshooting.md)
* [Notes de publication](releasenotes.md)
