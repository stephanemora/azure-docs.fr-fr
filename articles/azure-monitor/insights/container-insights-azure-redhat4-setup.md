---
title: Configurer Azure Red Hat OpenShift v4.x avec Azure Monitor pour conteneurs | Microsoft Docs
description: Cet article explique comment configurer la supervision d’un cluster Kubernetes avec Azure Monitor hébergé sur Azure Red Hat OpenShift version 4 et ultérieures.
ms.topic: conceptual
ms.date: 04/22/2020
ms.openlocfilehash: 4b827524845874dabaabe535163d99c408f77a60
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82195748"
---
# <a name="configure-azure-red-hat-openshift-v4x-with-azure-monitor-for-containers"></a>Configurer Azure Red Hat OpenShift v4.x avec Azure Monitor pour conteneurs

Azure Monitor pour les conteneurs offre une expérience d’analyse riche pour les clusters Azure Kubernetes Service (AKS) et Moteur AKS. Cet article explique comment activer la supervision des clusters Kubernetes hébergés [Azure Red Hat OpenShift](../../openshift/intro-openshift.md) version 4.x et ultérieures pour obtenir une expérience de supervision similaire.

>[!NOTE]
>La prise en charge d’Azure Red Hat OpenShift est actuellement une fonctionnalité en préversion publique.
>

Azure Monitor pour conteneurs peut être activé pour un ou plusieurs déploiements existants d’Azure Red Hat OpenShift v4.x à l’aide des méthodes prises en charge suivantes :

