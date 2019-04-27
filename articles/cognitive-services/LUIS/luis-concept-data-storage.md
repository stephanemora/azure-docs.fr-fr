---
title: Stockage des données
titleSuffix: Language Understanding - Azure Cognitive Services
description: LUIS stocke les données chiffrées dans un magasin de données Azure correspondant à la région spécifiée par la clé.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 03/28/2019
ms.author: diberry
ms.openlocfilehash: a1093c2a6303b453a17a52058303913de5ecfa8d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60812937"
---
# <a name="data-storage-and-removal-in-language-understanding-luis-cognitive-services"></a>Stockage et suppression des données dans Language Understanding (LUIS) Cognitive Services
LUIS stocke les données chiffrées dans un magasin de données Azure correspondant à la région spécifiée par la clé. Ces données sont stockées pendant 30 jours. 

## <a name="export-and-delete-app"></a>Exporter et supprimer l’application
Les utilisateurs ont le contrôle total sur l’[exportation](luis-how-to-start-new-app.md#export-app) et la [suppression](luis-how-to-start-new-app.md#delete-app) de l’application. 

## <a name="utterances"></a>Énoncés

Énoncés peuvent être stockées dans deux emplacements différents. 

* Au cours de **le processus de création**, énoncés sont créées et stockées dans l’intention. Énoncés dans les intentions sont requis pour une application LUIS réussie. Une fois que l’application est publiée et reçoit des requêtes au point de terminaison, chaîne de requête de la demande de point de terminaison, `log=false`, détermine si l’énoncé de point de terminaison est stocké. Si le point de terminaison est stocké, il devient partie énoncés apprentissage actif trouvé dans le **Build** section du portail, dans le **passez en revue les énoncés de point de terminaison** section. 
* Lorsque vous **passez en revue les énoncés de point de terminaison**et ajouter un énoncé à une intention, l’énoncé n’est plus stocké dans le cadre des énoncés de point de terminaison à réviser. Il est ajouté à intentions de l’application. 

<a name="utterances-in-an-intent"></a>

### <a name="delete-example-utterances-from-an-intent"></a>Supprimer des énoncés d’exemple à partir d’une intention
Supprimer des exemples d’énoncés utilisés pour l’apprentissage de [LUIS](luis-reference-regions.md). Si vous supprimez un exemple d’énoncé de votre application LUIS, il est supprimé du service web LUIS et n’est plus disponible pour l’exportation.

<a name="utterances-in-review"></a>

### <a name="delete-utterances-in-review-from-active-learning"></a>Supprimer des énoncés dans la révision d’apprentissage actif

Vous pouvez supprimer des énoncés de la liste des énoncés d’utilisateurs que LUIS suggère dans la page **[Réviser les énoncés de point de terminaison](luis-how-to-review-endpoint-utterances.md)**. La suppression d’énoncés de cette liste a pour effet d’empêcher leur suggestion, mais pas de les supprimer des journaux d’activité.

Si vous ne souhaitez pas énoncés d’apprentissage actif, vous pouvez [désactiver apprentissage actif](luis-how-to-review-endpoint-utterances.md#disable-active-learning). La désactivation d’apprentissage actif désactive également la journalisation.

### <a name="disable-logging-utterances"></a>Désactiver la journalisation des énoncés
[La désactivation d’apprentissage actif](luis-how-to-review-endpoint-utterances.md#disable-active-learning) désactive ouvre une session.


<a name="accounts"></a>

## <a name="delete-an-account"></a>Supprimer un compte
Si vous supprimez un compte, toutes les applications sont supprimées, ainsi que leurs exemples d’énoncés et journaux d’activité. Les données sont conservées pendant 60 jours avant que le compte et les données ne soient définitivement supprimés.

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
