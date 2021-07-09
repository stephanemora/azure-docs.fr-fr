---
title: Informations de référence YAML sur les points de terminaison en ligne managés (préversion)
titleSuffix: Azure Machine Learning
description: Apprenez-en davantage sur les fichiers YAML utilisés pour déployer des modèles en tant que points de terminaison en ligne managés
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
author: rsethur
ms.author: seramasu
ms.date: 05/25/2021
ms.reviewer: laobri
ms.openlocfilehash: c64c36a6f0c0cd43ceb51041dfcc1ceafa27358a
ms.sourcegitcommit: c385af80989f6555ef3dadc17117a78764f83963
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2021
ms.locfileid: "111412040"
---
# <a name="managed-online-endpoints-preview-yaml-reference"></a>Informations de référence YAML sur les points de terminaison en ligne managés (préversion) 

L’extension Azure CLI pour Azure Machine Learning (CLI 2.0) utilise des documents YAML pour fournir la configuration de nombreuses commandes. Dans cet article, vous allez découvrir le document YAML utilisé lors de l’utilisation de points de terminaison en ligne managés.

Pour plus d’informations sur le déploiement d’un modèle, consultez [Guide pratique pour déployer des points de terminaison en ligne managés](how-to-deploy-managed-online-endpoints.md).

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

> [!NOTE]
> Un exemple YAML complet pour les points de terminaison en ligne managés est disponible à des fins de [référence](https://azuremlschemas.azureedge.net/latest/managedOnlineEndpoint.template.yaml)

## <a name="schema"></a>schéma

| Clé | Description |
| --- | --- |
| $schema    | \[__Facultatif__\] Schéma YAML. Vous pouvez afficher le schéma de l’exemple ci-dessus dans un navigateur pour voir toutes les options disponibles dans le fichier YAML.|
| name       | Nom du point de terminaison, Doit être unique au niveau de la région Azure.|
| traffic | Pourcentage du trafic depuis le point de terminaison à détourner vers chaque déploiement. La somme des valeurs de trafic doit être égale à 100. |
| auth_mode | Utilisez `key` pour l’authentification basée sur une clé et `aml_token` pour l’authentification basée sur un jeton Azure Machine Learning. `key` n’expire pas mais `aml_token` expire. Récupérez le jeton le plus récent avec la commande `az ml endpoint list-keys`). |
| identité | Sert à configurer des identités managées affectées par le système et affectées par l’utilisateur. |
| app_insights_enabled | `True` pour activer l’intégration avec Azure AppInsights associé à votre espace de travail Azure Machine Learning. `False` par défaut.
| tags | Dictionnaire d’étiquettes Azure à associer au point de terminaison. |
| description | Description du point de terminaison. |
| target | Si cette clé n’est pas définie, le point de terminaison est déployé en tant que point de terminaison en ligne managé. Pour utiliser AKS, définissez la valeur de cette clé sur le nom de la cible de calcul inscrite, par exemple `target:azureml:my-aks`. 
| deployments | Contient la liste des déploiements à créer dans le point de terminaison. Dans ce cas, nous n’avons qu’un seul déploiement, nommé `blue`. |

### <a name="attributes-of-the-deployments-key"></a>Attributs de la clé `deployments`
 
| Clé | Description |
| --- | --- |
| name  | Le nom du déploiement. |
| model | Nom de la version de modèle inscrite au format `model: azureml:my-model:1`. Vous pouvez également spécifier des propriétés de modèle inline : `name`, `version` et `local_path`. Les fichiers de modèle sont chargés et inscrits automatiquement. Un inconvénient de la spécification inline est que vous devez incrémenter la version manuellement si vous souhaitez mettre à jour les fichiers de modèle.|
| code_configuration.code.local_path | Répertoire qui contient tout le code source Python pour le scoring du modèle. Les répertoires/packages imbriqués sont pris en charge. |
| code_configuration.scoring_script | Fichier Python dans le répertoire de scoring ci-dessus. Ce code Python doit avoir une fonction `init()` et une fonction `run()`. La fonction `init()` sera appelée une fois le modèle créé ou mis à jour (vous pouvez l’utiliser pour mettre en cache le modèle en mémoire, et ainsi de suite). La fonction `run()` est appelée à chaque appel du point de terminaison pour effectuer le scoring réel/la prédiction réelle. |
| Environnement | Contient les détails de l’environnement Azure Machine Learning pour héberger le modèle et le code. En guise de bonne pratique pour la production, vous devez inscrire le modèle et l’environnement séparément et spécifier le nom et la version inscrits dans le code YAML. Par exemple : `environment: azureml:my-env:1`. |
| instance_type | Référence SKU de machine virtuelle pour héberger vos instances de déploiement. Pour plus d’informations, consultez [Références SKU de machine virtuelle prises en charge par des points de terminaison en ligne managés](reference-managed-online-endpoints-vm-sku-list.md).|
| scale_settings.scale_type | Actuellement, cette valeur doit être `manual`. Pour effectuer un scale-up ou un scale-down après la création du point de terminaison et du déploiement, mettez à jour `instance_count` dans le code YAML et exécutez la commande `az ml endpoint update -n $ENDPOINT_NAME --file <yaml filepath>`. |
| scale_settings.instance_count | Nombre d’instances dans le déploiement. Basez la valeur sur la charge de travail que vous attendez. Pour une haute disponibilité, Microsoft vous recommande de la définir sur au moins `3`. |
| scale_settings.min_instances | Nombre minimal d’instances devant toujours être présentes. |
| scale_settings.max_instances | Nombre maximal d’instances possibles pour la mise à l’échelle du déploiement. Le quota sera réservé à max_instances. |
| request_settings.request_timeout_ms | Délai d’expiration de scoring en millisecondes. La valeur par défaut est 5000 pour les points de terminaison en ligne managés. |
| request_settings.max_concurrent_requests_per_instance | Nombre maximal de requêtes simultanées par nœud autorisées par déploiement. La valeur par défaut est de 1. __Ne modifiez pas la valeur de ce paramètre (la valeur par défaut est 1), sauf indication contraire du support technique Microsoft ou d’un membre de l’équipe Azure Machine Learning.__ |
| request_settings.max_queue_wait_ms | Durée maximale pendant laquelle une requête reste dans la file d’attente (en millisecondes). La valeur par défaut est 500. |
| liveness_probe | La probe liveness supervise régulièrement l’intégrité du conteneur. |
| liveness_probe.period | Fréquence (en secondes) d’exécution de probe liveness. La valeur par défaut est de 10 secondes. La valeur minimale est 1. |
| liveness_probe.initial_delay | Nombre de secondes après le démarrage du conteneur avant le lancement des probes liveness. La valeur par défaut est 10. |
| liveness_probe.timeout | Nombre de secondes après lequel la probe liveness expire. La valeur par défaut est de deux secondes. La valeur minimale est 1. |
| liveness_probe.failure_threshold | Le système essaiera failure_threshold fois avant d’abandonner. La valeur par défaut est 30. La valeur minimale est 1. |
| liveness_probe.success_threshold | Nombre minimal de réussites consécutives pour que la probe liveness soit considérée comme réussie après avoir échoué. La valeur par défaut est de 1. La valeur minimale est 1. |
| readiness_probe | La probe readiness valide si le conteneur est prêt à traiter le trafic. Les propriétés et les valeurs par défaut sont identiques à celles de la probe liveness. |
| tags | Dictionnaire d’étiquettes Azure que vous souhaitez associer au déploiement. |
| description | Description du déploiement. |

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment [déployer un modèle avec un point de terminaison en ligne managé](how-to-deploy-managed-online-endpoints.md).