---
title: Configuration d’Azure Monitor pour la collecte de données de l’agent de conteneurs | Microsoft Docs
description: Cet article décrit comment configurer Azure Monitor pour que l’agent de conteneurs contrôle stdout/stderr et la collecte des journaux de variables d’environnement.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/22/2019
ms.author: magoedte
ms.openlocfilehash: 1e7506e311c38d87371dd1b65440b6fb41a7ce78
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/24/2019
ms.locfileid: "67341662"
---
# <a name="configure-agent-data-collection-for-azure-monitor-for-containers"></a>Configurer la collecte de données de l’agent pour Azure Monitor pour conteneurs

Azure Monitor pour conteneurs collecte stdout, stderr et des variables d’environnement à partir de charges de travail de conteneur déployées sur des clusters Kubernetes managés hébergés sur Azure Kubernetes Service (AKS) à partir de l’agent conteneurisé. Vous pouvez configurer les paramètres de collecte de données de l’agent en créant une ConfigMaps Kubernetes personnalisée pour contrôler cette expérience. Cet article montre comment créer la ConfigMap et configurer la collecte de données selon vos besoins.

## <a name="configure-your-cluster-with-custom-data-collection-settings"></a>Configurer votre cluster avec les paramètres de collecte de données personnalisés

Un fichier ConfigMap de modèle est fourni. Vous pouvez le modifier facilement avec vos personnalisations sans avoir à le créer à partir de zéro. Avant de commencer, vous devez consulter la documentation Kubernetes sur [ConfigMaps](https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-configmap/) et vous familiariser avec la manière de créer, de configurer et de déployer ConfigMaps. Cela vous permet de filtrer stderr et stdout par espace de noms ou dans l’ensemble du cluster et les variables d’environnement pour les conteneurs en cours d’exécution sur tous les nœuds/pods du cluster.

>[!IMPORTANT]
>La version minimale de l’agent prise en charge par cette fonctionnalité est microsoft/oms:ciprod06142019 ou version ultérieure. 

### <a name="overview-of-configurable-data-collection-settings"></a>Présentation des paramètres de collecte de données configurables

Voici les paramètres qui peuvent être configurés pour contrôler la collecte de données.

|Clé |Type de données |Valeur |Description |
|----|----------|------|------------|
|`schema-version` |Chaîne (respecte la casse) |v1 |Il s’agit de la version de schéma utilisée par l’agent lors de l’analyse de cette ConfigMap. Actuellement, la version prise en charge est v1. Modifier cette valeur n’est pas pris en charge et est rejeté lors de l’évaluation de l’élément ConfigMap.|
|`config-version` |Chaîne | | Prend en charge la possibilité d’effectuer le suivi de la version de ce fichier de configuration dans votre système/dépôt de contrôle de code source. Le nombre maximal de caractères autorisé est 10 et tous les autres caractères sont tronqués. |
|`[log_collection_settings.stdout] enabled =` |Boolean | true ou false | Ce paramètre contrôle si la collecte de journaux de conteneur stdout est activée. Lorsque la valeur est `true` et qu’aucun espace de noms n’est exclus de la collecte de journaux stdout (paramètre `log_collection_settings.stdout.exclude_namespaces` ci-dessous), les journaux stdout sont collectés à partir de tous les conteneurs parmi l’ensemble des nœuds/pods du cluster. Si elle n’est pas spécifiée dans ConfigMaps, la valeur par défaut est `enabled = true`. |
|`[log_collection_settings.stdout] exclude_namespaces =`|Chaîne | Tableau séparé par des virgules |Tableau d’espaces de noms Kubernetes pour lesquels aucun journal stdout n’est collecté. Ce paramètre est effectif uniquement si `log_collection_settings.stdout.enabled` est défini sur `true`. S’il n’est pas spécifié dans ConfigMap, la valeur par défaut est `exclude_namespaces = ["kube-system"]`.|
|`[log_collection_settings.stderr] enabled =` |Boolean | true ou false |Ce paramètre contrôle si la collecte de journaux de conteneur stderr est activée. Lorsque la valeur est `true` et qu’aucun espace de noms n’est exclus de la collecte de journaux stdout (paramètre `log_collection_settings.stderr.exclude_namespaces`), les journaux stderr sont collectés à partir de tous les conteneurs parmi l’ensemble des nœuds/pods du cluster. Si elle n’est pas spécifiée dans ConfigMaps, la valeur par défaut est `enabled = true`. |
|`[log_collection_settings.stderr] exclude_namespaces =` |Chaîne |Tableau séparé par des virgules |Tableau d’espaces de noms Kubernetes pour lesquels aucun journal stderr n’est collecté. Ce paramètre est effectif uniquement si `log_collection_settings.stdout.enabled` est défini sur `true`. S’il n’est pas spécifié dans ConfigMap, la valeur par défaut est `exclude_namespaces = ["kube-system"]`. |
| `[log_collection_settings.env_var] enabled =` |Boolean | true ou false | Cette option détermine si la collecte de variables d’environnement est activée. Lorsque la valeur est définie sur `false`, aucune variable d’environnement n’est collectée pour les conteneurs exécutés sur tous les nœuds/pods du cluster. Si elle n’est pas spécifiée dans ConfigMap, la valeur par défaut est `enabled = true`. |

