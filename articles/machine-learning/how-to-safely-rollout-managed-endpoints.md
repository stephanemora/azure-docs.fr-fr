---
title: Déploiement sécurisé pour les points de terminaison en ligne
titleSuffix: Azure Machine Learning
description: Déployez des versions plus récentes de modèles ML sans perturbation.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: seramasu
ms.reviewer: laobri
author: rsethur
ms.date: 05/25/2021
ms.topic: how-to
ms.custom: how-to
ms.openlocfilehash: 61754eec2c866a7bf5897b2faa2a2b2ae7b60d02
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110382616"
---
# <a name="safe-rollout-for-online-endpoints-preview"></a>Déploiement sécurisé pour les points de terminaison en ligne (préversion)

Vous avez un modèle existant déployé en production et vous voulez déployer une nouvelle version du modèle. Comment déployer votre nouveau modèle ML sans provoquer de perturbation ? Une bonne réponse est le déploiement blue-green (bleu-vert), une approche dans laquelle une nouvelle version d’un service web est introduite en production en déployant la modification sur une petite partie d’utilisateurs/de demandes avant de la déployer complètement. 

Dans cet article, vous allez apprendre à effectuer les opérations suivantes :

> [!div class="checklist"]
> * Déployer un nouveau point de terminaison en ligne appelé « blue » qui fait fonctionner la version 1 du modèle
> * Mettre à l’échelle ce déploiement afin qu’il puisse gérer plus de demandes
> * Déployer la version 2 du modèle sur un point de terminaison appelé « green », qui n’accepte aucun trafic dynamique
> * Tester le déploiement green de façon isolée 
> * Envoyer 10 % du trafic dynamique au déploiement green
> * Transférer entièrement tout le trafic dynamique au déploiement green
> * Supprimer le déploiement blue de la version 1 maintenant inutilisée

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="prerequisites"></a>Prérequis

