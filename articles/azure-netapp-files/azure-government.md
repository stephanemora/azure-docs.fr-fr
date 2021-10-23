---
title: Azure NetApp Files pour Azure Government | Microsoft Docs
description: Décrit comment se connecter à Azure Government pour utiliser Azure NetApp Files et la disponibilité des fonctionnalités Azure NetApp Files dans Azure Government.
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
ms.date: 10/14/2021
ms.author: b-juche
ms.openlocfilehash: 3e820abf824a9593c265fd7d9d4c6ccd9be5cb38
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130047586"
---
# <a name="azure-netapp-files-for-azure-government"></a>Azure NetApp Files pour Azure Government 

[Microsoft Azure Government](../azure-government/documentation-government-welcome.md) fournit un cloud dédié permettant aux administrations et à leurs partenaires de transformer des charges de travail stratégiques dans le cloud.  

Cet article décrit la disponibilité des fonctionnalités Azure NetApp Files dans Azure Government. Il explique également comment accéder au service Azure NetApp Files dans Azure Government.

## <a name="feature-availability"></a>Disponibilité des fonctionnalités

Pour connaître les régions Azure Government prises en charge par Azure NetApp Files, consultez la *[page Disponibilité des produits par région](https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-texas,usgov-virginia)* .  

Toutes les [fonctionnalités Azure NetApp Files](whats-new.md) disponibles sur le cloud public Azure sont également disponibles sur les régions Azure Government prises en charge, ***à l’exception des fonctionnalités répertoriées dans le tableau suivant*** : 

| Fonctionnalités Azure NetApp Files | Disponibilité sur le cloud public Azure |  Disponibilité sur Azure Government |
|:--- |:--- |:--- |
| Réplication interrégion Azure NetApp Files | Disponibilité générale (GA) | [Limité](cross-region-replication-introduction.md#supported-region-pairs) |
| Sauvegarde Azure NetApp Files | Préversion publique | Non |

## <a name="portal-access"></a>Accès au portail

Les utilisateurs d’Azure Government peuvent accéder à Azure NetApp Files en pointant leur navigateur sur **portal.azure.us**.Le nom du site de portail est **Microsoft Azure Government**.Pour plus de détails, consultez [Se connecter à Azure Government à l’aide du portail](../azure-government/documentation-government-get-started-connect-with-portal.md).   

![Capture d’écran du portail Azure Government mettant en évidence portal.azure.us comme URL](../media/azure-netapp-files/azure-government.jpg)

À partir du portail Microsoft Azure Government, vous pouvez accéder à Azure NetApp Files de la même façon que vous le feriez dans le portail Azure.Par exemple, vous pouvez entrer **Azure NetApp Files** dans la zone Rechercher des ressources du portail, puis sélectionner **Azure NetApp Files** dans la liste qui s’affiche.  

Vous pouvez suivre la documentation d’[Azure NetApp Files](/azure/azure-netapp-files/) pour en savoir plus sur l’utilisation du service.

## <a name="azure-cli-access"></a>Accès à Azure CLI

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

## <a name="rest-api-access"></a>Accès à l’API REST

Les points de terminaison Azure Government sont différents des points de terminaison Azure commerciaux. Pour obtenir la liste des différents points de terminaison, consultez l’[Aide pour les développeurs](../azure-government/compare-azure-government-global-azure.md#guidance-for-developers) d’Azure Government.

## <a name="powershell-access"></a>Accès à PowerShell

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
* [Nouveautés d’Azure NetApp Files](whats-new.md)
* [Comparer Azure Government et Azure mondial](../azure-government/compare-azure-government-global-azure.md)
* [API REST d’Azure NetApp Files](azure-netapp-files-develop-with-rest-api.md)
* [API REST Azure NetApp Files à l’aide de PowerShell](develop-rest-api-powershell.md)
