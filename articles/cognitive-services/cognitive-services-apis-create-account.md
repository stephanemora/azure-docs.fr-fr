---
title: Créer un compte d’API Cognitive Services sur le Portail Azure | Microsoft Docs
description: Guide pratique de création d’un compte d’API Microsoft Cognitive Services sur le Portail Azure.
services: cognitive-services
documentationcenter: ''
author: garyericson
manager: cgronlun
editor: ''
ms.assetid: b6176bb2-3bb6-4ebf-84d1-3598ee6e01c6
ms.service: cognitive-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/01/2018
ms.author: garye
ms.reviewer: gibattag
ms.openlocfilehash: ed5f19b23375ecb83e19274c7405e9a1208a7985
ms.sourcegitcommit: 04fc1781fe897ed1c21765865b73f941287e222f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/13/2018
ms.locfileid: "39036151"
---
# <a name="create-a-cognitive-services-apis-account-in-the-azure-portal"></a>Créer un compte d’API Cognitive Services sur le Portail Azure

Pour utiliser les API Microsoft Cognitive Services, vous devez d’abord créer un compte sur le Portail Azure.

1. Connectez-vous au [Portail Azure](http://portal.azure.com).

2. Cliquez sur **+ Créer une ressource**.

3. Dans la Place de marché Azure, sélectionnez **IA + Cognitive Services** et découvrez la liste des API disponibles. Cliquez sur **Afficher tout** pour afficher la liste complète des API Cognitive Services. Cliquez sur l’API de votre choix pour continuer.

    ![Sélectionner API Cognitive Services](media/cognitive-services-apis-create-account/select-cognitive-services-apis.png)

4. Sur la page **Créer**, renseignez les informations suivantes :

   - **Nom du compte :** nom du compte. Nous recommandons d’utiliser un nom explicite, par exemple *AFaceAPIAccount*.

   - **Abonnement :** sélectionnez l’un des abonnements Azure disponibles pour lequel vous disposez au moins des autorisations Collaborateur.

   - **Type d’API :** choisissez l’API Cognitive Services que vous souhaitez utiliser. Pour plus d’informations sur les différentes API Cognitive Services disponibles, visitez le site [Cognitive Services](https://azure.microsoft.com/services/cognitive-services/).

   - **Niveau tarifaire :** le coût de votre compte Cognitive Services dépend de l’utilisation réelle et des options que vous choisissez ci-dessous. Pour plus d’informations sur les tarifs de chaque API, consultez les [pages de tarification](https://azure.microsoft.com/pricing/details/cognitive-services/).

   - **Groupe de ressources :** un groupe de ressources désigne une collection de ressources qui partagent un cycle de vie, des autorisations et des stratégies identiques. Pour plus d’informations sur les groupes de ressources, consultez la page [Gérer les ressources Azure sur le portail](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal).

   - **Emplacement du groupe de ressources :** requis uniquement si l’API sélectionnée est globale (non liée à un emplacement). Toutefois, si l’API est globale et non liée à un emplacement, vous devez spécifier un emplacement pour le groupe de ressources où résident les métadonnées associées au compte d’API Cognitive Services. Cet emplacement n’a aucune incidence sur la disponibilité de votre compte au moment de l’exécution. Pour plus d’informations sur le groupe de ressources, consultez la page [Gérer les ressources Azure sur le portail](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal).

   - **Accusé de réception explicite des conditions d'utilisation des services en ligne :** Afin de créer un compte, les propriétaires d’abonnement ou les collaborateurs (tels que définis par le [Contrôle d’accès en fonction du rôle Azure](https://docs.microsoft.com/azure/role-based-access-control/overview)) doivent reconnaître explicitement les conditions d'utilisation qui s’appliquent à Cognitive Services dans [Conditions d'utilisation des services en ligne](https://www.microsoft.com/en-us/Licensing/product-licensing/products.aspx). 

     Le propriétaire de l’abonnement peut désactiver la création du compte Cognitive Services pour un abonnement ou un groupe de ressources spécifique par le biais des [stratégies Azure](../azure-policy/azure-policy-introduction.md) en suivant l’article [Utilisation du portail Azure pour affecter et gérer des stratégies de ressources](../azure-policy/assign-policy-definition.md), en affectant une définition de stratégie « Types de ressources non autorisés » et en spécifiant **Microsoft.CognitiveServices/accounts** comme type de ressource cible.

     Si la création du compte a été désactivée, l’erreur suivante s’affiche au moment de la création du compte :

     ![Erreur de création de compte](media/cognitive-services-apis-create-account/error-message.png)

5. Pour épingler le compte au tableau de bord du Portail Azure, cliquez sur **Épingler au tableau de bord**.

6. Cliquez sur **Créer** pour créer le compte.

Une fois le compte Cognitive Services déployé avec succès, cliquez sur la vignette dans le tableau de bord pour afficher les informations du compte.

Vous pouvez utiliser **l’URL du point de terminaison** dans la section **Vue d’ensemble** et les clés dans la section **Clés** pour effectuer des appels d’API dans vos applications.

![Afficher les informations sur le compte](media/cognitive-services-apis-create-account/display-account.png)

![Afficher les clés du compte](media/cognitive-services-apis-create-account/account-keys.png)

### <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur tous services Microsoft Cognitive Services disponibles, consultez la page [Cognitive Services](https://azure.microsoft.com/services/cognitive-services/).

Pour utiliser quelques exemples d’API Cognitive Services, consultez les guides de démarrage rapide :

 - [Guides de démarrage rapide de Vision par ordinateur en C#](computer-vision/quickstarts/csharp.md)
 - [Analyse de texte avec Python](text-analytics/quickstarts/python.md)
 - [API Visage avec JavaScript](face/quickstarts/javascript.md)
