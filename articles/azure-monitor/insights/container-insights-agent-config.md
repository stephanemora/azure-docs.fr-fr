---
title: Configuration d’Azure Monitor pour la collecte de données de l’agent de conteneurs | Microsoft Docs
description: Cet article décrit comment configurer Azure Monitor pour que l’agent de conteneurs contrôle stdout/stderr et la collecte des journaux de variables d’environnement.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 10/15/2019
ms.openlocfilehash: 0d654dc05668a71b0fe69de32e5c09f8936951f8
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/09/2019
ms.locfileid: "74951579"
---
# <a name="configure-agent-data-collection-for-azure-monitor-for-containers"></a>Configurer la collecte de données de l’agent pour Azure Monitor pour conteneurs

Azure Monitor pour conteneurs collecte stdout, stderr et des variables d’environnement à partir de charges de travail de conteneur déployées sur des clusters Kubernetes managés hébergés sur Azure Kubernetes Service (AKS) à partir de l’agent conteneurisé. Vous pouvez configurer les paramètres de collecte de données de l’agent en créant une ConfigMaps Kubernetes personnalisée pour contrôler cette expérience. 

Cet article montre comment créer la ConfigMap et configurer la collecte de données selon vos besoins.

## <a name="configmap-file-settings-overview"></a>Vue d’ensemble des paramètres de fichier ConfigMap

Un fichier ConfigMap de modèle est fourni. Vous pouvez le modifier facilement avec vos personnalisations sans avoir à le créer à partir de zéro. Avant de commencer, vous devez consulter la documentation Kubernetes sur [ConfigMaps](https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-configmap/) et vous familiariser avec la manière de créer, de configurer et de déployer ConfigMaps. Cela vous permet de filtrer stderr et stdout par espace de noms ou dans l’ensemble du cluster et les variables d’environnement pour les conteneurs en cours d’exécution sur tous les nœuds/pods du cluster.

