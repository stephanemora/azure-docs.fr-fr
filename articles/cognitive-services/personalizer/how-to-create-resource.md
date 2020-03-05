---
title: Créer une ressource Personalizer
description: La configuration du service porte sur la façon dont le service traite les récompenses, la fréquence à laquelle le service effectue une exploration, la fréquence à laquelle le modèle est réentraîné et la quantité de données stockées.
ms.topic: conceptual
ms.date: 02/19/2020
ms.openlocfilehash: cb14415f3a5950ad1534d9eb8da94198a41f4f91
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/26/2020
ms.locfileid: "77624183"
---
# <a name="create-a-personalizer-resource"></a>Création d’une ressource Personalizer

Une ressource Personalizer est la même chose qu’une boucle d’apprentissage learning loop. Une seule ressource, ou boucle d’apprentissage, est créée pour chacun de vos domaines de sujet et chacune de vos zones de contenu. N’utilisez pas plusieurs zones de contenu dans la même boucle d’apprentissage, car celle-ci s’en trouverait perturbée et produirait de mauvaises prédictions.

Si vous souhaitez que Personalizer sélectionne le meilleur contenu pour plusieurs zones de contenu d’une page web, utilisez une boucle d’apprentissage différente pour chacune.


## <a name="create-a-resource-in-the-azure-portal"></a>Créer une ressource dans le portail Azure

Créez une ressource Personalizer pour chaque boucle de rétroaction.

1. Connectez-vous au [portail Azure](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer). Le lien précédent vous permet d'accéder à la page **Créer** du service Personalizer.
1. Entrez le nom de votre service, puis sélectionnez un abonnement, un emplacement, un niveau tarifaire et un groupe de ressources.

    > [!div class="mx-imgBorder"]
    > ![Utiliser le Portail Azure pour créer une ressource Personalizer, également appelée « boucle d’apprentissage »](./media/how-to-create-resource/how-to-create-personalizer-resource-learning-loop.png)

1. Sélectionnez **Créer** pour créer la ressource.

1. Une fois votre ressource Personalizer déployée, sélectionnez le bouton **Accéder à la ressource** pour y accéder. Accédez à la page **Configuration** de la nouvelle ressource pour [configurer la boucle d’apprentissage](how-to-settings.md).

## <a name="create-a-resource-with-the-azure-cli"></a>Création d’une ressource avec Azure CLI

1. Connectez-vous à Azure CLI avec la commande suivante :

    ```bash
    az login
    ```

1. Créez un groupe de ressources, regroupement logique permettant de gérer toutes les ressources Azure que vous avez l’intention d’utiliser avec la ressource Personalizer.


    ```bash
    az group create \
        --name your-personalizer-resource-group \
        --location westus2
    ```

1. Créez une ressource Personalizer, la _boucle d’apprentissage_, avec la commande suivante pour un groupe de ressources existant.

    ```bash
    az cognitiveservices account create \
        --name your-personalizer-learning-loop \
        --resource-group your-personalizer-resource-group \
        --kind Personalizer \
        --sku F0 \
        --location westus2 \
        --yes
    ```
## <a name="next-steps"></a>Étapes suivantes

* [Configuration](how-to-settings.md) de la boucle d’apprentissage Personalizer