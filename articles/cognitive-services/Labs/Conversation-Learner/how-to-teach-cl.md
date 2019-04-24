---
title: Formation avec Conversation Learner - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Apprenez à former avec Conversation Learner.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 8c55bb27ce5a413c5ceae22371ad61a5acf47281
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60322887"
---
# <a name="how-to-teach-with-conversation-learner"></a>Formation avec Conversation Learner 

Ce document explique de quels signaux Conversation Learner tient compte et décrit comment il apprend.  

La formation peut être divisée en deux étapes distinctes : extraction d’entité et sélection d’action.

## <a name="entity-extraction"></a>Extraction d’entité

En coulisses, l’Apprenant de conversation utilise [LUIS](https://www.luis.ai) pour l’extraction d’entité.  Si vous êtes familiarisé avec LUIS, cette expérience s’applique à l’extraction d’entité dans l’Apprenant de conversation.

Les modèles d’extraction d’entité tiennent compte du *contenu* et du *contexte* dans l’énoncé d’un utilisateur.  Par exemple, si le mot « Seattle » a été étiqueté comme une ville dans un énoncé tel que « Quel temps fait-il à Seattle », l’extraction d’entité est capable de reconnaître ce même contenu (« Seattle ») en tant que ville dans un autre énoncé tel que « Population de Seattle », même si les énoncés sont très différents.  À l’inverse, si « Francis » a été reconnu en tant que nom dans « Planification d’une réunion avec Francis », un nouveau nom peut être reconnu dans un contexte similaires, tel qu’« Organiser une réunion avec Robin ».  Le Machine Learning déduit le moment auquel s’occuper du contenu, du contexte ou des deux, sur la base d’exemples d’apprentissage.

À l’heure actuelle, l’extraction d’entité tient uniquement compte du contenu de l’énoncé actuel.  Contrairement à la sélection d’une action (ci-dessous), elle ne tient pas compte de l’historique de la boîte de dialogue, par exemple les fois précédentes du système, les fois précédentes des utilisateurs, ou les entités reconnues précédemment.  Par conséquent, le comportement d’extraction d’entité est « partagé » entre tous les énoncés.  Par exemple, si l’énoncé d’utilisateur « I want Apple » (Je veux une pomme) a étiqueté « Apple » (pomme) en tant que type d’entité « Fruit » dans un énoncé d’utilisateur, le modèle d’extraction d’entité s’attendra à ce que cet énoncé (« I want Apple ») étiquette toujours « Apple » comme un « Fruit ».

Si l’extraction d’entité ne se comporte pas comme prévu, il est possible d’y remédier :

- La première chose à faire est d’essayer d’ajouter des exemples d’apprentissage, en particulier des exemples qui révèlent un contexte d’entité typique (mots environnants) ou des exceptions.
- Envisagez d’ajouter une propriété « Entité attendue » à une action, le cas échéant.  Pour plus d’informations, consultez le tutoriel sur les entités attendues.
- Bien qu’il soit possible d’ajouter un traitement manuel à `EntityExtractionCallback` pour extraire des entités avec du code, cette approche est la moins recommandée, car elle ne pourra pas bénéficier des améliorations du Machine Learning au fur et à mesure que votre système arrivera à maturité.

## <a name="action-selection"></a>Sélection d'action

La sélection d’action utilise un réseau neuronal récurrent, qui part de l’historique des conversations.  Par conséquent, la sélection d’action est un processus avec état qui tient compte des précédents énoncés d’utilisateurs, des valeurs d’entité et des énoncés du système.  

Certains signaux sont naturellement préférés par le processus d’apprentissage.  En d’autres termes, si l’Apprenant de Conversation peut expliquer une décision de sélection d’action avec des signaux « préférés », il le fera ; dans le cas contraire, il utilisera des signaux « moins préférés ».

Voici un tableau indiquant tous les signaux de Conversation Learner et ceux utilisés par la sélection d’action.  Notez que l’ordre des mots dans les énoncés de l’utilisateur est ignoré.

Signal | Préférence (1 = préféré) | Notes
--- | --- | --- 
Action du système la fois précédente | 1 | 
Entités présentes actuellement | 1 | 
S’il s’agit de la première fois | 1 |
Correspondance exacte des mots dans l’énoncé actuel de l’utilisateur actuel | 2 | 
Mots similaires dans l’énoncé actuel de l’utilisateur | 3 | 
Actions du système avant la fois précédente | 4 |
Entités présentes les fois précédentes | 4 | 
Énoncés précédents de l’utilisateur | 5. | 

> [!NOTE]
> La sélection d’action ne prend pas le contenu des actions du système (texte, contenu de carte, ou bien nom ou comportement d’API), mais uniquement l’identité de l’action du système.  Par conséquent, la modification du contenu d’une action ne modifiera pas le comportement du modèle de sélection d’action.
>
> Par ailleurs, les contenu/valeurs d’entités proprement dits ne sont pas utilisés, mais uniquement leur présence/absence.

Si la sélection d'action ne se comporte pas comme prévu, il est possible d’y remédier :

- Ajoutez plusieurs boîtes de dialogue de formation, notamment celles qui illustrent les signaux auxquels la sélection d’action devrait faire attention.  Par exemple, si la sélection d’action doit préférer un signal à un autre, donnez des exemples qui montrent le signal préféré dans le même état et les autres signaux variables.  Certaines séquences d’apprentissage peuvent nécessiter un certain nombre de boîtes de dialogue.
- Ajoutez des entités « Requises » et « Disqualifiantes » à des définitions d’action.  Ceci limite la disponibilité des actions et peut être utile pour exprimer des règles d’entreprise et certains modèles de bon sens. 

## <a name="updates-to-models"></a>Mises à jour de modèles

À chaque fois que vous ajoutez ou modifiez une entité, une action ou un dialogue de formation à l’IU, ceci génère une requête de nouvelle formation du modèle d’extraction d’entité et du modèle de sélection d’action.  Cette requête est placée dans une file d’attente et le nouvel apprentissage s’effectue de façon asynchrone.  Lorsqu’un nouveau modèle est disponible, il est immédiatement utilisé pour l’extraction d’entités et pour la sélection d’actions.  Ce processus de nouvel apprentissage prend généralement environ 5 secondes, mais cela peut être plus long si le modèle est complexe, ou si la charge du service d’apprentissage est élevée.

Étant donné que l’apprentissage est effectué de façon asynchrone, il est possible que les modifications apportées par vos soins ne soient pas répercutées immédiatement.  Cela peut être le cas si l’extraction d’entité ou la sélection de l’action ne se comportent pas comme prévu suite aux modifications effectuées dans les 5 à 10 dernières secondes.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Limites et valeurs par défaut](./cl-values-and-boundaries.md)
