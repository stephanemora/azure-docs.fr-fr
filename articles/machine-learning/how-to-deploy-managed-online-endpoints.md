---
title: Déployer un modèle ML avec un point de terminaison en ligne managé
titleSuffix: Azure Machine Learning
description: Découvrez comment déployer votre modèle Machine Learning en tant que service web automatiquement managé par Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: seramasu
ms.reviewer: laobri
author: rsethur
ms.date: 05/13/2021
ms.topic: how-to
ms.custom: how-to
ms.openlocfilehash: 85b587dcaed162a0372f03240f9c4cb33d7507c1
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111969061"
---
# <a name="deploy-and-score-a-machine-learning-model-with-a-managed-online-endpoint-preview"></a>Déployer et scorer un modèle Machine Learning avec un point de terminaison en ligne managé (préversion)

Les points de terminaison en ligne managés (préversion) vous permettent de déployer votre modèle sans avoir à créer ni gérer l’infrastructure sous-jacente. Dans cet article, vous allez commencer par déployer un modèle sur votre ordinateur local pour déboguer les erreurs éventuelles, puis vous allez le déployer et le tester dans Azure. Vous allez également apprendre à afficher les journaux et à surveiller le contrat de niveau de service (SLA). Vous commencez avec un modèle et obtenez un point de terminaison HTTPS/REST évolutif qui peut être utilisé pour le scoring en ligne/en temps réel.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="prerequisites"></a>Prérequis

