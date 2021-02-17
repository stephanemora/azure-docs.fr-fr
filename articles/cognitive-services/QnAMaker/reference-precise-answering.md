---
title: Réponse précise à l’aide de la détection de l’étendue des réponses - QnA Maker
description: Découvrez la fonctionnalité Réponse précise disponible dans QnA Maker managé.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 11/09/2020
ms.openlocfilehash: 5dde3da693d87d537fd2177a6f12b55297b5776e
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/05/2021
ms.locfileid: "99582194"
---
# <a name="precise-answering"></a>Réponse précise

La fonctionnalité Réponse précise introduite dans QnA Maker managé (préversion) vous permet d’obtenir la réponse brève précise du meilleur passage candidat de la réponse présent dans la base de connaissances pour toute requête utilisateur. Cette fonctionnalité utilise un modèle deep learning qui, au moment de l’exécution, comprend l’intention de la requête utilisateur et détecte la réponse brève précise dans le passage de la réponse, s’il existe une réponse brève présente en tant que fait dans le passage de la réponse. 

Cette fonctionnalité est activée par défaut dans le volet de test, ce qui vous permet d’en tester le fonctionnement propre à votre scénario. Cette fonctionnalité est extrêmement précieuse pour les développeurs de contenus et les utilisateurs finaux. Maintenant, les développeurs de contenus n’ont pas besoin d’organiser manuellement des paires QnA spécifiques pour chaque fait présent dans la base de connaissances, et l’utilisateur final n’a pas besoin de parcourir l’ensemble du passage de la réponse retourné par le service pour trouver le fait réel qui répond à sa requête. 

## <a name="precise-answering-on-qna-maker-portal"></a>Réponse précise sur le portail QnA Maker

Dans le portail QnA Maker, quand vous ouvrez le volet de test, vous voyez une option qui permet d’**afficher une réponse brève** tout en haut. Cette option est sélectionnée par défaut. Quand vous entrez une requête dans le volet de test, vous voyez une réponse brève, ainsi que le passage de la réponse, s’il existe une réponse brève dans le passage de la réponse. 
 
![Volet de test activé (version managée)](../QnAMaker/media/conversational-context/test-pane-with-managed.png)

Vous pouvez désélectionner l’option **Display short answer** (Afficher une réponse brève) si vous voulez voir uniquement le passage de la réponse dans le volet de test. 

Le service retourne aussi le score de confiance de la réponse précise sous la forme d’un **score d’étendue des réponses** que vous pouvez vérifier en sélectionnant l’option **Inspecter** figurant juste en dessous de la requête dans le volet de test.

![Score d’étendue des réponses managé](../QnAMaker/media/conversational-context/managed-answer-span-score.png)

## <a name="publishing-a-qna-maker-bot"></a>Publication d’un bot QnA Maker

Quand vous publiez un bot, vous obtenez l’expérience de réponse précise par défaut dans votre application, qui vous permet de voir une réponse brève en plus du passage de la réponse. Reportez-vous à la référence d’API pour [Génération de la réponse](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerv5.0-preview.1/knowledgebase/generateanswer#answerspan) pour voir comment utiliser la réponse précise (appelée AnswerSpan) dans la réponse. L’utilisateur a la possibilité de choisir d’autres expériences en mettant à jour le modèle par le biais du service d’application Bot. 

## <a name="language-support"></a>Support multilingue

Actuellement, la fonctionnalité de réponse précise est uniquement prise en charge pour l’anglais.
