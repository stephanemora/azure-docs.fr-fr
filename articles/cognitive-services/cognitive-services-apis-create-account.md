---
title: Créer un compte Cognitive Services dans le portail Azure
titleSuffix: Azure Cognitive Services
description: Commencez à utiliser Azure Cognitive Services en créant et en vous abonnant à une ressource dans le Portail Azure.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 07/16/2019
ms.author: aahi
ms.openlocfilehash: af01c0c2586ce7df1902a0bcc502c6fd06a5215d
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/31/2019
ms.locfileid: "68697912"
---
# <a name="create-a-cognitive-services-resource-using-the-azure-portal"></a>Créer un compte Cognitive Services dans le portail Azure

Utilisez ce guide de démarrage rapide pour commencer à utiliser Azure Cognitive Services à l’aide du portail Azure. Les services Cognitive Services sont représentés par des [ressources](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal) Azure que vous créez dans votre abonnement Azure. Après avoir créé la ressource, utilisez les clés et le point de terminaison générés pour authentifier vos applications. 

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure valide : [créez-en un gratuitement](https://azure.microsoft.com/free/).

[!INCLUDE [cognitive-services-subscription-types](../../includes/cognitive-services-subscription-types.md)]

## <a name="create-a-new-azure-cognitive-services-resource"></a>Créer une ressource Azure Cognitive Services

Avant de créer une ressource Cognitive Services, vous devez disposer d’un groupe de ressources Azure pour contenir la ressource. Quand vous créez une ressource, vous avez le choix entre créer un groupe de ressources ou utiliser un groupe existant. Cet article montre comment créer un groupe de ressources.

1. Connectez-vous au [portail Azure](https://portal.azure.com), puis cliquez sur **+ Créer une ressource**.

    ![Sélectionner API Cognitive Services](media/cognitive-services-apis-create-account/azurePortalScreenMulti.png)

2. Vous pouvez trouver les services cognitifs disponibles en procédant comme suit :
    * Utilisez la barre de recherche et entrez le nom du service auquel vous voulez vous abonner.
        * Pour créer une ressource pour un abonnement multiservice, entrez **Cognitive Services** dans la barre de recherche, puis sélectionnez la ressource **Cognitive Services**.

        ![Rechercher Cognitive Services](media/cognitive-services-apis-create-account/azureCogServSearchMulti.png)

    * Pour afficher tous les services cognitifs disponibles, sélectionnez **IA + Machine Learning** sous **Place de marché Azure**. Si vous ne voyez pas le service qui vous intéresse, cliquez sur **Tout afficher** et faites défiler jusqu'à **Cognitive Services**. Cliquez sur **Plus** pour afficher la totalité du catalogue d’API Cognitive Services.
    
        ![Sélectionner API Cognitive Services](media/cognitive-services-apis-create-account/azureMarketplace.png)

3. Sur la page **Créer**, renseignez les informations suivantes :

    > [!IMPORTANT]
    > Retenez votre emplacement Azure, car vous pourriez en avoir besoin lors de l’appel à Azure Cognitive Services.

    |    |    |
    |--|--|
    | **Nom** | Nom descriptif de votre ressource Cognitive Services. Par exemple *MyCognitiveServicesAccount*. |
    | **Abonnement** | Sélectionnez l’un de vos abonnements Azure disponibles. |
    | **Lieu** | Emplacement de votre instance Cognitive Services. Des emplacements différents peuvent entraîner une latence. Toutefois, cela n’aura pas d’impact sur la disponibilité d’exécution de votre ressource. |
    | **Niveau tarifaire** | Le coût associé à votre compte Cognitive Services dépend des options que vous choisissez, ainsi que de votre utilisation. Pour plus d'informations, consultez le [détail des tarifs](https://azure.microsoft.com/pricing/details/cognitive-services/) de l’API.
    | **Groupe de ressources** | [Groupe de ressources Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/governance/resource-consistency/azure-resource-access#what-is-an-azure-resource-group) comprenant votre ressource Cognitive Services. Vous pouvez créer un groupe ou l’ajouter à un groupe préexistant. |

    ![Écran Création de ressources](media/cognitive-services-apis-create-account/resource_create_screen.png)


## <a name="get-the-keys-for-your-resource"></a>Obtenir les clés pour votre ressource

Après avoir créé votre ressource, vous pouvez y accéder à partir du tableau de bord Azure, si vous l’y avez épinglée. Sinon, vous pouvez y accéder dans **Groupes de ressources**. Après avoir sélectionné votre ressource, vous pouvez obtenir les clés en sélectionnant **Clés** sous **Gestion des ressources**.

[!INCLUDE [cognitive-services-environment-variables](../../includes/cognitive-services-environment-variables.md)]

## <a name="pricing-tiers-and-billing"></a>Niveaux tarifaires et facturation

Les niveaux tarifaires (et le montant facturé) sont basés sur le nombre de transactions que vous envoyez à l’aide de vos informations d’authentification. Chaque niveau tarifaire spécifie :
* le nombre maximal de transactions par seconde (TPS) autorisées ;
* les fonctionnalités de service activées dans le niveau tarifaire ;
* le coût d’un nombre prédéfini de transactions. Si vous dépassez ce nombre, des frais supplémentaires vous seront facturés, comme indiqué dans les [détails de la tarification](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) de votre service.

## <a name="clean-up-resources"></a>Supprimer des ressources

Si vous souhaitez nettoyer et supprimer un abonnement Cognitive Services, vous pouvez supprimer la ressource ou le groupe de ressources. La suppression du groupe de ressources efface également toutes les autres ressources se trouvant dans le groupe.

Pour supprimer un groupe de ressources dans le portail Azure :

1. Sur le portail Azure, développez le menu de gauche pour ouvrir le menu des services, et sélectionnez **Groupes de ressources** pour afficher la liste de vos groupes de ressources.
2. Recherchez le groupe de ressources à supprimer, puis faites un clic droit sur le bouton Plus (...) se trouvant à droite de la liste.
3. Sélectionnez **Supprimer le groupe de ressources** et confirmez.

## <a name="see-also"></a>Voir aussi

* [Authentifier des requêtes auprès d’Azure Cognitive Services](authentication.md)
* [Présentation d’Azure Cognitive Services](Welcome.md)
* [Prise en charge en langage naturel](language-support.md)
* [Prise en charge des conteneurs Docker](cognitive-services-container-support.md)
