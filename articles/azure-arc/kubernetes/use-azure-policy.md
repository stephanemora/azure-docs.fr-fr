---
title: Utiliser Azure Policy pour appliquer des configurations de cluster à grande échelle
services: azure-arc
ms.service: azure-arc
ms.date: 03/03/2021
ms.topic: article
author: mlearned
ms.author: mlearned
description: Utiliser Azure Policy pour appliquer des configurations de cluster à grande échelle
keywords: Kubernetes, Arc, Azure, K8s, conteneurs
ms.openlocfilehash: 05a6665a985ef8b229ee58082dc9b2c10cdcece3
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102121454"
---
# <a name="use-azure-policy-to-apply-cluster-configurations-at-scale"></a>Utiliser Azure Policy pour appliquer des configurations de cluster à grande échelle

Vous pouvez utiliser Azure Policy pour appliquer des configurations (type de ressource `Microsoft.KubernetesConfiguration/sourceControlConfigurations`) à grande échelle sur des clusters Kubernetes avec Azure Arc (`Microsoft.Kubernetes/connectedclusters`).

Pour utiliser Azure Policy, sélectionnez une définition de stratégie existante et créez une attribution de stratégie. Lors de la création de l’attribution de stratégie :
1. Définissez l’étendue de l’attribution.
    * L’étendue sera un groupe de ressources ou un abonnement Azure. 
2. Définissez les paramètres de la configuration à créer. 

Une fois l’affectation créée, le moteur Azure Policy identifie tous les clusters Kubernetes avec Azure Arc situés dans l’étendue et applique la configuration à chaque cluster.

Vous pouvez créer plusieurs configurations, chacune pointant vers un dépôt Git différent, à l’aide de plusieurs affectations de stratégie. Par exemple, un dépôt pour le service informatique central/l’opérateur de cluster, et d’autres dépôts pour les équipes d’application.

## <a name="prerequisite"></a>Configuration requise

Vérifiez que vous disposez d’autorisations `Microsoft.Authorization/policyAssignments/write` sur l’étendue (abonnement ou groupe de ressources) où vous allez créer cette affectation de stratégie.

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

Une fois l’affectation de stratégie créée, la configuration est appliquée aux nouveaux clusters Kubernetes avec Azure Arc créés dans l’étendue de l’affectation de stratégie.

Pour les clusters existants, vous devez exécuter manuellement une tâche de correction. Cette tâche nécessite généralement 10 à 20 minutes pour que l’attribution de stratégie prenne effet.

## <a name="verify-a-policy-assignment"></a>Vérifier une attribution de stratégie

1. Dans le portail Azure, accédez à l’un de vos clusters Kubernetes avec Azure Arc.
1. Dans la section **Paramètres** de la barre latérale, sélectionnez **Stratégies**. 
    * Dans la liste des stratégies, vous devriez voir l’attribution de stratégie que vous avez créée ci-dessus, et l’**État de conformité** devrait être *Conforme*.
1. Dans la section **Paramètres** de la barre latérale, sélectionnez **Configurations**.
    * Dans la liste des configurations, vous devez voir la configuration créée par l’affectation de stratégie.
1. Utilisez `kubectl` pour interroger le cluster. 
    * Vous devez voir l’espace de noms et les artefacts créés par les ressources de configuration.
    * En moins de 5 minutes (en supposant que le cluster dispose d’une connectivité réseau vers Azure), vous devez voir se créer sur le cluster les objets décrits par les manifestes dans le dépôt Git.

## <a name="next-steps"></a>Étapes suivantes

[Configurer Azure Monitor pour des conteneurs avec des clusters Kubernetes compatibles avec Azure Arc](../../azure-monitor/containers/container-insights-enable-arc-enabled-clusters.md).
