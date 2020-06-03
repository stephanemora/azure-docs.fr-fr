---
title: Utiliser Azure Policy pour appliquer des configurations de cluster à grande échelle (préversion)
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: Utiliser Azure Policy pour appliquer des configurations de cluster à grande échelle
keywords: Kubernetes, Arc, Azure, K8s, conteneurs
ms.openlocfilehash: c017e9422733069ffd93f6dff72ecb884da057c4
ms.sourcegitcommit: a9784a3fd208f19c8814fe22da9e70fcf1da9c93
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/22/2020
ms.locfileid: "83779961"
---
# <a name="use-azure-policy-to-apply-cluster-configurations-at-scale-preview"></a>Utiliser Azure Policy pour appliquer des configurations de cluster à grande échelle (préversion)

## <a name="overview"></a>Vue d’ensemble

Utilisez Azure Policy pour appliquer à chaque ressource `Microsoft.Kubernetes/connectedclusters` ou `Microsoft.ContainerService/managedClusters` compatible git-OPS des `Microsoft.KubernetesConfiguration/sourceControlConfigurations` spécifiques.  Pour utiliser Azure Policy, vous sélectionnez une définition de stratégie et créez une attribution de stratégie.  Lors de la création de l’attribution de stratégie, vous définissez l’étendue de l’attribution, à savoir un groupe de ressources ou un abonnement Azure.  Vous définissez également les paramètres pour la configuration `sourceControlConfiguration` qui sera créée.  Une fois l’attribution créée, le moteur de stratégie identifie toutes les ressources `connectedCluster` ou `managedCluster` qui se trouvent dans l’étendue, et applique la `sourceControlConfiguration` à chacune d’elles.

Si vous utilisez plusieurs dépôts Git comme sources fidèles pour chaque cluster (par exemple, un dépôt pour l’opérateur IT/de cluster central et d’autres pour les équipes d’applications), vous pouvez activer cela en utilisant plusieurs attributions de stratégies, chacune étant configurée pour utiliser dépôt Git distinct.

## <a name="create-a-custom-policy-definition"></a>Créer une définition de stratégie personnalisée

1. Dans le portail Azure, accédez à Stratégie, puis, dans la section **Création** de la barre latérale, sélectionnez **Définitions**.
2. Sélectionnez **+ Définition de stratégie**.
3. Définissez l’**Emplacement de la définition** sur votre abonnement ou groupe d’administration.  Cela déterminera l’étendue maximale dans laquelle la définition de stratégie peut être utilisée.
4. Attribuez à la stratégie un **Nom** et une **Description**.
5. Sous la catégorie, choisissez **Créer nouveau**, puis écrivez *Cluster Kubernetes – Azure Arc*
6. Dans la zone d’édition **Règle de stratégie**, copiez/collez le contenu de cet [exemple de définition de stratégie](https://raw.githubusercontent.com/Azure/arc-k8s-demo/master/policy/Ensure-GitOps-configuration-for-Kubernetes-cluster.json).
7. **Enregistrez.** .

Cette étape de création d’une définition de stratégie personnalisée n’est pas nécessaire une fois terminé le travail visant à en faire une stratégie intégrée.

## <a name="create-a-policy-assignment"></a>Créer une affectation de stratégie

1. Dans le portail Azure, accédez à Stratégie, puis, dans la section **Création** de la barre latérale, sélectionnez **Définitions**.
2. Recherchez la définition que vous venez de créer, puis sélectionnez-la.
3. Dans les actions de la page, sélectionnez **Attribuer**.
4. Définissez l’**Étendue** sur le groupe d’administration, l’abonnement ou le groupe de ressources auxquels s’appliquera l’attribution de stratégie.
5. Si vous souhaitez exclure des ressources de l’étendue de la stratégie, définissez **Exclusions**.
6. Donnez un **Nom** et une **Description** à l’affectation de stratégie, qui permettront de l’identifier facilement.
7. Assurez-vous que l’option **Application de la stratégie** est définie sur *Activée*.
8. Sélectionnez **Suivant**.
9. Définissez les valeurs de paramètre qui seront utilisées lors de la création de la `sourceControlConfiguration`.
10. Sélectionnez **Suivant**.
11. Activez l’option **Créer une tâche de correction**.
12. Assurez-vous que l’option **Créer une identité managée** est activée et que l’identité aura des autorisations de **Contributeur**.  Pour plus d’informations sur les autorisations dont vous avez besoin, consultez [ce document](https://docs.microsoft.com/azure/governance/policy/assign-policy-portal) et [le commentaire dans ce document](https://docs.microsoft.com/azure/governance/policy/how-to/remediate-resources).
13. Sélectionnez **Revoir + créer**.

Une fois l’attribution de stratégie créée, pour toute nouvelle ressource `connectedCluster` (ou ressource `managedCluster` avec les agents GitOps installés) se trouvant dans l’étendue de l’affectation, la `sourceControlConfiguration` sera appliquée.  Pour des clusters existants, vous devrez exécuter manuellement une tâche de correction.  La prise d’effet de l’attribution de stratégie prend généralement de 10 à 20 minutes.

## <a name="verify-a-policy-assignment"></a>Vérifier une attribution de stratégie

1. Dans le portail Azure, accédez à l’une de vos ressources `connectedCluster`, puis, dans la section **Paramètres** de la barre latérale, sélectionnez **Stratégies** (l’expérience utilisateur pour le cluster managé AKS n’est pas encore implémentée, mais elle est disponible).
2. Dans la liste, vous devriez voir l’attribution de stratégie que vous avez créée ci-dessus, et l’**État de conformité** devrait être *Conforme*.
3. Dans la section **Paramètres** de la barre latérale, sélectionnez **Configurations**.
4. Dans la liste, vous devriez voir la `sourceControlConfiguration` que l’attribution de stratégie a créée.
5. Utilisez **kubectl** pour interroger le cluster. Vous devriez voir l’espace de noms et les artefacts créés par la `sourceControlConfiguration`.
6. Après au maximum 5 minutes, vous devriez voir dans le cluster les artefacts décrits dans les manifestes du dépôt Cit configuré.

## <a name="next-steps"></a>Étapes suivantes

* [Configurer Azure Monitor pour des conteneurs avec des clusters Kubernetes compatibles avec Azure Arc](./deploy-azure-monitor-for-containers.md)
