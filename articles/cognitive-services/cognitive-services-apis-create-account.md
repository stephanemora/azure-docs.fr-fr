---
title: Créer un compte Cognitive Services dans le portail Azure
titlesuffix: Azure Cognitive Services
description: Guide pratique de création d’un compte d’API Azure Cognitive Services sur le Portail Azure.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 06/11/2019
ms.author: aahi
ms.openlocfilehash: b857ee0395c447c8699b8f6a812853528812a7bd
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67445851"
---
# <a name="create-a-cognitive-services-account-using-the-azure-portal"></a>Créer un compte Cognitive Services dans le portail Azure

Dans ce guide de démarrage rapide, vous allez apprendre à vous inscrire à Azure Cognitive Services et à créer un compte disposant d’un abonnement monoservice ou multiservice. Ces services sont représentés par des [ressources](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal) Azure qui vous permettent de vous connecter à une ou plusieurs des API Azure Cognitive Services.

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure valide. [Créez un compte](https://azure.microsoft.com/free/) gratuitement.

[!INCLUDE [cognitive-services-subscription-types](../../includes/cognitive-services-subscription-types.md)]

## <a name="create-a-new-azure-cognitive-services-resource"></a>Créer une ressource Azure Cognitive Services

Avant de créer une ressource, vous devez disposer d’un groupe de ressources Azure. Chaque compte Cognitive Services (et ses ressources Azure associées) doivent appartenir à un groupe de ressources Azure. Quand vous créez un compte, vous avez le choix entre créer un groupe de ressources ou utiliser un groupe existant. Cet article montre comment créer un groupe de ressources.

1. Connectez-vous au [portail Azure](https://portal.azure.com), puis cliquez sur **+ Créer une ressource**.

    ![Sélectionner API Cognitive Services](media/cognitive-services-apis-create-account/azurePortalScreenMulti.png)

2. Vous pouvez trouver les services cognitifs disponibles en procédant comme suit :
    * Utilisez la barre de recherche et entrez le nom du service auquel vous voulez vous abonner.
        * Pour créer une ressource pour un abonnement multiservice, entrez **Cognitive Services** dans la recherche de la barre, puis sélectionnez la ressource **Cognitive Services**.

        ![Rechercher Cognitive Services](media/cognitive-services-apis-create-account/azureCogServSearchMulti.png)

    * Pour afficher tous les services cognitifs disponibles, sélectionnez **IA + Machine Learning** sous **Place de marché Azure**. Si vous ne voyez pas le service qui vous intéresse, cliquez sur **Tout afficher** et faites défiler jusqu'à **Cognitive Services**. Cliquez sur **Plus** pour afficher la totalité du catalogue d’API Cognitive Services.
    
        ![Sélectionner API Cognitive Services](media/cognitive-services-apis-create-account/azureMarketplace.png)

3. Sur la page **Créer**, renseignez les informations suivantes :

    > [!IMPORTANT]
    > Retenez votre emplacement Azure, car vous pourriez en avoir besoin lors de l’appel à Azure Cognitive Services.

    |    |    |
    |--|--|
    | **Nom** | Nom descriptif de votre ressource Cognitive Services. Nous vous recommandons d’utiliser un nom explicite, par exemple *MyCognitiveServicesAccount*. |
    | **Abonnement** | Sélectionnez l’un de vos abonnements Azure disponibles. |
    | **Lieu** | Emplacement de votre instance Cognitive Services. Des emplacements différents peuvent entraîner une latence. Toutefois, cela n’aura pas d’impact sur la disponibilité d’exécution de votre ressource. |
    | **Niveau tarifaire** | Le coût associé à votre compte Cognitive Services dépend des options que vous choisissez, ainsi que de votre utilisation. Pour plus d'informations, consultez le [détail des tarifs](https://azure.microsoft.com/pricing/details/cognitive-services/) de l’API.
    | **Groupe de ressources** | [Groupe de ressources Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/governance/resource-consistency/azure-resource-access#what-is-an-azure-resource-group) comprenant votre ressource Cognitive Services. Vous pouvez créer un groupe ou l’ajouter à un groupe préexistant. |

    ![Écran Création de ressources](media/cognitive-services-apis-create-account/resource_create_screen.png)


## <a name="get-the-keys-for-your-subscription"></a>Obtenir les clés pour votre abonnement

Après avoir créé votre ressource, vous pouvez y accéder à partir du tableau de bord Azure, si vous l’y avez épinglée. Sinon, vous pouvez y accéder dans **Groupes de ressources**. Après avoir sélectionné votre ressource, vous pouvez obtenir les clés en sélectionnant **Clés** sous **Gestion des ressources**.

[!INCLUDE [cognitive-services-environment-variables](../../includes/cognitive-services-environment-variables.md)]

## <a name="clean-up-resources"></a>Supprimer des ressources

Si vous souhaitez nettoyer et supprimer un abonnement Cognitive Services, vous pouvez supprimer la ressource ou le groupe de ressources. La suppression du groupe de ressources efface également les autres ressources liées au groupe de ressources.

Pour supprimer un groupe de ressources dans le portail Azure :

1. Sur le portail Azure, développez le menu de gauche pour ouvrir le menu des services, et sélectionnez **Groupes de ressources** pour afficher la liste de vos groupes de ressources.
2. Recherchez le groupe de ressources à supprimer, puis faites un clic droit sur le bouton Plus (...) se trouvant à droite de la liste.
3. Sélectionnez **Supprimer le groupe de ressources** et confirmez.

## <a name="see-also"></a>Voir aussi

* [Authentifier des requêtes auprès d’Azure Cognitive Services](authentication.md)
* [Présentation d’Azure Cognitive Services](Welcome.md)
* [Prise en charge en langage naturel](language-support.md)
* [Prise en charge des conteneurs Docker](cognitive-services-container-support.md)
