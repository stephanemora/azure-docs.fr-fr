---
title: Modules PowerShell pour Machine Learning Studio
titleSuffix: Azure Machine Learning Studio
description: Utilisez PowerShell pour créer et gérer des espaces de travail Azure Machine Learning Studio, des expériences, des services web, etc.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: article
author: ericlicoding
ms.author: amlstudiodocs
ms.custom: previous-ms.author=haining, previous-author=hning86
ms.date: 01/25/2019
ms.openlocfilehash: b6080e6e81b6cedce4d08cf20e082c87e5cecade
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/31/2019
ms.locfileid: "55489816"
---
# <a name="powershell-modules-for-azure-machine-learning-studio"></a>Modules PowerShell pour Azure Machine Learning Studio

Grâce aux modules PowerShell, vous pouvez gérer par programmation vos ressources Studio et des ressources telles que les espaces de travail, les jeux de données et les services web.

Vous pouvez interagir avec les ressources Studio à l’aide de trois modules PowerShell :

* [Azure PowerShell Az](#az-rm), publié en 2018, inclut toutes les fonctionnalités d’AzureRM, mais avec des noms cmdlets différents
* [AzureRM](#az-rm) publié en 2016
* [Azure Machine Learning PowerShell classique](#classic) publié en 2016

Même si ces modules ont des similitudes, chacun d’eux est conçu pour certains scénarios particuliers. Cet article décrit les différences entre les modules PowerShell et vous aide à décider lequel choisir.

## <a name="choosing-modules"></a> Choix des modules

Le choix entre les modules PowerShell disponibles varie selon le type de ressources que vous gérez.

Consultez le [tableau de prise en charge](#support-table) ci-dessous pour connaître les ressources prises en charge par chaque module. Dans la mesure où PowerShell classique peut être installé en parallèle avec Az ou AzureRM, vous pouvez installer deux modules et couvrir tous les types de ressources (classiques avec Az ou classiques avec AzureRM)

Il n’est toutefois pas recommandé d’installer Az et AzureRM en même temps. Pour choisir entre Az et AzureRM, Microsoft recommande Az pour tous les déploiements futurs. N’utilisez AzureRm que si des circonstances particulières dans votre environnement l’exigent.

Pour en savoir plus sur les différences entre Az et AzureRM, ainsi que sur notre chemin de migration fourni, consultez notre [présentation d’Azure PowerShell Az.](https://docs.microsoft.com/powershell/azure/new-azureps-module-az)

## <a name="az-rm"></a> Azure PowerShell Az et AzureRM

Az et AzureRM gèrent les solutions déployées à l’aide du modèle de déploiement **Azure Resource Manager**. Ces ressources incluent des espaces de travail Studio et de nouveaux services web Studio. Pour gérer les ressources déployées à l’aide du modèle de déploiement classique, vous devez utiliser le module PowerShell classique. Si vous souhaitez en savoir plus sur les modèles de déploiement, consultez l’article [Déploiement Azure Resource Manager et déploiement Classic](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model).

Az est maintenant le module PowerShell prévu pour interagir avec Azure et inclut toutes les fonctionnalités précédentes d’AzureRM. AzureRM continue de bénéficier de correctifs de bogues, mais ne recevra aucune nouvelle cmdlet ni fonctionnalité. Bien qu’un chemin de mise à niveau à partir d’AzureRM soit possible, si vous rencontrez des problèmes avec Az lorsque vous utilisez Studio, signalez le problème et réutilisez AzureRM.

Pour vous familiariser avec Az, suivez les [instructions d’installation pour Azure Az](https://docs.microsoft.com/powershell/azure/install-az-ps).

## <a name="classic"></a> PowerShell classique

Le [module PowerShell classique](https://aka.ms/amlps) Studio vous permet de gérer les ressources déployées à l’aide du **modèle de déploiement classique**. Ces ressources incluent des ressources utilisateur Studio, des services web classiques et des points de terminaison de service web classique.

Microsoft vous recommande toutefois d’utiliser le modèle de déploiement Resource Manager pour les nouvelles ressources afin de simplifier le déploiement et la gestion des ressources. Si vous souhaitez en savoir plus sur les modèles de déploiement, consultez l’article [Déploiement Azure Resource Manager et déploiement Classic](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model).

Pour bien démarrer avec PowerShell classique, téléchargez le [package de la version](https://github.com/hning86/azuremlps/releases) dans GitHub et suivez les [instructions d’installation](https://github.com/hning86/azuremlps/blob/master/README.md). Les instructions indiquent comment débloquer la DLL téléchargée/décompressée et l’importer dans votre environnement PowerShell.

## <a name="support-table"></a> Tableau de prise en charge PowerShell

 **Espaces de travail Studio** | **Az** |  **AzureRM** | **PowerShell Classic** |
| --- | --- | --- | --- | --- |
| Créer/supprimer des espaces de travail | [Modèles Microsoft Azure Resource Manager](https://docs.microsoft.com/azure/machine-learning/studio/deploy-with-resource-manager-template) | [Modèles Microsoft Azure Resource Manager](https://docs.microsoft.com/azure/machine-learning/studio/deploy-with-resource-manager-template) |  |
| Gérer les utilisateurs de l’espace de travail |  |  | [Add-AmlWorkspaceUsers](https://github.com/hning86/azuremlps#add-amlworkspaceusers)|
| Gérer les plans d’engagement | [New-AzMlCommitmentPlan](https://docs.microsoft.com/powershell/module/az.machinelearning/new-azmlcommitmentplan) | [New-AzureRmMlCommitmentPlan](https://docs.microsoft.com/powershell/module/azurerm.machinelearning/new-azurermmlcommitmentplan) |
|||
| **Services web** | **Az** | **AzureRM** | **PowerShell Classic** |
| Gérer des services web | [New-AzMlWebService](https://docs.microsoft.com/powershell/module/az.machinelearning/new-azmlwebservice) <br> (Nouveaux services web) | [New-AzureRmMlWebService](https://docs.microsoft.com/powershell/module/azurerm.machinelearning/new-azurermmlwebservice) <br> (Nouveaux services web) |[New-AmlWebService](https://github.com/hning86/azuremlps#manage-classic-web-service) <br> (Services web classiques) |
| Gérer les points de terminaison/clés |  [Get-AzMlWebServiceKeys](https://docs.microsoft.com/powershell/module/az.machinelearning/get-azmlwebservicekeys) <br> (Nouveaux services web) | [Get-AzureRmMlWebServiceKeys](https://docs.microsoft.com/powershell/module/azurerm.machinelearning/get-azurermmlwebservicekeys) <br> (Nouveaux services web) | [Add-AmlWebServiceEndpoint](https://github.com/hning86/azuremlps#manage-classic-web-servcie-endpoint) <br> (Services web classiques) |
|||
| **Ressources utilisateur** | **Az** | **AzureRM** | **PowerShell Classic** |
| Gérer les jeux de données/modèles formés |  |  | [Get-AmlDataset](https://github.com/hning86/azuremlps#manage-user-assets-dataset-trained-model-transform) |
| Gérer les expériences |  |  | [Start-AmlExperiment](https://github.com/hning86/azuremlps#manage-experiment) |
| Gérer les modules personnalisés |  |  | [New-AmlCustomModule](https://github.com/hning86/azuremlps#manage-custom-module) |


## <a name="next-steps"></a>Étapes suivantes
Suivez ces liens pour accéder à la documentation complète sur les modules PowerShell :
* [AzureRM](https://docs.microsoft.com/powershell/module/azurerm.machinelearning/#machine_learning)
* [PowerShell Classic](https://aka.ms/amlps)
* [Azure PowerShell Az](https://docs.microsoft.com/powershell/module/az.machinelearning/#machine_learning)
