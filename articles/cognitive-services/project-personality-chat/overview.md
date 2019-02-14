---
title: Qu’est-ce que Project Personality Chat ?
titlesuffix: Azure Cognitive Services
description: Cet article présente une vue d’ensemble d’Azure Project Personality Chat, une API basée sur le cloud qui permet d’améliorer les fonctionnalités de conversation de votre bot.
services: cognitive-services
author: noellelacharite
manager: nitinme
ms.service: cognitive-services
ms.subservice: personality-chat
ms.topic: overview
ms.date: 05/07/2018
ms.author: nolachar
comment: As a bot developer, I want my bot to be able to handle small talk in a consistent tone so that my bot appears more complete and conversational.
ms.openlocfilehash: c7f7a8c65717acd5a19e92b7e0437dc4b8628909
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55867084"
---
# <a name="what-is-project-personality-chat"></a>Qu’est-ce que Project Personality Chat ?

Project Personality Chat accroît les capacités conversationnelles de votre bot en gérant les conversations anodines en fonction d’une personnalité distincte, choisie. Personality Chat utilise des classifieurs d’intention pour identifier les intentions dans des discussions courantes, et générer des réponses cohérentes par rapport à une personnalité. Selon les scores d’intention et de confiance, le bot choisit la meilleure réponse à partir d’une base rédactionnelle organisée, ou génère une réponse en temps réel au moyen de réseaux neuronaux profonds (DNN). Vous pouvez choisir parmi trois personnages par défaut. Le modèle de personnage retourne les réponses qui sont les plus proches de la personnalité choisie.

Un ensemble de contenus rédactionnels personnalisables pour les requêtes de conversations courantes est disponible. Il est facilement intégrable au kit SDK Microsoft Bot Framework. Ce kit SDK vous fait gagner du temps et faire des économies en vous évitant de rédiger ces réponses intégralement.

## <a name="getting-started-with-project-personality-chat"></a>Bien démarrer avec Project Personality Chat

