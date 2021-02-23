---
title: Utiliser Azure Policy pour appliquer des configurations de cluster à grande échelle (préversion)
services: azure-arc
ms.service: azure-arc
ms.date: 02/15/2021
ms.topic: article
author: mlearned
ms.author: mlearned
description: Utiliser Azure Policy pour appliquer des configurations de cluster à grande échelle
keywords: Kubernetes, Arc, Azure, K8s, conteneurs
ms.openlocfilehash: b80e50cb4823632f054de3b7f9da71392f8578d7
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100560178"
---
# <a name="use-azure-policy-to-apply-cluster-configurations-at-scale-preview"></a>Utiliser Azure Policy pour appliquer des configurations de cluster à grande échelle (préversion)

## <a name="overview"></a>Vue d’ensemble

Vous pouvez utiliser Azure Policy pour appliquer à l’une des ressources suivantes l’élément `Microsoft.KubernetesConfiguration/sourceControlConfigurations` spécifique :
*  ressource `Microsoft.Kubernetes/connectedclusters`.
* Ressource `Microsoft.ContainerService/managedClusters` compatible avec GitOps. 

Pour utiliser Azure Policy, sélectionnez une définition de stratégie existante et créez une attribution de stratégie. Lors de la création de l’attribution de stratégie :
1. Définissez l’étendue de l’attribution.
    * L’étendue sera un groupe de ressources ou un abonnement Azure. 
2. Définissez les paramètres de la configuration `sourceControlConfiguration` qui va être créée. 

Une fois l’attribution créée, le moteur Azure Policy identifie toutes les ressources `connectedCluster` ou `managedCluster` qui se trouvent dans l’étendue, et applique la `sourceControlConfiguration` à chacune d’elles.

Vous pouvez activer plusieurs référentiels Git comme sources de vérité pour chaque cluster à l’aide de plusieurs attributions de stratégie. Chaque attribution de stratégie est configurée de manière à utiliser un autre référentiel Git, par exemple, un référentiel pour l’opérateur informatique/du cluster central et d’autres référentiels pour les équipes d’applications.

## <a name="prerequisite"></a>Prérequis

Vérifiez que vous disposez des autorisations `Microsoft.Authorization/policyAssignments/write` sur l’étendue (abonnement ou groupe de ressources) dans laquelle vous allez créer cette attribution de stratégie.

## <a name="create-a-policy-assignment"></a>Créer une affectation de stratégie

1. Dans le portail Azure, accédez à **Stratégie**.
1. Dans la section **Création** de la barre latérale, sélectionnez **Définitions**.
1. Dans la catégorie « Kubernetes », choisissez la stratégie intégrée « Déployer GitOps sur le cluster Kubernetes ». 
1. Cliquez sur **Attribuer**.
1. Définissez **Portée** sur le groupe d’administration, l’abonnement ou le groupe de ressources auquel s’appliquera l’attribution de stratégie.
    * Si vous souhaitez exclure des ressources de la portée de la stratégie, définissez **Exclusions**.
1. Donnez à l’attribution de stratégie un **Nom** et une **Description** facilement identifiables.
1. Assurez-vous que l’option **Application de la stratégie** est définie sur **Activée**.
1. Sélectionnez **Suivant**.
1. Définissez les valeurs de paramètre à utiliser lors de la création du `sourceControlConfiguration`.
1. Sélectionnez **Suivant**.
1. Activez l’option **Créer une tâche de correction**.
1. Vérifiez que l’option **Créer une identité managée** est activée et que l’identité aura des autorisations de **Contributeur**. 
    * Pour plus d’informations, consultez le [Démarrage rapide Créer une attribution de stratégie](../../governance/policy/assign-policy-portal.md) et l’[article Corriger les ressources non conformes avec Azure Policy](../../governance/policy/how-to/remediate-resources.md).
1. Sélectionnez **Revoir + créer**.

Une fois l’attribution de stratégie créée, le `sourceControlConfiguration` s’applique aux ressources suivantes situées dans l’étendue de l’affectation :
* Nouvelles ressources `connectedCluster`.
* Nouvelles ressources `managedCluster` avec les agents GitOps installés. 

Pour des clusters existants, vous devrez exécuter manuellement une tâche de correction. Cette tâche nécessite généralement 10 à 20 minutes pour que l’attribution de stratégie prenne effet.

## <a name="verify-a-policy-assignment"></a>Vérifier une attribution de stratégie

1. Dans le portail Azure, accédez à vos ressources `connectedCluster`.
1. Dans la section **Paramètres** de la barre latérale, sélectionnez **Stratégies**. 
    * L’expérience utilisateur du cluster AKS n’est pas encore implémentée.
    * Dans la liste des stratégies, vous devriez voir l’attribution de stratégie que vous avez créée ci-dessus, et l’**État de conformité** devrait être *Conforme*.
1. Dans la section **Paramètres** de la barre latérale, sélectionnez **Configurations**.
    * Dans la liste des configurations, vous devriez voir la `sourceControlConfiguration` que l’attribution de stratégie a créée.
1. Utilisez `kubectl` pour interroger le cluster. 
    * Vous devriez voir l’espace de noms et les artefacts créés par la `sourceControlConfiguration`.
    * Après au maximum 5 minutes, vous devriez voir dans le cluster les artefacts décrits dans les manifestes du dépôt Cit configuré.

## <a name="next-steps"></a>Étapes suivantes

* [Configurer Azure Monitor pour des conteneurs avec des clusters Kubernetes compatibles avec Azure Arc](../../azure-monitor/insights/container-insights-enable-arc-enabled-clusters.md)
