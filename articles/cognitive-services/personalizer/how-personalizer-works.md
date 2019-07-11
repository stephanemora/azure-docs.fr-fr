---
title: Fonctionnement de Personalizer - Personalizer
titleSuffix: Azure Cognitive Services
description: Personalizer utilise le machine learning pour découvrir quelle action utiliser dans un contexte donné. Chaque boucle d’apprentissage a un modèle qui est entraîné exclusivement sur les données que vous avez envoyées via des appels de l’API de classement (Rank) et de récompense (Reward). Chaque boucle d’apprentissage est complètement indépendante des autres.
author: edjez
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: overview
ms.date: 06/07/2019
ms.author: edjez
ms.openlocfilehash: d0a0a6101d876493188426d19f2fa845d20ee274
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67055462"
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

## <a name="example-use-cases-for-personalizer"></a>Exemple de cas d’utilisation pour Personalizer

* Clarification et désambiguïsation de l’intention : aidez vos utilisateurs à vivre une meilleure expérience lorsque leur intention n’est pas claire en fournissant une option personnalisée pour chaque utilisateur.
* Suggestions par défaut pour les menus et les options : demandez au bot de suggérer l’élément le plus probable de manière personnalisée, avant de présenter un menu impersonnel ou une liste d’alternatives.
* Caractéristiques et ton du bot : pour les bots pouvant varier le ton, la verbosité et le style d’écriture, envisagez de personnaliser ces caractéristiques.
* Contenu des notifications et alertes : choisissez le texte à utiliser pour les alertes afin d’impliquer davantage les utilisateurs.
* Minutage des notifications et alertes : personnalisez l’apprentissage du moment opportun pour envoyer des notifications aux utilisateurs afin de les impliquer davantage.

## <a name="checklist-for-applying-personalizer"></a>Liste de contrôle pour l’application de Personalizer

Vous pouvez appliquer Personalizer dans les situations où :

