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
ms.openlocfilehash: 1a732e22d72c36afe11030e42bae529baa35df1a
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38992258"
---
# <a name="deploy-azure-firewall-using-a-template"></a>Déployer un pare-feu Azure à l’aide d’un modèle

[!INCLUDE [firewall-preview-notice](../../includes/firewall-preview-notice.md)]

Les exemples des articles sur le service Pare-feu Azure supposent que vous avez déjà activé la préversion publique de Pare-feu Azure. Pour plus d’informations, consultez [Activer la préversion publique de Pare-feu Azure](public-preview.md).

Ce modèle crée un pare-feu et un environnement réseau de test. Le réseau dispose d’un réseau virtuel, doté de trois sous-réseaux : *AzureFirewallSubnet*, *ServersSubnet* et *JumpboxSubnet*. ServersSubnet et JumpboxSubnet sont chacun équipés d’un serveur Windows Server à 2 cœurs.

Le pare-feu se trouve dans AzureFirewallSubnet et est configuré au moyen d’un regroupement de règles d’application avec une seule règle qui autorise l’accès à www.microsoft.com.

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

