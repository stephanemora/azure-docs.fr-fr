---
title: 'Démarrage rapide : Créer un pare-feu Azure et des groupes IP - Modèle Resource Manager'
description: Dans ce guide de démarrage rapide, découvrez comment utiliser un modèle Azure Resource Manager (modèle ARM) pour créer un pare-feu Azure et des groupes IP.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: quickstart
ms.custom: subject-armqs, devx-track-azurepowershell
ms.date: 05/10/2021
ms.author: victorh
ms.openlocfilehash: 4bb2488bde959281abc5284b3d28b2a7fe3301e3
ms.sourcegitcommit: 190658142b592db528c631a672fdde4692872fd8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/11/2021
ms.locfileid: "112005332"
---
# <a name="quickstart-create-an-azure-firewall-and-ip-groups---arm-template"></a>Démarrage rapide : Créer un pare-feu Azure et des groupes IP - Modèle ARM

Dans ce guide de démarrage rapide, vous utilisez un modèle Azure Resource Manager (modèle ARM) pour déployer un pare-feu Azure avec des exemples de groupes IP utilisés dans une règle de réseau et une règle d’application. Un groupe IP est une ressource de niveau supérieur qui vous permet de définir et de grouper des adresses, plages et sous-réseaux IP en un seul objet. Cette ressource est utile dans la gestion des adresses IP des règles de pare-feu Azure. Vous pouvez entrer manuellement les adresses IP, ou les importer à partir d’un fichier.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Si votre environnement remplit les prérequis et que vous êtes déjà familiarisé avec l’utilisation des modèles ARM, sélectionnez le bouton **Déployer sur Azure**. Le modèle s’ouvre dans le portail Azure.

[![Déployer sur Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.network%2Fazurefirewall-create-with-ipgroups-and-linux-jumpbox%2Fazuredeploy.json)

## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="review-the-template"></a>Vérifier le modèle

Ce modèle crée un pare-feu Azure et des groupes d’adresses IP, ainsi que les ressources nécessaires à la prise en charge du pare-feu Azure.

Le modèle utilisé dans ce démarrage rapide est tiré des [modèles de démarrage rapide Azure](https://azure.microsoft.com/resources/templates/azurefirewall-create-with-ipgroups-and-linux-jumpbox).

:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.network/azurefirewall-create-with-ipgroups-and-linux-jumpbox/azuredeploy.json":::

Plusieurs ressources Azure sont définies dans le modèle :

- [**Microsoft.Network/ipGroups**](/azure/templates/microsoft.network/ipGroups)
- [**Microsoft.Storage/storageAccounts**](/azure/templates/microsoft.storage/storageAccounts)
- [**Microsoft.Network/routeTables**](/azure/templates/microsoft.network/routeTables)
- [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses)
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces)
- [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines)
- [**Microsoft.Network/azureFirewalls**](/azure/templates/microsoft.network/azureFirewalls)

## <a name="deploy-the-template"></a>Déployer le modèle

Déployez le modèle ARM sur Azure :

1. Sélectionnez **Déployer sur Azure** pour vous connecter à Azure et ouvrir le modèle. Le modèle crée un pare-feu Azure, l’infrastructure réseau et deux machines virtuelles.

   [![Déployer sur Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.network%2Fazurefirewall-create-with-ipgroups-and-linux-jumpbox%2Fazuredeploy.json)

2. Dans le portail, affichez la page **Créer un pare-feu Azure avec des groupes IP**, puis tapez ou sélectionnez les valeurs suivantes :
   - Abonnement : sélectionnez à partir d’abonnements existants.
   - Groupe de ressources :  sélectionnez à partir de groupe de ressources existants, ou sélectionnez **Créer**, puis **OK**.
   - Localisation : Sélectionner un emplacement
   - Nom du réseau virtuel : tapez le nom du nouveau réseau virtuel (VNet).
   - Nom du groupe IP 1 : tapez le nom du groupe IP 1
   - Nom du groupe IP 2 : tapez le nom du groupe IP 2
   - Nom de l’utilisateur administrateur : tapez le nom d’utilisateur du compte de l’utilisateur administrateur.
   - Authentification : sélectionnez SshPublicKey ou Mot de passe.
   - Mot de passe administrateur : tapez une clé ou un mot de passe administrateur.

3. Sélectionnez **J’accepte les conditions générales mentionnées ci-dessus**, puis **Acheter**. Le déploiement peut prendre 10 minutes ou plus.

## <a name="review-deployed-resources"></a>Vérifier les ressources déployées

Dans le portail Azure, passez en revue les ressources déployées, en particulier les règles de pare-feu qui utilisent des groupes IP.

:::image type="content" source="media/quick-create-ipgroup-template/ipgroups.png" alt-text="Groupes IP.":::

:::image type="content" source="media/quick-create-ipgroup-template/network-rule.png" alt-text="Règles de réseau.":::

Pour en savoir plus sur les propriétés et la syntaxe JSON d’un pare-feu dans un modèle, consultez [Informations de référence sur le modèle Microsoft.Network/azureFirewalls](/azure/templates/Microsoft.Network/2019-11-01/azureFirewalls).

## <a name="clean-up-resources"></a>Nettoyer les ressources

Quand vous n’avez plus besoin des ressources que vous avez créées avec le pare-feu, supprimez le groupe de ressources. Vous supprimez ainsi le pare-feu et toutes les ressources associées.

Pour supprimer le groupe de ressources, appelez l’applet de commande `Remove-AzResourceGroup` :

```azurepowershell-interactive
Remove-AzResourceGroup -Name "<your resource group name>"
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Tutoriel : Déployer et configurer un pare-feu Azure dans un réseau hybride à l’aide du portail Azure](tutorial-hybrid-portal.md)
