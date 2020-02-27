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
ms.date: 01/21/2020
ms.author: diberry
ms.openlocfilehash: bf0710ebef21226d8d8582a920d64027bb015d34
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/26/2020
ms.locfileid: "77622718"
---
# <a name="what-is-personalizer"></a>Qu’est-ce que Personalizer ?

Azure Personalizer est un service d’API basé sur le cloud qui permet à votre application cliente de choisir l’élément de _contenu_ le mieux adapté à chaque utilisateur. Le service sélectionne l’élément le mieux adapté parmi les éléments de contenu, en fonction des informations collectives en temps réel que vous fournissez sur le contenu et le contexte.

Une fois que vous avez présenté l’élément de contenu à votre utilisateur, votre système supervise le comportement de l’utilisateur et fournit à Personalizer un score de récompense afin qu’il puisse améliorer sa capacité à sélectionner le contenu le mieux adapté, en fonction des informations de contexte qu’il reçoit.

Le **contenu** peut correspondre à n’importe quelle unité d’information (texte, image, URL, e-mail, etc.) que vous souhaitez présenter à l’utilisateur.

<!--
![What is personalizer animation](./media/what-is-personalizer.gif)
-->

## <a name="how-does-personalizer-select-the-best-content-item"></a>Comment Personalizer sélectionne l’élément de contenu le mieux adapté ?

Personalizer utilise l’**apprentissage par renforcement** pour sélectionner l’élément (_action_) le mieux adapté, en fonction du comportement collectif et des scores de récompense de tous les utilisateurs. Les actions sont des éléments de contenu, tels que des articles de presse, des films ou des produits.

L’appel de l’API **Rank** prend l’élément d’action, les caractéristiques de l’action et les caractéristiques de contexte pour sélectionner le premier élément d’action de la liste :

* **Actions avec caractéristiques** : éléments de contenu avec des caractéristiques précises pour chaque élément
* **Caractéristiques de contexte** : caractéristiques de vos utilisateurs, leur contexte ou leur environnement, lors de l’utilisation de votre application

L’appel de l’API Rank retourne l’ID de l’élément de contenu (ou __action__) à présenter à l’utilisateur dans le champ **Reward Action ID** (ID de l’action récompensée).
L’__action__ qui est présentée à l’utilisateur est choisie à l’aide de modèles Machine Learning, ce qui permet d’optimiser la quantité totale de récompenses dans le temps.

Voici quelques exemples de scénarios :

