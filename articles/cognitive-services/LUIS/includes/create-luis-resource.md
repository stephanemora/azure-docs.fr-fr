---
title: Créer une ressource LUIS
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/07/2020
ms.author: aahi
ms.openlocfilehash: ebfe7e80577b163218a7bc501fa4e3e9b206fd62
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879198"
---
<a name="create-luis-resources"></a>

## <a name="create-luis-resources-in-azure-portal"></a>Créer des ressources LUIS sur le portail Azure

1. Utilisez [ce lien](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne) pour commencer à créer des ressources LUIS sur le portail Azure.

1. Entrez tous les paramètres obligatoires :

    |Nom|Objectif|
    |--|--|
    |Nom d’abonnement| Abonnement auquel la ressource sera facturée.|
    |Resource group| Nom de groupe de ressources personnalisé que vous choisissez ou créez. Les groupes de ressources vous permettent de regrouper des ressources Azure pour l’accès et la gestion.|
    |Nom| Nom personnalisé que vous choisissez, utilisé comme sous-domaine personnalisé pour vos requêtes de point de terminaison de création et de prédiction.|
    |Emplacement de création|Région associée à votre modèle.|
    |Niveau tarifaire de création|Le niveau tarifaire détermine le nombre maximal de transactions par seconde et par mois.|
    |Emplacement du runtime|Région associée à votre runtime de point de terminaison de prédiction publié.|
    |Niveau tarifaire du runtime|Le niveau tarifaire détermine le nombre maximal de transactions par seconde et par mois.|

    > [!div class="mx-imgBorder"]
    > [![Créer la ressource Language Understanding](../media/luis-how-to-azure-subscription/create-resource-in-azure-small.png)](../media/luis-how-to-azure-subscription/create-resource-in-azure-small.png#lightbox)

1. Cliquez sur **Vérifier + créer** et attendez que la ressource soit créée.
1. Une fois les deux ressources créées, toujours dans le portail Azure, sélectionnez la nouvelle ressource de création, puis choisissez **Démarrages rapides** pour obtenir l’**URL du point de terminaison** de création et la **clé** pour créer programmatiquement.

> [!TIP]
> Pour utiliser les ressources, sur le portail LUIS, [affectez-les](../luis-how-to-azure-subscription.md#assign-an-authoring-resource-in-the-luis-portal-for-all-apps).