---
title: 'Exemple de script Azure PowerShell : gérer le trafic web | Microsoft Docs'
description: 'Exemple de script Azure PowerShell : gérez le trafic web avec une passerelle d’application et un groupe de machines virtuelles identiques.'
services: application-gateway
documentationcenter: networking
author: vhorne
manager: jpconnock
editor: tysonn
tags: azure-resource-manager
ms.service: application-gateway
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 01/29/2018
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: ef6a4171c582e3eb82fbcc73171797e1c806de05
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/03/2018
---
# <a name="manage-web-traffic-with-azure-powershell"></a>Gérer le trafic web avec Azure PowerShell

Ce script permet de créer une passerelle d’application qui utilise un groupe de machines virtuelles identiques pour les serveurs backend. La passerelle d’application peut ensuite être configurée pour gérer le trafic web. Après avoir exécuté le script, vous pouvez tester la passerelle d’application à l’aide de son adresse IP publique.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exemple de script

[!code-powershell[main](../../../powershell_scripts/application-gateway/create-vmss/create-vmss.ps1 "Create application gateway")]

## <a name="clean-up-deployment"></a>Nettoyer le déploiement 

Exécutez la commande suivante pour supprimer le groupe de ressources, la passerelle d’application et toutes les ressources associées.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroupAG
```

## <a name="script-explanation"></a>Explication du script

Ce script a recours aux commandes suivantes pour créer le déploiement. Chaque élément du tableau renvoie à une documentation spécifique.

| Commande | Notes |
|---|---|
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | Crée un groupe de ressources dans lequel toutes les ressources sont stockées. |
| [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) | Crée la configuration de sous-réseau. |
| [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) | Crée le réseau virtuel à l’aide avec les configurations de sous-réseau. |
| [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) | Crée l’adresse IP publique pour la passerelle d’application. |
| [New-AzureRmApplicationGatewayIPConfiguration](/powershell/module/azurerm.network/new-azurermapplicationgatewayipconfiguration) | Crée la configuration qui associe un sous-réseau avec la passerelle d’application. |
| [New-AzureRmApplicationGatewayFrontendIPConfig](/powershell/module/azurerm.network/new-azurermapplicationgatewayfrontendipconfig) | Crée la configuration qui assigne une adresse IP publique à la passerelle d’application. |
| [New-AzureRmApplicationGatewayFrontendPort](/powershell/module/azurerm.network/new-azurermapplicationgatewayfrontendport) | Assigne un port à utiliser pour accéder à la passerelle d’application. |
| [New-AzureRmApplicationGatewayBackendAddressPool](/powershell/module/azurerm.network/new-azurermapplicationgatewaybackendaddresspool) | Crée un pool backend pour une passerelle d’application. |
| [New-AzureRmApplicationGatewayBackendHttpSettings](/powershell/module/azurerm.network/new-azurermapplicationgatewaybackendhttpsettings) | Configure les paramètres d’un pool backend. |
| [New-AzureRmApplicationGatewayHttpListener](/powershell/module/azurerm.network/new-azurermapplicationgatewayhttplistener) | Crée un écouteur. |
| [New-AzureRmApplicationGatewayRequestRoutingRule](/powershell/module/azurerm.network/new-azurermapplicationgatewayrequestroutingrule) | Crée une règle d’acheminement. |
| [New-AzureRmApplicationGatewaySku](/powershell/module/azurerm.network/new-azurermapplicationgatewaysku) | Spécifie le niveau et la capacité d’une passerelle d’application. |
| [New-AzureRmApplicationGateway](/powershell/module/azurerm.network/new-azurermapplicationgateway) | Créer une passerelle d’application |
| [Set-AzureRmVmssStorageProfile](/powershell/module/azurerm.compute/set-azurermvmssstorageprofile) | Crée un profil de stockage pour le groupe identique. |
| [Set-AzureRmVmssOsProfile](/powershell/module/azurerm.compute/set-azurermvmssosprofile) | Définit le système d’exploitation du groupe identique. |
| [Add-AzureRmVmssNetworkInterfaceConfiguration](/powershell/module/azurerm.compute/add-azurermvmssnetworkinterfaceconfiguration) | Définit l’interface réseau du groupe identique. |
| [New-AzureRmVmss](/powershell/module/azurerm.compute/new-azurermvm) | Crée un groupe de machines virtuelles identiques. |
| [Get-AzureRmPublicIPAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) | Obtient l’adresse IP publique d’une passerelle d’application. |
|[Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Supprime un groupe de ressources et toutes les ressources contenues. | 

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le module Azure PowerShell, consultez [Documentation Azure PowerShell](/powershell/azure/overview).

Vous trouverez d’autres exemples de scripts PowerShell de passerelle d’application dans la [documentation sur Azure Application Gateway](../powershell-samples.md).
