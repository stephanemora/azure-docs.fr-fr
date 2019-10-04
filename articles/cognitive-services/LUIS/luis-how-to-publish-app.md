---
title: Publier l'application - LUIS
titleSuffix: Azure Cognitive Services
description: Quand vous avez terminé la création et les tests de votre application LUIS active, mettez-la à disposition de votre application cliente en la publiant sur le point de terminaison.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/02/2019
ms.author: diberry
ms.openlocfilehash: f92776072038c5684e9334d2dda1690ebb7bcaa8
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70257810"
---
# <a name="publish-your-active-trained-app-to-a-staging-or-production-endpoint"></a>Publier votre application active, formée sur un point de terminaison intermédiaire ou de production

Quand vous avez terminé la création et les tests de votre application LUIS active, mettez-la à disposition de votre application cliente en la publiant sur le point de terminaison. 

<a name="publish-your-trained-app-to-an-http-endpoint"></a>

## <a name="publishing"></a>Publication

1. Pour publier sur le point de terminaison, sélectionnez **Publish** (Publier) dans le volet en haut à droite. 

    ![Bouton publier en haut, barre de navigation à droite](./media/luis-how-to-publish-app/publish-top-nav-bar.png)

1. Sélectionnez vos paramètres pour le point de terminaison de prédiction publié, puis sélectionnez **Publier**.

    ![Sélectionnez Paramètres de publication, puis le bouton Publier.](./media/luis-how-to-publish-app/publish-pop-up.png)

### <a name="publishing-slots"></a>Emplacements de publication

Sélectionnez l’emplacement correct quand la fenêtre contextuelle s’affiche : 

* Mise en lots.
* Production. 

L’utilisation des deux emplacements de publication vous permet d’avoir deux versions différentes de votre application disponibles aux points de terminaison publiés, ou la même version sur deux points de terminaison différents. 

### <a name="publishing-regions"></a>Régions de publication

L’application est publiée dans toutes les régions associées aux ressources de point de terminaison de prédiction LUIS ajoutées dans le portail LUIS. 

Par exemple, pour une application créée sur [www.luis.ai](https://www.luis.ai), si vous créez une ressource LUIS dans deux régions **westus** et **eastus**, et ajoutez celles-ci à l’application en tant que ressources, l’application est publiée dans les deux régions. Pour plus d’informations sur les régions LUIS, consultez [Régions](luis-reference-regions.md).


## <a name="configuring-publish-settings"></a>Configuration des paramètres de publication

Après avoir sélectionné l’emplacement, configurez les paramètres de publication suivants :

* Analyse de sentiments
* Correction orthographique
* Préparation vocale 

Après publication, ces paramètres sont accessibles via la page **Paramètres de publication** de la section **Gérer**. Vous pouvez modifier les paramètres pour chaque publication. Si vous annulez une publication, les modifications que vous avez apportées lors de la publication sont également annulées. 

### <a name="when-your-app-is-published"></a>Quand votre application est publiée

Une fois votre application correctement publiée, une notification de réussite de couleur verte s’affiche en haut du navigateur. La barre de notification verte comprend également un lien vers les points de terminaison. 

![Fenêtre contextuelle de publication avec un lien au point de terminaison](./media/luis-how-to-publish-app/publish-success.png)

Si vous avez besoin de l’URL de point de terminaison, sélectionnez le lien. Vous pouvez également accéder aux URL de point de terminaison en sélectionnant **Gérer** dans le menu supérieur, puis **Ressources Azure** dans le menu gauche. 

## <a name="sentiment-analysis"></a>analyse de sentiments

<a name="enable-sentiment-analysis"></a>

L’analyse des sentiments permet à LUIS de s’intégrer avec l’[Analyse de texte](https://azure.microsoft.com/services/cognitive-services/text-analytics/) pour fournir une analyse des sentiments et des expressions clés. 

Vous ne devez pas nécessairement fournir une clé d’Analyse de texte, et votre compte Azure ne sera pas facturé pour ce service. 

Les données de sentiment correspondent à un score compris entre 1 et 0 indiquant le sentiment, positif (plus proche de 1) ou négatif (plus proche de 0), des données. L’étiquette de sentiment de `positive`, `neutral` et `negative` est fonction de la culture prise en charge. Actuellement, seul l’anglais prend en charge les étiquettes de sentiment. 

Pour plus d’informations sur la réponse du point de terminaison JSON avec l’analyse des sentiments, voir [Analyse des sentiments](luis-concept-data-extraction.md#sentiment-analysis).

## <a name="spelling-correction"></a>Correction orthographique

Les corrections orthographiques sont effectuées avant la prédiction d’énoncé utilisateur LUIS. Vous pouvez voir toutes les modifications apportées à l’énoncé d’origine, y compris en lien avec l’orthographe, dans la réponse.

## <a name="speech-priming"></a>Préparation vocale

La préparation vocale est le processus d’envoi du modèle LUIS aux services Speech avant la synthèse vocale. Cela permet au service de fournir une conversion vocale plus précise pour votre modèle. Cela permet de faire passer les demandes du service Speech et les réponses du service LUIS dans un seul appel. La latence globale est ainsi réduite.

## <a name="next-steps"></a>Étapes suivantes

* Consultez [Gérer les clés](./luis-how-to-azure-subscription.md) pour ajouter des clés à la clé d’abonnement Azure dans LUIS et pour savoir comment définir la clé de vérification orthographique Bing et inclure toutes les intentions dans les résultats.
* Voir [Former et tester votre application](luis-interactive-test.md) pour obtenir des instructions sur la façon de tester votre application publiée dans la console de test.

