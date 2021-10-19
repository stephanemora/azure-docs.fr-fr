---
title: 'Guide pratique : Provisionner un service Relais Azure Fluid'
description: Procédure d’approvisionnement d’un service Relais Azure Fluid à l’aide du portail Azure
services: azure-fluid
author: hickeys
ms.author: hickeys
ms.date: 10/05/2021
ms.topic: article
ms.service: azure-fluid
ms.openlocfilehash: 29397802e0f4e4c4a47e82db8c1ef17bc89c7d9a
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/07/2021
ms.locfileid: "129661837"
---
# <a name="how-to-provision-an-azure-fluid-relay-service"></a>Guide pratique : Provisionner un service Relais Azure Fluid

> [!NOTE]
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.

Pour pouvoir connecter votre application à un serveur Relais Azure Fluid, vous devez approvisionner une ressource de type serveur Relais Azure Fluid sur votre compte Azure. Cet article décrit la procédure permettant d’approvisionner votre service Relais Azure Fluid pour qu’il soit prêt à être utilisé. 

## <a name="prerequisites"></a>Prérequis

Pour créer un service Relais Azure Fluid, vous devez disposer d’un compte Azure. Si vous n’avez pas de compte, vous pouvez [essayer Azure gratuitement](https://azure.com/free).

## <a name="create-a-resource-group"></a>Créer un groupe de ressources
Un groupe de ressources est une collection logique de ressources Azure. Toutes les ressources sont déployées et gérées dans un groupe de ressources. Pour créer un groupe de ressources :

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
2. Dans le volet de navigation de gauche, sélectionnez **Groupes de ressources**. Sélectionnez ensuite **Ajouter**.

    :::image type="content" source="../images/add-resource-group.png" alt-text="Capture d’écran de la page Groupe de ressources du portail Azure.":::

3. Sous Abonnement, sélectionnez le nom de l’abonnement Azure dans lequel vous souhaitez créer le groupe de ressources.

    :::image type="content" source="../images/create-resource-group.png" alt-text="Capture d’écran de la page Créer un groupe de ressources du portail Azure.":::

1. Tapez un nom unique pour le groupe de ressources. Le système vérifie immédiatement si le nom est disponible dans l’abonnement Azure actuellement sélectionné.
1. Sélectionnez une région pour le groupe de ressources.
1. Sélectionnez **Vérifier + créer**.
1. Dans la page Vérifier + créer, sélectionnez **Créer**.

## <a name="create-a-fluid-relay-resource"></a>Créer une ressource Relais Fluid
Chaque ressource de type serveur Relais Azure Fluid fournit un locataire que vous pouvez utiliser dans votre application Fluid. Au sein de ce locataire, vous pouvez créer de nombreux conteneurs/sessions. Pour créer un Relais Fluid dans votre groupe de ressources à l’aide du portail :

1. Dans le portail Azure, sélectionnez **Créer une ressource** en haut à gauche de l’écran.
2. Recherchez « Fluid ».
 
    :::image type="content" source="../images/marketplace-fluid-relay.png" alt-text="Capture d’écran de la page Créer une ressource avec les résultats de la recherche pour le terme « Fluid ».":::

3. Sélectionnez **Relais Fluid**, puis sélectionnez **Créer**.
 
    :::image type="content" source="../images/fluid-relay-details-page.png" alt-text="Capture d’écran de la page Détails de la Place de marché Relais Azure Fluid.":::

4. Sur la page Créer, procédez comme suit :

    :::image type="content" source="../images/create-fluid-relay-server.png" alt-text="Capture d’écran de la procédure de configuration d’un nouveau serveur Relais Azure Fluid.":::

    1. Sélectionnez l’abonnement dans lequel vous souhaitez créer l’espace de noms.
    2. Sélectionnez le groupe de ressources que vous avez créé à l’étape précédente.
    3. Entrez un nom pour la ressource Relais Fluid.
    4. Sélectionnez un emplacement pour l’espace de noms.
    
    > [!NOTE]
    > Pendant la préversion publique, les seules régions prises en charge sont USA Ouest 2, Europe Ouest et Asie Sud-Est

5. Cliquez sur le bouton **Vérifier + créer** au bas de la page.

6. Dans la page Vérifier + créer, passez en revue les paramètres, puis sélectionnez *Créer*. Attendez la fin du déploiement.

    :::image type="content" source="../images/create-server-validation-complete.png" alt-text="Capture d’écran de la page du nouveau service une fois la validation terminée.":::

7. Dans la page Déploiement, sélectionnez **Accéder à la ressource** pour accéder à la page de votre espace de noms.

    :::image type="content" source="../images/deployment-complete.png" alt-text="Capture d’écran du portail Azure indiquant que le déploiement est terminé.":::

8. Confirmez que vous voyez la page Relais Fluid telle que dans cet exemple.

    :::image type="content" source="../images/resource-details.png" alt-text="Capture d’écran d’un exemple de page des détails pour une ressource Relais Fluid déployée.":::

## <a name="next-steps"></a>Étapes suivantes
Vous venez de créer un groupe de ressources et d’approvisionner une ressource Relais Azure Fluid dans ce groupe. Ensuite, vous pouvez vous [connecter à votre service Relais Azure Fluid dans votre application](../quickstarts/quickstart-dice-roll.md).
