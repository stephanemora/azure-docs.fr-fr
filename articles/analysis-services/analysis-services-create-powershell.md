---
title: Démarrage rapide - Créer un serveur Azure Analysis Services à l’aide de PowerShell | Microsoft Docs
description: Découvrir comment se connecter à un serveur Azure Analysis Services à l’aide de PowerShell
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: quickstart
ms.date: 10/18/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 3971311bff90d0b005acd516336e13766028cccf
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/06/2018
ms.locfileid: "53001559"
---
# <a name="quickstart-create-a-server---powershell"></a>Démarrage rapide : Créer un serveur - PowerShell

Ce démarrage rapide décrit comment utiliser PowerShell à partir d’une ligne de commande pour créer un serveur Azure Analysis Services de votre abonnement Azure.

## <a name="prerequisites"></a>Prérequis

- **Abonnement Azure** : visitez [version d’évaluation gratuite d’Azure](https://azure.microsoft.com/offers/ms-azr-0044p/) pour créer un compte.
- **Azure Active Directory** : votre abonnement doit être associé à un client Azure Active Directory et vous devez disposer d’un compte dans ce répertoire. Pour en savoir plus, consultez [Authentification et autorisations utilisateur](analysis-services-manage-users.md).
- **Module Azure PowerShell, version 4.0 ou ultérieure**. Pour connaître la version de l’interface, exécutez ` Get-Module -ListAvailable AzureRM`. Pour installer ou mettre à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-azurerm-ps).

## <a name="import-azurermanalysisservices-module"></a>Importer le module de AzureRm.AnalysisServices

Pour créer un serveur dans votre abonnement, vous utilisez le module de composant [AzureRM.AnalysisServices](https://www.powershellgallery.com/packages/AzureRM.AnalysisServices). Chargez le module AzureRm.AnalysisServices dans votre session PowerShell.

```powershell
Import-Module AzureRM.AnalysisServices
```

## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous à votre abonnement Azure à l’aide de la commande [Connect-AzureRmAccount](/powershell/module/azurerm.profile/connect-azurermaccount). Suivez les instructions à l’écran.

```powershell
Connect-AzureRmAccount
```

## <a name="create-a-resource-group"></a>Créer un groupe de ressources

Un [groupe de ressources Azure](../azure-resource-manager/resource-group-overview.md) est un conteneur logique dans lequel les ressources Azure sont déployées et gérées en tant que groupe. Lorsque vous créez votre serveur, vous devez spécifier un groupe de ressources dans votre abonnement. Si vous ne disposez pas d’un groupe de ressources, créez-en un à l’aide de la commande [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). L’exemple suivant crée un groupe de ressources nommé `myResourceGroup` dans la région USA Ouest.

```powershell
New-AzureRmResourceGroup -Name "myResourceGroup" -Location "WestUS"
```

## <a name="create-a-server"></a>Créer un serveur

Créer un nouveau serveur à l’aide de la commande [New-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/new-azurermanalysisservicesserver). L’exemple suivant crée un serveur nommé myServer dans myResourceGroup, dans la région États-Unis de l’Ouest, au niveau D1, et spécifie philipc@adventureworks.com en tant qu’administrateur de serveur.

```powershell
New-AzureRmAnalysisServicesServer -ResourceGroupName "myResourceGroup" -Name "myserver" -Location WestUS -Sku D1 -Administrator "philipc@adventure-works.com"
```

## <a name="clean-up-resources"></a>Supprimer des ressources

Vous pouvez supprimer le serveur à partir de votre abonnement à l’aide de la commande [Remove-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/new-azurermanalysisservicesserver). Si vous continuez avec d’autres démarrages rapides et tutoriels de cette collection, ne supprimez pas votre serveur. L’exemple suivant supprime le serveur créé à l’étape précédente.


```powershell
Remove-AzureRmAnalysisServicesServer -Name "myserver" -ResourceGroupName "myResourceGroup"
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce démarrage rapide, vous avez appris à créer un serveur dans votre abonnement Azure à l’aide de PowerShell. Maintenant que vous avez serveur, vous pouvez le sécuriser en configurant un pare-feu de serveur (facultatif). Vous pouvez également ajouter un modèle de données d’exemple de base à votre serveur directement à partir du portail. Un exemple de modèle permet d’en savoir plus sur la configuration des rôles de base de données de modèle et le test des connexions client. Pour en savoir plus, passez au tutoriel sur l’ajout d’un exemple de modèle.

> [!div class="nextstepaction"]
> [Démarrage rapide : Configurer un pare-feu de serveur - Portail](analysis-services-qs-firewall.md)      
> [!div class="nextstepaction"]
> [Didacticiel : Ajouter un exemple de modèle à votre serveur](analysis-services-create-sample-model.md)