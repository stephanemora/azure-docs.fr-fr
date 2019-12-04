---
title: Déployer un pare-feu Azure à l’aide d’un modèle
description: Déployez Pare-feu Azure à l’aide d’un modèle. Le réseau créé possède un réseau virtuel avec trois sous-réseaux. Deux machines virtuelles Windows Server à deux cœurs sont déployées.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: b9e82998283bf44eede88148a12d88aae521b1ca
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74169196"
---
# <a name="deploy-azure-firewall-using-a-template"></a>Déployer un pare-feu Azure à l’aide d’un modèle

Le [modèle Create AzureFirewall sandbox setup](https://github.com/Azure/azure-quickstart-templates/tree/master/101-azurefirewall-with-zones-sandbox) crée un environnement réseau de test avec un pare-feu. Le réseau a un réseau virtuel avec trois sous-réseaux : *AzureFirewallSubnet*, *ServersSubnet* et *JumpboxSubnet*. Les sous-réseaux *ServersSubnet* et *JumpboxSubnet* ont chacun une machine virtuelle Windows Server à deux cœurs.

Le pare-feu se trouve dans le sous-réseau *AzureFirewallSubnet* et a une collection de règles d’application avec une seule règle qui autorise l’accès à `www.microsoft.com`.

Une route définie par l’utilisateur dirige le trafic réseau du sous-réseau *ServersSubnet* à travers le pare-feu, où les règles de pare-feu s’appliquent.

Pour plus d’informations sur le Pare-feu Azure, consultez [Déployer et configurer le Pare-feu Azure à l’aide du portail Azure](tutorial-firewall-deploy-portal.md).


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="use-the-template-to-deploy-azure-firewall"></a>Utiliser le modèle pour déployer le Pare-feu Azure

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

**Pour installer et déployer le Pare-feu Azure à l’aide du modèle :**

1. Accédez au modèle sur [https://github.com/Azure/azure-quickstart-templates/tree/master/101-azurefirewall-with-zones-sandbox](https://github.com/Azure/azure-quickstart-templates/tree/master/101-azurefirewall-with-zones-sandbox).
   
1. Lisez l’introduction et, une fois que vous êtes prêt pour le déploiement, sélectionnez **Déployer sur Azure**.
   
1. Connectez-vous au portail Azure si nécessaire. 

1. Dans le portail, dans la page **Créer un programme d’installation de bac à sable d’AzureFirewall**, tapez ou sélectionnez les valeurs suivantes :
   
   - **Groupe de ressources** : Sélectionnez **Créer nouveau**, tapez un nom pour le groupe de ressources et sélectionnez **OK**. 
   - **Nom du réseau virtuel** : Tapez un nom pour le nouveau réseau virtuel. 
   - **Nom d’utilisateur administrateur** : Tapez un nom d’utilisateur pour le compte d’utilisateur administrateur.
   - **Mot de passe administrateur** : Tapez un mot de passe administrateur. 
   
1. Lisez les conditions générales, puis cochez la case **J’accepte les conditions générales mentionnées ci-dessus**.
   
1. Sélectionnez **Achat**.
   
   La création des ressources prend quelques minutes. 
   
1. Explorez les ressources créées avec le pare-feu. 

Pour en savoir plus sur la syntaxe JSON et les propriétés d’un pare-feu dans un modèle, consultez [Microsoft.Network/azureFirewalls](/azure/templates/microsoft.network/azurefirewalls).

## <a name="clean-up-resources"></a>Supprimer des ressources

Quand vous n’en avez plus besoin, vous pouvez supprimer le groupe de ressources, le pare-feu et toutes les ressources associées en exécutant la commande PowerShell [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup). Pour supprimer un groupe de ressources nommé *MyResourceGroup*, exécutez : 

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyResourceGroup
```
Ne supprimez pas encore le groupe de ressources et le pare-feu si vous voulez continuer avec le tutoriel de supervision du pare-feu. 

## <a name="next-steps"></a>Étapes suivantes

Ensuite, vous pouvez surveiller les journaux d’activité de Pare-feu Azure :

> [!div class="nextstepaction"]
> [Tutoriel : Superviser les journaux d’activité de Pare-feu Azure](./tutorial-diagnostics.md)