- Pour un cluster existant à l’aide du script bash fourni et en cours d’exécution dans [Azure CLI](https://docs.microsoft.com/cli/azure/openshift?view=azure-cli-latest#az-openshift-create).

## <a name="supported-and-unsupported-features"></a>Fonctionnalités prises en charge et non prises en charge

Azure Monitor pour conteneurs prend en charge la supervision d’Azure Red Hat OpenShift v4.x comme décrit dans l’article [Vue d’ensemble](container-insights-overview.md), à l’exception des fonctionnalités suivantes :

- Données actives (préversion)
- [Collecter les métriques](container-insights-update-metrics.md) à partir des nœuds de cluster et des Pod, et les stocker dans la base de données de métriques Azure Monitor

## <a name="prerequisites"></a>Prérequis

- Azure CLI version 2.0.72 ou ultérieure

- Outil CLI [Helm 3](https://helm.sh/docs/intro/install/)

- [Bash version 4](https://www.gnu.org/software/bash/)

- Outil en ligne de commande [Kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)

- Pour activer et accéder aux fonctionnalités d’Azure Monitor pour les conteneurs, vous devez au minimum être membre du rôle Azure *Contributeur* dans l’abonnement Azure et membre du rôle [*Contributeur Log Analytics*](../platform/manage-access.md#manage-access-using-azure-permissions) de l’espace de travail Log Analytics configuré avec Azure Monitor pour les conteneurs.

- Pour afficher les données de surveillance, vous êtes un membre de l’autorisation du rôle [*Lecteur Log Analytics*](../platform/manage-access.md#manage-access-using-azure-permissions) avec l’espace de travail Log Analytics configuré avec Azure Monitor pour les conteneurs.

## <a name="enable-for-an-existing-cluster"></a>Activer pour un cluster existant

Effectuez les étapes suivantes pour activer la supervision d’un cluster Azure Red Hat OpenShift version 4 et ultérieures déployé dans Azure à l’aide du script bash fourni.

1. Connexion à Azure

    ```azurecli
    az login
    ```

2. Téléchargez et enregistrez dans un dossier local le script qui configure votre cluster avec le module complémentaire de supervision à l’aide des commandes suivantes :

    `curl -LO https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/aroV4/onboarding_azuremonitor_for_containers.sh.`

3. Pour identifier le **kube-context** de votre cluster, une fois connecté à votre cluster avec `oc login`, exécutez la commande `kubectl config current-context` et copiez la valeur.

### <a name="integrate-with-an-existing-workspace"></a>Intégrer avec un espace de travail existant

L’étape suivante permet de superviser votre cluster à l’aide du script bash que vous avez téléchargé. Pour effectuer une intégration avec un espace de travail Log Analytics existant, effectuez les étapes suivantes pour identifier tout d’abord l’ID de ressource complet de votre espace de travail Log Analytics requis pour le paramètre `workspaceResourceId`, puis exécutez la commande pour activer l’extension de supervision par rapport à l’espace de travail spécifié. Si vous n’avez pas d’espace de travail à spécifier, vous pouvez passer à l’étape 5 et laisser le script créer un nouvel espace de travail pour vous.

1. Affichez la liste de tous les abonnements auxquels vous avez accès à l’aide de la commande suivante :

    ```azurecli
    az account list --all -o table
    ```

    La sortie doit ressembler à ce qui suit :

    ```azurecli
    Name                                  CloudName    SubscriptionId                        State    IsDefault
    ------------------------------------  -----------  ------------------------------------  -------  -----------
    Microsoft Azure                       AzureCloud   68627f8c-91fO-4905-z48q-b032a81f8vy0  Enabled  True
    ```

    Copiez la valeur de **SubscriptionId**.

2. Basculez vers l’abonnement qui héberge l’espace de travail Log Analytics à l’aide de la commande suivante :

    ```azurecli
    az account set -s <subscriptionId of the workspace>
    ```

3. L’exemple suivant affiche la liste des espaces de travail de vos abonnements au format JSON par défaut.

    ```
    az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json
    ```

    Dans la sortie, recherchez le nom de l’espace de travail, puis copiez l’ID de ressource complet de cet espace de travail Log Analytics sous le champ **ID**.

4. Exécutez la commande suivante pour activer la supervision, en remplaçant la valeur du paramètre `workspaceResourceId` : 

    `bash onboarding_azuremonitor_for_containers.sh <kube-context> <azureAroV4ResourceId> <LogAnayticsWorkspaceResourceId>`

    Exemple :

    `bash onboarding_azuremonitor_for_containers.sh MyK8sTestCluster /subscriptions/57ac26cf-a9f0-4908-b300-9a4e9a0fb205/resourceGroups/test-aro-v4-rg/providers/Microsoft.RedHatOpenShift/OpenShiftClusters/test-aro-v4  /subscriptions/57ac26cf-a9f0-4908-b300-9a4e9a0fb205/resourcegroups/test-la-workspace-rg/providers/microsoft.operationalinsights/workspaces/test-la-workspace`

Une fois que vous avez activé la surveillance, 15 minutes peuvent s’écouler avant que vous puissiez voir les métriques d’intégrité du cluster.

### <a name="integrate-with-default-workspace"></a>Intégrer avec l’espace de travail par défaut

L’étape suivante permet de superviser votre cluster Azure Red Hat OpenShift v4.x à l’aide du script bash que vous avez téléchargé. Dans cet exemple, vous n'êtes pas obligé de créer ou de spécifier un espace de travail existant. Cette commande simplifie le processus en créant un espace de travail par défaut dans le groupe de ressources par défaut de l’abonnement cluster s’il n’existe pas dans la région. L’espace de travail créé par défaut est semblable au format de *DefaultWorkspace-\<GUID>-\<Région>* .  

    `bash onboarding_azuremonitor_for_containers.sh <kube-context> <azureAroV4ResourceId>`

    For example:

    `bash onboarding_azuremonitor_for_containers.sh MyK8sTestCluster /subscriptions/57ac26cf-a9f0-4908-b300-9a4e9a0fb205/resourceGroups/test-aro-v4-rg/providers/Microsoft.RedHatOpenShift/OpenShiftClusters/test-aro-v4`

Une fois que vous avez activé la surveillance, 15 minutes peuvent s’écouler avant que vous puissiez voir les métriques d’intégrité du cluster.

### <a name="from-the-azure-portal"></a>À partir du portail Azure

La vue multicluster dans Azure Monitor pour conteneurs met en surbrillance vos clusters Azure Red Hat OpenShift pour lesquels la supervision n’est pas activée sous l’onglet **Clusters non supervisés**. L’option **Activer** en regard de votre cluster ne lance pas l’intégration de la supervision à partir du portail. Vous êtes redirigé vers cet article pour activer manuellement la supervision en effectuant les étapes décrites plus haut dans cet article.

1. Connectez-vous au [portail Azure](https://portal.azure.com).

2. Dans le menu du Portail Azure ou dans la page Accueil, sélectionnez **Azure Monitor**. Sous la section **Insights**, sélectionnez **Conteneurs**.

3. Dans la page **Monitor - Conteneurs**, sélectionnez **Clusters non analysés**.

4. Dans la liste des clusters non surveillés, recherchez le cluster dans la liste et cliquez sur **Activer**. Vous pouvez identifier les résultats dans la liste en recherchant la valeur **ARO** sous la colonne **TYPE DE CLUSTER**. Après avoir cliqué sur **Activer**, vous êtes redirigé vers cet article.

## <a name="next-steps"></a>Étapes suivantes

- Une fois la supervision activée pour collecter l’utilisation des ressources et l’intégrité de votre cluster Red Hat OpenShift version 4.x et des charges de travail s’y exécutant, découvrez [comment utiliser](container-insights-analyze.md) Azure Monitor pour conteneurs.

- Par défaut, l’agent conteneurisé collecte les journaux de conteneurs stdout/ stderr de tous les conteneurs en cours d’exécution dans tous les espaces de noms, sauf kube-system. Pour configurer la collecte des journaux de conteneurs propres à un ou plusieurs espaces de noms particuliers, consultez [Configurer la collecte de données de l’agent pour Azure Monitor pour conteneurs](container-insights-agent-config.md) afin de configurer les paramètres de collecte de données souhaités dans votre fichier de configurations ConfigMap.

- Pour scraper et analyser des métriques Prometheus à partir de votre cluster, consultez [Configurer la capture des métriques Prometheus avec Azure Monitor pour conteneurs](container-insights-prometheus-integration.md).

- Pour savoir comment arrêter la surveillance de votre cluster avec Azure Monitor pour conteneurs, consultez [Comment arrêter la surveillance de votre cluster Azure Red Hat OpenShift](container-insights-optout-openshift.md).
