---
title: Qu’est-ce que Personalizer ?
titleSuffix: Azure Cognitive Services
description: Personalizer est un service d’API cloud qui vous permet de choisir la meilleure expérience à montrer à vos utilisateurs, en apprenant de leur comportement en temps réel.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: overview
ms.date: 10/23/2019
ms.author: diberry
ms.openlocfilehash: b5d38ffeda3600fd90c4ee84acdd29ed599886ae
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74707945"
---
# <a name="what-is-personalizer"></a>Qu’est-ce que Personalizer ?

Azure Personalizer est un service d’API cloud qui permet à votre application de choisir la meilleure expérience à montrer à vos utilisateurs, en apprenant de leur comportement collectif en temps réel.

* Fournissez des informations sur vos utilisateurs et sur le contenu, et recevez l’action classée en premier à montrer à vos utilisateurs. 
* Il n’est pas nécessaire de nettoyer et d’étiqueter les données avant d’utiliser Personalizer.
* Fournissez un feedback à Personalizer au moment où cela vous convient. 
* Consultez une analytique en temps réel. 

Voir une démonstration du [fonctionnement de Personalizer](https://personalizercontentdemo.azurewebsites.net/)

## <a name="how-does-personalizer-work"></a>Fonctionnement de Personalizer

Personalizer utilise des modèles Machine Learning pour découvrir quelle action classer en premier dans un contexte donné. Votre application cliente fournit une liste d’actions possibles, avec des informations sur celles-ci, et des informations sur le contexte, qui peuvent inclure des informations sur l’utilisateur, l’appareil, etc. Personalizer détermine l’action à effectuer. Une fois que votre application cliente utilise l’action choisie, elle fournit un feedback à Personalizer sous la forme d’un score de récompense. Une fois le feedback reçu, Personalizer met automatiquement à jour son propre modèle utilisé pour les classements ultérieurs. Au fil du temps, Personalizer entraîne un modèle qui peut suggérer la meilleure action à choisir dans chaque contexte en fonction de ses caractéristiques.

## <a name="how-do-i-use-the-personalizer"></a>Comment utiliser Personalizer ?

![Utilisation de Personalizer pour choisir quelle vidéo montrer à un utilisateur](media/what-is-personalizer/personalizer-example-highlevel.png)

1. Choisissez une expérience dans votre application à personnaliser.
1. Créez et configurez une instance du service de personnalisation dans le portail Azure. Chaque instance est une boucle Personalizer.
1. Utilisez l’[API Rank](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Rank) pour appeler Personalizer avec des informations (_fonctionnalités_) sur vos utilisateurs et le contenu (_actions_). Vous n’avez pas besoin de fournir des données nettoyées et étiquetées avant d’utiliser Personalizer. Les API peuvent être appelées directement ou à l’aide de Kits de développement logiciel (SDK) disponibles pour différents langages de programmation.
1. Dans l’application cliente, montrez à l’utilisateur l’action sélectionnée par Personalizer.
1. Utilisez l’[API Reward](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Reward) pour fournir à Personalizer un retour d’information indiquant si l’utilisateur a sélectionné l’action de Personalizer. Il s’agit d’un _[score de récompense](concept-rewards.md)_ .
1. Visualisez l’analytique dans le portail Azure pour évaluer comment le système fonctionne et comment vos données aident à la personnalisation.

## <a name="where-can-i-use-personalizer"></a>Où puis-je utiliser Personalizer ?

Par exemple, votre application cliente peut ajouter Personalizer pour :

* Personnaliser le choix de l’article qui est mis en évidence sur un site web d’info.    
* Optimiser le positionnement des publicités sur un site web.
* Afficher un « élément recommandé » personnalisé sur un site web de vente en ligne.
* Suggérer des éléments d’interface utilisateur, comme des filtres à appliquer à une photo spécifique.
* Choisir la réponse d’un chatbot pour clarifier l’intention de l’utilisateur ou suggérer une action.
* Hiérarchiser les suggestions de ce qu’un utilisateur devrait faire à l’étape suivante d’un processus métier.

Personalizer n’est pas un service permettant de conserver et de gérer les informations de profil utilisateur, ou de consigner les préférences ou l’historique des utilisateurs individuels. Personalizer apprend des caractéristiques de chaque interaction présente dans l’action d’un contexte survenant dans un modèle unique, celui-ci pouvant obtenir des récompenses maximales en cas de caractéristiques similaires. 

## <a name="personalization-for-developers"></a>Personnalisation pour les développeurs

Le service Personalizer a deux API :

* *Rank* : Utilisez l’API Rank pour déterminer l’_action_ à afficher dans le _contexte_ actuel. Les actions sont envoyées sous la forme d’un tableau d’objets JSON, avec un ID et des informations (_fonctionnalités_) à propos de chacun ; le contexte est envoyé sous la forme d’un autre objet JSON. L’API retourne l’ID d’action (actionId) que votre application doit afficher à l’utilisateur.
* *Récompense* : Une fois que votre utilisateur a interagi avec votre application, vous mesurez la réussite de la personnalisation entre 0 et 1 et l’envoyez en tant que [score de récompense](concept-rewards.md). 

![Séquence de base des événements pour la personnalisation](media/what-is-personalizer/personalization-intro.png)

## <a name="next-steps"></a>Étapes suivantes

* [Nouveautés de Personalizer](whats-new.md)
* [Fonctionnement de Personalizer](how-personalizer-works.md)
* [Qu’est-ce que l’apprentissage par renforcement ?](concepts-reinforcement-learning.md)
* [En savoir plus sur les fonctionnalités et les actions de la demande de classement](concepts-features.md)
* [En savoir plus sur le calcul du score de la demande de récompense](concept-rewards.md)
* [Utiliser la démonstration interactive](https://personalizationdemo.azurewebsites.net/)
