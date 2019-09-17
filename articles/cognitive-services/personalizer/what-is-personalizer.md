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
ms.date: 09/03/2019
ms.author: diberry
ms.openlocfilehash: 3132d31e9e45718fa95c39a1b8160ea303ded25d
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/11/2019
ms.locfileid: "70883661"
---
# <a name="what-is-personalizer"></a>Qu’est-ce que Personalizer ?

Azure Personalizer est un service d’API cloud qui vous permet de choisir la meilleure expérience à montrer à vos utilisateurs, en apprenant de leur comportement en temps réel.

* Fournissez des informations sur vos utilisateurs et sur le contenu, et recevez l’action classée en premier à montrer à vos utilisateurs. 
* Il n’est pas nécessaire de nettoyer et d’étiqueter les données avant d’utiliser Personalizer.
* Fournissez un feedback à Personalizer au moment où cela vous convient. 
* Consultez une analytique en temps réel. 
* Utilisez Personalizer dans le cadre d’une démarche de science des données plus vaste pour valider des expériences existantes.

## <a name="how-does-personalizer-work"></a>Fonctionnement de Personalizer

Personalizer utilise des modèles Machine Learning pour découvrir quelle action classer en premier dans un contexte donné. Votre application cliente fournit une liste d’actions possibles, avec des informations sur celles-ci, et des informations sur le contexte, qui peuvent inclure des informations sur l’utilisateur, l’appareil, etc. Personalizer détermine l’action à effectuer. Une fois que votre application cliente utilise l’action choisie, elle fournit un feedback à Personalizer sous la forme d’un score de récompense. Une fois le feedback reçu, Personalizer met automatiquement à jour son propre modèle utilisé pour les classements ultérieurs. Au fil du temps, Personalizer entraîne un modèle qui peut suggérer la meilleure action à choisir dans chaque contexte en fonction de ses caractéristiques.

## <a name="how-do-i-use-the-personalizer"></a>Comment utiliser Personalizer ?

![Utilisation de Personalizer pour choisir quelle vidéo montrer à un utilisateur](media/what-is-personalizer/personalizer-example-highlevel.png)

1. Choisissez une expérience dans votre application à personnaliser.
1. Créez et configurez une instance du service de personnalisation dans le portail Azure. Chaque instance est une boucle Personalizer.
1. Utilisez le SDK pour appeler Personalizer avec des informations (_caractéristiques_) sur vos utilisateurs et le contenu (_actions_). Vous n’avez pas besoin de fournir des données nettoyées et étiquetées avant d’utiliser Personalizer. 
1. Dans l’application cliente, montrez à l’utilisateur l’action sélectionnée par Personalizer.
1. Utilisez le SDK pour fournir un feedback à Personalizer indiquant si l’utilisateur a sélectionné l’action de Personalizer. Il s’agit d’un _score de récompense_, généralement compris entre -1 et 1.
1. Visualisez l’analytique dans le portail Azure pour évaluer comment le système fonctionne et comment vos données aident à la personnalisation.

## <a name="where-can-i-use-personalizer"></a>Où puis-je utiliser Personalizer ?

Par exemple, votre application cliente peut ajouter Personalizer pour :

* Personnaliser le choix de l’article qui est mis en évidence sur un site web d’info.    
* Optimiser le positionnement des publicités sur un site web.
* Afficher un « élément recommandé » personnalisé sur un site web de vente en ligne.
* Suggérer des éléments d’interface utilisateur, comme des filtres à appliquer à une photo spécifique.
* Choisir la réponse d’un chatbot pour clarifier l’intention de l’utilisateur ou suggérer une action.
* Hiérarchiser les suggestions de ce qu’un utilisateur devrait faire à l’étape suivante d’un processus métier.

Personalizer n’est pas un service permettant de conserver et de gérer les informations de profil utilisateur, ou de consigner les préférences ou l’historique des utilisateurs individuels. Personalizer apprend des caractéristiques de chaque interaction de l’action un contexte d’un modèle unique qui peut obtenir des récompenses maximales en cas de caractéristiques similaires. 

## <a name="personalization-for-developers"></a>Personnalisation pour les développeurs

Le service Personalizer a deux API :

* Envoyer des informations (_caractéristiques_) sur vos utilisateurs et le contenu (_actions_) à personnaliser. Personalizer répond avec l’action classée en premier.
* Envoyez un feedback à Personalizer indiquant dans quelle mesure le classement a fonctionné, sous la forme d’un nombre généralement compris entre 0 et 1 (la section précédente indiquait entre -1 et 1). 

![Séquence de base des événements pour la personnalisation](media/what-is-personalizer/personalization-intro.png)

## <a name="next-steps"></a>Étapes suivantes

* [Nouveautés de Personalizer](whats-new.md)
* [Fonctionnement de Personalizer](how-personalizer-works.md)
* [Qu’est-ce que l’apprentissage par renforcement ?](concepts-reinforcement-learning.md)
* [En savoir plus sur les fonctionnalités et les actions de la demande de classement](concepts-features.md)
* [En savoir plus sur le calcul du score de la demande de récompense](concept-rewards.md)
* [Utiliser la démonstration interactive](https://personalizationdemo.azurewebsites.net/)
