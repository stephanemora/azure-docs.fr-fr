---
title: Forum aux questions sur la reconnaissance vocale de ligne directe
titleSuffix: Azure Cognitive Services
description: Obtenez des réponses aux questions plus populaires sur voix en premier les assistants virtuels en utilisant le canal Direct vocale de ligne.
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: travisw
ms.openlocfilehash: 16e4bcbb1514cfd5bbddc22b663d636292095231
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65025895"
---
# <a name="voice-first-virtual-assistants-preview-frequently-asked-questions"></a>Assistants virtuels de voix en premier aperçu : Questions fréquentes (FAQ)

Si vous ne trouvez des réponses à vos questions dans ce document, consultez [autres options de support](support.md).

## <a name="general"></a>Généralités

**Q : Quelle est la reconnaissance vocale de ligne directe ?**

**R :** Le `SpeechBotConnector` le Speech SDK met à votre disposition bidirectionnelle, la communication asynchrone avec les robots qui sont connectés au canal Direct ligne vocale sur l’infrastructure de robot. Ce canal fournit un accès coordonné à reconnaissance vocale et synthèse vocale à partir des Services de reconnaissance vocale Azure qui permettent des robots devenir entièrement la voix dans, vocal out conversationnelles expériences. Avec la prise en charge pour les mots de la mise en éveil et réactiver la vérification de Word, cette solution permet de créer des assistants virtuels vocal en premier hautement personnalisables pour votre marque ou d’un produit.

**Q : Comment faire pour démarrer ?**

**R :** Le meilleur moyen pour commencer création d’un assistant virtuel vocal en premier est de commencer par [création d’un robot de Bot Framework base](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0). Ensuite, connectez votre robot à le [canal de reconnaissance vocale de ligne directe](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech.md).

## <a name="debugging"></a>Débogage

**Q : Je reçois une erreur 401 lors de la connexion et rien ne fonctionne. Je sais que ma clé d’abonnement de reconnaissance vocale est valide. Que se passe-t-il ?**

**R :** Dans la version préliminaire, vocale de ligne directe présente des limitations très spécifiques sur l’abonnement utilisé. Vérifiez que vous utilisez le **vocale** ressource (Microsoft.CognitiveServicesSpeechServices, « Vocal ») et *pas* le **Cognitive Services** (de ressources Microsoft.CognitiveServicesAllInOne, « Tous les Services Cognitive »). En outre, notez que seul le **westus2** région actuellement prend en charge.

![Corrigez l’abonnement pour la reconnaissance vocale par ligne directe](media/voice-first-virtual-assistants/faq-supported-subscription.png "exemple d’un abonnement de reconnaissance vocale compatible")

**Q : J’obtiens texte de la reconnaissance à partir de la reconnaissance vocale de ligne directe, mais je vois une erreur « 1011 » et rien à partir de mon bot. Pourquoi ?**

**R :** Cette erreur indique un problème de communication entre le robot et vocale de ligne directe. Vérifiez que vous avez [connecté le canal Direct ligne vocale](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech.md), [ajouté la prise en charge du protocole de diffusion en continu](https://aka.ms/botframework/addstreamingprotocolsupport) et votre bot (avec la prise en charge de WebSocket connexe), puis vérifiez que votre robot répond aux entrant demandes à partir du canal.

**Q : Cela ne fonctionne toujours pas et/ou j’obtiens une erreur différente lorsque vous utilisez un SpeechBotConnector et il n’est pas clair que dois-je faire. Ce que *doit* faire ?**

**R :** Journalisation basé sur le fichier fournit beaucoup plus de détails et peut accélérer les demandes de support. Pour ce faire, consultez [comment utiliser la journalisation de fichier](how-to-use-logging.md).

## <a name="next-steps"></a>Étapes suivantes

* [Dépannage](troubleshooting.md)
* [Notes de publication](releasenotes.md)
