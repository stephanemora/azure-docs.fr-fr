---
title: Créer des espaces de travail dans le portail
titleSuffix: Azure Machine Learning
description: Découvrez comment créer, afficher et supprimer des espaces de travail Azure Machine Learning dans le Portail Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: sgilley
author: sdgilley
ms.date: 12/27/2019
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: e2f13cbdca9d6372677bbba24d60f4a73436cfd7
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/31/2020
ms.locfileid: "89179267"
---
# <a name="create-and-manage-azure-machine-learning-workspaces-in-the-azure-portal"></a>Créer et gérer des espaces de travail Azure Machine Learning dans le Portail Azure
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Dans cet article, vous allez créer, afficher et supprimer des [**espaces de travail Azure Machine Learning**](concept-workspace.md) dans le portail Azure pour [Azure Machine Learning](overview-what-is-azure-ml.md).  Le portail est le moyen le plus simple de prendre en main les espaces de travail, mais à mesure que vos besoins évoluent ou que les besoins de l’automatisation augmentent, vous pouvez également créer et supprimer des espaces de travail [à l’aide de l’interface CLI](reference-azure-machine-learning-cli.md), [à l’aide de code Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) ou [via l’extension VS Code](tutorial-setup-vscode-extension.md).

## <a name="create-a-workspace"></a>Créer un espace de travail