* Vous avez un objectif métier ou d’utilisabilité pour votre application.
* Il y a un endroit dans votre application où une décision contextuelle de ce qu’il faut montrer aux utilisateurs va améliorer cet objectif.
* Le meilleur choix peut et doit être appris du comportement collectif des utilisateurs et du score de récompense total.
* L’utilisation du Machine Learning pour la personnalisation suit une série de [consignes et de choix d’utilisation responsable](ethics-responsible-use.md) pour votre équipe.
* La décision peut être exprimée sous la forme d’un classement de la meilleure option ([action](concepts-features.md#actions-represent-a-list-of-options)) parmi un ensemble limité de choix.
* Votre logique métier peut calculer l’opportunité de ce choix en mesurant un aspect du comportement des utilisateurs et en l’exprimant sous la forme d’une valeur comprise entre -1 et 1.
* Le score de récompense n’apporte pas trop de facteurs confondants ou externes. Singulièrement, la durée de l’expérience est suffisamment courte pour que le score de récompense puisse être calculé pendant la période de temps où il reste pertinent.
* Vous pouvez exprimer le contexte pour le classement sous la forme d’un dictionnaire d’au moins 5 caractéristiques dont vous pensez qu’elles peuvent aider à faire le bon choix, qui n’incluent pas d’informations d’identification personnelles.
* Vous disposez d’informations sur chaque action sous la forme d’un dictionnaire d’au moins 5 attributs ou caractéristiques dont vous pensez qu’ils peuvent aider Personalizer à opérer le bon choix.
* Vous pouvez conserver des données sur une période suffisamment longue pour compiler un historique d’au moins 100 000 interactions.

## <a name="machine-learning-considerations-for-applying-personalizer"></a>Considérations sur le machine learning pour l’application de Personalizer

Personalizer est basé sur un apprentissage par renforcement, une approche du Machine Learning basée sur votre retour d’expérience. 

Personalizer fait un meilleur apprentissage dans les situations où :
* Il existe suffisamment d’événements pour continuer à obtenir une personnalisation optimale si le problème se déplace au fil du temps (comme des préférences quant à des actualités ou à la mode). Personalizer s’adaptera aux changements continus du monde réel, mais les résultats ne seront pas optimaux s’il n’y pas suffisamment de données et d’événements servant de base à l’apprentissage pour découvrir et décider de nouveaux modèles. Vous devez choisir un cas d’utilisation qui se produit suffisamment souvent. Recherchez des cas d’utilisation qui se produisent au moins 500 fois par jour.
* Le contexte et les actions ont suffisamment de caractéristiques pour faciliter l’apprentissage.
* Il y a moins de 50 actions à classer par appel.
* Vos paramètres de conservation des données permettent à Personalizer de collecter suffisamment de données pour effectuer des évaluations hors connexion et l’optimisation de la stratégie. Ceci représente généralement au moins 50 000 points de données.

## <a name="how-to-use-personalizer-in-a-web-application"></a>Comment utiliser Personalizer dans une application web

L’ajout d’une boucle à une application web inclut les étapes suivantes :

* Déterminer l’expérience à personnaliser, les actions et fonctionnalités disponibles, les fonctionnalités à utiliser selon le contexte et la récompense à donner.
* Ajouter une référence au Kit de développement logiciel (SDK) Personnalisation dans votre application.
* Appeler l’API de classement lorsque vous êtes prêt à personnaliser.
* Stocker l’eventId. Vous enverrez une récompense avec l’API de récompense ultérieurement.
1. Appelez Activer pour l’événement une fois que vous êtes certain que l’utilisateur a vu votre page personnalisée.
1. Attendez que l’utilisateur sélectionne un contenu classé. 
1. Appelez l’API de récompense pour spécifier la qualité de la sortie de l’API de classement.

## <a name="how-to-use-personalizer-with-a-chat-bot"></a>Comment utiliser Personalizer avec un bot conversationnel

Cet exemple montre comment utiliser la fonctionnalité Personnalisation pour faire une suggestion par défaut au lieu d’envoyer à l’utilisateur une série de menus ou de choix à chaque fois.

* Téléchargez le [code](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/tree/master/samples/ChatbotExample) pour cet exemple.
* Configurez votre solution de bot. Veillez à publier votre application LUIS. 
* Gérez les appels des API de classement et de récompense pour le bot.
    * Ajoutez du code pour gérer le traitement des intentions LUIS. Si la valeur **Aucune** est renvoyée en tant que première intention ou si le score de la première intention est inférieur au seuil de votre logique métier, envoyez la liste des intentions à Personalizer pour classer les intentions.
    * Affichez la liste des intentions à l’utilisateur sous la forme de liens sélectionnables, avec la première intention en haut du classement dans la réponse de l’API de classement.
    * Capturez la sélection de l’utilisateur et envoyez-la dans l’appel de l’API de récompense. 

### <a name="recommended-bot-patterns"></a>Modèles bot recommandés

* Faites des appels de l’API de classement de Personalizer chaque fois qu’il est nécessaire de lever une ambiguïté, par opposition à la mise en cache des résultats pour chaque utilisateur. Le résultat de la désambiguïsation de l’intention peut changer au fil du temps pour une personne, et le fait de permettre à l’API de classement d’explorer les changements accélérera l’apprentissage global.
* Choisissez une interaction commune à de nombreux utilisateurs afin de disposer de suffisamment de données à personnaliser. Par exemple, des questions préliminaires peuvent être plus adaptées que des clarifications demandées à un stade de la conversation que de rares utilisateurs atteignent.
* Utilisez des appels de l’API de classement pour permettre des conversations de type « la première suggestion est la bonne », où l’utilisateur est invité à répondre à la question « Voulez-vous ceci ? » ou « Voulez-vous dire cela ? » en se contenant de donner une confirmation, par opposition au fait de proposer à l’utilisateur des options qu’il doit choisir dans un menu. Par exemple, si l’utilisateur dit « Je souhaiterais un café », le bot peut lui demander « Voulez-vous un double espresso ? ». De cette façon, le signal de récompense est également fort parce qu’il se rapporte directement à la suggestion.

## <a name="how-to-use-personalizer-with-a-recommendation-solution"></a>Comment utiliser Personalizer avec une solution de recommandation

Utilisez votre moteur de recommandations pour filtrer un vaste catalogue afin de n’en retenir que quelques éléments qui peuvent ensuite être présentés comme une trentaine d’actions possibles envoyées à l’API de classement.

Vous pouvez utiliser des moteurs de recommandation avec Personalizer pour effectuer les opérations suivantes :

* Configurer la [solution de recommandation](https://github.com/Microsoft/Recommenders/). 
* Lorsque vous affichez une page, appeler le modèle de recommandation pour obtenir une courte liste de recommandations.
* Appeler la fonctionnalité Personnalisation pour classer la sortie d’une solution de recommandation.
* Envoyer des commentaires sur l’action de l’utilisateur avec l’appel de l’API de récompense.


## <a name="pitfalls-to-avoid"></a>Pièges à éviter

* N’utilisez pas Personalizer lorsque le comportement personnalisé n’est pas une chose commune à tous les utilisateurs, mais plutôt une chose qui devrait être retenue pour un utilisateur spécifique, ou qui figure dans une liste d’alternatives spécifique d’un utilisateur. Par exemple, utiliser Personalizer pour suggérer une commande de pizza à partir d’une liste de 20 options de menu possibles est utile, mais le contact à appeler à partir de la liste de contacts de l’utilisateur quand celui-ci a besoin d’aide pour garder les enfants (comme « Grand-mère ») n’est pas un élément personnalisable dans votre base d’utilisateurs.


## <a name="adding-content-safeguards-to-your-application"></a>Ajout de protections de contenu à votre application

Si votre application autorise des variations importantes du contenu affiché aux utilisateurs, et qu’une partie de ce contenu peut être préjudiciable ou inappropriée pour certains utilisateurs, vous devez vous assurer à l’avance que les protections adéquates sont en place pour empêcher les utilisateurs de voir du contenu inapproprié. La meilleure procédure pour implémenter des protections est la suivante :
    * Obtenir la liste des actions à classer.
    * Filtrer les celles qui ne sont pas viables pour le public.
    * Classer uniquement les actions viables.
    * Afficher à l’utilisateur l’action en tête du classement.

Dans certaines architectures, la séquence ci-dessus peut être difficile à implémenter. Dans ce cas, il existe une approche alternative pour l’implémentation des protections après le classement, mais il convient de prendre des dispositions pour que les actions qui ne relèvent pas de la protection ne soient pas utilisées pour entraîner le modèle de Personalizer. La marche à suivre est la suivante.

* Répertorier les actions à classer avec l’apprentissage désactivé.
* Classer les actions.
* Vérifier si l’action en tête du classement est viable.
    * Si l’action en tête du classement est viable, activer l’apprentissage pour ce classement, puis l’afficher à l’utilisateur.
    * Si l’action en tête du classement n’est pas viable, ne pas activer l’apprentissage pour ce classement, et décider selon votre propre logique ou sur la base d’une autre approche ce qu’il convient de montrer à l’utilisateur. Même si vous utilisez la deuxième option la mieux classée, n’activez pas l’apprentissage pour ce classement.

## <a name="verifying-adequate-effectiveness-of-personalizer"></a>Vérification de l’efficacité appropriée de Personalizer

Vous pouvez vérifier périodiquement l’efficacité de Personalizer en effectuant des [évaluations hors connexion](how-to-offline-evaluation.md)

## <a name="next-steps"></a>Étapes suivantes

Comprendre [où vous pouvez utiliser Personalizer](where-can-you-use-personalizer.md).