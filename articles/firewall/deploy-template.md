---
title: 'Démarrage rapide : Créer un pare-feu Azure avec Zones de disponibilité - Modèle Resource Manager'
description: 'Déployez Pare-feu Azure à l’aide d’un modèle. Le réseau virtuel a un réseau virtuel avec trois sous-réseaux. Deux machines virtuelles Windows Server sont déployées : un serveur de rebond et un serveur.'
services: firewall
author: vhorne
ms.service: firewall
ms.topic: quickstart
ms.date: 04/30/2020
ms.author: victorh
ms.openlocfilehash: 17ab693033b61c25ba2f5b5bd588ef52caf8c046
ms.sourcegitcommit: acc558d79d665c8d6a5f9e1689211da623ded90a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/30/2020
ms.locfileid: "82597703"
---
# <a name="quickstart-deploy-azure-firewall-with-availability-zones---resource-manager-template"></a>Démarrage rapide : Déployer un pare-feu Azure avec Zones de disponibilité - Modèle Resource Manager

Dans ce guide de démarrage rapide, vous utilisez un modèle Resource Manager pour déployer un pare-feu Azure dans trois zones de disponibilité. 

Le modèle crée un environnement réseau de test avec un pare-feu. Le réseau a un réseau virtuel avec trois sous-réseaux : *AzureFirewallSubnet*, *ServersSubnet* et *JumpboxSubnet*. Les sous-réseaux *ServersSubnet* et *JumpboxSubnet* ont chacun une machine virtuelle Windows Server à deux cœurs.

Le pare-feu se trouve dans le sous-réseau *AzureFirewallSubnet* et a une collection de règles d’application avec une seule règle qui autorise l’accès à `www.microsoft.com`.

Une route définie par l’utilisateur dirige le trafic réseau du sous-réseau *ServersSubnet* à travers le pare-feu, où les règles de pare-feu s’appliquent.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Pour plus d’informations sur le Pare-feu Azure, consultez [Déployer et configurer le Pare-feu Azure à l’aide du portail Azure](tutorial-firewall-deploy-portal.md).

## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-an-azure-firewall-with-availability-zones"></a>Créer un pare-feu Azure avec Zones de disponibilité

Ce modèle crée un pare-feu Azure avec Zones de disponibilité ainsi que les ressources nécessaires à la prise en charge du pare-feu Azure.

### <a name="review-the-template"></a>Vérifier le modèle

Le modèle utilisé dans ce guide de démarrage rapide est tiré des [modèles de démarrage rapide Azure](https://github.com/Azure/azure-quickstart-templates/blob/master/101-azurefirewall-with-zones-sandbox/azuredeploy.json).

:::code language="json" source="~/quickstart-templates/101-azurefirewall-with-zones-sandbox/azuredeploy.json" range="001-444" highlight="369-442":::

Plusieurs ressources Azure sont définies dans le modèle :

- [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses)
- [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines)
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces)
- [**Microsoft.Storage/storageAccounts**](/azure/templates/microsoft.storage/storageAccounts)
- [**Microsoft.Network/azureFirewalls**](/azure/templates/microsoft.network/azureFirewalls)
- [**Microsoft.Network/routeTables**](/azure/templates/microsoft.network/routeTables)

### <a name="deploy-the-template"></a>Déployer le modèle

Déployez le modèle Resource Manager sur Azure :

1. Sélectionnez **Déployer sur Azure** pour vous connecter à Azure et ouvrir le modèle. Le modèle crée un pare-feu Azure, l’infrastructure réseau et deux machines virtuelles.

   [![Déployer sur Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurefirewall-with-zones-sandbox%2Fazuredeploy.json)

2. Dans le portail, dans la page **Créer un programme d’installation de bac à sable de Pare-feu Azure avec zones**, tapez ou sélectionnez les valeurs suivantes :
   - **Groupe de ressources** : Sélectionnez **Créer nouveau**, tapez un nom pour le groupe de ressources et sélectionnez **OK**. 
   - **Nom du réseau virtuel** : Tapez un nom pour le nouveau réseau virtuel. 
   - **Nom d’utilisateur administrateur** : Tapez un nom d’utilisateur pour le compte d’utilisateur administrateur.
   - **Mot de passe administrateur** : Tapez un mot de passe administrateur. 

3. Passez en revue les termes et conditions, cochez la case **J’accepte les termes et conditions mentionnés ci-dessus**, puis cochez la case **Acheter**. Le déploiement peut prendre 10 minutes ou plus.

## <a name="review-deployed-resources"></a>Vérifier les ressources déployées

Explorez les ressources créées avec le pare-feu.

Pour en savoir plus sur la syntaxe JSON et les propriétés d’un pare-feu dans un modèle, consultez [Microsoft.Network/azureFirewalls](/azure/templates/microsoft.network/azurefirewalls).

## <a name="clean-up-resources"></a>Nettoyer les ressources

Quand vous n’en avez plus besoin, vous pouvez supprimer le groupe de ressources, le pare-feu et toutes les ressources associées en exécutant la commande PowerShell `Remove-AzResourceGroup`. Pour supprimer un groupe de ressources nommé *MyResourceGroup*, exécutez : 

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyResourceGroup
```
Ne supprimez pas le groupe de ressources et le pare-feu si vous voulez continuer avec le tutoriel de supervision du pare-feu. 

## <a name="next-steps"></a>Étapes suivantes

Ensuite, vous pouvez surveiller les journaux d’activité de Pare-feu Azure :

[Tutoriel : Superviser les journaux d’activité de Pare-feu Azure](./tutorial-diagnostics.md)