Vous pouvez visiter la page des labos Project Personality Chat, bavarder avec la démonstration disponible et demander un accès en avant-première dès que le service est disponible.
Aujourd’hui, vous pouvez également intégrer à vos bots la bibliothèque uniquement rédactionnelle et personnalisable par le biais du kit SDK Microsoft Bot Framework. <br>
[Exemples : Intégrer Personality Chat à un bot](https://github.com/Microsoft/BotBuilder-PersonalityChat/) <br>
[Essayer la bibliothèque Personality Chat](https://github.com/Microsoft/BotBuilder-PersonalityChat/tree/master/CSharp)

## <a name="generating-responses-using-neural-networks"></a>Génération de réponses au moyen de réseaux neuronaux

Personality Chat utilise des modèles d’apprentissage profond (deep learning) pour apprendre des trames de conversation générale et générer des réponses. Le modèle de génération de réponse est un réseau de neurones récurrents (RNN). Ce modèle est entraîné avec des millions de conversations, à partir desquelles il découvre et retient des modèles présents dans les relations humaines. À l’aide des modèles de variantes syntaxiques appris, de nouvelles requêtes sont formées, et des réponses sont générées. Lors de la génération d’une nouvelle réponse, le modèle effectue une recherche dans un « vocabulaire écrit » de mots, et récupère les n-meilleurs mots principaux pour constituer la réponse. S’appuyant sur la recherche en faisceau, il poursuit sa recherche des n-meilleurs mots concomitants pour chacun des premiers mots générés. Une réponse est considérée comme complète lorsque le modèle choisit le mot de « fin de phrase » (EOS). Lorsqu’il a toutes les réponses, il choisit les n-meilleures réponses en fonction du score de probabilité obtenu pour la réponse complète.

Le modèle apprend à générer des solutions appropriées par rapport au contexte, et présentant la bonne « structure ». Par exemple, une question telle que « Voulez-vous discuter maintenant ? » en dit assez long sur la structure d’une réponse plausible : celle-ci commencera probablement par une quelconque paraphrase de « oui » ou de « non », et le pronom vraisemblablement utilisé sera « je ». Une réponse « non » est plus susceptible d’inclure une justification polie, et ainsi de suite.

Le système tente d’apprendre un modèle de langage servant de structure de base à la conversation. Cette structure doit permettre aux réponses d’être influencées en partie par des contraintes extérieures, telles que des sujets de conversation, une personnalité, etc.  Ces contraintes étant acquises à partir de modèles larges, elles sont adaptées (sans être limitées) à l’application, à des discussions anodines.

![Modèle de séquence à séquence pour la génération de réponse](./media/overview/sequence-to-sequence-model.png)

## <a name="personality-modeling"></a>Modélisation de personnalité

 Avec un modèle conversationnel piloté par des données, la production systématique d’une « personnalité » cohérente relève du défi. Le PERSONNAGE se définit comme étant le caractère qui s’est forgé au cours d’interactions conversationnelles. Un personnage peut être considéré comme élaboré à partir d’éléments propres à l’identité, au comportement du langage et au style d’interaction. Dans la version actuelle de Personality Chat, l’importance est donnée au comportement du langage et au style d’interaction.

Ce modèle représente chaque interlocuteur individuel comme un vecteur ou une incorporation. Il encode les informations à partir de l’intervenant qui influence le contenu et le style de ses réponses. Le modèle apprend ainsi les représentations d’interlocuteurs en fonction du contenu conversationnel donné par différents intervenants. Les interlocuteurs dont les réponses sont similaires ont tendance à avoir des incorporations semblables ; ils occupent des positions voisines dans l’espace vectoriel. Ainsi, les données d’apprentissage des interlocuteurs proches dans un espace vectoriel permettent d’améliorer les capacités de généralisation du modèle d’interlocuteur. Ce modèle regroupe efficacement les intervenants qui montrent des représentations similaires dans l’espace vectoriel, afin de former une entité de personnage, dotée d’un « ID de personnage ».

Pour les personnages par défaut, des attributs et des réponses organisées sont utilisés afin de trouver le regroupement d’interlocuteurs le plus concordant. Cette entité est ensuite choisie en tant qu’ID de personnage pour chacune des personnalités par défaut. Une personnalisation constante peut exister pour tout type de personnalité en prenant un ensemble de réponses de bot/de marque. Les conversations sont ensuite construites et émulées si fidèlement que le personnage de l’individu adopte le comportement de la réponse linguistique ainsi que d’autres caractéristiques principales.

![Modélisation de personnage à l’aide de regroupements d’interlocuteurs](./media/overview/persona-modeling.png)

## <a name="small-talk-intent-understanding"></a>Présentation de l’intention dans les conversations anodines

Personality Chat dispose de classifieurs d’intention pour garantir une réponse aux intentions présentes dans les conversations courantes. Ces classifieurs n’interfèrent pas dans les tâches ni dans les demandes d’informations. Un classifieur de conversations évolué détermine si la requête relève d’une intention de discuter ou de bavarder. Il prend sa décision en utilisant des classifieurs sémantiques et lexicaux, et en combinant leurs scores. Ces intentions sont entraînées avec des données conversationnelles (des échantillons d’intentions positives) et de requêtes de recherche/de tâches (des échantillons d’intentions négatives).

D’autres classifieurs d’intentions mineures sont utilisés pour identifier les sous-classes de conversations anodines et limiter les types de conversations auxquelles le service répond, par exemple : les messages d’accueil, les compliments, les opinions, etc. Ces classifieurs deep learning, en s’appuyant sur le modèle CDSSM (Convolutional Deep Structure Semantic Model), convertissent toutes les requêtes en vecteurs. Pour les intentions mineures, ils sont entraînés avec des dizaines de milliers de requêtes organisées. Le classifieur associe ensuite une requête aux classes d’intentions identifiées existantes en recherchant la meilleure concordance dans l’espace vectoriel.

Un certain nombre de contrôles ont été mis en place pour aider à prévenir des réponses défavorables, en s’appuyant sur les connaissances d’agents intelligents tels que Zo. Par défaut, Project Personality Chat est configuré pour réagir uniquement aux intentions d’utilisateurs reconnus. Vous pouvez tester Project Personality Chat pour voir s’il est adapté à votre situation. Vos commentaires sont les bienvenus si vous constatez que quelque chose nécessite une formation supplémentaire.
