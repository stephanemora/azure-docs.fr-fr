---
title: Azure NetApp Files pour Azure Government | Microsoft Docs
description: Cet article explique comment se connecter à Azure Government US en vue de l’utiliser avec Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/15/2021
ms.author: b-juche
ms.openlocfilehash: b76895fdd82122413c83ebc017944578001ce0b9
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128700540"
---
# <a name="azure-netapp-files-for-azure-government"></a>Azure NetApp Files pour Azure Government 

[Microsoft Azure Government](../azure-government/documentation-government-welcome.md) fournit un cloud dédié permettant aux administrations et à leurs partenaires de transformer des charges de travail stratégiques dans le cloud.  

Cet article explique comment accéder au service Azure NetApp Files dans Azure Government. 

## <a name="portal"></a>Portail 

Les utilisateurs d’Azure Government peuvent accéder à Azure NetApp Files en pointant leur navigateur sur **portal.azure.us**.Le nom du site de portail est **Microsoft Azure Government**.Pour plus de détails, consultez [Se connecter à Azure Government à l’aide du portail](../azure-government/documentation-government-get-started-connect-with-portal.md).   

![Capture d’écran du portail Azure Government mettant en évidence portal.azure.us comme URL](../media/azure-netapp-files/azure-government.jpg)

À partir du portail Microsoft Azure Government, vous pouvez accéder à Azure NetApp Files de la même façon que vous le feriez dans le portail Azure.Par exemple, vous pouvez entrer **Azure NetApp Files** dans la zone Rechercher des ressources du portail, puis sélectionner **Azure NetApp Files** dans la liste qui s’affiche.  

Vous pouvez suivre la documentation d’[Azure NetApp Files](/azure/azure-netapp-files/) pour en savoir plus sur l’utilisation du service.

## <a name="azure-cli"></a>Azure CLI 

Vous pouvez vous connecter à Azure Government en définissant le nom du cloud sur `AzureUSGovernment`, puis en vous connectant comme vous le feriez normalement avec la commande `az login`. Après l’exécution de la commande de connexion, une fenêtre de navigateur s’ouvre, dans laquelle vous entrez les informations d’identification Azure Government appropriées.  

```azurecli 

az cloud set --name AzureUSGovernment 

``` 

Pour confirmer que le cloud a été défini sur `AzureUSGovernment`, exécutez : 

```azurecli 

az cloud list --output table 

``` 

Cette commande génère un tableau avec des emplacements cloud Azure. La colonne `isActive` pour `AzureUSGovernment` doit indiquer `true`.  

Pour plus d’informations, consultez [Se connecter à Azure Government avec Azure CLI](../azure-government/documentation-government-get-started-connect-with-cli.md).

## <a name="rest-api"></a>API REST

Les points de terminaison Azure Government sont différents des points de terminaison Azure commerciaux. Pour obtenir la liste des différents points de terminaison, consultez l’[Aide pour les développeurs](../azure-government/compare-azure-government-global-azure.md#guidance-for-developers) d’Azure Government.

## <a name="powershell"></a>PowerShell

Quand vous vous connectez à Azure Government par le biais de PowerShell, vous devez spécifier un paramètre environnemental afin d’être sûr de vous connecter aux points de terminaison corrects. À partir de là, vous pouvez continuer à utiliser Azure NetApp Files comme vous le feriez normalement avec PowerShell. 

| Type de connexion | Commande | 
| --- | --- | 
| Commandes [Azure](/powershell/module/az.accounts/Connect-AzAccount) |`Connect-AzAccount -EnvironmentName AzureUSGovernment` | 
| Commandes d’[Azure Active Directory](/powershell/module/azuread/connect-azuread) |`Connect-AzureAD -AzureEnvironmentName AzureUSGovernment` | 
| Commandes [Azure (modèle de déploiement Classic)](/powershell/module/servicemanagement/azure.service/add-azureaccount) |`Add-AzureAccount -Environment AzureUSGovernment` | 
| Commandes [Azure Active Directory (modèle de déploiement classique)](/previous-versions/azure/jj151815(v=azure.100)) |`Connect-MsolService -AzureEnvironment UsGovernment` | 

Pour plus d’informations, consultez [Se connecter à Azure Government avec PowerShell](../azure-government/documentation-government-get-started-connect-with-ps.md).

## <a name="next-steps"></a>Étapes suivantes
* [Qu’est-ce qu’Azure Government ?](../azure-government/documentation-government-welcome.md)
* [Comparer Azure Government et Azure mondial](../azure-government/compare-azure-government-global-azure.md)
* [API REST d’Azure NetApp Files](azure-netapp-files-develop-with-rest-api.md)
* [API REST Azure NetApp Files à l’aide de PowerShell](develop-rest-api-powershell.md)
