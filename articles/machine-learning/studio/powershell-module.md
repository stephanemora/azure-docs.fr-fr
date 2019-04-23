---
title: Modules PowerShell pour Machine Learning Studio
titleSuffix: Azure Machine Learning Studio
description: Utilisez PowerShell pour créer et gérer des espaces de travail Azure Machine Learning Studio, des expériences, des services web, etc.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.date: 04/25/2019
ms.openlocfilehash: e3c2587fceed265c9768b6ea6f2ecf3b9a8b7b1a
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2019
ms.locfileid: "60011336"
---
# <a name="powershell-modules-for-azure-machine-learning-studio"></a>Modules PowerShell pour Azure Machine Learning Studio

Grâce aux modules PowerShell, vous pouvez gérer par programmation vos ressources Studio et des ressources telles que les espaces de travail, les jeux de données et les services web.

Vous pouvez interagir avec les ressources Studio à l’aide de trois modules PowerShell :

* [Azure PowerShell Az](#az-rm), publié en 2018, inclut toutes les fonctionnalités d’AzureRM, mais avec des noms cmdlets différents
* [AzureRM](#az-rm) publiée en 2016, remplacé par PowerShell Az
* [Azure Machine Learning PowerShell classique](#classic) publié en 2016

Bien que ces modules PowerShell ont des similitudes, chacun est conçu pour certains scénarios particuliers. Cet article décrit les différences entre les modules PowerShell et vous aide à décider lequel choisir.  

Consultez le [tableau de prise en charge](#support-table) ci-dessous pour connaître les ressources prises en charge par chaque module. 

## <a name="az-rm"></a> Azure PowerShell Az et AzureRM

Az est maintenant le module PowerShell prévu pour interagir avec Azure et inclut toutes les fonctionnalités précédentes d’AzureRM. AzureRM continue de bénéficier de correctifs de bogues, mais ne recevra aucune nouvelle cmdlet ni fonctionnalité.  Az et AzureRM gèrent les solutions déployées à l’aide du modèle de déploiement **Azure Resource Manager**. Ces ressources incluent des espaces de travail Studio et les services web « New » Studio. 

PowerShell classiques peuvent être installés en même temps que Az ou AzureRM pour couvrir les deux types de ressources « nouveau » et « classique ». Il n’est toutefois pas recommandé d’installer Az et AzureRM en même temps. Pour choisir entre Az et AzureRM, Microsoft recommande Az pour tous les déploiements futurs.  En savoir plus sur Az / AzureRM et le chemin de migration dans [présentation d’Azure PowerShell Az](https://docs.microsoft.com/powershell/azure/new-azureps-module-az).

Pour vous familiariser avec Az, suivez les [instructions d’installation pour Azure Az](https://docs.microsoft.com/powershell/azure/install-az-ps).

## <a name="classic"></a> PowerShell classique

Le [module PowerShell classique](https://aka.ms/amlps) Studio vous permet de gérer les ressources déployées à l’aide du **modèle de déploiement classique**. Ces ressources incluent des ressources de l’utilisateur Studio, les services web « classique » et les points de terminaison de service web « classique ».

Toutefois, Microsoft recommande d’utiliser le modèle de déploiement Resource Manager pour toutes les ressources futures pour simplifier le déploiement et la gestion des ressources. Si vous souhaitez en savoir plus sur les modèles de déploiement, consultez l’article [Déploiement Azure Resource Manager et déploiement Classic](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model).

Pour bien démarrer avec PowerShell classique, téléchargez le [package de la version](https://github.com/hning86/azuremlps/releases) dans GitHub et suivez les [instructions d’installation](https://github.com/hning86/azuremlps/blob/master/README.md). Les instructions indiquent comment débloquer la DLL téléchargée/décompressée et l’importer dans votre environnement PowerShell.

PowerShell classiques peuvent être installés en même temps que Az ou AzureRM pour couvrir les deux types de ressources « nouveau » et « classique ».

## <a name="support-table"></a> Tableau de prise en charge PowerShell


| | **Az** |  **PowerShell Classic** |
| --- | --- | --- |
| Créer ou supprimer des espaces de travail | [Modèles Microsoft Azure Resource Manager](https://docs.microsoft.com/azure/machine-learning/studio/deploy-with-resource-manager-template) |  |
| Gérer les plans d’engagement espace de travail | [New-AzMlCommitmentPlan](https://docs.microsoft.com/powershell/module/az.machinelearning/new-azmlcommitmentplan) | |
| Gérer les utilisateurs de l’espace de travail |  | [Add-AmlWorkspaceUsers](https://github.com/hning86/azuremlps#add-amlworkspaceusers)|
| Gérer des services web | [New-AzMlWebService](https://docs.microsoft.com/powershell/module/az.machinelearning/new-azmlwebservice) <br>(« nouveau » web services)|| [New-AmlWebService](https://github.com/hning86/azuremlps#manage-classic-web-service) <br>(services web « classiques ») |
| Gérer les points de terminaison de service web/clés |  [Get-AzMlWebServiceKeys](https://docs.microsoft.com/powershell/module/az.machinelearning/get-azmlwebservicekeys)|  [Add-AmlWebServiceEndpoint](https://github.com/hning86/azuremlps#manage-classic-web-servcie-endpoint)|
| Gérer les modèles formés/jeux de données utilisateur| | [Get-AmlDataset](https://github.com/hning86/azuremlps#manage-user-assets-dataset-trained-model-transform) |
| Gérer les expériences utilisateur |  | [Start-AmlExperiment](https://github.com/hning86/azuremlps#manage-experiment) |
| Gérer les modules personnalisés | | [New-AmlCustomModule](https://github.com/hning86/azuremlps#manage-custom-module) |


## <a name="next-steps"></a>Étapes suivantes
Consultez la documentation complète sur ces module PowerShell :
* [PowerShell Classic](https://aka.ms/amlps)
* [Azure PowerShell Az](https://docs.microsoft.com/powershell/module/az.machinelearning/#machine_learning)