### <a name="configure-and-deploy-configmaps"></a>Configurer et déployer ConfigMaps

Procédez comme suit pour configurer et déployer votre fichier de configuration ConfigMap dans votre cluster.

1. [Téléchargez](https://github.com/microsoft/OMS-docker/blob/ci_feature_prod/Kubernetes/container-azm-ms-agentconfig.yaml) le fichier yaml ConfigMap de modèle et enregistrez-le sous le nom container-azm-ms-agentconfig.yaml.  
1. Modifiez le fichier yaml ConfigMap avec vos personnalisations. 

    - Pour exclure des espaces de noms spécifiques pour la collecte de journaux stdout, configurez la clé/valeur à l’aide de l’exemple suivant : `[log_collection_settings.stdout] enabled = true exclude_namespaces = ["my-namespace-1", "my-namespace-2"]`.
    - Pour désactiver la collecte de variables d’environnement pour un conteneur spécifique, définissez la clé/valeur `[log_collection_settings.env_var] enabled = true` pour activer la collecte de variables globalement, puis suivez les étapes décrites [ici](container-insights-manage-agent.md#how-to-disable-environment-variable-collection-on-a-container) pour terminer la configuration pour le conteneur spécifique.
    - Pour désactiver la collecte de journaux stderr au niveau du cluster, configurez la clé/valeur à l’aide de l’exemple suivant : `[log_collection_settings.stderr] enabled = false`.

1. Créez un ConfigMap en exécutant la commande kubectl suivante : `kubectl apply -f <configmap_yaml_file.yaml>`.
    
    Exemple : `kubectl apply -f container-azm-ms-agentconfig.yaml`. 
    
    Quelques minutes peuvent être nécessaires pour que la modification de configuration soit effective. Ensuite, tous les pods omsagent du cluster redémarrent. Le redémarrage s’effectue de façon progressive pour tous les pods omsagent. Tous ne redémarrent pas en même temps. Lorsque les redémarrages sont terminés, un message similaire à celui-ci s’affiche avec les résultats : `configmap "container-azm-ms-agentconfig" created`.

Pour vérifier que la configuration a été correctement appliquée, utilisez la commande suivante pour passer en revue les journaux à partir d’un pod d’agent : `kubectl logs omsagent-fdf58 -n=kube-system`. S’il existe des erreurs de configuration à partir de pods osmagent, la sortie affiche des erreurs similaires à ce qui suit :

``` 
***************Start Config Processing******************** 
config::unsupported/missing config schema version - 'v21' , using defaults
```

Des erreurs empêchent omsagent d’analyser le fichier, ce qui provoque son redémarrage et l’utilisation de la configuration par défaut. Une fois que vous avez corrigé les erreurs dans ConfigMap, enregistrez le fichier yaml et appliquez la ConfigMaps mise à jour en exécutant la commande : `kubectl apply -f <configmap_yaml_file.yaml`.

## <a name="applying-updated-configmap"></a>Application du ConfigMap mis à jour

Si vous avez déjà déployé un ConfigMap pour votre cluster et que vous souhaitez le mettre à jour avec une configuration plus récente, vous pouvez simplement modifier le fichier ConfigMap que vous avez utilisé précédemment, puis l’appliquer à l’aide de la même commande qu’avant, `kubectl apply -f <configmap_yaml_file.yaml`.

Quelques minutes peuvent être nécessaires pour que la modification de configuration soit effective. Ensuite, tous les pods omsagent du cluster redémarrent. Le redémarrage s’effectue de façon progressive pour tous les pods omsagent. Tous ne redémarrent pas en même temps. Lorsque les redémarrages sont terminés, un message similaire à celui-ci s’affiche avec les résultats : `configmap "container-azm-ms-agentconfig" updated`.

## <a name="verifying-schema-version"></a>Vérification de la version du schéma

Les versions de schéma de configuration pris en charge sont disponibles en tant qu’annotation de pod (versions de schéma) sur le pod omsagent. Vous pouvez les voir avec la commande kubectl suivante : `kubectl describe pod omsagent-fdf58 -n=kube-system`

La sortie qui s’affiche est similaire à ce qui suit avec les versions de schéma d’annotation :

```
    Name:           omsagent-fdf58
    Namespace:      kube-system
    Node:           aks-agentpool-95673144-0/10.240.0.4
    Start Time:     Mon, 10 Jun 2019 15:01:03 -0700
    Labels:         controller-revision-hash=589cc7785d
                    dsName=omsagent-ds
                    pod-template-generation=1
    Annotations:    agentVersion=1.10.0.1
                  dockerProviderVersion=5.0.0-0
                    schema-versions=v1 
```

## <a name="next-steps"></a>Étapes suivantes

- Pour continuer à découvrir comment utiliser Azure Monitor et surveiller les autres aspects de votre cluster AKS, consultez [Connaître l’état d’Azure Kubernetes Service](container-insights-analyze.md).
- Consultez les [exemples de requêtes de journal](container-insights-log-search.md#search-logs-to-analyze-data) pour voir les requêtes prédéfinies et des exemples permettant d’évaluer ou de personnaliser la génération d’alertes, la visualisation ou l’analyse de vos clusters.