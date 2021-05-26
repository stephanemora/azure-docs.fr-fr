---
title: Détection de caractères animés avec Azure Video Analyzer for Media (anciennement Video Indexer)
titleSuffix: Azure Media Services
description: Cette rubrique montre comment utiliser la détection de caractères animés avec Azure Video Analyzer for Media (anciennement Video Indexer).
services: media-services
author: Juliako
manager: femila
ms.topic: article
ms.date: 11/19/2019
ms.author: juliako
ms.openlocfilehash: 1ebdd2cfa3e380b6c111c8c8c7edbad252a43c50
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110386192"
---
# <a name="animated-character-detection-preview"></a>Détection de personnages animés (préversion)

Azure Video Analyzer for Media (précédemment Video Indexer) prend en charge la détection, le regroupement et la reconnaissance de personnages dans le contenu animé via l’intégration avec [Cognitive Services Custom Vision](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/). Cette fonctionnalité est disponible à la fois via le portail et l’API.

Après avoir chargé une vidéo animée avec un modèle d’animation spécifique, Video Analyzer for Media extrait les images clés, détecte les personnages animés dans ces images, regroupe les personnages similaires et choisit le meilleur échantillon. Ensuite, il envoie les personnages groupés à Custom Vision qui identifie les personnages en fonction des modèles sur lesquels il a été formé. 

Avant de commencer la formation de votre modèle, les personnages sont détectés sans nom. À mesure que vous ajoutez des noms et que vous formez le modèle, Video Analyzer for Media reconnaît les personnages et les nomme en conséquence.

## <a name="flow-diagram"></a>Diagramme de flux

Le diagramme suivant illustre le déroulement du processus de détection de personnages animés.

![Diagramme de flux](./media/animated-characters-recognition/flow.png)

## <a name="accounts"></a>Comptes

En fonction du type de votre compte Video Analyzer for Media, différents ensembles de fonctionnalités sont disponibles. Pour plus d’informations sur la connexion de votre compte à Azure, consultez [Créer un compte Video Analyzer for Media connecté à Azure](connect-to-azure.md).

* Compte d’essai : Video Analyzer for Media utilise un compte Custom Vision interne pour créer un modèle et le connecter à votre compte Video Analyzer for Media. 
* Compte payant : Connectez votre compte Custom Vision à votre compte Video Analyzer for Media (vous devez d’abord créer un compte si vous n’en avez pas déjà un).

### <a name="trial-vs-paid"></a>Différences entre les comptes d’essai et payants

|Fonctionnalités|Version d’évaluation|Payant|
|---|---|---|
|Compte Custom Vision|Géré en arrière-plan par Video Analyzer for Media. |Votre compte Custom Vision est connecté à Video Analyzer for Media.|
|Nombre de modèles d’animation|Une|Jusqu’à 100 modèles par compte (limitation de Custom Vision).|
|Formation du modèle|Video Analyzer for Media forme le modèle pour de nouveaux personnages et des exemples supplémentaires des personnages existants.|Le propriétaire du compte forme le modèle lorsqu’il est prêt à apporter des modifications.|
|Options avancées dans Custom Vision|Pas d’accès au portail Custom Vision.|Vous pouvez ajuster les modèles vous-même dans le portail Custom Vision.|

## <a name="use-the-animated-character-detection-with-portal--and-api"></a>Utiliser la détection de personnages animés avec le portail et l’API

Pour plus d’informations, consultez [Utiliser la détection de personnages animés avec le portail et l’API](animated-characters-recognition-how-to.md).

## <a name="limitations"></a>Limites

* Actuellement, la fonctionnalité « Identification d’animation » n’est pas prise en charge dans la région Asie de l’Est.
* Les personnages qui semblent être petits ou éloignés dans la vidéo peuvent ne pas être identifiés correctement si la qualité de la vidéo est médiocre.
* Il est recommandé d’utiliser un modèle par ensemble de personnages animés (par exemple, une série animée).

## <a name="next-steps"></a>Étapes suivantes

[Vue d’ensemble de Video Analyzer for Media](video-indexer-overview.md)