* Pour utiliser Azure Machine Learning, vous devez avoir un abonnement Azure. Si vous n’avez pas d’abonnement Azure, créez un compte gratuit avant de commencer. Essayez la [version gratuite ou payante d’Azure Machine Learning](https://aka.ms/AMLFree) dès aujourd’hui.

* Vous devez installer et configurer Azure CLI et l’extension ML. Pour plus d’informations, consultez [Installer, configurer et utiliser l’interface CLI 2.0 (préversion)](how-to-configure-cli.md). 

* Vous devez avoir un groupe de ressources Azure dans lequel vous (ou le principal de service que vous utilisez) devez disposer d’un accès `Contributor`. Vous disposez d’un tel groupe de ressources si vous avez configuré votre extension ML conformément à l’article ci-dessus. 

* Vous devez avoir un espace de travail Azure Machine Learning. Vous disposez d’un tel espace de travail si vous avez configuré votre extension ML conformément à l’article ci-dessus.

* Si vous n’avez pas encore défini les paramètres par défaut pour Azure CLI, vous devez enregistrer vos paramètres par défaut. Pour éviter d’avoir à passer transmettre plusieurs fois les valeurs, exécutez :

```azurecli
az account set --subscription <subscription id>
az configure --defaults workspace=<azureml workspace name> group=<resource group>
```

* Un point de terminaison managé existant. Cet article suppose que votre déploiement est tel que décrit dans [Déployer et scorer un modèle Machine Learning avec un point de terminaison en ligne managé (préversion)](how-to-deploy-managed-online-endpoints.md).

* Si vous n’avez pas déjà défini la variable d’environnement $ENDPOINT _NAME, faites-le maintenant :

:::code language="azurecli" source="~/azureml-examples-main/cli/how-to-deploy-declarative-safe-rollout-online-endpoints.sh" ID="set_endpoint_name":::

* (Recommandé) Clonez le dépôt d’exemples et accédez au répertoire `cli/` du dépôt : 

```azurecli
git clone https://github.com/Azure/azureml-examples
cd azureml-examples/cli
```

Les commandes de ce tutoriel se trouvent dans le fichier `how-to-deploy-declarative-safe-rollout-online-endpoints.sh` et les fichiers de configuration YAML se trouvent dans le sous-répertoire `endpoints/online/managed/canary-declarative-flow/`.

## <a name="confirm-your-existing-deployment-is-created"></a>Vérifier que votre déploiement existant est créé

Vous pouvez visualiser l’état de votre déploiement existant en exécutant : 

```azurecli
az ml endpoint show --name $ENDPOINT_NAME 
```

Vous devez normalement voir le point de terminaison identifié par `$ENDPOINT_NAME` et un déploiement appelé `blue`. 

## <a name="scale-your-existing-deployment-to-handle-more-traffic"></a>Mettre à l’échelle votre déploiement existant pour gérer davantage de trafic

Dans le déploiement décrit dans [Déployer et scorer un modèle Machine Learning avec un point de terminaison en ligne managé (préversion)](how-to-deploy-managed-online-endpoints.md), vous définissez `instance_count` sur la valeur `1`. Pour gérer davantage de trafic, la deuxième version du fichier YAML (`2-scale-blue.yml`) remplace la valeur par `2` :

:::code language="yaml" source="~/azureml-examples-main/cli/endpoints/online/managed/canary-declarative-flow/2-scale-blue.yml" range="29":::

Mettez à jour le déploiement avec :

:::code language="azurecli" source="~/azureml-examples-main/cli/how-to-deploy-declarative-safe-rollout-online-endpoints.sh" ID="scale_blue" :::

> [!IMPORTANT]
> La mise à jour avec le fichier YAML est déclarative. Autrement dit, les modifications apportées dans le fichier YAML seront reflétées dans les ressources Azure Resource Manager sous-jacentes (points de terminaison et déploiements). Cette approche facilite [GitOps](https://www.atlassian.com/git/tutorials/gitops) : *TOUTES* les modifications apportées aux points de terminaison/déploiements passent par le fichier YAML (même `instance_count`). À titre d’effet secondaire, si vous supprimez un déploiement du fichier YAML et que vous exécutez `az ml endpoint update` en utilisant le fichier, ce déploiement sera supprimé. 

## <a name="deploy-a-new-model-but-send-it-no-traffic-yet"></a>Déployer un nouveau modèle, mais sans lui envoyer encore aucun trafic

Pour déployer votre nouveau modèle, ajoutez une nouvelle section à la section `deployments` de votre fichier de configuration, mais spécifiez dans la section `traffic` qu’il doit recevoir 0 % du trafic. Le fichier `3-create-green.yml` intègre cette modification :

:::code language="yaml" source="~/azureml-examples-main/cli/endpoints/online/managed/canary-declarative-flow/3-create-green.yml" range="7,35-56":::

Mettez à jour le déploiement : 

:::code language="azurecli" source="~/azureml-examples-main/cli/how-to-deploy-declarative-safe-rollout-online-endpoints.sh" ID="create_green" :::

### <a name="test-the-new-deployment"></a>Tester le nouveau déploiement

La configuration a spécifié 0 % du trafic vers le déploiement `green` que vous venez de créer. Pour le tester, vous pouvez l’appeler directement en spécifiant le nom `--deployment` :

:::code language="azurecli" source="~/azureml-examples-main/cli/how-to-deploy-declarative-safe-rollout-online-endpoints.sh" ID="test_green" :::

Si vous voulez utiliser un client REST pour appeler le déploiement directement sans passer par les règles de trafic, définissez l’en-tête HTTP suivant : `azureml-model-deployment: <deployment-name>`.

## <a name="test-the-new-deployment-with-a-small-percentage-of-live-traffic"></a>Tester le nouveau déploiement avec un faible pourcentage du trafic dynamique

Une fois que vous avez testé votre déploiement `green`, le fichier `4-flight-green.yml` montre comment traiter un certain pourcentage du trafic en modifiant la configuration de `traffic` dans le fichier de configuration :

:::code language="yaml" source="~/azureml-examples-main/cli/endpoints/online/managed/canary-declarative-flow/4-flight-green.yml" range="5-7":::

En dehors des lignes en surbrillance, le fichier de configuration n’est pas modifié. Mettez à jour votre déploiement avec :

:::code language="azurecli" source="~/azureml-examples-main/cli/how-to-deploy-declarative-safe-rollout-online-endpoints.sh" ID="green_10pct_traffic" :::

À présent, votre déploiement `green` va recevoir 10 % des demandes. 

## <a name="send-all-traffic-to-your-new-deployment"></a>Envoyer tout le trafic vers votre nouveau déploiement

Une fois que vous constatez que votre déploiement `green` est entièrement satisfaisant, basculez tout le trafic vers celui-ci. L’extrait de code suivant montre seulement le code pertinent du fichier de configuration, qui ne fait l’objet d’aucune autre modification :

:::code language="yaml" source="~/azureml-examples-main/cli/endpoints/online/managed/canary-declarative-flow/5-full-green.yml" range="5-7":::

Mettez à jour le déploiement : 

:::code language="azurecli" source="~/azureml-examples-main/cli/how-to-deploy-declarative-safe-rollout-online-endpoints.sh" ID="green_100pct_traffic" :::

## <a name="remove-the-old-deployment"></a>Supprimer l’ancien déploiement

Terminez la bascule vers votre nouveau modèle en supprimant l’ancien déploiement `blue`. Le fichier de configuration final ressemble à ceci :

:::code language="yaml" source="~/azureml-examples-main/cli/endpoints/online/managed/canary-declarative-flow/6-delete-blue.yml" :::

Mettez à jour le déploiement avec :

:::code language="azurecli" source="~/azureml-examples-main/cli/how-to-deploy-declarative-safe-rollout-online-endpoints.sh" ID="delete_blue" :::

## <a name="delete-the-endpoint-and-deployment"></a>Supprimer le point de terminaison et le déploiement

Si vous ne prévoyez pas d’utiliser le déploiement, vous devez le supprimer avec :

:::code language="azurecli" source="~/azureml-examples-main/cli/how-to-deploy-declarative-safe-rollout-online-endpoints.sh" ID="delete_endpoint" :::