Pour créer un espace de travail, vous avez besoin d’un abonnement Azure. Si vous n’avez pas d’abonnement Azure, créez un compte gratuit avant de commencer. Essayez la [version gratuite ou payante d’Azure Machine Learning](https://aka.ms/AMLFree) dès aujourd’hui.

1. Connectez-vous au [Portail Azure](https://portal.azure.com/) à l’aide des informations d’identification de votre abonnement Azure. 

1. En haut à gauche du portail Azure, sélectionnez **+ Créer une ressource**.

      ![Créer une nouvelle ressource](./media/how-to-manage-workspace/create-workspace.gif)

1. Utilisez la barre de recherche pour rechercher **Machine Learning**.

1. Sélectionnez **Machine Learning**.

1. Dans le volet **Machine Learning**, sélectionnez **Créer** pour commencer.

1. Fournissez les informations suivantes pour configurer votre nouvel espace de travail :

   Champ|Description 
   ---|---
   Nom de l’espace de travail |Entrez un nom unique qui identifie votre espace de travail. Dans cet exemple, nous allons utiliser **docs-ws**. Dans le groupe de ressources, les noms doivent être uniques. Utilisez un nom dont il est facile de se rappeler et que vous pouvez facilement différencier des autres espaces de travail. Le nom de l’espace de travail n’est pas sensible à la casse.
   Abonnement |Sélectionnez l’abonnement Azure que vous souhaitez utiliser.
   Resource group | Utilisez un groupe de ressources existant dans votre abonnement, ou entrez un nom pour créer un groupe de ressources. Un groupe de ressources contient les ressources associées d’une solution Azure. Dans cet exemple, nous allons utiliser **docs-aml**. Pour utiliser un groupe de ressources existant, vous devez disposer du rôle de *contributeur* ou de *propriétaire*.  Pour plus d'informations sur l'accès, consultez [Gérer l'accès à un espace de travail Azure Machine Learning](how-to-assign-roles.md).
   Emplacement | Sélectionnez l’emplacement le plus proche de vos utilisateurs et des ressources de données pour créer votre espace de travail.
   Emplacement | Sélectionnez l’emplacement le plus proche de vos utilisateurs et des ressources de données pour créer votre espace de travail.
   Édition de l’espace de travail | Sélectionnez **De base** ou **Entreprise**.  Cette édition de l’espace de travail détermine les fonctionnalités auxquelles vous aurez accès et la tarification. En savoir plus sur les [offres De base et Édition Entreprise](overview-what-is-azure-ml.md#sku). 

    ![Configurer votre espace de travail](./media/how-to-manage-workspace/select-edition.png)

1. Lorsque vous avez terminé de configurer l’espace de travail, sélectionnez **Vérifier + créer**. Si vous le souhaitez, utilisez les sections [Mise en réseau](#networking) et [Avancés](#advanced) pour configurer d’autres paramètres pour l’espace de travail.
2. Passez en revue les paramètres et apportez toute modification ou correction supplémentaire. Lorsque vous êtes satisfait des paramètres, sélectionnez **Créer**.

   > [!Warning] 
   > La création de votre espace de travail dans le cloud peut prendre plusieurs minutes.

   Une fois le processus terminé, un message indiquant la réussite du déploiement s’affiche. 
 
 1. Pour afficher le nouvel espace de travail, sélectionnez **Accéder à la ressource**.


### <a name="networking"></a>Mise en réseau

> [!IMPORTANT]
> Pour plus d’informations sur l’utilisation d’un point de terminaison privé et d’un réseau virtuel avec votre espace de travail, consultez [Isolement réseau et confidentialité](how-to-enable-virtual-network.md).

1. La configuration réseau par défaut consiste à utiliser un __point de terminaison public__, accessible sur l’Internet public. Pour limiter l’accès de votre espace de travail à un réseau virtuel Azure que vous avez créé, vous pouvez à la place sélectionner __Point de terminaison privé__ (préversion) comme __Méthode de connectivité__, puis utiliser __+ Ajouter__ pour configurer le point de terminaison.

   > [!IMPORTANT]
   > L’utilisation d’un point de terminaison privé avec un espace de travail Azure Machine Learning est actuellement en préversion publique. Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

   :::image type="content" source="media/how-to-manage-workspace/select-private-endpoint.png" alt-text="Sélection du point de terminaison privé":::

1. Dans le formulaire __Créer un point de terminaison privé__, définissez l’emplacement, le nom et le réseau virtuel à utiliser. Si vous souhaitez utiliser le point de terminaison avec une zone DNS privée, sélectionnez __Intégrer à une zone DNS privée__, puis choisissez la zone à l’aide du champ __Zone DNS privée__. Cliquez sur __OK__ pour créer le point de terminaison. 

   :::image type="content" source="media/how-to-manage-workspace/create-private-endpoint.png" alt-text="Création d’un point de terminaison privé":::

1. Lorsque vous avez terminé la configuration de la mise en réseau, vous pouvez sélectionner __Vérifier + créer__ ou passer à la configuration facultative __Avancé__.

    > [!WARNING]
    > Lorsque vous créez un point de terminaison privé, une nouvelle zone DNS privée nommée __privatelink.api.azureml.ms__ est créée. Elle contient un lien vers le réseau virtuel. Si vous créez plusieurs espaces de travail avec des points de terminaison privés dans le même groupe de ressources, seul le réseau virtuel du premier point de terminaison privé peut être ajouté à la zone DNS. Pour ajouter des entrées pour les réseaux virtuels utilisés par les autres espaces de travail/points de terminaison privés, procédez comme suit :
    > 
    > 1. Dans le [portail Azure](https://portal.azure.com), sélectionnez le groupe de ressources contenant l’espace de travail. Sélectionnez ensuite la ressource Zone DNS privée nommée __privatelink.api.azureml.ms__.
    > 2. Sous __Paramètres__, sélectionnez __Liens de réseau virtuel__.
    > 3. Sélectionnez __Ajouter__. Dans la page __Ajouter un lien de réseau virtuel__, fournissez un __nom de lien__ unique, puis sélectionnez le __réseau virtuel__ à ajouter. Sélectionnez __OK__ pour ajouter le lien réseau.
    >
    > Pour plus d’informations, consultez [Configuration DNS des points de terminaison privés Azure](/azure/private-link/private-endpoint-dns).

### <a name="advanced"></a>Avancé

Par défaut, les métriques et les métadonnées de l’espace de travail sont stockées dans une instance d’Azure Cosmos DB gérée par Microsoft. Les données sont chiffrées avec des clés managées par Microsoft. 

Pour limiter les données collectées par Microsoft sur votre espace de travail, sélectionnez __Espace de travail à fort impact sur l'activité__.

> [!IMPORTANT]
> Sélectionner High Business Impact ne peut être effectué que lors de la création d’un espace de travail. Vous ne pouvez pas modifier ce paramètre une fois l’espace de travail créé.

Si vous utilisez la version __Enterprise__ d’Azure Machine Learning, vous pouvez fournir votre propre clé. Cela crée l’instance d’Azure Cosmos DB qui stocke les métriques et les métadonnées dans votre abonnement Azure. Pour utiliser votre propre clé, procédez comme suit :

> [!IMPORTANT]
> Avant de commencer, vous devez effectuer les actions suivantes :
>
> 1. Autorisez l’__application Azure Machine Learning__ (dans la gestion des identités et des accès) avec des autorisations de contributeur pour votre abonnement.
> 1. Suivez les étapes décrites dans [Configurer les clés gérées par le client](/azure/cosmos-db/how-to-setup-cmk) pour :
>     * Inscrire le fournisseur Azure Cosmos DB
>     * Créer et configurer un coffre Azure Key Vault
>     * Générer une clé
>
>     Vous n’avez pas besoin de créer manuellement l’instance d’Azure Cosmos DB, l’une sera créée pour vous lors de la création de l’espace de travail. Cette instance de Azure Cosmos DB sera créée dans un groupe de ressources distinct à l’aide d’un nom basé sur ce modèle : `<your-resource-group-name>_<GUID>`.
>
> Vous ne pouvez pas modifier ce paramètre une fois l’espace de travail créé. Si vous supprimez l’Azure Cosmos DB utilisé par votre espace de travail, vous devez également supprimer l’espace de travail qui l’utilise.

1. Sélectionnez __Clés gérées par le client__, puis __Cliquer pour sélectionner la clé__.

    :::image type="content" source="media/how-to-manage-workspace/advanced-workspace.png" alt-text="Clés gérées par le client":::

1. Dans le formulaire __Sélectionner une clé dans Azure Key Vault__, sélectionnez un coffre de clés Azure Key Vault existant, une clé dans ce coffre, et la version de la clé. Cette clé est utilisée pour chiffrer toutes les données stockées dans Azure Cosmos DB. Enfin, cliquez sur le bouton __Sélectionner__ pour utiliser cette clé.

   :::image type="content" source="media/how-to-manage-workspace/select-key-vault.png" alt-text="Sélectionner la clé":::

## <a name="upgrade-to-enterprise-edition"></a><a name="upgrade"></a>Mise à jour vers l’édition Enterprise

Vous pouvez mettre à niveau votre espace de travail de l’édition De base vers l’édition Entreprise pour tirer parti des fonctionnalités améliorées, telles que les expériences à faible niveau de code et les fonctionnalités de sécurité améliorées.

1. Connectez-vous à [Azure Machine Learning Studio](https://ml.azure.com).

1. Sélectionnez l’espace de travail que vous souhaitez mettre à jour.

1. Sélectionnez **En savoir plus** en haut à droite de la page.

   [ ![Mettre à niveau un espace de travail](./media/how-to-manage-workspace/upgrade.png) ](./media/how-to-manage-workspace/upgrade.png#lightbox)

1. Sélectionnez **Mettre à niveau** dans la fenêtre qui s’affiche.


> [!IMPORTANT]
> Vous ne pouvez pas rétrograder un espace de travail Édition Entreprise vers une édition De base.

### <a name="download-a-configuration-file"></a>Télécharger un fichier de configuration

1. Si vous comptez créer une [instance de calcul](tutorial-1st-experiment-sdk-setup.md#azure), ignorez cette étape.

1. Si vous prévoyez d’utiliser du code sur votre environnement local qui référence cet espace de travail, sélectionnez **Télécharger config.json** dans la section **Vue d’ensemble** de l’espace de travail.  

   ![Télécharger config.json](./media/how-to-manage-workspace/configure.png)
   
   Placez le fichier dans la structure de répertoires avec vos scripts Python ou vos notebooks Jupyter. Il peut se trouver dans le même répertoire, dans un sous-répertoire nommé *.azureml* ou dans un répertoire parent. Quand vous créez une instance de calcul, ce fichier est automatiquement ajouté dans le répertoire approprié sur la machine virtuelle.

## <a name="find-a-workspace"></a><a name="view"></a>Trouver un espace de travail

1. Connectez-vous au [portail Azure](https://portal.azure.com/).

1. Dans le champ de recherche du haut, tapez **Machine Learning**.  

1. Sélectionnez **Machine Learning**.

   ![Rechercher l’espace de travail Azure Machine Learning](./media/how-to-manage-workspace/find-workspaces.png)

1. Examinez la liste des espaces de travail trouvés. Vous pouvez filtrer en fonction d’abonnements, de groupes de ressources et de localisations.  

1. Sélectionnez un espace de travail pour afficher ses propriétés.

## <a name="delete-a-workspace"></a>Supprimer un espace de travail

Dans le [portail Azure](https://portal.azure.com/), sélectionnez **Supprimer** en haut de l’espace de travail que vous souhaitez supprimer.

:::image type="content" source="./media/how-to-manage-workspace/delete-workspace.png" alt-text="Supprimer un espace de travail":::

## <a name="clean-up-resources"></a>Nettoyer les ressources

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

## <a name="troubleshooting"></a>Dépannage

### <a name="resource-provider-errors"></a>Erreurs du fournisseur de ressources

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="moving-the-workspace"></a>Déplacement de l’espace de travail

> [!WARNING]
> Le déplacement de votre espace de travail Azure Machine Learning vers un autre abonnement, ou le déplacement de l’abonnement propriétaire vers un nouveau locataire, n’est pas pris en charge. En effet, cela peut provoquer des erreurs.

### <a name="deleting-the-azure-container-registry"></a>Suppression d'Azure Container Registry

L'espace de travail Azure Machine Learning utilise Azure Container Registry (ACR) pour certaines opérations. Il crée automatiquement une instance ACR dès qu'il en a besoin.

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

## <a name="next-steps"></a>Étapes suivantes

Suivez le didacticiel complet pour apprendre à utiliser un espace de travail et créer, faire l’apprentissage et déployer des modèles avec Azure Machine Learning.

> [!div class="nextstepaction"]
> [Tutoriel : Entraîner des modèles](tutorial-train-models-with-aml.md)