* Pour utiliser Azure Machine Learning, vous devez disposer d’un abonnement Azure. Si vous n’avez pas d’abonnement Azure, créez un compte gratuit avant de commencer. Essayez la [version gratuite ou payante d’Azure Machine Learning](https://aka.ms/AMLFree) dès aujourd’hui.

* Vous devez installer et configurer Azure CLI et l’extension ML. Pour plus d’informations, consultez [Installer, configurer et utiliser l’interface CLI 2.0 (préversion)](how-to-configure-cli.md). 

* Vous devez avoir un groupe de ressources Azure dans lequel vous (ou le principal de service que vous utilisez) devez disposer d’un accès `Contributor`. Vous disposez d’un tel groupe de ressources si vous avez configuré votre extension ML conformément à l’article ci-dessus. 

* Vous devez avoir un espace de travail Azure Machine Learning. Vous disposez d’un tel espace de travail si vous avez configuré votre extension ML conformément à l’article ci-dessus.

* Si vous n’avez pas encore défini les paramètres par défaut pour Azure CLI, vous devez enregistrer vos paramètres par défaut. Pour éviter d’avoir à transmettre plusieurs fois les valeurs, exécutez :

   ```azurecli
   az account set --subscription <subscription id>
   az configure --defaults workspace=<azureml workspace name> group=<resource group>

* [Optional] To deploy locally, you must have [Docker engine](https://docs.docker.com/engine/install/) running locally. This step is **highly recommended**. It will help you debug issues.

## Prepare your system

To follow along with the article, clone the samples repository, and navigate to the right directory by running the following commands:

```azurecli
git clone https://github.com/Azure/azureml-examples
cd azureml-examples
cd cli
```

Définissez le nom de votre point de terminaison (renommez `YOUR_ENDPOINT_NAME` ci-dessous avec un nom unique). La commande ci-dessous est destinée aux environnements UNIX :

```azurecli
export ENDPOINT_NAME=YOUR_ENDPOINT_NAME
```

Si vous utilisez un système d’exploitation Windows, utilisez la commande `set ENDPOINT_NAME=YOUR_ENDPOINT_NAME` à la place.

> [!NOTE]
> Les noms des points de terminaison doivent être uniques au niveau de la région Azure. Par exemple, il ne peut y avoir qu’un seul point de terminaison avec le nom `my-endpoint` dans `westus2`. 

## <a name="define-the-endpoint-configuration"></a>Définir la configuration des points de terminaison

Les entrées nécessaires pour déployer un modèle sur un point de terminaison en ligne sont les suivantes :

- Fichiers de modèle (ou le nom et la version d’un modèle déjà inscrit dans votre espace de travail). Dans l’exemple, nous avons un modèle `scikit-learn` qui effectue une régression.
- Code nécessaire pour effectuer le scoring du modèle. Dans ce cas, nous disposons d’un fichier `score.py`.
- Un environnement dans lequel votre modèle est exécuté (comme vous le verrez, l’environnement peut être une image Docker avec des dépendances Conda, ou un fichier Dockerfile).
- Paramètres pour spécifier le type d’instance et la capacité de mise à l’échelle.

L’extrait de code suivant montre le fichier `endpoints/online/managed/simple-flow/1-create-endpoint-with-blue.yml` qui capture toutes les informations ci-dessus : 

:::code language="yaml" source="~/azureml-examples-main/cli/endpoints/online/managed/simple-flow/1-create-endpoint-with-blue.yml":::

> [!Note]
> Un exemple YAML complet pour les points de terminaison en ligne managés est disponible à des fins de [référence](https://azuremlschemas.azureedge.net/latest/managedOnlineEndpoint.template.yaml)

La référence pour le format YAML de point de terminaison est indiquée ci-dessous. Pour comprendre comment spécifier ces attributs, reportez-vous à l’exemple YAML de cet article ou à l’exemple YAML complet mentionné dans la remarque précédente. Pour plus d’informations sur les limites associées aux points de terminaison managés, consultez [Gérer et augmenter les quotas pour les ressources avec Azure Machine Learning](how-to-manage-quotas.md).

| Clé | Description |
| --- | --- |
| $schema    | [Facultatif] Schéma YAML. Vous pouvez afficher le schéma de l’exemple ci-dessus dans un navigateur pour voir toutes les options disponibles dans le fichier YAML.|
| name       | Nom du point de terminaison, Doit être unique au niveau de la région Azure.|
| traffic | Pourcentage du trafic depuis le point de terminaison à détourner vers chaque déploiement. La somme des valeurs de trafic doit correspondre à 100 |
| auth_mode | Utilisez `key` pour l’authentification basée sur une clé et `aml_token` pour l’authentification basée sur un jeton Azure Machine Learning. `key` n’expire pas mais `aml_token` expire. Récupérez le jeton le plus récent avec la commande `az ml endpoint get-credentials`). |
| deployments | Contient la liste des déploiements à créer dans le point de terminaison. Dans ce cas, nous n’avons qu’un seul déploiement, nommé `blue`. Pour plus d’informations sur les déploiements multiples, consultez [Déploiement sécurisé pour les points de terminaison en ligne (préversion)](how-to-safely-rollout-managed-endpoints.md)|

Attributs de `deployments` :

| Clé | Description |
| --- | --- |
| name  | Nom du déploiement |
| model | Dans cet exemple, nous spécifions les propriétés de modèle inline : `name`, `version` et `local_path`. Les fichiers de modèle sont chargés et inscrits automatiquement. Un inconvénient de la spécification inline est que vous devez incrémenter la version manuellement si vous souhaitez mettre à jour les fichiers de modèle. Lisez la partie **Conseil** de la section ci-dessous pour connaître les bonnes pratiques associées. |
| code_configuration.code.local_path | Répertoire qui contient tout le code source Python pour le scoring du modèle. Les répertoires/packages imbriqués sont pris en charge. |
| code_configuration.scoring_script | Fichier Python dans le répertoire de scoring ci-dessus. Ce code Python doit avoir une fonction `init()` et une fonction `run()`. La fonction `init()` sera appelée une fois le modèle créé ou mis à jour (vous pouvez l’utiliser pour mettre en cache le modèle en mémoire, et ainsi de suite). La fonction `run()` est appelée à chaque appel du point de terminaison pour effectuer le scoring réel/la prédiction réelle. |
| Environnement | Contient les détails de l’environnement pour héberger le modèle et le code. Dans cet exemple, nous avons des définitions inline qui incluent `name`, `version` et `path`. Dans cet exemple, `environment.docker.image` est utilisé en tant qu’image et les dépendances `conda_file` sont installées par-dessus. Pour plus d’informations, consultez la partie **Conseil** dans la section ci-dessous. |
| instance_type | Référence SKU de machine virtuelle pour héberger vos instances de déploiement. Pour plus d’informations, consultez [Références SKU de machine virtuelle prises en charge par des points de terminaison en ligne managés](reference-managed-online-endpoints-vm-sku-list.md). |
| scale_settings.scale_type | Actuellement, cette valeur doit être `manual`. Pour effectuer un scale-up ou un scale-down après la création du point de terminaison et du déploiement, mettez à jour `instance_count` dans le code YAML et exécutez la commande `az ml endpoint update -n $ENDPOINT_NAME --file <yaml filepath>`.|
| scale_settings.instance_count | Nombre d’instances dans le déploiement. Basez la valeur sur la charge de travail que vous attendez. Pour une haute disponibilité, Microsoft vous recommande de la définir sur au moins `3`. |

Pour plus d’informations sur le schéma YAML, consultez le document [Référence YAML sur les points de terminaison en ligne](reference-online-endpoint-yaml.md).

> [!Note]
> Pour utiliser Azure Kubernetes Service (AKS) comme cible de calcul à la place des points de terminaison managés :
> 1. Créez et attachez votre cluster AKS comme cible de calcul à votre espace de travail Azure Machine Learning [avec le studio Azure ML](how-to-create-attach-compute-studio.md#whats-a-compute-target)
> 2. Utilisez ce [code YAML de point de terminaison](https://github.com/Azure/azureml-examples/blob/main/cli/endpoints/online/aks/simple-flow/1-create-aks-endpoint-with-blue.yml) pour cibler AKS à la place du code YAML de point de terminaison managé ci-dessus. Vous devrez modifier le code YAML pour remplacer la valeur de `target` par le nom de votre cible de calcul inscrite.
> Les commandes de cet article, à l’exception de la surveillance des contrats SLA et de l’intégration Log Analytics facultatives, sont interchangeables entre les points de terminaison managés et AKS.

### <a name="registering-your-model-and-environment-separately"></a>Enregistrement séparé de votre modèle et de votre environnement

 Dans cet exemple, nous spécifions les propriétés du modèle et de l’environnement inline : `name`, `version` et `local_path` à partir duquel charger les fichiers. En coulisses, l’interface CLI charge les fichiers et enregistre automatiquement le modèle et l’environnement. En guise de bonne pratique pour la production, vous devez inscrire le modèle et l’environnement séparément et spécifier le nom et la version inscrits dans le code YAML. La forme est `model: azureml:my-model:1` ou `environment: azureml:my-env:1`.

 Pour effectuer l’inscription, vous pouvez extraire les définitions YAML de `model` et `environment` dans des fichiers YAML distincts et utiliser les commandes `az ml model create` et `az ml environment create`. Pour en savoir plus sur ces commandes, exécutez `az ml model create -h` et `az ml environment create -h`.

### <a name="using-different-cpu--gpu-instance-types"></a>Utilisation de différents types d’instances de processeur et GPU

Le code YAML ci-dessus utilise un type à usage général (`Standard_F2s_v2`) et une image Docker non GPU (dans le code YAML, reportez-vous à l’attribut `image`). Pour le calcul GPU, vous devez choisir une référence SKU de type de calcul GPU et une image Docker GPU.

Vous pouvez voir les types d’instance à usage général et GPU pris en charge dans les [références SKU de machine virtuelle prises en charge par des points de terminaison en ligne managés](reference-managed-online-endpoints-vm-sku-list.md). Vous trouverez la liste des images de base de processeur et GPU d’Azure ML dans [Images de base Azure Machine Learning](https://github.com/Azure/AzureML-Containers).

### <a name="using-more-than-one-model"></a>Utilisation de plusieurs modèles

Actuellement, vous pouvez spécifier un seul modèle par déploiement dans le code YAML. Si vous avez plusieurs modèles, vous pouvez contourner cette limitation : quand vous inscrivez le modèle, copiez tous les modèles (sous forme de fichiers ou de sous-répertoires) dans un dossier que vous utilisez pour l’inscription. Dans votre script de scoring, vous pouvez utiliser la variable d’environnement `AZUREML_MODEL_DIR` pour obtenir le chemin du dossier racine du modèle ; la structure de répertoires sous-jacente est conservée.

## <a name="understand-the-scoring-script"></a>Comprendre le script de scoring

> [!Tip]
> Le format du script de scoring pour les points de terminaison en ligne managés est le même que celui utilisé dans la version précédente de l’interface CLI et dans le kit SDK Python.

Comme indiqué dans le code YAML ci-dessus, `code_configuration.scoring_script` doit avoir une fonction `init()` et une fonction `run()`. Cet exemple utilise ce [fichier score.py](https://github.com/Azure/azureml-examples/blob/main/cli/endpoints/online/model-1/onlinescoring/score.py). La fonction `init()` est appelée lorsque le conteneur est initialisé/démarré. Cette initialisation se produit généralement peu après la création ou la mise à jour du déploiement. Écrivez la logique ici pour effectuer des opérations d’initialisation globales telles que la mise en cache du modèle en mémoire (comme dans cet exemple). La fonction `run()` est appelée à chaque appel du point de terminaison et doit effectuer le scoring réel/la prédiction réelle. Dans l’exemple, nous extrayons les données de l’entrée JSON, appelons la méthode `predict()` du modèle `scikit-learn` et retournons le résultat.

## <a name="deploy-and-debug-locally-using-local-endpoints"></a>Déployer et déboguer localement à l’aide de points de terminaison locaux

Pour gagner du temps lors du débogage, il est **fortement recommandé** d’effectuer une série de tests localement sur votre point de terminaison.

> [!Note]
> * Pour déployer localement, vous devez avoir installé le [moteur Docker](https://docs.docker.com/engine/install/)
> * Votre moteur Docker doit être en cours d’exécution. En général, le moteur démarre au démarrage. Si ce n’est pas le cas, vous pouvez [résoudre les problèmes ici](https://docs.docker.com/config/daemon/#start-the-daemon-manually).

> [!Important]
> L’objectif d’un déploiement de point de terminaison local consiste à valider et déboguer votre code et votre configuration avant le déploiement sur Azure. Un déploiement local présente les limitations suivantes :
> - Les points de terminaison locaux **ne prennent pas** en charge les règles de trafic, l’authentification, les paramètres de mise à l’échelle ni les paramètres de sonde. 
> - Les points de terminaison locaux ne prennent en charge qu’un seul déploiement par point de terminaison. Autrement dit, dans un déploiement local, vous ne pouvez pas utiliser une référence à un modèle ou à un environnement inscrit dans votre espace de travail Azure Machine Learning. 

### <a name="deploy-the-model-locally"></a>Déployer le modèle localement

Pour déployer le modèle localement, exécutez la commande suivante :

```azurecli
az ml endpoint create --local -n $ENDPOINT_NAME -f endpoints/online/managed/simple-flow/1-create-endpoint-with-blue.yml
```

L’indicateur `--local` oriente l’interface CLI pour déployer le point de terminaison dans l’environnement Docker.

>[!NOTE]
>Si vous utilisez un système d’exploitation Windows, utilisez `%ENDPOINT_NAME%` à la place de `$ENDPOINT_NAME` ici et dans les commandes suivantes

### <a name="check-if-the-local-deployment-succeeded"></a>Vérifier si le déploiement local a réussi

Vérifiez si le modèle a été déployé sans erreur en vérifiant les journaux :

```azurecli
az ml endpoint get-logs --local -n $ENDPOINT_NAME --deployment blue
```

### <a name="invoke-the-local-endpoint-to-score-data-with-your-model"></a>Appeler le point de terminaison local pour effectuer le scoring des données avec votre modèle

Appelez le point de terminaison pour effectuer le scoring du modèle à l’aide de la commande pratique `invoke` et en transmettant les paramètres de requête stockés dans un fichier JSON :

```azurecli
az ml endpoint invoke --local -n $ENDPOINT_NAME --request-file endpoints/online/model-1/sample-request.json
```

Si vous souhaitez utiliser un client REST (comme curl), vous avez besoin de l’URI de scoring. Vous pouvez l’obtenir à l’aide de la commande `az ml endpoint show --local -n $ENDPOINT_NAME`. Dans les données retournées, vous trouverez un attribut nommé `scoring_uri`. 

### <a name="review-the-logs-for-output-from-the-invoke-operation"></a>Rechercher dans les journaux la sortie de l’opération d’appel

Dans l’exemple `score.py`, la méthode `run()` journalise une sortie dans la console. Vous pouvez afficher cette sortie en réutilisant la commande `get-logs` :

```azurecli
az ml endpoint get-logs --local -n $ENDPOINT_NAME --deployment blue
```

##  <a name="deploy-your-managed-online-endpoint-to-azure"></a>Déployer votre point de terminaison en ligne managé dans Azure 

### <a name="deploy-to-azure"></a>Déployer dans Azure

Pour déployer la configuration YAML dans le cloud, exécutez la commande suivante :

::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint.sh" ID="deploy" :::

Ce déploiement peut prendre jusqu’à 15 minutes selon que l’environnement/l’image sous-jacent est généré pour la première fois ou non. Les déploiements ultérieurs avec le même environnement seront plus rapides.

> [!Tip]
> Si vous préférez ne pas bloquer votre console CLI, vous pouvez ajouter l’indicateur `--no-wait` à la commande. Toutefois, cela arrêtera l’affichage interactif de l’état du déploiement.

> [!Tip]
> Utilisez la [résolution des problèmes de déploiement de points de terminaison en ligne managés](how-to-troubleshoot-managed-online-endpoints.md) pour déboguer les erreurs.

### <a name="check-the-status-of-the-deployment"></a>Vérifier l’état du déploiement

La commande `show` contient `provisioning_status` pour le point de terminaison et le déploiement :

::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint.sh" ID="get_status" :::

Vous pouvez répertorier tous les points de terminaison dans l’espace de travail sous la forme d’un tableau à l’aide de la commande `list` :

```azurecli
az ml endpoint list --output table
```

### <a name="check-if-the-cloud-deployment-succeeded"></a>Vérifier si le déploiement cloud a réussi

Vérifiez si le modèle a été déployé sans erreur en vérifiant les journaux :

:::code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint.sh" ID="get_logs" :::

Par défaut, les journaux sont extraits du serveur d’inférence. Si vous souhaitez afficher les journaux à partir de l’initialiseur de stockage (qui monte les ressources telles que le modèle et le code dans le conteneur), ajoutez l’indicateur `--container storage-initializer`.

### <a name="invoke-the-endpoint-to-score-data-with-your-model"></a>Appeler le point de terminaison pour effectuer le scoring des données avec votre modèle

Vous pouvez utiliser la commande `invoke` ou un client REST de votre choix pour appeler le point de terminaison et effectuer le scoring de certaines données : 

::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint.sh" ID="test_endpoint" :::

Vous pouvez à nouveau utiliser la commande `get-logs` indiquée précédemment pour voir les journaux d’appel.

Pour utiliser un client REST, vous avez besoin de `scoring_uri` et de la clé/du jeton d’authentification. `scoring_uri` est disponible dans la sortie de la commande `show` :
 
::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint.sh" ID="get_scoring_uri" :::

Notez comment nous utilisons `--query` pour filtrer les attributs sur les attributs nécessaires uniquement. Vous pouvez en apprendre davantage sur `--query` dans [Interroger la sortie de commande Azure CLI](/cli/azure/query-azure-cli).

Récupérez les informations d’identification nécessaires à l’aide de la commande `get-credentials` :

```azurecli
az ml endpoint get-credentials -n $ENDPOINT_NAME
```

### <a name="optional-update-the-deployment"></a>[Facultatif] Mettre à jour le déploiement

Si vous souhaitez mettre à jour le code, le modèle, l’environnement ou vos paramètres de mise à l’échelle, mettez à jour le fichier YAML et exécutez la commande `az ml endpoint update`. 

>[!IMPORTANT]
> Vous ne pouvez modifier qu’**un seul** aspect (trafic, paramètres de mise à l’échelle, code, modèle ou environnement) dans une seule commande `update`. 

Pour comprendre comment `update` fonctionne :

1. Ouvrez le fichier `online/model-1/onlinescoring/score.py`.
1. Modifiez la dernière ligne de la fonction `init()` : après `logging.info("Init complete")`, ajoutez `logging.info("Updated successfully")`. 
1. Enregistrez le fichier.
1. Exécutez la commande suivante :
```azurecli
az ml endpoint update -n $ENDPOINT_NAME -f endpoints/online/managed/simple-flow/1-create-endpoint-with-blue.yml
```

> [!IMPORTANT]
> La mise à jour à l’aide du code YAML est déclarative. Autrement dit, les modifications apportées dans le fichier YAML seront reflétées dans les ressources Azure Resource Manager sous-jacentes (points de terminaison et déploiements). Cette approche facilite [GitOps](https://www.atlassian.com/git/tutorials/gitops) : *TOUTES* les modifications apportées aux points de terminaison/déploiements passent par le fichier YAML (même `instance_count`). En tant qu’effet secondaire, si vous supprimez un déploiement du code YAML et exécutez `az ml endpoint update` à l’aide du fichier, ce déploiement sera supprimé. Vous pouvez effectuer des mises à jour sans utiliser le code YAML à l’aide de l’indicateur `--set `, comme cela est décrit dans la partie Conseil suivante.

5. Comme vous avez modifié la fonction `init()` qui s’exécute lors de la création ou de la mise à jour du point de terminaison, le message `Updated successfully` figure dans les journaux. Récupérez les journaux en exécutant :
```azurecli
az ml endpoint get-logs -n $ENDPOINT_NAME --deployment blue
```

Dans les rares cas où vous souhaitez supprimer et recréer votre déploiement en raison d’un problème insoluble, utilisez :

```azurecli
az ml endpoint delete -n $ENDPOINT_NAME --deployment blue
```

La commande `update` fonctionne également avec les points de terminaison locaux. Utilisez la même commande `az ml endpoint update` avec l’indicateur `--local`.

> [!Tip]
> Avec la commande `az ml endpoint update`, vous pouvez utiliser le paramètre [`--set` disponible dans Azure CLI](/cli/azure/use-cli-effectively#generic-update-arguments) pour remplacer des attributs dans votre code YAML **ou** pour définir des attributs spécifiques sans transmettre le fichier YAML. L’utilisation de `--set` pour les attributs uniques est particulièrement importante dans les scénarios de développement et de test. Par exemple, pour effectuer un scale-up de `instance_count` du premier déploiement, vous pouvez utiliser l’indicateur `--set deployments[0].scale_settings.instance_count=2`. Toutefois, étant donné que le code YAML n’est pas mis à jour, cette technique ne facilite pas [GitOps](https://www.atlassian.com/git/tutorials/gitops).

### <a name="optional-monitor-sla-using-azure-monitor"></a>[Facultatif] Surveiller le contrat SLA à l’aide d’Azure Monitor

Vous pouvez afficher les métriques et définir des alertes en fonction de votre contrat SLA en suivant les instructions fournies dans [Superviser des points de terminaison en ligne managés](how-to-monitor-online-endpoints.md).

### <a name="optional-integrate-with-log-analytics"></a>[Facultatif] Intégrer à Log Analytics

La commande `get-logs` ne fournira que les quelques dernières centaines de lignes de journaux d’une instance sélectionnée automatiquement. Toutefois, Log Analytics offre un moyen de stocker et d’analyser durablement les journaux. En premier lieu, suivez les étapes décrites dans [Créer un espace de travail Log Analytics dans le portail Azure](../azure-monitor/logs/quick-create-workspace.md#create-a-workspace) pour créer un espace de travail Log Analytics.

Ensuite, dans le portail Azure :

1. Accédez au groupe de ressources.
1. Choisissez votre point de terminaison.
1. Sélectionnez la **page de ressources ARM**.
1. Sélectionnez **Paramètres de diagnostic**.
1. Sélectionnez **Ajouter des paramètres** : Activez l’envoi des journaux de console vers l’espace de travail Log Analytics.

Notez que la connexion des journaux peut prendre jusqu’à une heure. Envoyez des demandes de scoring après ce délai, puis vérifiez les journaux en procédant comme suit :

1. Ouvrez l’espace de travail Log Analytics. 
1. Sélectionnez **Journaux** dans le volet de navigation gauche.
1. Fermez la fenêtre contextuelle **Requêtes** qui s’ouvre automatiquement.
1. Double-cliquez sur **AmlOnlineEndpointConsoleLog**.
1. Sélectionnez *Exécuter*.

## <a name="delete-the-endpoint-and-deployment"></a>Supprimez le point de terminaison et le déploiement.

Si vous n’envisagez pas d’utiliser le déploiement, vous devez le supprimer avec la commande ci-dessous (cela supprime le point de terminaison et tous les déploiements sous-jacents) :

::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint.sh" ID="delete_endpoint" :::

## <a name="next-steps"></a>Étapes suivantes

- [Déploiement sûr pour les points de terminaison en ligne (préversion)](how-to-safely-rollout-managed-endpoints.md)
- [Résolution des problèmes de déploiement de points de terminaison en ligne managés](how-to-troubleshoot-managed-online-endpoints.md)