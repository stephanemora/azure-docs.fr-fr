---
title: Créer un compte Cognitive Services dans le portail Azure
titlesuffix: Azure Cognitive Services
description: Comment créer un compte d’API d’Azure Cognitive Services dans le portail Azure.
services: cognitive-services
author: garyericson
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: garye
ms.openlocfilehash: 6950cba5ac958233e7ea77c8dc783ca86cc5a386
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2019
ms.locfileid: "58519882"
---
# <a name="quickstart-create-a-cognitive-services-account-in-the-azure-portal"></a>Démarrage rapide : Créer un compte Cognitive Services dans le portail Azure

Dans ce guide de démarrage rapide, vous allez apprendre à vous inscrire à Azure Cognitive Services et à créer un abonnement monoservice ou multiservice. Ces services sont représentés par des [ressources](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal) Azure qui vous permettent de vous connecter à une ou plusieurs des API Azure Cognitive Services.

## <a name="prerequisites"></a>Conditions préalables

* Un abonnement Azure valide. [Créez un compte](https://azure.microsoft.com/free/) gratuitement.

## <a name="create-and-subscribe-to-an-azure-cognitive-services-resource"></a>Créer et s’abonner à une ressource Azure Cognitive Services

Avant de commencer, il est important de noter qu’il existe deux types d’abonnements Azure Cognitive Services. Le premier est un abonnement à un seul service, comme Vision par ordinateur ou les services Speech. Un abonnement monoservice est limité à une ressource. Le second est un abonnement multiservice à Azure Cognitive Services. Cet abonnement permet d’utiliser la plupart des services Azure Cognitive Services avec un seul abonnement. Cette option permet également de consolider la facturation. Pour plus d’informations, consultez les [tarifs de Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/).

>[!WARNING]
> À ce stade, les services suivants **ne prennent pas en charge** les clés multiservices : QnA Maker Services Speech, Vision personnalisée et détecteur d’anomalies.

Les sections suivantes vous guident tout au long de création d’un abonnement monoservice ou multiservice.


### <a name="multi-service-subscription"></a>Abonnement multiservice

1. Connectez-vous au [portail Azure](https://portal.azure.com), puis cliquez sur **+ Créer une ressource**.

    ![Sélectionner API Cognitive Services](media/cognitive-services-apis-create-account/azurePortalScreenMulti.png)

2. Recherchez la barre de recherche et entrez : **Cognitive Services**.

    ![Rechercher Cognitive Services](media/cognitive-services-apis-create-account/azureCogServSearchMulti.png)

3. Sélectionnez **Cognitive Services**.

    ![Sélectionner Cognitive Services](media/cognitive-services-apis-create-account/azureMarketplaceMulti.png)

3. Sur la page **Créer**, renseignez les informations suivantes :

    |    |    |
    |--|--|
    | **Nom** | Nom descriptif de votre ressource Cognitive Services. Nous vous recommandons d’utiliser un nom explicite, par exemple *MyCognitiveServicesAccount*. |
    | **Abonnement** | Sélectionnez l’un de vos abonnements Azure disponibles. |
    | **Lieu** | Emplacement de votre instance Cognitive Services. Des emplacements différents peuvent entraîner une latence. Toutefois, cela n’aura pas d’impact sur la disponibilité d’exécution de votre ressource. |
    | **Niveau tarifaire** | Le coût associé à votre compte Cognitive Services dépend des options que vous choisissez, ainsi que de votre utilisation. Pour plus d'informations, consultez le [détail des tarifs](https://azure.microsoft.com/pricing/details/cognitive-services/) de l’API.
    | **Groupe de ressources** | [Groupe de ressources Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/getting-started/azure-resource-access#what-is-an-azure-resource-group) comprenant votre ressource Cognitive Services. Vous pouvez créer un groupe ou l’ajouter à un groupe préexistant. |

    ![Écran Création de ressources](media/cognitive-services-apis-create-account/resource_create_screen_multi.png)

### <a name="single-service-subscription"></a>Abonnement monoservice

1. Connectez-vous au [portail Azure](https://portal.azure.com), puis cliquez sur **+ Créer une ressource**.

    ![Sélectionner API Cognitive Services](media/cognitive-services-apis-create-account/azurePortalScreen.png)

2. Sous Place de marché Azure, sélectionnez **IA + Machine Learning**. Si vous ne voyez pas le service qui vous intéresse, cliquez sur **Tout afficher** pour afficher la totalité du catalogue des API Cognitive Services.

    ![Sélectionner API Cognitive Services](media/cognitive-services-apis-create-account/azureMarketplace.png)

3. Sur la page **Créer**, renseignez les informations suivantes :

    |    |    |
    |--|--|
    | **Nom** | Nom descriptif de votre ressource Cognitive Services. Nous recommandons d’utiliser un nom explicite, par exemple *MyNameFaceAPIAccount*. |
    | **Abonnement** | Sélectionnez l’un de vos abonnements Azure disponibles. |
    | **Lieu** | Emplacement de votre instance Cognitive Services. Des emplacements différents peuvent entraîner une latence. Toutefois, cela n’aura pas d’impact sur la disponibilité d’exécution de votre ressource. |
    | **Niveau tarifaire** | Le coût associé à votre compte Cognitive Services dépend des options que vous choisissez, ainsi que de votre utilisation. Pour plus d'informations, consultez le [détail des tarifs](https://azure.microsoft.com/pricing/details/cognitive-services/) de l’API.
    | **Groupe de ressources** | [Groupe de ressources Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/getting-started/azure-resource-access#what-is-an-azure-resource-group) comprenant votre ressource Cognitive Services. Vous pouvez créer un groupe ou l’ajouter à un groupe préexistant. |

    ![Écran Création de ressources](media/cognitive-services-apis-create-account/resource_create_screen.png)

## <a name="access-your-resource"></a>Accéder à votre ressource

> [!NOTE]
> Les propriétaires d’abonnements peuvent désactiver la création de comptes Cognitive Services pour les groupes de ressources et les abonnements, en appliquant une [stratégie Azure](https://docs.microsoft.com/azure/governance/policy/overview#policy-definition), en attribuant la définition de stratégie « Types de ressources non autorisés » et en spécifiant **Microsoft.CognitiveServices/accounts** comme type de ressource cible.

Après avoir créé votre ressource, vous pouvez y accéder à partir du tableau de bord Azure, si vous l’y avez épinglée. Sinon, vous pouvez y accéder dans **Groupes de ressources**.

Dans votre ressource Cognitive Services, vous pouvez utiliser l’URL du point de terminaison et les clés dans la section **Vue d’ensemble** pour effectuer des appels d’API dans vos applications.

![Écran Ressources](media/cognitive-services-apis-create-account/resourceScreen.png)

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Authentifier des requêtes auprès d’Azure Cognitive Services](authentication.md)

## <a name="see-also"></a>Voir aussi

* [Démarrage rapide : Extraire du texte manuscrit à partir d’une image](https://docs.microsoft.com/azure/cognitive-services/computer-vision/quickstarts/csharp-hand-text)
* [Tutoriel : Créer une application pour détecter et encadrer des visages dans une image](https://docs.microsoft.com/azure/cognitive-services/Face/Tutorials/FaceAPIinCSharpTutorial)
* [Créer une page web de recherche personnalisée](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search/tutorials/custom-search-web-page)
* [Intégrer LUIS (Language Understanding) à un bot à l’aide de Bot Framework](https://docs.microsoft.com/azure/cognitive-services/luis/luis-nodejs-tutorial-build-bot-framework-sample)
