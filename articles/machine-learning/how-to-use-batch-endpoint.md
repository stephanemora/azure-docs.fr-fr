---
title: Utiliser des points de terminaison de traitement de lots pour le scoring par lots
titleSuffix: Azure Machine Learning
description: Dans cet article, vous allez apprendre à créer un point de terminaison de traitement de lots pour effectuer un scoring par lots continu de données volumineuses.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: tracych
ms.author: tracych
ms.reviewer: laobri
ms.date: 5/25/2021
ms.custom: how-to
ms.openlocfilehash: 53fa68fdffd27c1d48322104c541894c6f9c4dd8
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/09/2021
ms.locfileid: "111751250"
---
# <a name="use-batch-endpoints-preview-for-batch-scoring"></a>Utiliser des points de terminaison de traitement de lots (préversion) pour le scoring par lots

Dans cet article, vous allez apprendre à utiliser des points de terminaison de traitement de lots (préversion) pour effectuer un scoring par lots. Les points de terminaison de traitement de lots simplifient le processus d’hébergement de vos modèles pour le scoring par lots, ce qui vous permet de vous concentrer sur le machine learning, et non sur l’infrastructure. Après avoir créé un point de terminaison de traitement de lots, vous pouvez déclencher des tâches de scoring par lots avec Azure CLI ou à partir de n’importe quelle plateforme utilisant une bibliothèque HTTP et l’API REST. Pour plus d’informations, consultez [Que sont les points de terminaison Azure Machine Learning (préversion) ?](concept-endpoints.md).

Cet article vous montre comment effectuer les tâches suivantes :

