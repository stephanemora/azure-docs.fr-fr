---
title: Procédure d’intégration d’Azure Monitor pour les conteneurs | Microsoft Docs
description: Cet article décrit la façon dont vous intégrez et configurez Azure Monitor pour les conteneurs afin que vous puissiez comprendre les performances de votre conteneur et les problèmes liés aux performances identifiés.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/25/2019
ms.author: magoedte
ms.openlocfilehash: 10b80a9749c5698195ac5d3493ac3b07fd6e24e1
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65073307"
---
# <a name="how-to-onboard-azure-monitor-for-containers"></a>Procédure d’intégration d’Azure Monitor pour les conteneurs  

Cet article fournit une vue d’ensemble des options disponibles pour le programme d’installation d’Azure Monitor pour les conteneurs pour analyser les performances des charges de travail qui sont déployés sur des environnements Kubernetes et hébergé sur [Azure Kubernetes Service](https://docs.microsoft.com/azure/aks/).

Azure Monitor pour conteneurs peut être activé pour un ou plusieurs déploiements d’AKS nouveaux ou existants, à l’aide des méthodes prises en charge suivantes :

* À partir du portail Azure, Azure PowerShell, ou avec Azure CLI
* Utiliser [Terraform et AKS](../../terraform/terraform-create-k8s-cluster-with-tf-and-aks.md)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Conditions préalables 
Avant de commencer, vérifiez que vous disposez des éléments suivants :

- **Un espace de travail Analytique de journal.** Vous pouvez le créer lorsque vous activez la supervision de votre nouveau cluster AKS ou lorsque vous laissez l’expérience d’intégration créer un espace de travail par défaut dans le groupe de ressources par défaut de l’abonnement de cluster AKS. Si vous choisissez de le créer vous-même, vous pouvez le créer via [Azure Resource Manager](../platform/template-workspace-configuration.md), [PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json) ou le [portail Azure](../learn/quick-create-workspace.md).
- Vous êtes membre de la **rôle de contributeur Log Analytique** pour activer la surveillance de conteneur. Pour plus d’informations sur la façon de contrôler l’accès à un espace de travail Log Analytics, consultez [Gérer les espaces de travail](../platform/manage-access.md).
- Vous êtes membre de la **[propriétaire](../../role-based-access-control/built-in-roles.md#owner)** rôle sur la ressource de cluster AKS. 

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

## <a name="components"></a>Composants 

Votre capacité à superviser les performances dépend d’un agent Log Analytics conteneurisé pour Linux, spécialement développé pour Azure Monitor pour les conteneurs. Cet agent spécialisé collecte les données de performances et d’événements à partir de tous les nœuds du cluster, et l’agent est automatiquement déployé puis inscrit auprès de l’espace de travail Log Analytics spécifié pendant le déploiement. La version d’agent est microsoft/oms:ciprod04202018 ou ultérieure. Elle est représentée par une date au format suivant : *mmjjaaaa*. 

>[!NOTE]
>Avec la version préliminaire de prise en charge de Windows Server pour AKS, un cluster AKS avec des nœuds de serveur Windows n’ont pas d’un agent installé pour collecter des données et les envoyer à Azure Monitor. Au lieu de cela, un nœud Linux automatiquement déployé dans le cluster en tant que partie du déploiement standard de collecte et transfère les données à Azure Monitor procuration tous les nœuds Windows dans le cluster.  
>

Lorsqu’une nouvelle version de l’agent est disponible, celui-ci est automatiquement mis à niveau sur vos clusters Kubernetes managés hébergés sur Azure Kubernetes Service (AKS). Pour suivre les versions publiées, consultez [Annonces des versions de l’agent](https://github.com/microsoft/docker-provider/tree/ci_feature_prod). 

>[!NOTE] 
>Si vous avez déjà déployé un cluster AKS, activez la supervision à l’aide d’Azure CLI ou un modèle Azure Resource Manager fourni, comme indiqué plus loin dans cet article. Vous ne pouvez pas utiliser `kubectl` pour mettre à niveau, supprimer, redéployer ou déployer l’agent. Le modèle doit être déployé dans le même groupe de ressources que le cluster.

Vous activez Azure Monitor pour les conteneurs en utilisant l’une des méthodes suivantes décrites dans le tableau suivant.

| État du déploiement | Méthode | Description | 
|------------------|--------|-------------| 
| Nouveau cluster AKS | [Créer le cluster à l’aide d’Azure CLI](../../aks/kubernetes-walkthrough.md#create-aks-cluster)| Vous pouvez activer la surveillance d’un nouveau cluster ACS que vous créez avec Azure CLI. | 
| | [Créer le cluster à l’aide de Terraform](container-insights-enable-new-cluster.md#enable-using-terraform)| Vous pouvez activer la surveillance d’un nouveau cluster AKS, que vous créez à l’aide de l’outil open source Terraform. | 
| Cluster ACS existant | [Activer à l’aide d’Azure CLI](container-insights-enable-existing-clusters.md#enable-using-azure-cli) | Vous pouvez activer la surveillance d’un cluster AKS déjà déployé à l’aide d’Azure CLI. | 
| |[Activer à l’aide de Terraform](container-insights-enable-existing-clusters.md#enable-using-terraform) | Vous pouvez activer la surveillance d’un cluster AKS déjà déployé à l’aide de l’outil open source Terraform. | 
| | [Activer à partir d’Azure Monitor](container-insights-enable-existing-clusters.md#enable-from-azure-monitor-in-the-portal)| Vous pouvez activer la surveillance d’un ou plusieurs clusters AKS déjà déployées à partir de la page de plusieurs cluster ACS dans Azure Monitor. | 
| | [Activer à partir du cluster AKS](container-insights-enable-existing-clusters.md#enable-directly-from-aks-cluster-in-the-portal)| Vous pouvez activer la surveillance directement à partir d’un cluster AKS dans le portail Azure. | 
| | [Activer à l’aide d’un modèle Azure Resource Manager](container-insights-enable-existing-clusters.md#enable-using-an-azure-resource-manager-template)| Vous pouvez activer la surveillance d’un cluster AKS avec un modèle Azure Resource Manager préconfiguré. | 

## <a name="next-steps"></a>Étapes suivantes

* Vous pouvez activer la fonctionnalité de supervision pour collecter des métriques d’intégrité pour les nœuds et pods du cluster AKS et les consulter dans le Portail Azure. Pour savoir comment utiliser Azure Monitor pour les conteneurs, consultez l’article [Connaître l’état d’Azure Kubernetes Service](container-insights-analyze.md).
