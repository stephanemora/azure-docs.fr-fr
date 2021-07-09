---
title: Nouveautés - Personalizer
titleSuffix: Azure Cognitive Services
description: Cet article contient des actualités sur Personalizer.
manager: nitinme
services: cognitive-services
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: overview
ms.date: 05/28/2021
ms.openlocfilehash: e181dd8618d571a2aea4af5475f8c6ac06a94f8b
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/29/2021
ms.locfileid: "110698711"
---
# <a name="whats-new-in-personalizer"></a>Nouveautés de Personalizer

Découvrir les nouveautés du service. Ces éléments peuvent inclure des notes de publication, des vidéos, des billets de blog et d’autres types d’informations. Marquez cette page pour rester aux faits des nouveautés du service.

## <a name="release-notes"></a>Notes de publication

### <a name="may-2021----build-conference"></a>Mai 2021 - //Conférence Build

* Optimisation automatique (préversion) : vous pouvez configurer une boucle Personalizer dans le but de vous améliorer continuellement avec moins d’efforts. Personalizer exécutera automatiquement les évaluations hors connexion, puis il découvrira les meilleurs paramètres de machine learning et les appliquera. Pour plus d’informations, consultez [Optimisation automatique (préversion) de Personalizer](concept-auto-optimization.md).
* Personnalisation multi-emplacement (préversion) : si vous avez des mises en page en mosaïque, des carrousels et/ou des barres latérales, il est plus facile d’utiliser Personalizer pour chaque emplacement où vous recommandez des produits ou du contenu sur une même page. Personalizer peut maintenant prendre une liste d’emplacements dans l’API Rank, affecter une action à chacun et apprendre des scores de récompense que vous envoyez pour chaque emplacement. Pour plus d’informations, consultez [Personnalisation multi-emplacement (préversion)](concept-multi-slot-personalization.md).
* Personalizer est désormais disponible dans plusieurs régions.
* Exemples de code mis à jour (GitHub) et documentation Utilisez les liens ci-dessous pour voir les exemples mis à jour :
  * [C#/.NET](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/Personalizer)
  * [JavaScript](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/javascript/Personalizer)
  * [Exemples Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/python/Personalizer)

### <a name="july-2020"></a>Juillet 2020

* Nouveau tutoriel – [Utilisation de Personalizer dans un chatbot](tutorial-use-personalizer-chat-bot.md)

### <a name="june-2020"></a>Juin 2020

* Nouveau tutoriel – [Utilisation de Personalizer dans une application web](tutorial-use-personalizer-web-app.md)

### <a name="may-2020---build-conference"></a>Mai 2020 - //Conférence Build

Les fonctionnalités suivantes sont disponibles en **préversion publique** :

 * [Mode apprenti](concept-apprentice-mode.md) en tant que comportement d'apprentissage.

### <a name="march-2020"></a>Mars 2020

* La sécurité TLS 1.2 est maintenant appliquée pour toutes les requêtes HTTP adressées à ce service. Pour plus d’informations, consultez [Sécurité Azure Cognitive Services](../cognitive-services-security.md).

### <a name="november-2019---ignite-conference"></a>Novembre 2019 - Conférence Ignite

* Personalizer est mis à la disposition générale
* [Didacticiel](tutorial-use-azure-notebook-generate-loop-data.md) Azure Notebooks tout au long du cycle de vie

### <a name="may-2019---build-conference"></a>Mai 2019 - //Conférence Build

Les fonctionnalités d’évaluation suivantes ont été publiées lors de la conférence Build 2019 :

* [Boucle d’apprentissage du classement et de la récompense](what-is-personalizer.md)

## <a name="videos"></a>Vidéos

### <a name="2019-build-videos"></a>Vidéos Build 2019

* [Fournir des expériences et du contenu appropriés comme Xbox avec Cognitive Services Personalizer](https://azure.microsoft.com/resources/videos/build-2019-deliver-the-right-experiences-and-content-with-cognitive-services-personalizer/)

## <a name="service-updates"></a>Mises à jour de service

[Annonces de mise à jour Azure pour Cognitive Services](https://azure.microsoft.com/updates/?product=cognitive-services)

## <a name="next-steps"></a>Étapes suivantes

* [Démarrage rapide : Créer une boucle de rétroaction dans C#](./quickstart-personalizer-sdk.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp)
* [Utiliser la démonstration interactive](https://personalizationdemo.azurewebsites.net/)