|Type de contenu|**Actions (avec caractéristiques)**|**Caractéristiques de contexte**|ID de l’action récompensée retournée<br>(afficher ce contenu)|
|--|--|--|--|
|Liste des actualités|a. `The president...` (national, politique, [texte])<br>b. `Premier League ...` (mondial, sport, [texte, image, vidéo])<br> c. `Hurricane in the ...` (régional, météo, [texte,image]|Pays/Région de lecture des actualités<br>Mois ou saison<br>|a `The president...`|
|Liste de films|1. `Star Wars` (1977, [action, aventure, fantastique], George Lucas)<br>2. `Hoop Dreams` (1994, [documentaire, sport], Steve James<br>3. `Casablanca` (1942, [romantique, drame, guerre], Michael Curtiz)|Pays/Région du visionnage du film<br>Taille de l’écran<br>Type d’utilisateur<br>|3. `Casablanca`|
|Liste de produits|i. `Product A` (3 kg, $$$$, livraison en 24 heures)<br>ii. `Product B` (20 kg, $$, livraison en 2 semaines + frais de douanes)<br>iii. `Product C` (3 kg, $$$, livraison en 48 heures)|Pays/Région de consultation des produits<br>Niveau de dépense de l’utilisateur<br>Mois ou saison|ii. `Product B`|

Personalizer a utilisé l’apprentissage par renforcement pour sélectionner l’action la mieux adaptée (_ID d’action récompensée_) en se basant sur une combinaison des éléments suivants :
* Modèle entraîné : informations passées reçues par le service Personalizer
* Actions actuelles : certaines actions avec caractéristiques et certaines caractéristiques de contexte

## <a name="when-to-call-personalizer"></a>Quand appeler Personalizer ?

L’[API](https://go.microsoft.com/fwlink/?linkid=2092082) **Rank** de Personalizer est appelée _chaque fois_ que vous présentez du contenu, en temps réel. Il s’agit d’un **événement**, qui est associé à un _ID d’événement_.

L’[API](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Reward) **Reward** de Personalizer peut être appelée en temps réel ou retardée pour mieux répondre aux besoins de votre infrastructure. Vous déterminez le score de récompense en fonction des besoins de votre entreprise. Il peut s’agir d’une valeur unique comme 1 pour Bon ou 0 pour Mauvais, ou d’un nombre produit par un algorithme que vous créez en tenant compte de vos objectifs et métriques métier.

## <a name="personalizer-content-requirements"></a>Conditions relatives au contenu Personalizer

Utilisez Personalizer si votre contenu :

* Comprend un ensemble restreint d’éléments (environ 50 max.). Si vous disposez d’une liste plus longue, [utilisez un moteur de recommandations](where-can-you-use-personalizer.md#how-to-use-personalizer-with-a-recommendation-solution) pour réduire la liste à 50 éléments.
* Contient des informations décrivant le contenu que vous souhaitez classer : _actions avec caractéristiques_ et _caractéristiques de contexte_.
* A besoin d’un minimum de 1 000 événements de contenu par jour pour que Personalizer soit efficace. Si Personalizer ne reçoit pas le trafic minimal nécessaire, le service met plus de temps à déterminer l’élément de contenu le mieux adapté.

Étant donné que Personalizer utilise des informations collectives quasiment en temps réel pour retourner l’élément de contenu le mieux adapté, le service :
* Ne conserve pas et ne gère pas les informations de profil utilisateur
* Ne journalise pas les préférences ni l’historique de chaque utilisateur
* Ne nécessite pas le nettoyage ni l’étiquetage du contenu

## <a name="how-to-design-and-implement-personalizer-for-your-client-application"></a>Comment concevoir et implémenter Personalizer pour votre application cliente

1. [Concevez](concepts-features.md) et planifiez le contenu, les **_actions_** et le **_contexte_** . Déterminez l’algorithme de récompense pour le score de **_récompense_** .
1. Chaque [ressource Personalizer](how-to-settings.md) que vous créez est considérée comme 1 boucle d’apprentissage. La boucle recevra les appels des API Rank et Reward pour ce contenu ou cette expérience utilisateur.
1. Ajoutez Personalizer à votre site web ou à votre système de contenu :
    1. Ajoutez un appel **Rank** à Personalizer dans votre application, votre site web ou votre système afin de déterminer l’élément de _contenu_ le mieux adapté avant que celui-ci ne soit présenté à l’utilisateur.
    1. Présentez l’élément de _contenu_ le mieux adapté (qui correspond à l’_ID d’action récompensée_) à l’utilisateur.
    1. Appliquez un _algorithme_ aux informations collectées concernant le comportement de l’utilisateur, afin de déterminer le score de **récompense**, par exemple :

        |Comportement|Score de récompense calculé|
        |--|--|
        |L’utilisateur a sélectionné l’élément de _contenu_ le mieux adapté (ID d’action récompensée)|**1**|
        |L’utilisateur a sélectionné un autre contenu|**0**|
        |L’utilisateur s’est interrompu, a fait défiler la liste de manière indécise, puis a sélectionné l’élément de _contenu_ le mieux adapté (ID d’action récompensée)|**0,5**|

    1. Ajoutez un appel **Reward** en envoyant un score de récompense compris entre 0 et 1 :
        * Immédiatement après avoir affiché le contenu
        * Ou ultérieurement, sur un système hors connexion
    1. [Évaluez votre boucle](concepts-offline-evaluation.md) avec une évaluation hors connexion après une certaine période d’utilisation. L’évaluation hors connexion permet de tester et d’évaluer l’efficacité du service Personalizer sans changer votre code ni affecter l’expérience utilisateur.

## <a name="next-steps"></a>Étapes suivantes


* [Fonctionnement de Personalizer](how-personalizer-works.md)
* [Qu’est-ce que l’apprentissage par renforcement ?](concepts-reinforcement-learning.md)
* [En savoir plus sur les fonctionnalités et les actions de la demande de classement](concepts-features.md)
* [En savoir plus sur le calcul du score de la demande de récompense](concept-rewards.md)
* [Démarrages rapides](sdk-learning-loop.md)
* [Didacticiel](tutorial-use-azure-notebook-generate-loop-data.md)
* [Utiliser la démonstration interactive](https://personalizationdemo.azurewebsites.net/)
