---
title: Protéger des déploiements Kubernetes hybrides et multicloud avec Azure Defender pour Kubernetes
description: Utiliser Azure Defender pour Kubernetes avec vos clusters Kubernetes locaux et multicloud
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 04/06/2021
ms.author: memildin
ms.openlocfilehash: f59c8b4e4f4ddc32cb0ec14e264e4dbb5b093971
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2021
ms.locfileid: "108762854"
---
# <a name="defend-azure-arc-enabled-kubernetes-clusters-running-in-on-premises-and-multi-cloud-environments"></a>Défendre des clusters Kubernetes avec Azure Arc s’exécutant dans des environnements locaux et multicloud

L’**Extension de cluster Azure Defender pour Kubernetes** peut défendre vos clusters locaux avec les mêmes fonctionnalités de détection des menaces que celles offertes pour les clusters Azure Kubernetes Service. Activez [Kubernetes avec Azure Arc](../azure-arc/kubernetes/overview.md) sur vos clusters et déployez l’extension comme décrit dans cette page. 

L’extension peut également protéger des clusters Kubernetes sur d’autres fournisseurs de cloud, mais pas sur leurs services Kubernetes managés.

> [!TIP]
> Nous avons mis en place des exemples de fichiers destinés à faciliter le processus d’installation dans [Exemples d’installation sur GitHub](https://aka.ms/kubernetes-extension-installation-examples).

## <a name="availability"></a>Disponibilité

| Aspect | Détails |
|--------|---------|
| État de publication | **Préversion**<br>[!INCLUDE [Legalese](../../includes/security-center-preview-legal-text.md)]|
| Rôles et autorisations obligatoires | L’[administrateur de sécurité](../role-based-access-control/built-in-roles.md#security-admin) peut ignorer les alertes<br>Le [Lecteur de sécurité](../role-based-access-control/built-in-roles.md#security-reader) peut afficher les résultats |
| Tarifs | Nécessite [Azure Defender pour Kubernetes](defender-for-kubernetes-introduction.md) |
| Distributions Kubernetes prises en charge | [Service Azure Kubernetes sur Azure Stack HCI](/azure-stack/aks-hci/overview)<br>[Kubernetes](https://kubernetes.io/docs/home/)<br> [Moteur AKS](https://github.com/Azure/aks-engine)<br> [Red Hat OpenShift](https://www.openshift.com/learn/topics/kubernetes/) (version 4.6 ou plus récente)<br> [VMware Tanzu Kubernetes Grid](https://tanzu.vmware.com/kubernetes-grid)<br> [Rancher Kubernetes Engine](https://rancher.com/docs/rke/latest/en/) |
| Limites | Kubernetes avec Azure Arc et l’extension Azure Defender **ne prennent pas en charge** les offres Kubernetes managées, telles que Google Kubernetes Engine et Elastic Kubernetes Service. [Azure Defender est disponible en mode natif pour Azure Kubernetes Service (AKS)](defender-for-kubernetes-introduction.md), et ne nécessite pas de connexion du cluster à Azure Arc. |
| Environnements et régions | La disponibilité de cette extension est identique à celle de [Kubernetes avec Azure Arc](../azure-arc/kubernetes/overview.md)|

## <a name="architecture-overview"></a>Vue d’ensemble de l’architecture

Pour tous les clusters Kubernetes autres que AKS, vous devez connecter votre cluster à Azure Arc. Une fois le cluster connecté, Azure Defender pour Kubernetes peut être déployé sur des ressources [Kubernetes Azure avec Azure Arc](../azure-arc/kubernetes/overview.md) en tant qu’[extension de cluster](../azure-arc/kubernetes/extensions.md).

Les composants d’extension collectent les données des journaux d’audit Kubernetes de tous les nœuds de plan de contrôle du cluster, et les envoient serveur principal Azure Defender pour Kubernetes dans le cloud à des fins d’analyse plus approfondie. L’extension est inscrite auprès d’un espace de travail Log Analytics utilisé comme pipeline de données, mais les données du journal d’audit ne sont pas stockées dans l’espace de travail Log Analytics.

Ce diagramme illustre l’interaction entre Azure Defender pour Kubernetes et le cluster Kubernetes avec Azure Arc :

:::image type="content" source="media/defender-for-kubernetes-azure-arc/defender-for-kubernetes-architecture-overview.png" alt-text="Diagramme d’architecture de haut niveau présentant l’interaction entre Azure Defender pour Kubernetes et un cluster Kubernetes avec Azure Arc." lightbox="media/defender-for-kubernetes-azure-arc/defender-for-kubernetes-architecture-overview.png":::

## <a name="prerequisites"></a>Prérequis

- Azure Defender pour Kubernetes est [activé sur votre abonnement](enable-azure-defender.md)
- Votre cluster Kubernetes est [connecté à Azure Arc](../azure-arc/kubernetes/quickstart-connect-cluster.md)
- Vous avez rempli les conditions préalables indiquées dans la [documentation sur les extensions de cluster génériques](../azure-arc/kubernetes/extensions.md#prerequisites).

## <a name="deploy-the-azure-defender-extension"></a>Déployer l’extension Azure Defender

Vous pouvez déployer l’extension Azure Defender à l’aide d’une plage de méthodes. Pour obtenir des instructions détaillées, sélectionnez l’onglet approprié.

### <a name="azure-portal"></a>[**Portail Azure**](#tab/k8s-deploy-asc)

### <a name="use-the-fix-button-from-the-security-center-recommendation"></a>Utilisez le bouton Corriger de la recommandation de Security Center

Une recommandation dédiée dans Azure Security Center offre ce qui suit :

- **Visibilité** sur les clusters sur lesquels l’extension Defender pour Kubernetes est déployée
- Bouton **Corriger** pour opérer un déploiement sur ces clusters sans l’extension

1. Dans la page des recommandations d’Azure Security Center, ouvrez le contrôle de sécurité **Activer Azure Defender**.

1. Utilisez le filtre pour trouver la recommandation nommée **Les clusters Kubernetes avec Azure Arc doivent avoir l’extension Azure Defender installée**.

    :::image type="content" source="media/defender-for-kubernetes-azure-arc/extension-recommendation.png" alt-text="Recommandation d’Azure Security Center pour le déploiement de l’extension Azure Defender pour les clusters Kubernetes avec Azure Arc." lightbox="media/defender-for-kubernetes-azure-arc/extension-recommendation.png":::

    > [!TIP]
    > Notez l’icône **Corriger** dans la colonne actions

1. Sélectionnez l’extension pour afficher les détails des ressources saines et non saines (clusters avec et sans extension).

1. Dans la liste ressources non saines, sélectionnez un cluster, choisissez **Corriger** pour ouvrir le volet contenant les options de correction.

1. Sélectionnez l’espace de travail Log Analytics approprié, puis choisissez **Corriger la ressource x**.

    :::image type="content" source="media/defender-for-kubernetes-azure-arc/security-center-deploy-extension.gif" alt-text="Déployez l’extension Azure Defender pour Azure Arc avec l’option de correctif de Security Center.":::


### <a name="azure-cli"></a>[**Azure CLI**](#tab/k8s-deploy-cli)

### <a name="use-azure-cli-to-deploy-the-azure-defender-extension"></a>Utiliser Azure CLI pour déployer l’extension Azure Defender

1. Connexion à Azure :

    ```azurecli
    az login
    az account set --subscription <your-subscription-id>
    ```

    > [!IMPORTANT]
    > Veillez à utiliser le même ID d’abonnement pour ``<your-subscription-id>`` que celui utilisé lors de la connexion de votre cluster à Azure Arc.

1. Exécutez la commande suivante pour déployer l’extension sur votre cluster Kubernetes avec Azure Arc :

    ```azurecli
    az k8s-extension create --name microsoft.azuredefender.kubernetes --cluster-type connectedClusters --cluster-name <cluster-name> --resource-group <resource-group> --extension-type microsoft.azuredefender.kubernetes
    ```

    Vous trouverez ci-dessous une description de tous les paramètres de configuration pris en charge sur le type d’extension Azure Defender :

    | Propriété | Description |
    |----------|-------------|
    | logAnalyticsWorkspaceResourceID | **Facultatif**. ID de ressource complet de votre propre espace de travail Log Analytics.<br>Quand il n’est pas fourni, l’espace de travail par défaut de la région est utilisé.<br><br>Pour obtenir l’ID de ressource complet, exécutez la commande suivante pour afficher la liste des espaces de travail dans vos abonnements au format JSON par défaut :<br>```az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json```<br><br>La syntaxe de L’ID de ressource de l’espace de travail Log Analytics est la suivante :<br>/subscriptions/{id de votre abonnement}/resourceGroups/{votre groupe de ressources}/providers/Microsoft.OperationalInsights/workspaces/{nom de votre espace de travail}. <br>En savoir plus sur les [Espaces de travail Log Analytics](../azure-monitor/logs/data-platform-logs.md#log-analytics-workspaces) |
    | auditLogPath |**Facultatif**. Chemin d’accès complet aux fichiers journaux d’audit.<br>Quand il n’est pas fourni, le chemin d’accès par défaut ``/var/log/kube-apiserver/audit.log`` est utilisé.<br>Pour le moteur AKS, le chemin d’accès standard est ``/var/log/kubeaudit/audit.log`` |

    La commande ci-dessous montre un exemple d’utilisation de tous les champs facultatifs :

    ```azurecli
    az k8s-extension create --name microsoft.azuredefender.kubernetes --cluster-type connectedClusters --cluster-name <your-connected-cluster-name> --resource-group <your-rg> --extension-type microsoft.azuredefender.kubernetes --configuration-settings logAnalyticsWorkspaceResourceID=<log-analytics-workspace-resource-id> auditLogPath=<your-auditlog-path>
    ```

### <a name="resource-manager"></a>[**Gestionnaire de ressources**](#tab/k8s-deploy-resource-manager)

### <a name="use-azure-resource-manager-to-deploy-the-azure-defender-extension"></a>Utiliser Azure Resource Manager pour déployer l’extension Azure Defender

Pour utiliser Azure Resource Manager pour déployer l’extension Azure Defender, vous avez besoin d’un espace de travail Log Analytics sur votre abonnement. En savoir plus sur les [Espaces de travail Log Analytics](../azure-monitor/logs/data-platform-logs.md#log-analytics-workspaces).

Vous pouvez utiliser le modèle Resource Manager **azure-defender-extension-arm-template.json** figurant dans les [exemples d’installation](https://aka.ms/kubernetes-extension-installation-examples) de Security Center.

> [!TIP]
> Si vous ne connaissez pas les modèles Resource Manager, commencez ici : [Que sont les modèles Azure Resource Manager ?](../azure-resource-manager/templates/overview.md)

### <a name="rest-api"></a>[**API REST**](#tab/k8s-deploy-api)

### <a name="use-rest-api-to-deploy-the-azure-defender-extension"></a>Utiliser l’API REST pour déployer l’extension Azure Defender 

Pour utiliser l’API REST afin de déployer l’extension Azure Defender, vous avez besoin d’un espace de travail Log Analytics sur votre abonnement. En savoir plus sur les [Espaces de travail Log Analytics](../azure-monitor/logs/data-platform-logs.md#log-analytics-workspaces).

> [!TIP]
> La façon la plus simple d’utiliser l’API pour déployer l’extension Azure Defender consiste à utiliser l’exemple **JSON de collection Postman** fourni parmi les [Exemples d’installation](https://aka.ms/kubernetes-extension-installation-examples) de Security Center.
- Pour modifier le JSON de collection Postamn, ou pour déployer manuellement l’extension avec l’API REST, exécutez la commande PUT suivante :

    ```rest
    PUT https://management.azure.com/subscriptions/{{Subscription Id}}/resourcegroups/{{Resource Group}}/providers/Microsoft.Kubernetes/connectedClusters/{{Cluster Name}}/providers/Microsoft.KubernetesConfiguration/extensions/microsoft.azuredefender.kubernetes?api-version=2020-07-01-preview
    ```

    Où :

    | Nom            | Dans   | Obligatoire | Type   | Description                                  |
    |-----------------|------|----------|--------|----------------------------------------------|
    | Identifiant d’abonnement | path | True     | string | ID d’abonnement de votre ressource Kubernetes avec Azure Arc |
    | Groupe de ressources  | path | True     | string | Nom du groupe de ressources contenant votre ressource Kubernetes avec Azure Arc |
    | Nom du cluster    | path | True     | string | Nom de votre ressource Kubernetes avec Azure Arc  |


    Pour l’**authentification**, votre en-tête doit avoir un jeton du porteur (comme pour d’autres API Azure). Pour obtenir un jeton du porteur, exécutez la commande suivante :

    ```az account get-access-token --subscription <your-subscription-id>``` Utilisez la structure suivante pour le corps de votre message :
    ```json
    { 
    "properties": { 
        "extensionType": "microsoft.azuredefender.kubernetes",
    "con    figurationSettings": { 
            "logAnalytics.workspaceId":"YOUR-WORKSPACE-ID"
        // ,    "auditLogPath":"PATH/TO/AUDITLOG"
        },
        "configurationProtectedSettings": {
            "logAnalytics.key":"YOUR-WORKSPACE-KEY"
        }
        }
    } 
    ```

    La description des propriétés figure ci-dessous :

    | Propriété | Description | 
    | -------- | ----------- |
    | logAnalytics.workspaceId | ID d’espace de travail de la ressource Log Analytics |
    | logAnalytics.key         | Clé de la ressource Log Analytics | 
    | auditLogPath             | **Facultatif**. Chemin d’accès complet aux fichiers journaux d’audit. La valeur par défaut est ``/var/log/kube-apiserver/audit.log``. |

---

## <a name="verify-the-deployment"></a>Vérifier le déploiement

Pour vérifier que l’extension Azure Defender est installée sur votre cluster, suivez les étapes de l’un des onglets ci-dessous :

### <a name="azure-portal---security-center"></a>[**Portail Azure – Security Center**](#tab/k8s-verify-asc)

### <a name="use-security-center-recommendation-to-verify-the-status-of-your-extension"></a>Utiliser une recommandation de Security Center pour vérifier l’état de votre extension

1. Dans la page des recommandations d’Azure Security Center, ouvrez le contrôle de sécurité **Activer Azure Defender**.

1. Sélectionnez la recommandation nommée **L’extension d’Azure Defender doit être installée sur les clusters Kubernetes avec Azure Arc**.

    :::image type="content" source="media/defender-for-kubernetes-azure-arc/extension-recommendation.png" alt-text="Recommandation d’Azure Security Center pour le déploiement de l’extension Azure Defender pour les clusters Kubernetes avec Azure Arc." lightbox="media/defender-for-kubernetes-azure-arc/extension-recommendation.png":::

1. Vérifiez que le cluster sur lequel vous avez déployé l’extension est répertorié comme **Sain**.


### <a name="azure-portal---azure-arc"></a>[**Portail Azure – Azure Arc**](#tab/k8s-verify-arc)

### <a name="use-the-azure-arc-pages-to-verify-the-status-of-your-extension"></a>Utiliser les pages Azure Arc pour vérifier l’état de votre extension

1. Dans le portail Azure, ouvrez **Azure Arc**.
1. Dans la liste d’infrastructures, sélectionnez **Clusters Kubernetes**, puis choisissez le cluster spécifique.
1. Ouvrez la page Extensions. Les extensions sur le cluster sont répertoriées. Vérifiez la colonne **État de l’installation** pour vérifier que l’extension Azure Defender a été correctement installée.

    :::image type="content" source="media/defender-for-kubernetes-azure-arc/extension-installed-clusters-page.png" alt-text="Page Azure Arc pour vérifier l’état de toutes les extensions installées sur un cluster Kubernetes." lightbox="media/defender-for-kubernetes-azure-arc/extension-installed-clusters-page.png":::

1. Pour plus d’informations, sélectionnez l’extension.

    :::image type="content" source="media/defender-for-kubernetes-azure-arc/extension-details-page.png" alt-text="Détails complets d’une extension Azure Arc sur un cluster Kubernetes.":::


### <a name="azure-cli"></a>[**Azure CLI**](#tab/k8s-verify-cli)

### <a name="use-azure-cli-to-verify-that-the-extension-is-deployed"></a>Utiliser Azure CLI pour vérifier que l’extension est déployée

1. Exécutez la commande suivante sur Azure CLI :

    ```azurecli
    az k8s-extension show --cluster-type connectedClusters --cluster-name <your-connected-cluster-name> --resource-group <your-rg> --name microsoft.azuredefender.kubernetes
    ```

1. Dans la réponse, recherchez « extensionType »: « microsoft.azuredefender.kubernetes » et « installState »: « Installed ».

    > [!NOTE]
    > La réponse peut afficher « installState » : « Pending » pour les premières minutes.
    
1. Si l’état est **Installed**, exécutez la commande suivante sur votre machine avec le fichier `kubeconfig` pointé vers votre cluster pour vérifier qu’un pod appelé « azuredefender-xxxxx » est dans l’état « Running » :
    
    ```console
    kubectl get pods -n azuredefender
    ```

### <a name="rest-api"></a>[**API REST**](#tab/k8s-verify-api)

### <a name="use-the-rest-api-to-verify-that-the-extension-is-deployed"></a>Utiliser l’API REST pour vérifier que l’extension est déployée

Pour confirmer la réussite du déploiement ou valider l’état de votre extension à tout moment :

1. Exécutez la commande GET suivante :

    ```rest
    GET https://management.azure.com/subscriptions/{{Subscription Id}}/resourcegroups/{{Resource Group}}/providers/Microsoft.Kubernetes/connectedClusters/{{Cluster Name}}/providers/Microsoft.KubernetesConfiguration/extensions/microsoft.azuredefender.kubernetes?api-version=2020-07-01-preview
    ```

1. Dans la réponse, recherchez, dans « extensionType »: « microsoft.azuredefender.kubernetes », « installState »: « Installed ».

    > [!TIP]
    > La réponse peut afficher « installState » : « Pending » pour les premières minutes.
    
1. Si l’état est **Installed**, exécutez la commande suivante sur votre machine avec le fichier `kubeconfig` pointé vers votre cluster pour vérifier qu’un pod appelé « azuredefender-xxxxx » est dans l’état « Running » :
    
    ```console
    kubectl get pods -n azuredefender
    ```
---

## <a name="simulate-security-alerts-from-azure-defender-for-kubernetes"></a>Simuler des alertes de sécurité d’Azure Defender pour Kubernetes

La liste complète des alertes prises en charge est disponible dans le [tableau de référence de toutes les alertes de sécurité dans Azure Security Center](alerts-reference.md#alerts-akscluster).

1. Pour simuler une alerte Azure Defender, exécutez la commande suivante :

    ```console
    kubectl get pods --namespace=asc-alerttest-662jfi039n
    ```

    La réponse attendue est « Aucune ressource trouvée ».

    Dans les 30 minutes, Azure Defender détectera cette activité et déclenchera une alerte de sécurité.

1. Dans le portail Azure, ouvrez la page des alertes de sécurité d’Azure Security Center et recherchez l’alerte sur la ressource concernée :

    :::image type="content" source="media/defender-for-kubernetes-azure-arc/sample-kubernetes-security-alert.png" alt-text="Exemple d’alerte d’Azure Defender pour Kubernetes." lightbox="media/defender-for-kubernetes-azure-arc/sample-kubernetes-security-alert.png":::

## <a name="removing-the-azure-defender-extension"></a>Suppression de l’extension Azure Defender

Vous pouvez supprimer l’extension à l’aide du portail Azure, d’Azure CLI ou de l’API REST, comme expliqué dans les onglets ci-dessous.

### <a name="azure-portal---arc"></a>[**Azure portal – Arc**](#tab/k8s-remove-arc)

### <a name="use-azure-portal-to-remove-the-extension"></a>Utiliser le portail Azure pour supprimer l’extension

1. Dans le portail Azure, ouvrez Azure Arc.
1. Dans la liste d’infrastructures, sélectionnez **Clusters Kubernetes**, puis choisissez le cluster spécifique.
1. Ouvrez la page Extensions. Les extensions sur le cluster sont répertoriées.
1. Sélectionnez le cluster, puis choisissez **Désinstaller**.

    :::image type="content" source="media/defender-for-kubernetes-azure-arc/extension-uninstall-clusters-page.png" alt-text="Suppression d’une extension de votre cluster Kubernetes avec Azure Arc." lightbox="media/defender-for-kubernetes-azure-arc/extension-uninstall-clusters-page.png":::

### <a name="azure-cli"></a>[**Azure CLI**](#tab/k8s-remove-cli)

### <a name="use-azure-cli-to-remove-the-azure-defender-extension"></a>Utiliser Azure CLI pour supprimer l’extension Azure Defender

1. Supprimez l’extension Azure Defender pour Kubernetes avec Azure Arc avec les commandes suivantes :

    ```azurecli
    az login
    az account set --subscription <subscription-id>
    az k8s-extension delete --cluster-type connectedClusters --cluster-name <your-connected-cluster-name> --resource-group <your-rg> --name microsoft.azuredefender.kubernetes --yes
    ```

    La suppression de l’extension peut prendre quelques minutes. Nous vous recommandons d’attendre avant d’essayer de vérifier qu’elle a réussi.

1. Pour vérifier que l’extension a été supprimée avec succès, exécutez les commandes suivantes :

    ```azurecli
    az k8s-extension show --cluster-type connectedClusters --cluster-name <your-connected-cluster-name> --resource-group <your-rg> --name microsoft.azuredefender.kubernetes
    ```

    La suppression de la ressource d’extension d’Azure Resource Manager devrait être immédiate. Après cela, vérifiez qu’aucun pod nommé « azuredefender-XXXXX » ne figure sur le cluster en exécutant la commande suivante avec le fichier `kubeconfig` pointé vers votre cluster : 

    ```console
    kubectl get pods -n azuredefender
    ```

    La suppression des pods peut prendre quelques minutes.

### <a name="rest-api"></a>[**API REST**](#tab/k8s-remove-api)

### <a name="use-rest-api-to-remove-the-azure-defender-extension"></a>Utiliser l’API REST pour supprimer l’extension Azure Defender 

Pour supprimer l’extension à l’aide de l’API REST, exécutez la commande DELETE suivante :

```rest
DELETE https://management.azure.com/subscriptions/{{Subscription Id}}/resourcegroups/{{Resource Group}}/providers/Microsoft.Kubernetes/connectedClusters/{{Cluster Name}}/providers/Microsoft.KubernetesConfiguration/extensions/microsoft.azuredefender.kubernetes?api-version=2020-07-01-preview
```

| Nom            | Dans   | Obligatoire | Type   | Description                                           |
|-----------------|------|----------|--------|-------------------------------------------------------|
| Identifiant d’abonnement | path | True     | string | ID d’abonnement de votre cluster Kubernetes avec Azure Arc |
| Groupe de ressources  | path | True     | string | Groupe de ressources de votre cluster Kubernetes avec Azure Arc  |
| Nom du cluster    | path | True     | string | Nom de votre cluster Kubernetes avec Azure Arc            |

Pour l’**authentification**, votre en-tête doit avoir un jeton du porteur (comme pour d’autres API Azure). Pour obtenir un jeton du porteur, exécutez la commande suivante :

```azurecli
az account get-access-token --subscription <your-subscription-id>
```

L’exécution de la demande peut prendre plusieurs minutes.

---

## <a name="next-steps"></a>Étapes suivantes

Cette page vous a expliqué comment déployer l’extension Azure Defender pour des clusters Kubernetes avec Azure Arc. Pour en savoir plus sur Azure Defender et les fonctionnalités de sécurité de conteneur d’Azure Security Center, consultez les pages suivantes :

- [Sécurité des conteneurs dans Security Center](container-security.md)
- [Présentation d’Azure Defender pour Kubernetes](defender-for-kubernetes-introduction.md)
- [Protéger vos charges de travail Kubernetes](kubernetes-workload-protections.md)
