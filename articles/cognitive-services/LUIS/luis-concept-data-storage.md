---
title: Stockage des données
titleSuffix: Language Understanding - Azure Cognitive Services
description: LUIS stocke les données chiffrées dans un magasin de données Azure correspondant à la région spécifiée par la clé.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: diberry
ms.openlocfilehash: a382c3b3f9949e95ebc09f9db5072a123c59f970
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55223202"
---
# <a name="data-storage-and-removal-in-language-understanding-luis-cognitive-services"></a>Stockage et suppression des données dans Language Understanding (LUIS) Cognitive Services
LUIS stocke les données chiffrées dans un magasin de données Azure correspondant à la région spécifiée par la clé. Ces données sont stockées pendant 30 jours. 

## <a name="export-and-delete-app"></a>Exporter et supprimer l’application
Les utilisateurs ont le contrôle total sur l’[exportation](luis-how-to-start-new-app.md#export-app) et la [suppression](luis-how-to-start-new-app.md#delete-app) de l’application. 

## <a name="utterances-in-an-intent"></a>Énoncés dans une intention
Supprimer des exemples d’énoncés utilisés pour l’apprentissage de [LUIS](luis-reference-regions.md). Si vous supprimez un exemple d’énoncé de votre application LUIS, il est supprimé du service web LUIS et n’est plus disponible pour l’exportation.

## <a name="utterances-in-review"></a>Énoncés et révision
Vous pouvez supprimer des énoncés de la liste des énoncés d’utilisateurs que LUIS suggère dans la page **[Réviser les énoncés de point de terminaison](luis-how-to-review-endoint-utt.md)**. La suppression d’énoncés de cette liste a pour effet d’empêcher leur suggestion, mais pas de les supprimer des journaux.

## <a name="accounts"></a>Comptes
Si vous supprimez un compte, toutes les applications sont supprimées, ainsi que leurs exemples d’énoncés et journaux. Les données sont conservées pendant 60 jours avant que le compte et les données ne soient définitivement supprimés.

La suppression d’un compte est possible à partir de la page **Paramètres**. Sélectionnez le nom de votre compte en haut à droite dans la barre de navigation supérieure pour accéder à la page **Paramètres**.

## <a name="data-inactivity-as-an-expired-subscription"></a>Inactivité de données considérée comme un abonnement arrivé à expiration
Dans le cadre de la conservation et de la suppression des données, une application LUIS inactive peut, à la _discrétion de Microsoft_ être traitée comme un abonnement arrivé à expiration. Une application est considérée comme inactive si elle répond aux critères suivants pendant les 90 derniers jours : 

* Elle n’a reçu **aucun** appel.
* Elle n’a pas été modifiée.
* Aucune clé actuelle ne lui est assignée.
* Aucun utilisateur ne s’y est connecté.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [En savoir plus sur l’exportation et la suppression d’une application](luis-how-to-start-new-app.md)
