---
title: 'Démarrage rapide : Diriger le trafic web à l’aide d’un modèle Resource Manager'
titleSuffix: Azure Application Gateway
description: Découvrez comment utiliser un modèle Resource Manager pour créer une passerelle Azure Application Gateway qui dirige le trafic web vers les machines virtuelles d’un pool de back-ends.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: quickstart
ms.date: 03/23/2020
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 0612371605f6b216eadeef49d9adb1497ba31591
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/26/2020
ms.locfileid: "80148228"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway---resource-manager-template"></a>Démarrage rapide : Diriger le trafic web avec Azure Application Gateway - Modèle Resource Manager

Dans ce guide de démarrage rapide, vous allez utiliser un modèle Resource Manager pour créer une passerelle Azure Application Gateway. Ensuite, vous la testerez pour être sûr qu’elle fonctionne correctement.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Vous pouvez également suivre ce guide de démarrage rapide en utilisant le [portail Azure](quick-create-portal.md), [Azure PowerShell](quick-create-powershell.md) ou [Azure CLI](quick-create-cli.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

## <a name="create-an-application-gateway"></a>Créer une passerelle d’application

Par souci de simplicité, ce modèle crée une configuration simple avec une adresse IP front-end publique, un écouteur de base pour héberger un site unique sur cette passerelle d’application, une règle de routage des requêtes simple et deux machines virtuelles dans le pool de back-ends.

### <a name="review-the-template"></a>Vérifier le modèle

Le modèle utilisé dans ce guide de démarrage rapide est tiré des [modèles de démarrage rapide Azure](https://github.com/Azure/azure-quickstart-templates/blob/master/ag-docs-qs/azuredeploy.json).

:::code language="json" source="~/quickstart-templates/ag-docs-qs/azuredeploy.json" range="001-343" highlight="197-297":::

Plusieurs ressources Azure sont définies dans le modèle :

- [**Microsoft.Network/applicationgateways**](/azure/templates/microsoft.network/applicationgateways)
- [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses) : une pour la passerelle d’application et deux pour les machines virtuelles.
- [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines) : deux machines virtuelles
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces) : deux pour les machines virtuelles
- [**Microsoft.Compute/virtualMachine/extensions**](/azure/templates/microsoft.compute/virtualmachines/extensions) : pour configurer IIS et les pages web


### <a name="deploy-the-template"></a>Déployer le modèle

Déployez le modèle Resource Manager sur Azure :

1. Sélectionnez **Déployer sur Azure** pour vous connecter à Azure et ouvrir le modèle. Le modèle crée une passerelle d’application, l’infrastructure réseau et deux machines virtuelles dans le pool de back-ends exécutant IIS.

   <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fag-docs-qs%2Fazuredeploy.json"><img src="./media/quick-create-template/deploy-to-azure.png" alt="deploy to azure"/></a>

2. Sélectionnez ou créez votre groupe de ressources, puis tapez le nom d’utilisateur et le mot de passe de l’administrateur de machine virtuelle.
3. Sélectionnez **J’accepte les conditions générales mentionnées ci-dessus**, puis **Acheter**. Le déploiement peut prendre 20 minutes ou plus.

## <a name="validate-the-deployment"></a>Valider le déploiement

Même si IIS n’est pas obligatoire pour créer la passerelle d’application, il est installé pour vérifier qu’Azure a bien créé la passerelle d’application. Utilisez IIS pour tester la passerelle d’application :

1. Trouvez l’adresse IP publique de la passerelle d’application dans la page **Vue d’ensemble** correspondante. ![Enregistrez l’adresse IP publique de la passerelle d’application](./media/application-gateway-create-gateway-portal/application-gateway-record-ag-address.png) Vous pouvez aussi sélectionner **Toutes les ressources**, entrer *myAGPublicIPAddress* dans la zone de recherche, puis sélectionner l’adresse IP publique dans les résultats de la recherche. Azure affiche l’adresse IP publique dans la page **Vue d’ensemble**.
2. Copiez l’adresse IP publique, puis collez-la dans la barre d’adresses de votre navigateur pour y accéder.
3. Vérifiez la réponse. Une réponse valide vérifie que la passerelle d’application a bien été créée avec succès et qu’elle est capable de se connecter au back-end.

   ![Tester la passerelle d’application](./media/application-gateway-create-gateway-portal/application-gateway-iistest.png)

   Actualisez plusieurs fois le navigateur ; vous devriez voir les connexions à myVM1 et myVM2.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Quand vous n’avez plus besoin des ressources que vous avez créées avec la passerelle d’application, supprimez le groupe de ressources. Cela supprime la passerelle d’application et toutes les ressources associées.

Pour supprimer le groupe de ressources, appelez l’applet de commande `Remove-AzResourceGroup` :

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Gérer le trafic web avec une passerelle d’application à l’aide d’Azure CLI](./tutorial-manage-web-traffic-cli.md)
