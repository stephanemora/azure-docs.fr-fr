---
title: Modérer du contenu à l’aide de révisions manuelles dans la console d’API - Content Moderator
titlesuffix: Azure Cognitive Services
description: Découvrez comment créer des révisions humaines dans la console d’API de Content Moderator.
services: cognitive-services
author: sanjeev3
manager: cgronlun
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: conceptual
ms.date: 08/05/2017
ms.author: sajagtap
ms.openlocfilehash: bb95341a09f09ce8020f34476e720270fd401909
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/26/2018
ms.locfileid: "47219751"
---
# <a name="create-reviews-from-the-api-console"></a>Créer des révisions à partir de la console d’API

Utilisez les [opérations de révision](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4) de l’API Review (Révision) pour créer des révisions de texte ou d’image pour la modération humaine. Les modérateurs utilisent l’outil de révision pour vérifier le contenu. Utilisez cette opération en fonction de votre logique métier post-modération. Utilisez-la une fois que vous avez analysé votre contenu à l’aide des API de texte ou d’image de Content Moderator ou d’autres API Cognitive Services. 

Une fois qu’un modérateur a examiné les balises affectées automatiquement ainsi que les données de prédiction et soumis une décision finale en matière de modération, l’API Review (Révision) soumet toutes les informations à votre point de terminaison d’API.

## <a name="use-the-api-console"></a>Utiliser la console d’API
Pour évaluer l’API à l’aide de la console en ligne, vous devez entrer quelques valeurs dans la console :

- **teamName** : nom de l’équipe que vous avez créée lorsque vous avez configuré le compte de l’outil de révision. 
- **ContentId** : cette chaîne est transmise à l’API et retournée via le rappel. Cet ID est utile pour associer des métadonnées ou des identificateurs internes aux résultats d’un travail de modération.
- **Metadata** : paires clé-valeur personnalisées retournées à votre point de terminaison d’API pendant le rappel. Si la clé est un code court qui est défini dans l’outil de révision, elle s’affiche sous forme d’une balise.
- **Ocp-Apim-Subscription-Key** : clé située dans l’onglet **Paramètres**. Pour plus d’informations, consultez l’article de [présentation](overview.md).

Pour accéder à une console de test, le plus simple est d’utiliser la fenêtre **Informations d’identification**.

1.  Dans la fenêtre **Informations d’identification**, sélectionnez [Review API reference](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4) (Référence de l’API Révision).

  La page **Review - Create** (Révision - Créer) s’ouvre.

2.  Pour l’option **Open API testing console** (Ouvrir la console de test de l’API), sélectionnez la région qui décrit le mieux votre emplacement.

  ![Sélection d’une région dans la page Review - Create (Révision - Créer)](images/test-drive-region.png)

  La console de l’API **Review - Create** (Révision - Créer) s’ouvre.
  
3.  Entrez des valeurs pour les paramètres de requête requis, le type de contenu, puis votre clé d’abonnement. Dans la zone **Corps de la demande**, spécifiez le contenu (par exemple, l’emplacement de l’image), les métadonnées et les autres informations associées au contenu.

  ![Paramètres de la requête, en-têtes et zone Corps de la demande dans la console Review - Create (Révision - Créer)](images/test-drive-review-1.PNG)
  
4.  Sélectionnez **Envoyer**. Un ID de révision est créé. Copiez cet ID pour l’utiliser dans les étapes suivantes.

  ![La zone Contenu de la réponse de la console Review - Create (Révision - Créer) affiche l’ID de révision](images/test-drive-review-2.PNG)
  
5.  Sélectionnez **Obtenir**, puis ouvrez l’API en sélectionnant le bouton qui correspond à votre région. Sur la page qui s’affiche, entrez les valeurs des paramètres **teamName**, **ReviewID** et **Clé d’abonnement**. Sélectionnez le bouton **Envoyer** sur la page. 

  ![Obtention des résultats dans la console Review - Create (Révision - Créer)](images/test-drive-review-3.PNG)
  
6.  Vous observez les résultats de l’analyse.

  ![Zone Contenu de la réponse de la console Review - Create (Révision - Créer)](images/test-drive-review-4.PNG)
  
7.  Dans le tableau de bord Content Moderator, sélectionnez **Réviser** > **Image**. L’image que vous avez analysée s’affiche et peut être révisée.

  ![Image de l’outil de révision présentant un ballon de football](images/test-drive-review-5.PNG)

## <a name="next-steps"></a>Étapes suivantes

Utilisez l’API REST dans votre code ou commencez par le [démarrage rapide de révisions .NET](moderation-reviews-quickstart-dotnet.md) pour en intégrer à votre application.