>[!IMPORTANT]
>La version minimale de l’agent prise en charge pour collecter des variables stdout, stderr et environnementales à partir de charges de travail de conteneur est ciprod06142019 ou ultérieure. Pour vérifier la version de votre agent, sous l’onglet **Nœud**, sélectionnez un nœud, puis dans la valeur note du volet Propriétés de la propriété **Balise d’image de l’agent**. Pour plus d’informations sur les versions de l’agent et sur ce qui est inclus dans chaque version, consultez les [notes de publication de l’agent](https://github.com/microsoft/Docker-Provider/tree/ci_feature_prod).

### <a name="data-collection-settings"></a>Paramètres de collecte de données

Voici les paramètres qui peuvent être configurés pour contrôler la collecte de données.

|Clé |Type de données |Valeur |Description |
|----|----------|------|------------|
|`schema-version` |Chaîne (respecte la casse) |v1 |Il s’agit de la version de schéma utilisée par l’agent lors de l’analyse de cette ConfigMap. Actuellement, la version prise en charge est v1. Modifier cette valeur n’est pas pris en charge et est rejeté lors de l’évaluation de l’élément ConfigMap.|
|`config-version` |Chaîne | | Prend en charge la possibilité d’effectuer le suivi de la version de ce fichier de configuration dans votre système/dépôt de contrôle de code source. Le nombre maximal de caractères autorisé est 10 et tous les autres caractères sont tronqués. |
|`[log_collection_settings.stdout] enabled =` |Boolean | true ou false | Ce paramètre contrôle si la collecte de journaux de conteneur stdout est activée. Lorsque la valeur est `true` et qu’aucun espace de noms n’est exclus de la collecte de journaux stdout (paramètre `log_collection_settings.stdout.exclude_namespaces` ci-dessous), les journaux stdout sont collectés à partir de tous les conteneurs parmi l’ensemble des nœuds/pods du cluster. Si elle n’est pas spécifiée dans ConfigMaps, la valeur par défaut est `enabled = true`. |
|`[log_collection_settings.stdout] exclude_namespaces =`|Chaîne | Tableau séparé par des virgules |Tableau d’espaces de noms Kubernetes pour lesquels aucun journal stdout n’est collecté. Ce paramètre est effectif uniquement si `log_collection_settings.stdout.enabled` est défini sur `true`. S’il n’est pas spécifié dans ConfigMap, la valeur par défaut est `exclude_namespaces = ["kube-system"]`.|
|`[log_collection_settings.stderr] enabled =` |Boolean | true ou false |Ce paramètre contrôle si la collecte de journaux de conteneur stderr est activée. Lorsque la valeur est `true` et qu’aucun espace de noms n’est exclus de la collecte de journaux stdout (paramètre `log_collection_settings.stderr.exclude_namespaces`), les journaux stderr sont collectés à partir de tous les conteneurs parmi l’ensemble des nœuds/pods du cluster. Si elle n’est pas spécifiée dans ConfigMaps, la valeur par défaut est `enabled = true`. |
|`[log_collection_settings.stderr] exclude_namespaces =` |Chaîne |Tableau séparé par des virgules |Tableau d’espaces de noms Kubernetes pour lesquels aucun journal stderr n’est collecté. Ce paramètre est effectif uniquement si `log_collection_settings.stdout.enabled` est défini sur `true`. S’il n’est pas spécifié dans ConfigMap, la valeur par défaut est `exclude_namespaces = ["kube-system"]`. |
| `[log_collection_settings.env_var] enabled =` |Boolean | true ou false | Ce paramètre contrôle la collecte des variables d’environnement sur tous les pods/nœuds du cluster et a pour valeur par défaut `enabled = true` lorsqu’il n’est pas spécifié dans ConfigMaps. Si la collecte des variables d’environnement est globalement activée, vous pouvez la désactiver pour un conteneur spécifique en définissant la variable d’environnement `AZMON_COLLECT_ENV` sur **False** avec un paramètre Dockerfile ou dans le [fichier de configuration pour le pod](https://kubernetes.io/docs/tasks/inject-data-application/define-environment-variable-container/) dans la section **env :** . Si la collecte des variables d’environnement est désactivée globalement, vous ne pouvez pas activer la collecte pour un conteneur spécifique (autrement dit, la seule substitution applicable au niveau du conteneur consiste à désactiver la collecte lorsqu’elle est déjà activée globalement). |

ConfigMaps est une liste globale et il ne peut y avoir qu’un seul élément ConfigMap appliqué à l’agent. Vous ne pouvez pas avoir un autre élément ConfigMaps qui annule les collectes.

## <a name="configure-and-deploy-configmaps"></a>Configurer et déployer ConfigMaps

Procédez comme suit pour configurer et déployer votre fichier de configuration ConfigMap dans votre cluster.

1. [Téléchargez](https://github.com/microsoft/OMS-docker/blob/ci_feature_prod/Kubernetes/container-azm-ms-agentconfig.yaml) le fichier yaml ConfigMap de modèle et enregistrez-le sous le nom container-azm-ms-agentconfig.yaml.  

2. Modifiez le fichier YAML ConfigMap avec vos personnalisations pour collecter les variables stdout, stderr et/ou d’environnement.

    - Pour exclure des espaces de noms spécifiques pour la collecte de journaux stdout, configurez la clé/valeur à l’aide de l’exemple suivant : `[log_collection_settings.stdout] enabled = true exclude_namespaces = ["my-namespace-1", "my-namespace-2"]`.
    
    - Pour désactiver la collecte de variables d’environnement pour un conteneur spécifique, définissez la clé/valeur `[log_collection_settings.env_var] enabled = true` pour activer la collecte de variables globalement, puis suivez les étapes décrites [ici](container-insights-manage-agent.md#how-to-disable-environment-variable-collection-on-a-container) pour terminer la configuration pour le conteneur spécifique.
    
    - Pour désactiver la collecte de journaux stderr au niveau du cluster, configurez la clé/valeur à l’aide de l’exemple suivant : `[log_collection_settings.stderr] enabled = false`.

3. Créez un ConfigMap en exécutant la commande kubectl suivante : `kubectl apply -f <configmap_yaml_file.yaml>`.
    
    Exemple : `kubectl apply -f container-azm-ms-agentconfig.yaml`. 
    
    Quelques minutes peuvent être nécessaires pour que la modification de configuration soit effective. Ensuite, tous les pods omsagent du cluster redémarrent. Le redémarrage s’effectue de façon progressive pour tous les pods omsagent. Tous ne redémarrent pas en même temps. Lorsque les redémarrages sont terminés, un message similaire à celui-ci s’affiche avec les résultats : `configmap "container-azm-ms-agentconfig" created`.

## <a name="verify-configuration"></a>Vérifier la configuration 

Pour vérifier que la configuration a été correctement appliquée, utilisez la commande suivante pour passer en revue les journaux à partir d’un pod d’agent : `kubectl logs omsagent-fdf58 -n=kube-system`. S’il existe des erreurs de configuration à partir de pods osmagent, la sortie affiche des erreurs similaires à ce qui suit :

``` 
***************Start Config Processing******************** 
config::unsupported/missing config schema version - 'v21' , using defaults
```

Des erreurs liées à l’application des modifications de configuration sont également disponibles pour évaluation. Les options suivantes sont disponibles pour effectuer un dépannage supplémentaire des modifications de configuration :

- À partir de journaux d’activité d’un pod d’agent à l’aide de la même commande `kubectl logs`. 

- À partir de journaux d’activité dynamiques. Les journaux dynamiques affichent des erreurs similaires à ce qui suit :

    ```
    config::error::Exception while parsing config map for log collection/env variable settings: \nparse error on value \"$\" ($end), using defaults, please check config map for errors
    ```

- À partir de la table **KubeMonAgentEvents** dans votre espace de travail Log Analytics. Les données sont envoyées toutes les heures avec la gravité de *l’erreur* pour les erreurs de configuration. S’il n’y a pas d’erreur, l’entrée de la table contient des données indiquant les *informations* de gravité, lesquelles ne signalent aucune erreur. La propriété **Balises** contient plus d’informations sur le pod et l’ID de conteneur où l’erreur s’est produite, ainsi que sur la première occurrence, la dernière occurrence et le nombre d’occurrences au cours de la dernière heure.

Des erreurs empêchent omsagent d’analyser le fichier, ce qui provoque son redémarrage et l’utilisation de la configuration par défaut. Une fois que vous avez corrigé les erreurs dans ConfigMap, enregistrez le fichier yaml et appliquez la ConfigMaps mise à jour en exécutant la commande : `kubectl apply -f <configmap_yaml_file.yaml`.

## <a name="applying-updated-configmap"></a>Application du ConfigMap mis à jour

Si vous avez déjà déployé un ConfigMap dans votre cluster et que vous souhaitez le mettre à jour avec une configuration plus récente, vous pouvez modifier le fichier ConfigMap que vous utilisiez précédemment, puis l’appliquer à l’aide de la même commande qu’avant, `kubectl apply -f <configmap_yaml_file.yaml`.

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

- Azure Monitor pour conteneurs n’inclut pas d’un ensemble prédéfini d’alertes. Pour savoir comment créer les alertes recommandées pour une utilisation élevée du processeur et de la mémoire, consultez [Créer des alertes de performances avec Azure Monitor pour conteneurs](container-insights-alerts.md).

- Une fois l’analyse activée pour collecter l’utilisation des ressources et l’intégrité de votre Azure Kubernetes Service ou de vos clusters hybrides et charges de travail s’y exécutant, découvrez [comment utiliser](container-insights-analyze.md) Azure Monitor pour les conteneurs.

- Consultez les [exemples de requêtes de journal](container-insights-log-search.md#search-logs-to-analyze-data) pour voir les requêtes prédéfinies et des exemples permettant d’évaluer ou de personnaliser la génération d’alertes, la visualisation ou l’analyse de vos clusters.
