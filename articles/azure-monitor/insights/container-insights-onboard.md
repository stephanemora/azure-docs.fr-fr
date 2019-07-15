---
title: Procédure d’activation d’Azure Monitor pour les conteneurs | Microsoft Docs
description: Cet article décrit la façon dont vous activez et configurez Azure Monitor pour les conteneurs afin que vous puissiez comprendre les performances de votre conteneur et les problèmes liés aux performances identifiés.
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
ms.date: 07/02/2019
ms.author: magoedte
ms.openlocfilehash: aff2dcebdab1ad93b8b1958164764b66eb755d1c
ms.sourcegitcommit: 6cb4dd784dd5a6c72edaff56cf6bcdcd8c579ee7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2019
ms.locfileid: "67514503"
---
# <a name="how-to-enable-azure-monitor-for-containers"></a>Procédure d’activation d’Azure Monitor pour les conteneurs

Cet article offre un aperçu des options disponibles pour configurer Azure Monitor pour les conteneurs en vue de surveiller les performances des charges de travail qui sont déployées dans un environnement Kubernetes et hébergées sur [Azure Kubernetes Service](https://docs.microsoft.com/azure/aks/).

Azure Monitor pour conteneurs peut être activé pour un ou plusieurs déploiements d’AKS nouveaux ou existants, à l’aide des méthodes prises en charge suivantes :

* Dans le portail Azure, Azure PowerShell ou avec Azure CLI
* Utiliser [Terraform et AKS](../../terraform/terraform-create-k8s-cluster-with-tf-and-aks.md)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Prérequis
Avant de commencer, vérifiez que vous disposez des éléments suivants :

* **Un espace de travail Log Analytics.**

    Azure Monitor pour conteneurs prend en charge un espace de travail Log Analytics dans les régions répertoriées dans [Produits Azure par région](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor), à l’exclusion de la région **US Gov Virginie**.

    Vous pouvez le créer lorsque vous activez la supervision de votre nouveau cluster AKS ou lorsque vous laissez l’expérience d’intégration créer un espace de travail par défaut dans le groupe de ressources par défaut de l’abonnement de cluster AKS. Si vous choisissez de le créer vous-même, vous pouvez le créer via [Azure Resource Manager](../platform/template-workspace-configuration.md), [PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json) ou le [portail Azure](../learn/quick-create-workspace.md). Pour obtenir la liste des paires de mappage prises en charge utilisées pour l’espace de travail par défaut, consultez [Mappage des régions pour Azure Monitor pour conteneurs](container-insights-region-mapping.md).

* Vous êtes membre du **rôle de contributeur Log Analytics** pour activer la supervision des conteneurs. Pour plus d’informations sur la façon de contrôler l’accès à un espace de travail Log Analytics, consultez [Gérer les espaces de travail](../platform/manage-access.md).

* Vous êtes membre du rôle de **[Propriétaire](../../role-based-access-control/built-in-roles.md#owner)** sur la ressource de cluster AKS.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

## <a name="components"></a>Composants

Votre capacité à superviser les performances dépend d’un agent Log Analytics conteneurisé pour Linux, spécialement développé pour Azure Monitor pour les conteneurs. Cet agent spécialisé collecte les données de performances et d’événements à partir de tous les nœuds du cluster, et l’agent est automatiquement déployé puis inscrit auprès de l’espace de travail Log Analytics spécifié pendant le déploiement. La version d’agent est microsoft/oms:ciprod04202018 ou ultérieure. Elle est représentée par une date au format suivant : *mmjjaaaa*.

>[!NOTE]
>Avec la version préliminaire de la prise en charge de Windows Server pour AKS, un cluster AKS avec des nœuds Windows Server ne dispose d’aucun agent installé afin de collecter des données et les transmettre à Azure Monitor. Au lieu de cela, un nœud Linux automatiquement déployé dans le cluster dans le cadre du déploiement standard collecte et transfère les données à Azure Monitor pour tous les nœuds Windows du cluster.  
>

Lorsqu’une nouvelle version de l’agent est disponible, celui-ci est automatiquement mis à niveau sur vos clusters Kubernetes managés hébergés sur Azure Kubernetes Service (AKS). Pour suivre les versions publiées, consultez [Annonces des versions de l’agent](https://github.com/microsoft/docker-provider/tree/ci_feature_prod).

>[!NOTE]
>Si vous avez déjà déployé un cluster AKS, activez la supervision à l’aide d’Azure CLI ou un modèle Azure Resource Manager fourni, comme indiqué plus loin dans cet article. Vous ne pouvez pas utiliser `kubectl` pour mettre à niveau, supprimer, redéployer ou déployer l’agent.
>Le modèle doit être déployé dans le même groupe de ressources que le cluster.

Vous activez Azure Monitor pour les conteneurs en utilisant l’une des méthodes du tableau suivant.

| État du déploiement | Méthode | Description |
|------------------|--------|-------------|
| Nouveau cluster AKS | [Créer un cluster à l’aide d’Azure CLI](../../aks/kubernetes-walkthrough.md#create-aks-cluster)| Vous pouvez activer la surveillance d’un nouveau cluster AKS que vous créez avec Azure CLI. |
| | [Créer un cluster à l’aide de Terraform](container-insights-enable-new-cluster.md#enable-using-terraform)| Vous pouvez activer la surveillance d’un nouveau cluster AKS que vous créez à l’aide de l’outil open source Terraform. |
| Cluster AKS existant | [Activer à l’aide d’Azure CLI](container-insights-enable-existing-clusters.md#enable-using-azure-cli) | Vous pouvez activer la surveillance d’un cluster AKS déjà déployé à l’aide d’Azure CLI. |
| |[Activer à l’aide de Terraform](container-insights-enable-existing-clusters.md#enable-using-terraform) | Vous pouvez activer la surveillance d’un cluster AKS déjà déployé à l’aide de l’outil open source Terraform. |
| | [Activer à partir d’Azure Monitor](container-insights-enable-existing-clusters.md#enable-from-azure-monitor-in-the-portal)| Vous pouvez activer la surveillance d’un ou plusieurs clusters AKS déjà déployés depuis la page multi-cluster AKS dans Azure Monitor. |
| | [Activer à partir du cluster AKS](container-insights-enable-existing-clusters.md#enable-directly-from-aks-cluster-in-the-portal)| Vous pouvez activer la surveillance directement depuis un cluster AKS dans le portail Azure. |
| | [Activer à l’aide d’un modèle Azure Resource Manager](container-insights-enable-existing-clusters.md#enable-using-an-azure-resource-manager-template)| Vous pouvez activer la surveillance d’un cluster AKS à l’aide d’un modèle Azure Resource Manager préconfiguré. |

## <a name="next-steps"></a>Étapes suivantes

* Vous pouvez activer la fonctionnalité de supervision pour collecter des métriques d’intégrité pour les nœuds et pods du cluster AKS et les consulter dans le Portail Azure. Pour savoir comment utiliser Azure Monitor pour les conteneurs, consultez l’article [Connaître l’état d’Azure Kubernetes Service](container-insights-analyze.md).