> [!div class="checklist"]
> * Créer un point de terminaison de traitement de lots avec une expérience sans code pour le modèle MLflow
> * Vérifier le détail d’un point de terminaison de traitement de lots
> * Démarrer un travail de scoring par lots à l’aide de l’interface CLI
> * Surveiller la progression de l’exécution du travail de scoring par lots et vérifier les résultats du scoring
> * Ajouter un nouveau déploiement à un point de terminaison de traitement de lots
> * Démarrer un travail de scoring par lots à l’aide de REST

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure – Si vous n’avez pas d’abonnement Azure, créez un compte gratuit avant de commencer. Essayez la [version gratuite ou payante d’Azure Machine Learning](https://aka.ms/AMLFree) dès aujourd’hui.

* Interface de ligne de commande Azure CLI et extension ML.

L’extension de Machine Learning nécessite Azure CLI version `>=2.15.0`. Vérifiez que cette condition est respectée :

```azurecli
az version
```

Si nécessaire, mettez à niveau Azure CLI :

```azurecli
az upgrade
```

> [!NOTE]
>
> La commande `az upgrade` a été ajoutée dans la version 2.11.0 et elle ne fonctionne pas avec les versions antérieures. Les anciennes versions peuvent être mises à jour en les réinstallant, comme décrit dans [Installer l’interface Azure CLI](/cli/azure/update-azure-cli).
>
> Cette commande met également à jour toutes les extensions installées par défaut. Pour plus d’options `az upgrade`, consultez la [page de référence des commandes](/cli/azure/reference-index#az_upgrade).

Ajoutez et configurez l’extension Azure ML :

```azurecli
az extension add -n ml
```

Pour plus d’informations sur la configuration de l’extension ML, consultez [Installer, configurer et utiliser l’interface CLI 2.0 (préversion)](how-to-configure-cli.md).

* Référentiel d’exemples

Clonez le [dépôt d’exemples AzureML](https://github.com/Azure/azureml-examples). Cet article utilise les ressources figurant dans `/cli/endpoints/batch`.

## <a name="create-a-compute-target"></a>Créer une cible de calcul

Le scoring par lots s’exécute uniquement sur des ressources de cloud computing, pas localement. La ressource de cloud computing est appelée « cible de calcul ». Une cible de calcul est un ordinateur virtuel réutilisable sur lequel vous pouvez exécuter des workflows de scoring par lots.

Exécutez le code suivant pour créer une cible [`AmlCompute`](/python/api/azureml-core/azureml.core.compute.amlcompute(class)?view=azure-ml-py&preserve-view=true) à usage général. Pour plus d’informations sur les cibles de calcul, consultez [Que sont les cibles de calcul dans Azure Machine Learning ?](./concept-compute-target.md).

```azurecli
az ml compute create --name cpu-cluster --type AmlCompute --min-instances 0 --max-instances 5
```

## <a name="create-a-batch-endpoint"></a>Créer un point de terminaison de traitement de lots

Si vous utilisez un modèle MLflow, vous pouvez utiliser la création d’un point de terminaison de traitement de lots sans code. Autrement dit, vous n’avez pas besoin de préparer un script et un environnement de scoring, les deux pouvant être générés automatiquement. Pour plus d’informations, consultez [Entraîner et suivre des modèles ML avec MLflow et Azure Machine Learning (préversion)](how-to-use-mlflow.md).

```azurecli
az ml endpoint create --type batch --file cli/endpoints/batch/create-batch-endpoint.yml
```

Fichier YAML définissant le point de terminaison de traitement de lots MLFlow :

:::code language="yaml" source="~/azureml-examples-main/cli/endpoints/batch/create-batch-endpoint.yml":::

| Clé | Description |
| --- | ----------- |
| $schema | [Facultatif] Schéma YAML. Vous pouvez afficher le schéma dans l’exemple ci-dessus dans un navigateur pour afficher toutes les options disponibles pour un fichier YAML de point de terminaison de traitement de lots. |
| name | Nom du point de terminaison de traitement de lots, qui doit être unique dans la région. La valeur `name` sera utilisée dans le cadre de l’URI de scoring. La valeur doit commencer par un caractère anglais, continuer par une combinaison de chiffres, de caractères et du symbole `-`, et se terminer par un chiffre ou un caractère. Il doit comprendre au moins 3 caractères. L’expression régulière de validation est : `^[a-zA-Z][-a-zA-Z0-9]+[a-zA-Z0-9]$`|
| type | Type du point de terminaison. Utilisez `batch` pour le point de terminaison de traitement de lots. |
| auth_mode | Utilisez `aad_token` pour l’authentification basée sur les jetons Azure. |
| traffic | Pourcentage du trafic routé vers ce déploiement. Pour les points de terminaison de traitement de lots, les seules valeurs valides pour `traffic` sont `0` et `100`. Le déploiement avec une valeur de traffic de `100` est actif. Lorsqu’elles sont appelées, toutes les données sont envoyées au déploiement actif. |
| deployments | Liste des déploiements à créer dans le point de terminaison de traitement de lots. L’exemple n’a qu’un seul déploiement nommé `autolog-deployment`. |

Attributs du déploiement :

| Clé | Description |
| --- | ----------- |
| name | Le nom du déploiement. |
| model | Modèle à utiliser pour le scoring par lots. Utilisez `name`, `version` et `local_path` pour charger un modèle à partir de votre ordinateur local. Utilisez le préfixe `azureml:` pour référencer une ressource de modèle existante dans votre espace de travail. Par exemple, `azureml: autolog:1` pointe vers la version 1 d’un modèle nommé `autolog`. |
| compute.target | La cible de calcul. Utilisez le préfixe `azureml:` pour référencer une ressource de calcul existante dans votre espace de travail. Par exemple, `azureml:cpu-cluster` pointe vers une cible de calcul nommée `cpu-cluster`. |
| compute.instance_count | Nombre de nœuds de calcul à utiliser pour le scoring par lots. La valeur par défaut est `1`.|
| mini_batch_size | [Facultatif] Nombre de fichiers qu’un `scoring_script` peut traiter en un appel `run()`. La valeur par défaut est `10`. |
| output_file_name | [Facultatif] Nom du fichier de sortie de scoring par lots. La valeur par défaut est `predictions.csv`. |
| retry_settings.max_retries | [Facultatif] Nombre maximal de tentatives pour un échec d’un `scoring_script` `run()`. La valeur par défaut est `3`. |
| retry_settings.timeout | [Facultatif] Délai d’attente, en secondes, pour un `scoring_script` `run()`. La valeur par défaut est `30`. |
| error_threshold | [Facultatif] Nombre d’échecs de fichier qui doivent être ignorés. Si le nombre d’erreurs présentes dans la totalité de l’entrée dépasse cette valeur, le travail va se terminer. Le seuil d’erreur concerne la totalité de l’entrée et non le mini-lot envoyé à la méthode `run()`. La valeur par défaut est `-1`, qui spécifie qu’un nombre quelconque d’échecs est autorisé sans terminer l’exécution. | 
| Niveau de journalisation | [Facultatif] Niveau de détail des journaux. Les valeurs permettant d’augmenter le niveau de détail sont : WARNING, INFO et DEBUG. La valeur par défaut est INFO. |

## <a name="check-batch-endpoint-details"></a>Vérifier les détails d’un point de terminaison de traitement de lots

Une fois qu’un point de terminaison batch a été créé, vous pouvez utiliser `show` pour vérifier les détails. Utilisez le [`--query parameter`](/cli/azure/query-azure-cli) pour obtenir uniquement des attributs spécifiques à partir des données retournées.

```azurecli
az ml endpoint show --name mybatchedp --type batch
```

## <a name="start-a-batch-scoring-job-using-cli"></a>Démarrer un travail de scoring par lots à l’aide de l’interface CLI

Une charge de travail de scoring par lots s’exécute en tant que travail hors connexion. Le scoring par lots est conçu pour traiter des données volumineuses. Les entrées sont traitées en parallèle sur le cluster de calcul. Une partition de données est affectée à un processus sur un nœud. Un nœud unique avec plusieurs processus aura plusieurs partitions exécutées en parallèle. Par défaut, le scoring par lots stocke les sorties de scoring dans le stockage Blob. Vous pouvez démarrer un travail de scoring par lots à l’aide de l’interface CLI en transmettant les entrées de données. Vous pouvez également configurer l’emplacement des sorties et remplacer certains paramètres pour obtenir les performances optimales.

### <a name="start-a-bath-scoring-job-with-different-inputs-options"></a>Démarrer un travail de scoring de lot avec différentes options d’entrée

Vous avez le choix entre trois options pour spécifier les entrées de données.

Option 1 : Données inscrites

Utilisez `--input-data` pour transmettre des données inscrites dans AML.

> [!NOTE]
> Pendant la préversion, seul FileDataset est pris en charge. 

```azurecli
az ml endpoint invoke --name mybatchedp --type batch --input-data azureml:<dataName>:<dataVersion>
```

Option 2 : Données dans le cloud

Utilisez `--input-datastore` pour spécifier un magasin de données inscrit dans AML, et utilisez `--input-path` pour spécifier le chemin relatif dans le magasin de données.

```azurecli
az ml endpoint invoke --name mybatchedp --type batch --input-datastore azureml:<datastoreName> --input-path <relativePath>
```

Si vos données sont disponibles publiquement, utilisez `--input-path` pour spécifier le chemin public.

Si vous utilisez l’exemple fourni, vous pouvez exécuter la commande suivante pour démarrer un travail de scoring par lots.

```azurecli
az ml endpoint invoke --name mybatchedp --type batch --input-path https://pipelinedata.blob.core.windows.net/sampledata/nytaxi/taxi-tip-data.csv
```

Option 3 : Données stockées localement

```azurecli
az ml endpoint invoke --name mybatchedp --type batch --input-local-path <localPath>
```

### <a name="configure-the-output-location"></a>Configurer l’emplacement de sortie

Les résultats de scoring par lots sont stockés par défaut dans le magasin d’objets blob par défaut de l’espace de travail, dans un dossier nommé par nom de travail (GUID généré par le système). Vous pouvez configurer l’emplacement où stocker les sorties de scoring lorsque vous démarrez un travail de scoring par lots. Utilisez `--output-datastore` pour configurer un magasin de données inscrit quelconque, et utilisez `--output-path` pour configurer le chemin relatif. Utilisez `--set output_file_name` pour configurer un nouveau nom de fichier de sortie.

> [!IMPORTANT]
> Vous devez utiliser un emplacement de sortie unique. Si le fichier de sortie existe, le travail de scoring par lots échouera. 

```azurecli
az ml endpoint invoke --name mybatchedp --type batch --input-path https://pipelinedata.blob.core.windows.net/sampledata/nytaxi/taxi-tip-data.csv --output-datastore azureml:workspaceblobstore --output-path mypath --set output_file_name=mypredictions.csv
```

### <a name="overwrite-settings"></a>Remplacer les paramètres

Certains paramètres peuvent être remplacés quand vous démarrez un travail de scoring par lots pour tirer le meilleur parti de la ressource de calcul et améliorer les performances : 

* Utilisez `--mini-batch-size` pour remplacer `mini_batch_size` si une taille différente des données d’entrée est utilisée. 
* Utilisez `--instance-count` pour remplacer `instance_count` si une ressource de calcul différente est nécessaire pour ce travail. 
* Utilisez `--set` pour remplacer d’autres paramètres, notamment `max_retries`, `timeout` et `error_threshold`.

```azurecli
az ml endpoint invoke --name mybatchedp --type batch --input-path https://pipelinedata.blob.core.windows.net/sampledata/nytaxi/taxi-tip-data.csv --set retry_settings.max_retries=1
```

## <a name="check-batch-scoring-job-execution-progress"></a>Vérifier la progression de l’exécution du travail de scoring par lots

Les travaux de scoring par lots prennent généralement un certain temps pour traiter l’ensemble des entrées. Vous pouvez surveiller la progression du travail à partir du studio Azure Machine Learning. Le lien Studio est fourni dans la réponse de `invoke`, en tant que valeur de `interactionEndpoints.Studio.endpoint`.

Vous pouvez également consulter les détails du travail ainsi que son état à l’aide de l’interface CLI.

Obtenez le nom du travail à partir de la réponse à l’appel.

```azurecli
job_name=$(az ml endpoint invoke --name mybatchedp --type batch --input-path https://pipelinedata.blob.core.windows.net/sampledata/nytaxi/taxi-tip-data.csv --query name -o tsv)
```

Utilisez `job show` pour vérifier les détails et l’état d’un travail de scoring par lots.

```azurecli
az ml job show --name $job_name
```

Effectuez le streaming des journaux des travaux à l’aide de `job stream`.

```azurecli
az ml job stream --name $job_name
```

## <a name="check-batch-scoring-results"></a>Vérifier les résultats du scoring par lots

Pour consulter les résultats du scoring :

1. Accédez à Studio.
1. Accédez à **Expériences**.
1. Accédez à l’exécution de votre point de terminaison (valeur de `interactionEndpoints.Studio.endpoint` dans la réponse à `endpoint invoke`).
1. Dans le graphique de l’exécution, cliquez dans l’étape `batchscoring`.
1. Choisissez l’onglet Sorties + journaux et choisissez **Afficher les sorties de données**. 
1. Choisissez l’icône **Afficher la sortie** :::image type="content" source="media/how-to-use-batch-endpoint/view-data-outputs.png" alt-text="Capture d’écran du studio montrant l’emplacement d’affichage des sorties de données" lightbox="media/how-to-use-batch-endpoint/view-data-outputs.png" :::.
1. Dans le volet contextuel, copiez le chemin et choisissez le lien « Ouvrir le magasin de données ».
1. Dans la page de magasin d’objets blob résultante, collez le chemin ci-dessus dans la zone de recherche. Vous trouverez les sorties de scoring dans le dossier.
:::image type="content" source="media/how-to-use-batch-endpoint/scoring-view.gif" alt-text="Capture vidéo de l’ouverture du dossier de scoring et de la sortie de scoring" lightbox="media/how-to-use-batch-endpoint/scoring-view.gif":::

## <a name="add-a-deployment-to-the-batch-endpoint"></a>Ajouter un déploiement au point de terminaison de traitement de lots

Un point de terminaison de traitement de lots peut avoir plusieurs déploiements. Chaque déploiement héberge un modèle pour le scoring par lots. 

### <a name="add-a-new-deployment"></a>Ajouter un nouveau déploiement

Utilisez la commande suivante pour ajouter un nouveau déploiement à un point de terminaison de traitement de lots existant.

```azurecli
az ml endpoint update --name mybatchedp --type batch --deployment-file cli/endpoints/batch/add-deployment.yml
```

Cet exemple utilise un modèle non-MLflow. Lors de l’utilisation d’un modèle non-MLflow, vous devez spécifier l’environnement et un script de scoring dans le fichier YAML :

:::code language="yaml" source="~/azureml-examples-main/cli/endpoints/batch/add-deployment.yml" :::

Autres attributs de déploiement pour le modèle non-MLflow :

| Clé | Description |
| --- | ----------- |
| code_configuration.code.local_path | Répertoire qui contient tout le code source de scoring du modèle. |
| code_configuration.scoring_script | Fichier Python dans le répertoire ci-dessus. Ce fichier doit avoir une fonction `init()` et une fonction `run()`. Utilisez la fonction `init()` pour toute préparation coûteuse ou courante. Par exemple, utilisez-la pour charger le modèle dans un objet global. Cette fonction est appelée une seule fois au début du processus. Utilisez `run(mini_batch)` pour effectuer le scoring de chaque entrée. La valeur de `mini_batch` est une liste de chemins de fichiers. La méthode `run()` doit retourner un `DataFrame` Pandas ou un tableau. Ces éléments retournés sont ajoutés au fichier de sortie commun. Chaque élément de sortie retourné indique une exécution réussie d’un élément d’entrée dans le mini-lot d’entrée. Veillez à ce que suffisamment de données soient incluses dans votre résultat d’exécution pour mapper l’entrée à un résultat de sortie d’exécution spécifique. La sortie d’exécution sera écrite dans le fichier de sortie et ne sera pas nécessairement dans l’ordre. Vous devez donc utiliser une clé dans la sortie pour la mapper à l’entrée correcte. |
| Environnement | Environnement utilisé pour le scoring du modèle sur la cible de calcul. Vous pouvez définir l’environnement inline en spécifiant le nom, la version et le chemin. Utilisez `conda_file` pour inclure les dépendances qui seront installées par-dessus `docker.image`. Utilisez le préfixe `azureml:` pour référencer un environnement existant. Par exemple, `azureml: mnist-env:1` pointe vers la version 1 d’un environnement nommé `mnist-env`. |

Pour passer en revue les détails de votre déploiement, exécutez :

```azurecli
az ml endpoint show --name mybatchedp --type batch
```

### <a name="activate-the-new-deployment"></a>Activer le nouveau déploiement

Pour l’inférence par lots, vous devez envoyer 100 % des demandes au déploiement souhaité. Pour définir votre déploiement nouvellement créé comme cible, utilisez :

```azurecli
az ml endpoint update --name mybatchedp --type batch --traffic mnist-deployment:100
```

Si vous réexaminez les détails de votre déploiement, vous verrez vos modifications :

```azurecli
az ml endpoint show --name mybatchedp --type batch
```

Vous pouvez maintenant appeler un travail de scoring par lots avec ce nouveau déploiement :

```azurecli
az ml endpoint invoke --name mybatchedp --type batch --input-path https://pipelinedata.blob.core.windows.net/sampledata/mnist --mini-batch-size 10 --instance-count 2
```

## <a name="start-a-batch-scoring-job-using-rest"></a>Démarrer un travail de scoring par lots à l’aide de REST

Les points de terminaison batch ont des URI de scoring pour l’accès REST. REST vous permet d’utiliser une bibliothèque HTTP quelconque sur une plateforme quelconque pour démarrer un travail de scoring par lots.

1. Obtenez `scoring_uri` :  

```azurecli
scoring_uri=$(az ml endpoint show --name mybatchedp --type batch --query scoring_uri -o tsv)
```

2. Obtenez le jeton d’accès :

```azurecli
auth_token=$(az account get-access-token --query accessToken -o tsv)
```

3. Utilisez `scoring_uri`, le jeton d’accès et les données JSON pour publier (POST) une demande et démarrer un travail de scoring par lots :

```bash
curl --location --request POST "$scoring_uri" --header "Authorization: Bearer $auth_token" --header 'Content-Type: application/json' --data-raw '{
"properties": {
  "dataset": {
    "dataInputType": "DataUrl",
    "Path": "https://pipelinedata.blob.core.windows.net/sampledata/mnist"
    }
  }
}'
```

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous n’avez pas l’intention d’utiliser les ressources que vous avez créées, supprimez-les pour éviter des frais :

1. Dans le portail Azure, dans le menu de gauche, sélectionnez **Groupes de ressources**.
1. Dans la liste des groupes de ressources, sélectionnez le groupe de ressources que vous avez créé.
1. Sélectionnez **Supprimer le groupe de ressources**.
1. Entrez le nom du groupe de ressources. Ensuite, sélectionnez **Supprimer**.

Vous pouvez également conserver le groupe de ressources mais supprimer un espace de travail unique. Affichez les propriétés de l’espace de travail, puis sélectionnez **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à créer et à appeler des points de terminaison batch, ce qui vous permet d’effectuer le scoring de grandes quantités de données. Pour en savoir plus sur Azure Machine Learning, consultez les articles suivants :

* [Résolution des problèmes de points de terminaison de traitement de lots](how-to-troubleshoot-batch-endpoints.md)
