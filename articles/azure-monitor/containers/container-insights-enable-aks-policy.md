---
title: Activer le module complémentaire AKS Monitoring à l’aide d’Azure Policy
description: Décrit comment activer le module complémentaire AKS Monitoring à l’aide d’une stratégie personnalisée Azure.
ms.topic: conceptual
ms.date: 02/04/2021
ms.openlocfilehash: 2163527cc83e70913e9a6e11bf2e22f9ed9c6690
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101713896"
---
# <a name="enable-aks-monitoring-addon-using-azure-policy"></a>Activer le module complémentaire AKS Monitoring à l’aide d’Azure Policy
Cet article décrit comment activer le module complémentaire AKS Monitoring à l’aide d’une stratégie personnalisée Azure. La stratégie personnalisée du module complémentaire Monitoring peut être affectée au niveau de l’abonnement ou du groupe de ressources. Si l’espace de travail Azure Log Analytics et le cluster AKS se trouvent dans des abonnements différents, l’identité managée utilisée par l’attribution de stratégie doit avoir les autorisations de rôle requises sur les abonnements ou au moins sur la ressource de l’espace de travail Log Analytics. De même, si la stratégie est limitée au groupe de ressources, l’identité managée doit avoir les autorisations de rôle requises sur l’espace de travail Log Analytics si l’espace de travail n’est pas dans l’étendue du groupe de ressources sélectionné.

Le module complémentaire Monitoring requiert les rôles suivants sur l’identité managée utilisée par Azure Policy :

 - [azure-kubernetes-service-contributor-role](../../role-based-access-control/built-in-roles.md#azure-kubernetes-service-contributor-role)
 - [log-analytics-contributor](../../role-based-access-control/built-in-roles.md#log-analytics-contributor)

## <a name="create-and-assign-policy-definition-using-azure-portal"></a>Créer et attribuer une définition de stratégie avec le portail Azure

### <a name="create-policy-definition"></a>Créer une définition de stratégie

1. Téléchargez la définition Stratégie personnalisée Azure pour activer le module complémentaire AKS Monitoring.
 
    ``` sh
    curl -o azurepolicy.json -L https://aka.ms/aks-enable-monitoring-custom-policy
    ```

3. Accédez à https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyMenuBlade/Definitions et créez la définition de stratégie avec les détails suivants dans la boîte de dialogue de création d’une définition de stratégie.
 
    - **Emplacement de la définition** : Choisissez l’abonnement Azure dans lequel la définition de stratégie doit être stockée.
    - **Nom** : *(Preview)AKS-Monitoring-Addon*
    - **Description** : *Stratégie personnalisée Azure pour activer le module complémentaire Monitoring sur le ou les clusters Azure Kubernetes dans l’étendue spécifiée*
    - **Catégorie** : Choisissez *utiliser élément existant* et sélectionnez *Kubernetes* dans la liste déroulante.
    - **Règle de stratégie** : Supprimez les exemples de règles existants et copiez le contenu du fichier *azurepolicy.json* téléchargé à l’étape 1 ci-dessus.

### <a name="assign-policy-definition-to-specified-scope"></a>Attribuer une définition de stratégie à l’étendue spécifiée

> [!NOTE]
>  L’identité managée sera créée automatiquement et des rôles spécifiés lui seront attribués dans la définition de stratégie.

1. Sélectionnez la définition de stratégie *(Préversion) Module complémentaire AKS Monitoring* que vous venez de créer.
4. Cliquez sur *Attribuer*** et spécifiez une **étendue** pour l’emplacement où la stratégie doit être attribuée. 
5. Cliquez sur **Suivant** et indiquez l’ID de ressource de l’espace de travail Azure Log Analytics. L’ID de ressource doit respecter le format suivant : `/subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroup>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>`.
6. Créez une tâche de correction au cas où vous souhaiteriez appliquer la stratégie à des clusters AKS existants dans l’étendue sélectionnée.
7. Cliquez sur l’option **Vérifier + créer** pour créer l’attribution de stratégie.
   
## <a name="create-and-assign-policy-definition-using-azure-cli"></a>Créer et attribuer une définition de stratégie avec Azure CLI

### <a name="create-policy-definition"></a>Créer une définition de stratégie

1. Téléchargez les fichiers de paramètres et de règles de la définition de stratégie personnalisée Azure à l’aide des commandes suivantes :

    ``` sh
    curl -o azurepolicy.rules.json -L https://aka.ms/aks-enable-monitoring-custom-policy-rules
    curl -o azurepolicy.parameters.json -L https://aka.ms/aks-enable-monitoring-custom-policy-parameters
    ```

2. Créez la définition de stratégie à l’aide de la commande suivante :

    ``` sh
    az cloud set -n <AzureCloud | AzureChinaCloud | AzureUSGovernment> # set the Azure cloud
    az login # login to cloud environment 
    az account set -s <subscriptionId>
    az policy definition create --name "(Preview)AKS-Monitoring-Addon" --display-name "(Preview)AKS-Monitoring-Addon" --mode Indexed --metadata version=1.0.0 category=Kubernetes --rules azurepolicy.rules.json --params azurepolicy.parameters.json
    ```

### <a name="assign-policy-definition-to-specified-scope"></a>Attribuer une définition de stratégie à l’étendue spécifiée

- Créez l’attribution de stratégie à l’aide de la commande suivante :

    ``` sh
    az policy assignment create --name aks-monitoring-addon --policy "(Preview)AKS-Monitoring-Addon" --assign-identity --identity-scope /subscriptions/<subscriptionId> --role Contributor --scope /subscriptions/<subscriptionId> --location <locatio> --role Contributor --scope /subscriptions/<subscriptionId> -p "{ \"workspaceResourceId\": { \"value\":  \"/subscriptions/<subscriptionId>/resourcegroups/<resourceGroupName>/providers/microsoft.operationalinsights/workspaces/<workspaceName>\" } }"
    ```

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur [Azure Policy](../../governance/policy/overview.md).
- En savoir plus sur le [fonctionnement de la sécurité de la correction](../../governance/policy/how-to/remediate-resources.md#how-remediation-security-works).
- [En savoir plus sur Container Insights](./container-insights-overview.md).
- Installez [Azure CLI](/cli/azure/install-azure-cli).