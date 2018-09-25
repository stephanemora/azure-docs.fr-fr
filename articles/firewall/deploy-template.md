---
title: Déployer un pare-feu Azure à l’aide d’un modèle
description: Déployer un pare-feu Azure à l’aide d’un modèle
services: firewall
author: vhorne
manager: jpconnock
ms.service: firewall
ms.topic: article
ms.date: 7/11/2018
ms.author: victorh
ms.openlocfilehash: d32e6e29c287d140c28206743e36dc025b26158b
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46991332"
---
# <a name="deploy-azure-firewall-using-a-template"></a>Déployer un pare-feu Azure à l’aide d’un modèle

Ce modèle crée un pare-feu et un environnement réseau de test. Le réseau dispose d’un réseau virtuel, doté de trois sous-réseaux : *AzureFirewallSubnet*, *ServersSubnet* et *JumpboxSubnet*. ServersSubnet et JumpboxSubnet sont chacun équipés d’un serveur Windows Server à 2 cœurs.

Le pare-feu se trouve dans AzureFirewallSubnet et est configuré à l’aide d’une collection de règles d’application avec une seule règle qui autorise l’accès à www.microsoft.com.

Un itinéraire défini par l’utilisateur est créé : à partir de ServersSubnet, il pointe en direction du trafic réseau via le pare-feu, où les règles de pare-feu sont appliquées.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="template-location"></a>Emplacement du modèle

Le modèle se trouve ici :

[https://github.com/Azure/azure-quickstart-templates/tree/master/101-azurefirewall-sandbox](https://github.com/Azure/azure-quickstart-templates/tree/master/101-azurefirewall-sandbox)

Lisez l’introduction et une fois que vous êtes prêt pour le déploiement, cliquez sur **Déployer sur Azure**.

## <a name="clean-up-resources"></a>Supprimer les ressources

Explorez d’abord les ressources qui ont été créées avec le pare-feu puis, quand vous n’en avez plus besoin, servez-vous de la commande [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) pour supprimer le groupe de ressources, le pare-feu et toutes les ressources associées.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup
```
## <a name="next-steps"></a>Étapes suivantes

Ensuite, vous pouvez surveiller les journaux de Pare-feu Azure :

- [Tutoriel : Surveiller les journaux de Pare-feu Azure](./tutorial-diagnostics.md)

