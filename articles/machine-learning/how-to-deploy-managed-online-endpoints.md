---
title: Déployer un modèle d’apprentissage automatique en utilisant un point de terminaison en ligne managé (préversion)
titleSuffix: Azure Machine Learning
description: Découvrez comment déployer votre modèle d’apprentissage automatique en tant que service web automatiquement managé par Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: seramasu
ms.reviewer: laobri
author: rsethur
ms.date: 08/05/2021
ms.topic: how-to
ms.custom: how-to, devplatv2
ms.openlocfilehash: 0c421148571b99bb182de6841586caafb116df42
ms.sourcegitcommit: 34aa13ead8299439af8b3fe4d1f0c89bde61a6db
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/18/2021
ms.locfileid: "122527794"
---
# <a name="deploy-and-score-a-machine-learning-model-by-using-a-managed-online-endpoint-preview"></a>Déployer et noter un modèle d’apprentissage automatique en utilisant un point de terminaison en ligne managé (préversion)

Découvrez comment utiliser un point de terminaison en ligne managé (préversion) pour déployer votre modèle, de sorte à n’avoir ni à créer ni à gérer l’infrastructure sous-jacente. Vous allez commencer par déployer un modèle sur votre ordinateur local pour déboguer les erreurs éventuelles, puis vous allez le déployer et le tester dans Azure. 

Vous allez également apprendre à afficher les journaux et à surveiller le contrat de niveau de service (SLA). Vous commencez avec un modèle et obtenez un point de terminaison HTTPS/REST évolutif qui peut être utilisé pour la notation en ligne et en temps réel. 

Pour plus d’informations, consultez [Que sont les points de terminaison Azure Machine Learning (préversion) ?](concept-endpoints.md).

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="prerequisites"></a>Prérequis

