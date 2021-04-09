---
title: 'Démarrage rapide : Créer un pare-feu Azure et une stratégie de pare-feu - Modèle Resource Manager'
description: Dans ce guide de démarrage rapide, vous déployez un pare-feu Azure et une stratégie de pare-feu.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 02/17/2021
ms.author: victorh
ms.openlocfilehash: 9cc263d311bd550a92a0c8f14ab5ce86d72e9ee3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100633629"
---
# <a name="quickstart-create-an-azure-firewall-and-a-firewall-policy---arm-template"></a>Démarrage rapide : Créer un pare-feu Azure et une stratégie de pare-feu - Modèle ARM

Dans ce guide de démarrage rapide, vous utilisez un modèle Azure Resource Manager (modèle ARM) pour créer un pare-feu Azure et une stratégie de pare-feu. La stratégie de pare-feu a une règle d’application qui autorise les connexions à `www.microsoft.com` et une règle qui autorise les connexions à Windows Update en utilisant la balise de nom de domaine complet **WindowsUpdate**. Une règle réseau autorise les connexions UDP à un serveur de temps sur 13.86.101.172.

En outre, des groupes IP sont utilisés dans les règles pour définir les adresses IP **Source**.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Pour plus d’informations sur Azure Firewall Manager, consultez [Qu’est-ce qu’Azure Firewall Manager ?](overview.md).

Pour plus d’informations sur le Pare-feu Azure, consultez [Qu’est-ce que le Pare-feu Azure ?](../firewall/overview.md).

Pour plus d’informations sur les groupes IP, consultez [Groupes IP dans le Pare-feu Azure](../firewall/ip-groups.md).

Si votre environnement remplit les prérequis et que vous êtes déjà familiarisé avec l’utilisation des modèles ARM, sélectionnez le bouton **Déployer sur Azure**. Le modèle s’ouvre dans le portail Azure.

[![Déployer sur Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurefirewall-create-with-firewallpolicy-apprule-netrule-ipgroups%2Fazuredeploy.json)

## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="review-the-template"></a>Vérifier le modèle

Ce modèle crée un réseau virtuel de type hub, ainsi que les ressources nécessaires au scénario.

Le modèle utilisé dans ce démarrage rapide est tiré des [modèles de démarrage rapide Azure](https://azure.microsoft.com/resources/templates/101-azurefirewall-create-with-firewallpolicy-apprule-netrule-ipgroups/).

:::code language="json" source="~/quickstart-templates/101-azurefirewall-create-with-firewallpolicy-apprule-netrule-ipgroups/azuredeploy.json":::

Plusieurs ressources Azure sont définies dans le modèle :

- [**Microsoft.Network/ipGroups**](/azure/templates/microsoft.network/ipGroups)
- [**Microsoft.Network/firewallPolicies**](/azure/templates/microsoft.network/firewallPolicies)
- [**Microsoft.Network/firewallPolicies/ruleCollectionGroups**](/azure/templates/microsoft.network/firewallPolicies/ruleCollectionGroups)
- [**Microsoft.Network/azureFirewalls**](/azure/templates/microsoft.network/azureFirewalls)
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses)

## <a name="deploy-the-template"></a>Déployer le modèle

Déployez le modèle ARM sur Azure :

1. Sélectionnez **Déployer sur Azure** pour vous connecter à Azure et ouvrir le modèle. Le modèle crée un Pare-feu Azure, un réseau WAN virtuel et un hub virtuel, l’infrastructure réseau et deux machines virtuelles.

   [![Déployer sur Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurefirewall-create-with-firewallpolicy-apprule-netrule-ipgroups%2Fazuredeploy.json)

2. Dans le portail, dans la page **Créer un pare-feu et une stratégie de pare-feu avec des règles et des groupes IP**, puis tapez ou sélectionnez les valeurs suivantes :
   - Abonnement : Sélectionnez-le dans les abonnements existants.
   - Groupe de ressources :  sélectionnez à partir de groupe de ressources existants, ou sélectionnez **Créer**, puis **OK**.
   - Région : Sélectionnez une région.
   - Nom du pare-feu : tapez un nom pour le pare-feu.

3. Sélectionnez **Vérifier + créer**, puis **Créer**. Le déploiement peut prendre 10 minutes ou plus.

## <a name="review-deployed-resources"></a>Vérifier les ressources déployées

Une fois le déploiement terminé, vous verrez les ressources similaires suivantes.

:::image type="content" source="media/quick-firewall-policy/qs-deployed-resources.png" alt-text="Ressources déployées":::

## <a name="clean-up-resources"></a>Nettoyer les ressources

Quand vous n’avez plus besoin des ressources que vous avez créées avec le pare-feu, supprimez le groupe de ressources. Vous supprimez ainsi le pare-feu et toutes les ressources associées.

Pour supprimer le groupe de ressources, appelez l’applet de commande `Remove-AzResourceGroup` :

```azurepowershell-interactive
Remove-AzResourceGroup -Name "<your resource group name>"
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Vue d’ensemble de la stratégie Azure Firewall Manager](policy-overview.md)
