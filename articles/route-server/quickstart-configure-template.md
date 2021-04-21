---
title: "Démarrage rapide : Créer un Serveur de routes Azure à l'aide d'un modèle Azure Resource Manager"
description: Ce guide de démarrage rapide explique comment créer un Serveur de routes Azure à l'aide d'un modèle Azure Resource Manager (modèle ARM).
services: route-server
author: duongau
ms.service: route-server
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 04/05/2021
ms.author: duau
ms.openlocfilehash: 3476e5fa2c274f0fc2c180711480375b0ebefaf2
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107388038"
---
# <a name="quickstart-create-an-azure-route-server-using-an-arm-template"></a>Démarrage rapide : Créer un Serveur de routes Azure à l'aide d'un modèle ARM

Ce guide de démarrage rapide explique comment utiliser un modèle Azure Resource Manager (modèle ARM) pour déployer un Serveur de routes Azure dans un réseau virtuel nouveau ou existant.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Si votre environnement remplit les prérequis et que vous êtes déjà familiarisé avec l’utilisation des modèles ARM, sélectionnez le bouton **Déployer sur Azure**. Le modèle s’ouvre dans le portail Azure.

[![Déployer sur Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-route-server%2Fazuredeploy.json)

## <a name="prerequisites"></a>Prérequis

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="review-the-template"></a>Vérifier le modèle

Le modèle utilisé dans ce démarrage rapide est tiré des [modèles de démarrage rapide Azure](https://azure.microsoft.com/resources/templates/101-route-server).

Dans le cadre de ce guide de démarrage rapide, vous allez déployer un Serveur de routes Azure dans un réseau virtuel nouveau ou existant. Un sous-réseau dédié nommé `RouteServerSubnet` sera créé pour héberger le Serveur de routes. Le Serveur de routes est également configuré avec l'ASN et l'adresse IP de l'homologue pour établir un peering BGP.

:::code language="json" source="~/quickstart-templates/101-route-server/azuredeploy.json" range="001-145" highlight="105-142":::

Plusieurs ressources Azure ont été définies dans le modèle :

* [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualNetworks)
* [**Microsoft.Network/virtualNetworks/subnets**](/azure/templates/microsoft.network/virtualNetworks/subnets) (deux sous-réseaux, dont un nommé `routeserversubnet`)
* [**Microsoft.Network/virtualHubs**](/azure/templates/microsoft.network/virtualhubs) (déploiement du Serveur de routes)
* [**Microsoft.Network/virtualHubs/ipConfigurations**](/azure/templates/microsoft.network/virtualhubs/ipConfigurations)
* [**Microsoft.Network/virtualHubs/bgpConnections**](/azure/templates/microsoft.network/virtualhubs/bgpconnections) (configuration de l'ASN et de l'adresse IP de l'homologue)


Pour rechercher d’autres modèles associés à ExpressRoute, consultez [Modèles de démarrage rapide Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a>Déployer le modèle

1. Sélectionnez **Essayer** à partir du bloc de code suivant pour ouvrir Azure Cloud Shell, puis suivez les instructions pour vous connecter à Azure.

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-route-server/azuredeploy.json"

    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri

    Read-Host -Prompt "Press [ENTER] to continue ..."
    ```

    Patientez jusqu'à ce que vous voyiez l’invite de la console.

1. Sélectionnez **Copier** dans le bloc de code précédent pour copier le script PowerShell.

1. Cliquez sur le volet de la console shell, puis sélectionnez **Coller**.

1. Entrez les valeurs.

    Le nom du groupe de ressources est le nom du projet avec **rg** ajouté.

    Le déploiement du modèle prend environ 20 minutes. Une fois l’opération terminée, le résultat ressemble à ce qui suit :

    :::image type="content" source="./media/quickstart-configure-template/powershell-output.png" alt-text="Sortie du déploiement PowerShell du modèle Resource Manager du Serveur de routes":::

Azure PowerShell est utilisé pour déployer le modèle. Outre Azure PowerShell, vous pouvez également utiliser le portail Azure, l’interface Azure CLI et l’API REST. Pour découvrir d’autres méthodes de déploiement, consultez [Déployer des modèles](../azure-resource-manager/templates/deploy-portal.md).

## <a name="validate-the-deployment"></a>Valider le déploiement

1. Connectez-vous au [portail Azure](https://portal.azure.com).

1. Sélectionnez **Groupes de ressources** dans le volet gauche.

1. Sélectionnez le groupe de ressources que vous avez créé dans la section précédente. Le nom du groupe de ressources par défaut est le nom du projet avec **rg** ajouté.

1. Le groupe de ressources doit uniquement contenir le réseau virtuel :

     :::image type="content" source="./media/quickstart-configure-template/resource-group.png" alt-text="Groupe de ressources de déploiement d'un Serveur de routes avec réseau virtuel.":::

1. Atteindre https://aka.ms/routeserver.

1. Sélectionnez le Serveur de routes nommé **routeserver** pour vérifier que le déploiement s'est bien déroulé.

    :::image type="content" source="./media/quickstart-configure-template/deployment.png" alt-text="Capture d’écran de la page Vue d’ensemble du serveur de routage.":::

## <a name="clean-up-resources"></a>Nettoyer les ressources

Lorsque vous n'avez plus besoin des ressources que vous avez créées avec le Serveur de routes, supprimez le groupe de ressources. Cela supprimera le Serveur de routes et toutes les ressources associées.

Pour supprimer le groupe de ressources, appelez l’applet de commande `Remove-AzResourceGroup` :

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Étapes suivantes

Dans le cadre de ce guide de démarrage rapide, vous avez créé les éléments suivants :

* Serveur de routes
* Réseau virtuel
* Subnet

Après avoir créé le serveur de routage Azure, découvrez à présent de quelle façon Azure Route Server interagit avec les passerelles VPN et ExpressRoute : 

> [!div class="nextstepaction"]
> [Prise en charge d’Azure ExpressRoute et de VPN Azure](expressroute-vpn-support.md)