* Pour utiliser Azure Machine Learning, vous devez disposer d’un abonnement Azure. Si vous n’avez pas d’abonnement Azure, créez un compte gratuit avant de commencer. Essayez la [version gratuite ou payante d’Azure Machine Learning](https://azure.microsoft.com/free/).

* Installer et configurer Azure CLI ainsi que l’extension `ml` pour l’interface CLI Azure. Pour plus d’informations, consultez [Installer, configurer et utiliser l’interface CLI (v2) (préversion)](how-to-configure-cli.md). 

* Vous devez avoir un groupe de ressources Azure dans lequel vous (ou le principal de service que vous utilisez) devez disposer d’un accès Contributeur. Un groupe de ressources est créé lors de [l’installation, de la configuration et de l’utilisation de l’interface CLI (v2) (préversion)](how-to-configure-cli.md). 

* Vous devez avoir un espace de travail Azure Machine Learning. Un espace de travail est crée dans [Installer, configurer et utiliser l’interface CLI (v2) (préversion)](how-to-configure-cli.md).

* Si vous n’avez pas encore défini les paramètres par défaut pour l’interface CLI Azure, enregistrez vos paramètres par défaut. Pour éviter de transmettre plusieurs fois les valeurs de votre abonnement, de votre espace de travail et de votre groupe de ressources, exécutez le code suivant :

   ```azurecli
   az account set --subscription <subscription ID>
   az configure --defaults workspace=<Azure Machine Learning workspace name> group=<resource group>
   ```

* (Facultatif) Pour déployer localement, vous devez [installer Moteur Docker](https://docs.docker.com/engine/install/) sur votre ordinateur local. Nous *recommandons fortement* cette option pour déboguer les problèmes plus facilement.

## <a name="prepare-your-system"></a>Préparer votre système

Pour suivre cet article, commencez par cloner le référentiel d’exemples (azureml-examples). Ensuite, exécutez le code suivant pour accéder au répertoire d’exemples :

```azurecli
git clone https://github.com/Azure/azureml-examples
cd azureml-examples
cd cli
```

Pour définir le nom de votre point de terminaison, choisissez l’une des commandes suivantes, en fonction de votre système d’exploitation (remplacez `YOUR_ENDPOINT_NAME` par un nom unique).

Pour UNIX, exécutez la commande suivante :

```azurecli
export ENDPOINT_NAME=YOUR_ENDPOINT_NAME
```

pour Windows, exécutez la commande suivante :

```azurecli
set ENDPOINT_NAME=YOUR_ENDPOINT_NAME
```

> [!NOTE]
> Les noms de points de terminaison doivent être uniques au sein d’une région Azure. Par exemple, dans la région Azure westus2, il ne peut y avoir qu’un seul point de terminaison avec le nom `my-endpoint`. 

## <a name="define-the-endpoint-configuration"></a>Définir la configuration des points de terminaison

Les entrées spécifiques pour déployer un modèle sur un point de terminaison en ligne sont les suivantes :

- Fichiers de modèle (ou le nom et la version d’un modèle déjà inscrit dans votre espace de travail). Dans l’exemple, nous avons un modèle scikit-learn qui effectue une régression.
- Code requis pour évaluer le modèle. Dans ce cas, nous disposons d’un fichier *score.py*.
- Environnement dans lequel votre modèle s’exécute. Comme vous le verrez, l’environnement peut être une image Docker avec les dépendances Conda, ou il peut s’agir d’un fichier Dockerfile.
- Paramètres pour spécifier le type d’instance et la capacité de mise à l’échelle.

L’extrait de code suivant montre le fichier *endpoints/online/managed/simple-flow/1-create-endpoint-with-blue.yml*, lequel capture toutes les entrées requises : 

:::code language="yaml" source="~/azureml-examples-main/cli/endpoints/online/managed/simple-flow/1-create-endpoint-with-blue.yml":::

> [!NOTE]
> Pour obtenir une description complète des YAML, consultez [Informations de référence YAML sur les points de terminaison en ligne managés (préversion)](reference-yaml-endpoint-managed-online.md).

La référence pour le format YAML du point de terminaison est décrite dans le tableau suivant. Pour savoir comment spécifier ces attributs, consultez l’exemple YAML dans [Préparez votre système](#prepare-your-system) ou la [référence YAML du point de terminaison en ligne](reference-yaml-endpoint-managed-online.md). Pour plus d’informations sur les limites associées aux points de terminaison managés, consultez [Gérer et augmenter les quotas pour les ressources avec Azure Machine Learning](how-to-manage-quotas.md#azure-machine-learning-managed-online-endpoints-preview).

| Clé | Description |
| --- | --- |
| `$schema`    | (Facultatif) Schéma YAML. Pour visualiser toutes les options disponibles dans le fichier YAML, vous pouvez visualiser le schéma dans l’exemple précédent dans un navigateur.|
| `name`       | Nom du point de terminaison. Il doit être unique au sein de la région Azure.|
| `traffic` | Pourcentage du trafic depuis le point de terminaison à détourner vers chaque déploiement. La somme des valeurs de trafic doit être 100. |
| `auth_mode` | Utilisez `key` pour l’authentification basée sur une clé ou `aml_token` pour l’authentification basée sur un jeton Azure Machine Learning. `key` n’expire pas mais `aml_token` expire. (Récupérez le jeton le plus récent avec la commande `az ml endpoint get-credentials`.) |
| `deployments` | Liste des déploiements à créer dans le point de terminaison. Dans ce cas, nous n’avons qu’un seul déploiement, nommé `blue`. Pour plus d’informations sur les déploiements multiples, consultez [Lancement sécurisé pour les points de terminaison en ligne (préversion)](how-to-safely-rollout-managed-endpoints.md).|

Le tableau suivant décrit les attributs de `deployments` :

| Clé | Description |
| --- | --- |
| `name`  | Le nom du déploiement. |
| `model` | Dans cet exemple, nous spécifions les propriétés de modèle inline : `name`, `version` et `local_path`. Les fichiers de modèle sont téléchargés et enregistrés automatiquement. Un inconvénient de la spécification inline est que vous devez incrémenter la version manuellement si vous souhaitez mettre à jour les fichiers de modèle. Pour connaître les meilleures pratiques associées, consultez les conseils dans la section suivante. |
| `code_configuration.code.local_path` | Répertoire qui contient tout le code source Python pour le scoring du modèle. Vous pouvez utiliser des répertoires et des packages imbriqués. |
| `code_configuration.scoring_script` | Fichier Python dans le répertoire de notation `code_configuration.code.local_path`. Ce code Python doit avoir une fonction `init()` et une fonction `run()`. La fonction `init()` sera appelée une fois le modèle créé ou mis à jour (vous pouvez l’utiliser pour mettre en cache le modèle en mémoire, par exemple). La fonction `run()` est appelée à chaque appel du point de terminaison pour effectuer la notation et la prédiction réelles. |
| `environment` | Contient les détails de l’environnement pour héberger le modèle et le code. Dans cet exemple, nous avons des définitions inline qui incluent `name`, `version` et `path`. Nous utiliserons `environment.docker.image` pour l’image. Les dépendances `conda_file` seront installées au-dessus de l’image. Pour en savoir plus, consultez les conseils dans la section suivante. |
| `instance_type` | Référence SKU de machine virtuelle pour héberger vos instances de déploiement. Pour plus d’informations, consultez [Références SKU de machine virtuelle prises en charge par des points de terminaison en ligne managés](reference-managed-online-endpoints-vm-sku-list.md). |
| `scale_settings.scale_type` | Actuellement, cette valeur doit être `manual`. Pour effectuer un scale-up ou un scale-down après la création du point de terminaison et du déploiement, mettez à jour `instance_count` dans le code YAML et exécutez la commande `az ml endpoint update -n $ENDPOINT_NAME --file <yaml filepath>`.|
| `scale_settings.instance_count` | Nombre d’instances dans le déploiement. Basez la valeur sur la charge de travail que vous attendez. Pour une haute disponibilité, nous vous recommandons de définir `scale_settings.instance_count` sur au moins `3`. |

Pour plus d’informations sur le schéma YAML, consultez le document [Référence YAML sur les points de terminaison en ligne](reference-yaml-endpoint-managed-online.md).

> [!NOTE]
> Pour utiliser le Service Azure Kubernetes (AKS) comme cible de calcul à la place des points de terminaison managés :
> 1. Créez et attachez votre cluster AKS comme cible de calcul à votre espace de travail Azure Machine Learning avec[Azure ML Studio](how-to-create-attach-compute-studio.md#whats-a-compute-target).
> 1. Utilisez le code [YAML de point de terminaison](https://github.com/Azure/azureml-examples/blob/main/cli/endpoints/online/aks/simple-flow/1-create-aks-endpoint-with-blue.yml) pour cibler AKS à la place du code YAML de point de terminaison managé. Vous devrez modifier le code YAML pour remplacer la valeur de `target` par le nom de votre cible de calcul inscrite.
>
> Toutes les commandes utilisées dans cet article (à l’exception de la surveillance du SLA facultative et de l’intégration d’Azure Log Analytics) peuvent être utilisées avec des points de terminaison managés ou avec des points de terminaison AKS.

### <a name="register-your-model-and-environment-separately"></a>Enregistrement séparé de votre modèle et de votre environnement

Dans cet exemple, nous spécifions les propriétés du modèle et de l’environnement inline : `name`, `version` et `local_path` (à partir duquel charger les fichiers). L’interface CLI charge automatiquement les fichiers et inscrit le modèle et l’environnement. En guise de bonne pratique pour la production, vous devez inscrire le modèle et l’environnement et spécifier séparément le nom et la version inscrits dans le code YAML. Utilisez le formulaire `model: azureml:my-model:1` ou `environment: azureml:my-env:1`.

Pour effectuer l’inscription, vous pouvez extraire les définitions YAML de `model` et `environment` dans des fichiers YAML distincts et utiliser les commandes `az ml model create` et `az ml environment create`. Pour en savoir plus sur ces commandes, exécutez `az ml model create -h` et `az ml environment create -h`.

### <a name="use-different-cpu-and-gpu-instance-types"></a>Utilisation de différents types d’instances de processeur et GPU

Le code YAML ci-dessus utilise un type à usage général (`Standard_F2s_v2`) et une image Docker non GPU (dans le code YAML, reportez-vous à l’attribut `image`). Pour le calcul GPU, choisissez une référence SKU de type de calcul GPU et une image Docker GPU.

Pour connaître les types d’instances à usage général et GPU pris en charge, consultez les [SKU de machine virtuelle prises en charge par des points de terminaison en ligne managés](reference-managed-online-endpoints-vm-sku-list.md). Pour obtenir la liste des images de base de processeur et GPU d’Azure ML, consultez [Images de base Azure Machine Learning](https://github.com/Azure/AzureML-Containers).

### <a name="use-more-than-one-model"></a>Utilisation de plusieurs modèles

Actuellement, vous pouvez spécifier un seul modèle par déploiement dans le code YAML. Si vous avez plusieurs modèles, quand vous inscrivez le modèle, copiez tous les modèles sous forme de fichiers ou de sous-répertoires dans un dossier que vous utilisez pour l’inscription. Dans votre script de notation, utilisez la variable d’environnement `AZUREML_MODEL_DIR` pour récupérer le chemin d’accès au dossier racine du modèle. La structure de répertoire sous-jacente est conservée.

## <a name="understand-the-scoring-script"></a>Comprendre le script de scoring

> [!TIP]
> Le format du script de notation pour les points de terminaison en ligne managés est le même que celui utilisé dans la version précédente de l’interface CLI et dans le kit SDK Python.

Comme spécifié plus tôt, le `code_configuration.scoring_script` doit avoir une fonction `init()` et une fonction `run()`. Cet exemple utilise le [fichier score.py](https://github.com/Azure/azureml-examples/blob/main/cli/endpoints/online/model-1/onlinescoring/score.py). La fonction `init()` est appelée lorsque le conteneur est initialisé ou démarré. Cette initialisation se produit généralement peu après la création ou la mise à jour du déploiement. Écrivez la logique ici pour effectuer des opérations d’initialisation globales telles que la mise en cache du modèle en mémoire (comme dans cet exemple). La fonction `run()` est appelée à chaque appel du point de terminaison et doit effectuer la notation et la prédiction réelles. Dans l’exemple, nous extrayons les données de l’entrée JSON, appelons la méthode `predict()` du modèle scikit-learn et retournons le résultat.

## <a name="deploy-and-debug-locally-by-using-local-endpoints"></a>Déployer et déboguer localement à l’aide de points de terminaison locaux

Pour gagner du temps lors du débogage, il est *fortement recommandé* d’effectuer une série de tests localement sur votre point de terminaison.

> [!NOTE]
> * Pour déployer localement, vous devez avoir installé le [moteur Docker](https://docs.docker.com/engine/install/).
> * Le moteur Docker doit être en cours d’exécution. Le moteur Docker démarre généralement au démarrage de l’ordinateur. Si ce n’est pas le cas, vous pouvez [dépanner le Moteur Docker](https://docs.docker.com/config/daemon/#start-the-daemon-manually).

> [!IMPORTANT]
> L’objectif d’un déploiement de points de terminaison local consiste à valider et déboguer votre code et votre configuration avant le déploiement sur Azure. Un déploiement local présente les limitations suivantes :
> - Les points de terminaison locaux *ne prennent pas* en charge les règles de trafic, l’authentification, les paramètres de mise à l’échelle ni les paramètres de sonde. 
> - Les points de terminaison locaux ne prennent en charge qu’un seul déploiement par point de terminaison. Autrement dit, dans un déploiement local, vous ne pouvez pas utiliser une référence à un modèle ou à un environnement inscrit dans votre espace de travail Azure Machine Learning. 

### <a name="deploy-the-model-locally"></a>Déployer le modèle localement

Pour déployer le modèle localement :

```azurecli
az ml endpoint create --local -n $ENDPOINT_NAME -f endpoints/online/managed/simple-flow/1-create-endpoint-with-blue.yml
```

> [!NOTE]
> Si vous utilisez un système d’exploitation Windows, utilisez `%ENDPOINT_NAME%` à la place de `$ENDPOINT_NAME` ici et dans les commandes suivantes

L’indicateur `--local` oriente l’interface CLI pour déployer le point de terminaison dans l’environnement Docker.

### <a name="verify-the-local-deployment-succeeded"></a>Vérifier si le déploiement local a réussi

Vérifiez les journaux pour voir si le modèle a été déployé sans erreur :

```azurecli
az ml endpoint get-logs --local -n $ENDPOINT_NAME --deployment blue
```

### <a name="invoke-the-local-endpoint-to-score-data-by-using-your-model"></a>Appeler le point de terminaison local pour effectuer la notation des données avec votre modèle

Appelez le point de terminaison pour effectuer la notation du modèle à l’aide de la commande pratique `invoke` et en transmettant les paramètres de requête stockés dans un fichier JSON :

```azurecli
az ml endpoint invoke --local -n $ENDPOINT_NAME --request-file endpoints/online/model-1/sample-request.json
```

Si vous souhaitez utiliser un client REST (par exemple, une boucle), vous devez disposer de l’URI de notation. Pour récupérer l’URI de notation, exécutez `az ml endpoint show --local -n $ENDPOINT_NAME`. Dans les données retournées, recherchez l’attribut `scoring_uri`. 

### <a name="review-the-logs-for-output-from-the-invoke-operation"></a>Rechercher dans les journaux la sortie de l’opération d’appel

Dans l’exemple fichier *score.py*, la méthode `run()` journalise une sortie dans la console. Vous pouvez afficher cette sortie en réutilisant la commande `get-logs` :

```azurecli
az ml endpoint get-logs --local -n $ENDPOINT_NAME --deployment blue
```

##  <a name="deploy-your-managed-online-endpoint-to-azure"></a>Déployer votre point de terminaison en ligne managé dans Azure

Puis, déployez votre point de terminaison en ligne managé dans Azure.

### <a name="deploy-to-azure"></a>Déployer sur Azure

Pour déployer la configuration YAML dans le cloud, exécutez le code suivant :

::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint.sh" ID="deploy" :::

Ce déploiement peut prendre jusqu’à 15 minutes selon que l’environnement ou l’image sous-jacent(e) est généré(e) pour la première fois ou non. Les déploiements suivants qui utilisent le même environnement termineront le traitement plus rapidement.

> [!TIP]
> * Si vous préférez ne pas bloquer votre console CLI, vous pouvez ajouter l’indicateur `--no-wait` à la commande. Toutefois, cela arrêtera l’affichage interactif de l’état du déploiement.
>
> * Utilisez la [résolution des problèmes de déploiement de points de terminaison en ligne managés (préversion)](how-to-troubleshoot-managed-online-endpoints.md) pour déboguer les erreurs.

### <a name="check-the-status-of-the-deployment"></a>Vérifier l’état du déploiement

La commande `show` contient des informations dans `provisioning_status` pour le point de terminaison et le déploiement :

::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint.sh" ID="get_status" :::

Vous pouvez répertorier tous les points de terminaison dans l’espace de travail sous la forme d’un tableau à l’aide de la commande `list` :

```azurecli
az ml endpoint list --output table
```

### <a name="check-the-status-of-the-cloud-deployment"></a>Vérifier l’état de déploiement du Cloud

Vérifiez les journaux pour voir si le modèle a été déployé sans erreur :

:::code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint.sh" ID="get_logs" :::

Par défaut, les journaux sont extraits du serveur d’inférence. Pour afficher les journaux de l’initialiseur de stockage (lequel monte des ressources telles que le modèle et le code dans le conteneur), ajoutez l’indicateur `--container storage-initializer`.

### <a name="invoke-the-endpoint-to-score-data-by-using-your-model"></a>Appeler le point de terminaison pour effectuer la notation des données avec votre modèle

Vous pouvez utiliser la commande `invoke` ou un client REST de votre choix pour appeler le point de terminaison et effectuer le scoring de certaines données : 

::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint.sh" ID="test_endpoint" :::

Pour visualiser les journaux d’appel, exécutez `get-logs` de nouveau.

Pour utiliser un client REST, vous devez disposer de la valeur de `scoring_uri` et de la clé ou du jeton d’authentification. La valeur `scoring_uri` est disponible dans la sortie de la commande `show` :
 
::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint.sh" ID="get_scoring_uri" :::

Nous utilisons l’indicateur `--query` pour filtrer les attributs sur les attributs nécessaires uniquement. Pour en apprendre davantage sur `--query` consultez [Interroger la sortie de commande Azure CLI](/cli/azure/query-azure-cli).

Récupérez les informations d’identification nécessaires à l’aide de la commande `get-credentials` :

```azurecli
az ml endpoint get-credentials -n $ENDPOINT_NAME
```

### <a name="optional-update-the-deployment"></a>(Facultatif) Mettre à jour le déploiement

Si vous souhaitez mettre à jour le code, le modèle, l’environnement ou vos paramètres de mise à l’échelle, mettez à jour le fichier YAML et exécutez la commande `az ml endpoint update`. 

> [!IMPORTANT]
> Vous ne pouvez modifier qu’*un seul* aspect (trafic, paramètres de mise à l’échelle, code, modèle ou environnement) dans une commande `update` unique. 

Pour comprendre comment `update` fonctionne :

1. Ouvrez le fichier *online/model-1/onlinescoring/score.py*.
1. Modifiez la dernière ligne de la fonction `init()` : après `logging.info("Init complete")`, ajoutez `logging.info("Updated successfully")`. 
1. Enregistrez le fichier .
1. Exécutez cette commande :

    ```azurecli
    az ml endpoint update -n $ENDPOINT_NAME -f endpoints/online/managed/simple-flow/1-create-endpoint-with-blue.yml
    ```

    > [!IMPORTANT]
    > La mise à jour à l’aide de YAML est déclarative. Autrement dit, les modifications apportées dans le fichier YAML seront reflétées dans les ressources du Gestionnaire de ressource Azure sous-jacentes (points de terminaison et déploiements). Une approche déclarative facilite les [GitOps](https://www.atlassian.com/git/tutorials/gitops) : *Toutes* les modifications apportées aux points de terminaison et déploiements passent (même `instance_count`) par le fichier YAML. En conséquence, si vous supprimez un déploiement du fichier YAML et exécutez `az ml endpoint update` en utilisant le fichier, le déploiement sera supprimé. Vous pouvez effectuer des mises à jour sans utiliser le YAML à l’aide de l’indicateur `--set`.
    
1. Comme vous avez modifié la fonction `init()` (`init()`qui s’exécute lors de la création ou de la mise à jour du point de terminaison), le message `Updated successfully` figurera dans les journaux. Récupérez les journaux en exécutant :

    ```azurecli
    az ml endpoint get-logs -n $ENDPOINT_NAME --deployment blue
    ```

Dans les rares cas où vous souhaitez supprimer et recréer votre déploiement en raison d’un problème insoluble, utilisez :

```azurecli
az ml endpoint delete -n $ENDPOINT_NAME --deployment blue
```

La commande `update` fonctionne également avec les points de terminaison locaux. Utilisez la même commande `az ml endpoint update` avec l’indicateur `--local`.

> [!TIP]
> Avec la commande `az ml endpoint update`, vous pouvez utiliser le paramètre [`--set` disponible dans l’interface CLI Azure](/cli/azure/use-cli-effectively#generic-update-arguments) pour remplacer des attributs dans votre code YAML *ou* pour définir des attributs spécifiques sans transmettre le fichier YAML. L’utilisation de `--set` pour les attributs uniques est particulièrement importante dans les scénarios de développement et de test. Par exemple, pour effectuer un scale-up de la valeur `instance_count` pour le premier déploiement, vous pouvez utiliser l’indicateur `--set deployments[0].scale_settings.instance_count=2`. Toutefois, étant donné que le code YAML n’est pas mis à jour, cette technique ne facilite pas les [GitOps](https://www.atlassian.com/git/tutorials/gitops).

### <a name="optional-monitor-sla-by-using-azure-monitor"></a>(Facultatif) Surveiller le contrat SLA à l’aide d’Azure Monitor

Pour afficher les mesures et définir des alertes en fonction de votre contrat SLA, effectuez les étapes décrites dans [surveiller les points de terminaison managés en ligne](how-to-monitor-online-endpoints.md).

### <a name="optional-integrate-with-log-analytics"></a>(Facultatif) Intégrer à Log Analytics

La commande `get-logs` ne fournit que les quelques dernières centaines de lignes de journaux d’une instance sélectionnée automatiquement. Toutefois, Log Analytics offre un moyen de stocker et d’analyser durablement les journaux. 

En premier lieu, créez un espace de travail Log Analytics en suivant les étapes décrites dans [Créer un espace de travail Log Analytics dans le Portail Azure](../azure-monitor/logs/quick-create-workspace.md#create-a-workspace).

Ensuite, dans le portail Azure :

1. Accédez au groupe de ressources.
1. Sélectionnez votre point de terminaison.
1. Sélectionnez la **page de ressources ARM**.
1. Sélectionnez **Paramètres de diagnostic**.
1. Sélectionnez **Ajouter des paramètres**.
1. Sélectionner pour activer l’envoi des journaux de diagnostic vers un espace de travail Log Analytics.

La connexion des journaux peut prendre jusqu’à une heure. Passée une heure, envoyez des demandes de notation, puis vérifiez les journaux en procédant comme suit :

1. Ouvrez l’espace de travail Log Analytics. 
1. Dans le menu de gauche, sélectionnez **Journaux**.
1. Fermez la fenêtre contextuelle **Requêtes** qui s’ouvre automatiquement.
1. Double cliquez sur **AmlOnlineEndpointConsoleLog**.
1. Sélectionnez **Exécuter**.

## <a name="delete-the-endpoint-and-the-deployment"></a>Supprimez le point de terminaison et le déploiement

Si vous n’envisagez pas d’utiliser le déploiement, vous devez le supprimer en exécutant le code suivant (cela supprime le point de terminaison et tous les déploiements sous-jacents) :

::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint.sh" ID="delete_endpoint" :::

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus, consultez les articles suivants :

- [Déployer des modèles avec REST (préversion)](how-to-deploy-with-rest.md)
- [Créer et utiliser des points de terminaison en ligne managés (préversion) dans le studio](how-to-use-managed-online-endpoint-studio.md)
- [Déploiement sûr pour les points de terminaison en ligne (préversion)](how-to-safely-rollout-managed-endpoints.md)
- [Utiliser des points de terminaison de traitement de lots (préversion) pour le scoring par lots](how-to-use-batch-endpoint.md)
- [Afficher les coûts d’un point de terminaison en ligne managé Azure Machine Learning (préversion)](how-to-view-online-endpoints-costs.md)
- [Tutoriel : Accéder aux ressources Azure avec un point de terminaison en ligne managé et une identité managée par le système (préversion)](tutorial-deploy-managed-endpoints-using-system-managed-identity.md)
- [Résolution des problèmes de déploiement de points de terminaison en ligne managés](how-to-troubleshoot-managed-online-endpoints.md)
