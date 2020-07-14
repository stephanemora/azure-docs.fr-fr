---
title: Créer un laboratoire avec Azure DevTest Labs et un modèle Resource Manager
description: Avec ce tutoriel, vous créez un labo dans Azure DevTest Labs à l’aide d’un modèle Azure Resource Manager. Un administrateur de laboratoire définit un laboratoire, crée dans celui-ci des machines virtuelles et configure des stratégies.
ms.topic: tutorial
ms.date: 06/26/2020
ms.openlocfilehash: 22ba4ffeec22a45c07b096b0a754d08c8230dd8f
ms.sourcegitcommit: 1d9f7368fa3dadedcc133e175e5a4ede003a8413
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/27/2020
ms.locfileid: "85476204"
---
# <a name="tutorial-set-up-a-lab-by-using-azure-devtest-labs-resource-manager-template"></a>Tutoriel : Configurer un labo en utilisant Azure DevTest Labs (modèle Resource Manager)
Dans ce tutoriel, vous allez créer un laboratoire avec une machine virtuelle Windows Server 2019 Datacenter à l’aide d’un modèle Azure Resource Manager. 

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Dans ce tutoriel, vous allez effectuer les actions suivantes :

> [!div class="checklist"]
> * Vérifier le modèle 
> * Déployer le modèle
> * Vérifier le modèle
> * Nettoyer les ressources

Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Prérequis

Aucun.

## <a name="review-the-template"></a>Vérifier le modèle

Le modèle utilisé dans ce guide de démarrage rapide est tiré des [modèles de démarrage rapide Azure](https://azure.microsoft.com/resources/templates/101-dtl-create-lab-windows-vm/).

:::code language="json" source="~/quickstart-templates/101-dtl-create-lab-windows-vm/azuredeploy.json" range="1-97" highlight="51-85":::

Les ressources définies dans le modèle incluent :

- [**Microsoft.DevTestLab/labs**](/azure/templates/microsoft.devtestlab/labs)
- [**Microsoft.DevTestLab labs/virtualnetworks**](/azure/templates/microsoft.devtestlab/labs/virtualnetworks)
- [**Microsoft.DevTestLab labs/virtualmachines**](/azure/templates/microsoft.devtestlab/labs/virtualmachines)

Pour obtenir d’autres exemples de modèles, voir [Modèles de démarrage rapide Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Devtestlab).

## <a name="deploy-the-template"></a>Déployer le modèle
Pour exécuter automatiquement le déploiement, cliquez sur le bouton ci-dessous. 

[![Déployer sur Azure](./media/create-lab-windows-vm-template/deploy-button.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-dtl-create-lab-windows-vm%2Fazuredeploy.json)

1. Créez un **groupe de ressources** pour qu’il soit facile de le nettoyer plus tard.
1. Sélectionnez l’**emplacement** du groupe de ressources. 
1. Entrez le **nom du laboratoire**. 
1. Entrez le **nom de la machine virtuelle**. 
1. Entrez le **nom d’utilisateur** de la personne pouvant accéder à la machine virtuelle. 
1. Entrez le **mot de passe** correspondant à l’utilisateur. 
1. **J’accepte les termes et conditions mentionnés ci-dessus**. 
1. Puis, sélectionnez **Acheter**.

    :::image type="content" source="./media/create-lab-windows-vm-template/deploy-template-page.png" alt-text="Page Modèle de déploiement":::

## <a name="verify-the-deployment"></a>Vérifier le déploiement
1. Sélectionnez **Notifications** en haut pour afficher l’état du déploiement, puis cliquez sur le lien **Déploiement en cours**.

    :::image type="content" source="./media/create-lab-windows-vm-template/deployment-notification.png" alt-text="Notification de déploiement":::
2. Sur la page **Déploiement - Vue d’ensemble**, attendez que le déploiement soit terminé. Cette opération (en particulier la création d’une machine virtuelle) prend un certain temps. Ensuite, sélectionnez **Accéder au groupe de ressources** ou le **nom du groupe de ressources**, comme montré dans l’image suivante : 

    :::image type="content" source="./media/create-lab-windows-vm-template/navigate-resource-group.png" alt-text="Accéder au groupe de ressources":::
3. Sur la page **Groupe de ressources**, la liste des ressources du groupe de ressources est affichée. Vérifiez que vous voyez votre labo de type `DevTest Lab` dans les ressources. Vous voyez également les ressources dépendantes, telles que le réseau virtuel et la machine virtuelle dans le groupe de ressources. 

    :::image type="content" source="./media/create-lab-windows-vm-template/resource-group-home-page.png" alt-text="Page d’accueil du groupe de ressources":::
4. Sélectionnez votre labo dans la liste des ressources pour afficher la page d’accueil de votre laboratoire. Vérifiez que la machine virtuelle Windows Server 2019 Datacenter est visible dans la liste **Mes machines virtuelles**. Dans l’image suivante, la section **Essentials** est réduite. 

    :::image type="content" source="./media/create-lab-windows-vm-template/lab-home-page.png" alt-text="Page d’accueil du laboratoire":::

    > [!IMPORTANT] 
    > Gardez cette page ouverte et suivez les instructions de la section suivante pour nettoyer les ressources et ainsi éviter les coûts d’exécution du laboratoire et de la machine virtuelle sur Azure. Si vous souhaitez suivre le tutoriel suivant pour tester l’accès à la machine virtuelle dans le laboratoire, nettoyez les ressources seulement après. 

## <a name="cleanup-resources"></a>Nettoyer les ressources

1. Tout d’abord, supprimez le laboratoire pour pouvoir supprimer le groupe de ressources. Vous ne pouvez pas supprimer un groupe de ressources dans lequel se trouve un laboratoire. Pour supprimer le labo, sélectionnez **Supprimer** dans la barre d’outils. 

    :::image type="content" source="./media/create-lab-windows-vm-template/delete-lab-button.png" alt-text="Bouton de suppression d’un labo":::
 2. Dans la page de confirmation, tapez le **nom du labo**, puis sélectionnez **Supprimer**. 
 3. Patientez jusqu’à la suppression du laboratoire. Sélectionnez l’icône de la **cloche** pour afficher la notification de l’opération de suppression. Ce processus prend un certain temps. Confirmez la suppression du laboratoire, puis sélectionnez le **groupe de ressources** dans le menu de navigation. 
 
    :::image type="content" source="./media/create-lab-windows-vm-template/confirm-lab-deletion.png" alt-text="Confirmation de la suppression de la machine virtuelle dans les notifications":::
 1. Sur la page **Groupe de ressources**, sélectionnez **Supprimer un groupe de ressources** dans la barre d’outils. Dans le volet de confirmation, tapez le **nom du groupe de ressources**, puis sélectionnez **Supprimer**. Contrôlez les notifications pour vérifier que le groupe de ressources est supprimé.
 
    :::image type="content" source="./media/create-lab-windows-vm-template/delete-resource-group-button.png" alt-text="Bouton Supprimer le groupe de ressources":::

## <a name="next-steps"></a>Étapes suivantes
Dans ce tutoriel, vous avez créé un laboratoire avec une machine virtuelle. Pour savoir comment accéder au laboratoire, passer au tutoriel suivant :

> [!div class="nextstepaction"]
> [Tutoriel : Accéder au laboratoire](tutorial-use-custom-lab.md)

