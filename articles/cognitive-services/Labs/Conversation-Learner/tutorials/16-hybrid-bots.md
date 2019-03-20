---
title: Guide pratique pour utiliser Conversation Learner avec d’autres technologies de génération de bots – Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Découvrez comment utiliser Conversation Learner avec d’autres technologies de génération de bots.
services: cognitive-services
author: mattm
manager: larsliden
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 07/13/2018
ms.author: v-jaswel
ms.openlocfilehash: b90fc42aa56bfc813ec464670336dea75cff0f0e
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/18/2019
ms.locfileid: "58117436"
---
# <a name="how-to-use-conversation-learner-with-other-bot-building-technologies"></a>Guide pratique pour utiliser Conversation Learner avec d’autres technologies de génération de bots

Ce tutoriel explique comment utiliser Conversation Learner avec d’autres technologies de génération de bots et comment faire en sorte qu’elles partagent la mémoire (ou l’état). 

## <a name="video"></a>Vidéo

[![Aperçu du tutoriel sur les Bots hybrides](https://aka.ms/cl_Tutorial_v3_Hybrid_Applications_Preview)](https://aka.ms/cl_Tutorial_v3_Hybrid_Applications)

## <a name="requirements"></a>Configuration requise
Ce tutoriel requiert l’utilisation de l’émulateur bot pour créer des boîtes de dialogue de journal, pas l’interface utilisateur web de la boîte de dialogue de journal. Pour plus d’informations sur la configuration de l’émulateur Bot Framework, cliquez [ici](https://docs.microsoft.com/azure/bot-service/bot-service-debug-emulator?view=azure-bot-service-4.0). 

Pour les besoins de ce tutoriel, le bot du tutoriel hybride doit être en cours d’exécution :

    npm run tutorial-hybrid

## <a name="details"></a>Détails

Bien que Conversation Learner soit aux commandes, tous les états liés à la session Conversation Learner doivent être stockés dans le gestionnaire de mémoire de Conversation Learner. En effet, le Machine Learning utilise l’état pour déterminer comment mener la conversation. L’état externe peut être transmis à Conversation Learner dans OnSessionStartCallback, qui est appelé au début de la session. L’état interne peut être retourné par OnSessionEndCallback lorsque la session se termine.

On pourrait presque considérer Conversation Learner comme un appel de fonction qui prend un état initial et retourne des valeurs.

Dans cet exemple, vous allez créer un bot hybride à l’aide de deux systèmes différents :
1. Un modèle Conversation Learner <br/>
    Utilise le modèle Conversation Learner pour déterminer l’action suivante du bot en fonction de la session active.Cette partie du bot prend une partie de l’état initial `isOpen` (qui indique si un magasin est ouvert ou fermé) et retourne une autre partie de l’état `purchaseItem` (le nom d’un article acheté par l’utilisateur).

2. La correspondance de texte <br />
    Cherche simplement des chaînes spécifiques dans le texte entrant et répond.Cette partie du bot gère les autres mécanismes de stockage des bots ; elle est responsable du démarrage de la session CL. Plus précisément, elle gère trois variables : `usingConversationLearner`, `storeIsOpen` et `purchaseItem`.

Commençons par regarder le modèle utilisé dans cette démonstration.

### <a name="open-the-demo"></a>Ouvrir la démonstration

Dans l’interface utilisateur web, cliquez sur « Import Tutorials » (« Importer des tutoriels »), puis sélectionnez le modèle nommé « Tutorial-16-HybridBot ».

## <a name="entities"></a>Entités

Ouvrez la page d’entités. Il y en a deux : `isOpen` et `purchaseItem`.

Pour comprendre comment ces entités sont utilisées, ouvrez le fichier `C:\<installedpath>\src\demos\tutorialHybrid.ts` afin d’examiner les rappels.

Comme on le voit, le code de `OnSessionStartCallback` copie la valeur de `storeIsOpen` du stockage de conversation BotBuilder comme valeur de l’entité `isOpen` afin qu’elle soit accessible à Conversation Learner. Regardez le code suivant :

![](../media/tutorial17_sessionstart.PNG)

De même, le code de `OnSessionEndCallback` (si la session a été arrêtée en raison d’une activité apprise et pas simplement d’un délai d’expiration) copie la valeur de l’entité `purchaseItem` vers le stockage BotBuilder `purchaseItem`. Regardez le code suivant :

![](../media/tutorial17_sessionend.PNG)

Examinons maintenant les actions.

## <a name="actions"></a>Actions

Vous remarquerez que le modèle comporte quatre actions.

Voici les règles prévues pour ces actions :

- Si l’entité `isOpen` est définie, le bot demandera « What would you like to buy? » et le stockera à l’emplacement `puchaseItem`.
- Si `isOpen` n’est pas définie, le bot dira « I’m sorry we’re closed » (« désolé, nous sommes fermés »).
- Les deux autres actions sont de type `END_SESSION`.
- L’action END_SESSION indique à Conversation Learner que la conversation est terminée.

### <a name="overall-bot-logic"></a>Logique globale du bot

On voit tout d’abord que, si l’indicateur `usingConversationLearner` de l’état du bot a été défini, le contrôle est transmis à Conversation Learner. Sinon, il est transféré à autre chose.  Cet exemple montre une correspondance de texte simple, mais il pourrait s’agir de n’importe quelle autre technologie de bot, notamment LUIS, QnA Maker ou même une autre instance de Conversation Learner.

Pour donner le moyen à l’utilisateur d’ouvrir et de fermer le magasin, la chaîne est comparée avec « open store » et « close store », et l’indicateur « storeIsOpen » est défini.

Il faut maintenant trouver un moyen de déclencher la transmission du contrôle au modèle Conversation Learner. En cas de correspondance avec la chaîne « shop » :
- L’indicateur `usingConversationLearner` est défini dans la mémoire du bot.
- La méthode « StartSession » du modèle Conversation Learner est appelée  pour déclencher « onSessionStartCallback » qui initialisera la valeur de l’entité `isOpen`.

Voir ci-dessous :

![](../media/tutorial17_useConversationLearner.PNG)

Par ailleurs, une correspondance de texte avec « history » présente le dernier article acheté.
Enfin, si rien d’autre n’est tapé, les commandes utilisateur disponibles s’affichent.

## <a name="train-dialog"></a>Boîte de dialogue d’apprentissage

Dans ce tutoriel, l’apprentissage du modèle a déjà été préparé.  Nous allons tester la totalité du bot pour voir dans la pratique l’effet des rappels de session de début et de fin.

## <a name="testing-the-bot"></a>Tester le bot

Contrairement aux bots du modèle Conversation Learner simple, il n’est pas possible de tester celui-ci dans l’interface utilisateur Conversation Learner, qui ne peut afficher que ce qui est géré par le modèle Conversation Learner.

### <a name="install-the-bot-framework-emulator"></a>Installez l’émulateur Bot Framework

- Accédez à [https://github.com/Microsoft/BotFramework-Emulator](https://github.com/Microsoft/BotFramework-Emulator).
- Téléchargez et installez l’émulateur.

### <a name="configure-the-emulator"></a>Configurez l’émulateur

- Ouvrez l’émulateur et vérifiez que l’URL cible le port sur lequel s’exécute le bot. C’est probablement `http://localhost:3978/api/messages`.

### <a name="test"></a>Test 

#### <a name="scenario-1-store-is-closed"></a>Scénario 1 : Le magasin est fermé
1. Entrez « shop ». Le scénario est géré par la correspondance de texte et donne le contrôle au modèle Conversation Learner.
2. Entrez « hello ».  La valeur `isOpen` n’étant pas définie, le bot dira « I’m sorry we’re closed » et mettra fin à la session.

#### <a name="scenario-2-store-is-open"></a>Scénario 2 : Le magasin est ouvert
1. Entrez « open store ».  `isOpen` devient True.
1. Entrez « shop ».
1. Entrez « hello ».  La valeur `isOpen` étant définie sur True, le bot dira « What would you like to buy? ».
1. Entrez « chair ». « chair » sera enregistré dans la mémoire CL comme étant l’entité `purchaseItem`. Le rappel de session de fin est appelé ; il copie cette valeur dans le magasin de conversation.
1. Entrez « history ».  Le bot dira « You bought chair », car c’était le dernier `purchaseItem`.

## <a name="conclusion"></a>Conclusion

Ce que vous avez appris ci-dessus devrait vous permettre de combiner Conversation Learner avec n’importe quelle autre technologie de construction de bot.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Création et suppression de branche](./17-branch-undo.md)
