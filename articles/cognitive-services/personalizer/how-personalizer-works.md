---
title: Fonctionnement de Personalizer - Personalizer
titleSuffix: Azure Cognitive Services
description: Personalizer utilise le machine learning pour découvrir quelle action utiliser dans un contexte donné. Chaque boucle d’apprentissage a un modèle qui est entraîné exclusivement sur les données que vous avez envoyées via des appels de l’API de classement (Rank) et de récompense (Reward). Chaque boucle d’apprentissage est complètement indépendante des autres.
author: edjez
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: overview
ms.date: 05/07/2019
ms.author: edjez
ms.openlocfilehash: 6b2237f27fba5eaf952932cd6592052649400b96
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65025492"
---
# <a name="how-personalizer-works"></a>Fonctionnement de Personalizer

Personalizer utilise le machine learning pour découvrir quelle action utiliser dans un contexte donné. Chaque boucle d’apprentissage a un modèle qui est entraîné exclusivement sur les données que vous avez envoyées via des appels de l’API de **classement** (Rank) et de **récompense** (Reward). Chaque boucle d’apprentissage est complètement indépendante des autres. Créez une boucle d’apprentissage pour chaque partie ou chaque comportement de votre application que vous voulez personnaliser.

Pour chaque boucle, **appelez l’API de classement** en fonction du contexte actuel, avec :

* Liste des actions possibles : éléments de contenu à partir desquels sélectionner l’action à classer en premier.
* Liste des [caractéristiques du contexte](concepts-features.md) : les données contextuellement pertinentes, comme l’utilisateur, le contenu et le contexte.

L’API de **classement** décide d’utiliser :

* _Exploiter_ : Le modèle actuel pour déterminer la meilleure action en fonction des données antérieures.
* _Explorer_ : Sélectionne une action différente de l’action classée en premier.

L’API de **récompense** :

* Collecte des données pour entraîner le modèle en enregistrant les caractéristiques et les scores de récompense de chaque appel du classement.
* Utilise ces données pour mettre à jour le modèle en fonction des paramètres spécifiés dans la _stratégie d’apprentissage_.

## <a name="architecture"></a>Architecture

L’illustration suivante montre l’architecture du flux des appels des API de classement et de récompense :

![texte de remplacement](./media/how-personalizer-works/personalization-how-it-works.png "Fonctionnement de la personnalisation")

1. Personalizer utilise un modèle d’intelligence artificielle interne pour déterminer le classement de l’action.
1. Le service décide d’exploiter le modèle actuel ou d’explorer de nouveaux choix pour le modèle.  
1. Le résultat du classement est envoyé à EventHub.
1. Quand Personalizer reçoit la récompense, elle est envoyée à EventHub. 
1. Le classement et la récompense sont mis en corrélation.
1. Le modèle d’intelligence artificielle est mis à jour en fonction des résultats de la corrélation.
1. Le moteur d’inférence est mis à jour avec le nouveau modèle. 

## <a name="research-behind-personalizer"></a>Recherches derrière Personalizer

Personalizer est basé sur la science et la recherche de pointe dans le domaine de l’[apprentissage par renforcement](concepts-reinforcement-learning.md), notamment sur des articles, des activités de recherche et des domaines en cours d’exploration de Microsoft Research.

## <a name="terminology"></a>Terminologie

* **Boucle d’apprentissage** : Vous pouvez créer une boucle d’apprentissage pour chaque partie de votre application qui peut tirer parti de la personnalisation. Si vous avez plusieurs expériences à personnaliser, créez une boucle pour chacune d’elles. 

* **Actions** : Les actions sont les éléments de contenu, comme des produits ou des promotions, parmi lesquels choisir. Personalizer choisit l’action à montrer en premier à vos utilisateurs, qui s’appelle l’_action de récompense_, via l’API de classement. Chaque action peut avoir des caractéristiques envoyées avec la demande de classement.

* **Context** : Pour fournir un classement plus précis, fournissez des informations sur votre contexte, par exemple :
    * Votre utilisateur.
    * L’appareil sur lequel il travaille. 
    * L’heure actuelle.
    * D’autres données relatives à la situation actuelle.
    * L’historique des données sur l’utilisateur ou sur le contexte.

    Votre application spécifique peut avoir des informations de contexte différentes. 

* **[Caractéristiques](concepts-features.md)**  : Une unité d’informations sur un élément de contenu ou un contexte utilisateur.

* **Récompense** : Une mesure de la façon dont l’utilisateur a répondu à l’action retournée par l’API de classement, sous la forme d’un score compris entre 0 et 1. La valeur de 0 à 1 est définie par votre logique métier, en fonction de la façon dont le choix a aidé à atteindre vos objectifs métier de personnalisation. 

* **Exploration** : Le service Personalizer effectue une exploration quand, au lieu de retourner la meilleure action, il choisit une autre action pour l’utilisateur. Le service Personalizer évite les dérives et la stagnation, et peut s’adapter au comportement actuel de l’utilisateur en effectuant une exploration. 

* **Durée de l’expérience** : Temps pendant lequel le service Personalizer attend une récompense, à partir du moment où l’appel du classement s’est produit pour cet événement.

* **Événements inactifs** : Un événement inactif est un événement où vous avez appelé le classement, mais où vous n’êtes pas sûr que l’utilisateur verra jamais le résultat, en raison des décisions de l’application cliente. Les événements inactifs vous permettent de créer et de stocker les résultats de la personnalisation, puis de décider de les abandonner plus tard sans affecter le modèle de machine learning.

* **Modèle** : Un modèle Personalizer capture toutes les données apprises sur le comportement des utilisateurs, obtenant les données d’entraînement de la combinaison des arguments que vous envoyez aux appels de classement et de récompense, et avec un comportement d’entraînement déterminé par la stratégie d’apprentissage. 

## <a name="next-steps"></a>Étapes suivantes

Comprendre [où vous pouvez utiliser Personalizer](where-can-you-use-personalizer.md).