---
title: Qu’est-ce que l’Apprenant de conversation ? Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Découvrez l’Apprenant de conversation et son fonctionnement.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 9cbf0e60382ef17d68aab47cf5f24ea9b8434f13
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35369581"
---
# <a name="what-is-conversation-learner"></a>Qu’est-ce que l’Apprenant de conversation ?

L’Apprenant de conversation vous permet de générer des interfaces conversationnelles qui apprennent des exemples d’interaction. 

Contrairement aux approches traditionnelles, l’Apprenant de conversation tient compte du contexte d’un dialogue de bout en bout pour améliorer les réponses et créer des expériences utilisateur plus attrayantes. L’Apprenant de conversation couvre un large éventail de cas d’usage orientés sur les tâches et applique le Machine Learning en arrière-plan pour que les bots et les agents intelligents soient moins susceptibles de frustrer les utilisateurs et d’entraîner des frais de service client supplémentaires, ainsi que pour permettre d’interagir de manière plus intuitive avec eux.

Pour commencer, le développeur entre des prototypes de dialogues qu’il souhaite imiter. À mesure que des dialogues supplémentaires sont entrés, le modèle est actualisé en permanence, et le développeur peut voir dans quelle mesure le modèle se généralise. Une fois que le modèle fonctionne bien, le bot peut être déployé pour les utilisateurs finaux. L’Apprenant de conversation enregistre les conversations avec les utilisateurs, et le développeur peut les réviser. Si des erreurs sont repérées, le développeur peut apporter une correction sur place. Le modèle est reformé et peut être utilisé immédiatement.

Cette approche réduit le codage manuel de la logique de contrôle du dialogue et permet aux propriétaires de l’entreprise ou aux experts de domaine de contribuer à une interface conversationnelle sans connaissances préalables de Machine Learning. Qu’il soit déployé en tant que bot, appareil intelligent ou agent intelligent, l’Apprenant de conversation peut rapidement répéter des compétences ou des comportements nouveaux et améliorer leur qualité. 

L’Apprenant de conversation permet aux développeurs d’accroître la vitesse de commercialisation et favorise des dialogues réussis sur de multiples canaux conversationnels autonomes de par leur propre infrastructure, ou via Microsoft Bot Framework.

Résumé et points forts :

- L’Apprenant de conversation est un premier moyen pour l’intelligence artificielle de créer des bots orientés sur les tâches.

- Il s’appuie sur un réseau neuronal récurrent de bout en bout (LSTM) et apprend directement à partir des exemples multitours de conversations. 

- Permet aux concepteurs, aux développeurs, aux utilisateurs professionnels et aux employés des centres d’appel de créer des bots et d’assurer leur maintenance. 

- Offre la possibilité d’exprimer des règles d'entreprise et du bon sens dans le code.

- Au cours des sessions d’apprentissage, le modèle de réseau neuronal est utilisé pour évaluer l’ensemble suivant d’actions attendues dans la conversation. Le développeur de bot peut alors sélectionner l’action appropriée et apprendre au réseau à fournir la bonne réponse.
 
- Une fois l’apprentissage terminé, le développeur peut utiliser les dialogues journal des interactions utilisateur pour apporter des corrections aux réponses du bot et former à nouveau le modèle. 

- Possibilité d’appeler des API tierces et spécifique à un domaine pour effectuer des tâches.

