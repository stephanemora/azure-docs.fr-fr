---
title: Mettre à niveau le système d’exploitation hôte pour l’instance et le cluster de calcul
titleSuffix: Azure Machine Learning
description: Mettez à niveau le système d’exploitation hôte pour le cluster de calcul et l’instance de calcul d’Ubuntu 16.04 LTS vers 18.04 LTS.
services: machine-learning
author: nishankgu
ms.author: nigup
ms.reviewer: larryfr
ms.service: machine-learning
ms.subservice: core
ms.date: 03/03/2021
ms.topic: how-to
ms.openlocfilehash: e6ef72031f000c0b638d47e235b75eccf3f885f8
ms.sourcegitcommit: 30e3eaaa8852a2fe9c454c0dd1967d824e5d6f81
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/22/2021
ms.locfileid: "112461392"
---
# <a name="upgrade-compute-instance-and-compute-cluster-host-os"></a>Mettre à niveau le système d’exploitation hôte de l’instance de calcul et du cluster de calcul

Le __cluster de calcul__ et l’__instance de calcul__ d’Azure Machine Learning sont une infrastructure de calcul managée. En tant que service géré, Microsoft gère le système d’exploitation hôte et les packages et versions des logiciels qui sont installés.

Le système d’exploitation hôte pour le cluster de calcul et l’instance de calcul était Ubuntu 16.04 LTS. Le **30 avril 2021**, Ubuntu met fin à la prise en charge de la version 16.04. À partir du __15 mars 2021__, Microsoft mettra automatiquement à jour le système d’exploitation hôte vers Ubuntu 18.04 LTS. La mise à jour vers la version 18.04 garantira la poursuite des mises à jour de sécurité et du support de la communauté Ubuntu. Cette mise à jour sera déployée dans toutes les régions Azure et sera disponible dans toutes les régions à partir du __9 avril 2021__. Pour plus d’informations sur la fin de la prise en charge d’Ubuntu 16.04, consultez le [billet de blog sur la version d’Ubuntu](https://wiki.ubuntu.com/Releases).

> [!TIP]
> * Le système d’exploitation hôte n’est pas la version du système d’exploitation que vous pouvez spécifier pour un [environnement](how-to-use-environments.md) lors de l’apprentissage ou du déploiement d’un modèle. Les environnements s’exécutent dans Docker. Docker s’exécute sur le système d’exploitation hôte.
> * Si vous utilisez actuellement des environnements Ubuntu 16.04 pour l’apprentissage ou le déploiement, Microsoft vous recommande de passer à l’utilisation d’images Ubuntu 18.04. Pour plus d’informations, consultez [Comment utiliser des environnements](how-to-use-environments.md) et le [Référentiel de conteneurs Azure Machine Learning](https://github.com/Azure/AzureML-Containers/tree/master/base).
> * Lors de l’utilisation d’une instance de calcul Azure Machine Learning basée sur Ubuntu 18.04, la version de Python par défaut est _Python 3.8_.
## <a name="creating-new-resources"></a>Création de nouvelles ressources

Les clusters de calcul ou instances de calcul créés après le __9 avril 2021__ utilisent Ubuntu 18.04 LTS comme système d’exploitation hôte par défaut. Vous ne pouvez pas sélectionner un autre système d’exploitation hôte.

## <a name="upgrade-existing-resources"></a>Mettre à niveau les ressources existantes

Si vous avez des clusters de calcul ou des instances de calcul existants créés avant le __15 mars 2021__, vous devez prendre des mesures pour mettre à niveau le système d’exploitation hôte vers Ubuntu 18.04. Selon la région à partir de laquelle vous accédez à Azure Machine Learning, nous vous recommandons d’effectuer ces actions après le __9 avril 2021__ pour vous assurer que nos modifications ont été déployées dans toutes les régions :

* __Cluster de calcul Azure Machine Learning__ :

    * Si le cluster est configuré avec __min nodes = 0__, il est automatiquement mis à niveau lorsque toutes les tâches sont terminées et qu’il est réduit à zéro nœud.
    * Si __min nodes > 0__, modifiez temporairement le nombre minimal de nœuds sur zéro et autorisez le cluster à être réduit à zéro nœud.

    Pour plus d’informations sur la modification du minimum de nœuds, consultez la commande Azure CLI [az ml computetarget update amlcompute](/cli/azure/ml(v1)/computetarget/update#az_ml_computetarget_update_amlcompute) ou la référence du SDK pour [AmlCompute.update()](/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#update-min-nodes-none--max-nodes-none--idle-seconds-before-scaledown-none-).

* __Instance de calcul Azure Machine Learning__ : créez une nouvelle instance de calcul (qui utilisera Ubuntu 18.04) et supprimez l’ancienne.

    * Tous les notebooks stockés dans le partage de fichiers de l’espace de travail, les banques de données et les jeux de données sont accessibles à partir de la nouvelle instance de calcul.
    * Si vous avez créé des environnements Conda personnalisés, vous pouvez exporter ces environnements à partir de l’instance existante et les importer sur la nouvelle. Pour plus d’informations sur l’exportation et l’importation avec Conda, consultez la [documentation de Conda](https://docs.conda.io/) sur docs.conda.io.

    Pour plus d’informations, consultez les articles [Qu’est-ce qu’une nstance de calcul](concept-compute-instance.md) et [Créer et gérer une instance de calcul Azure Machine Learning](how-to-create-manage-compute-instance.md)

## <a name="check-host-os-version"></a>Vérifier la version du système d’exploitation hôte

Pour plus d’informations sur la vérification de la version du système d’exploitation hôte, consultez la page Wiki de la communauté Ubuntu concernant la [vérification de votre version d’Ubuntu](https://help.ubuntu.com/community/CheckingYourUbuntuVersion).

> [!TIP]
> Pour utiliser la commande `lsb_release -a` du Wiki, vous pouvez [utiliser une session de terminal sur une instance de calcul](how-to-access-terminal.md).
## <a name="next-steps"></a>Étapes suivantes

N’hésitez pas à nous contacter à l’adresse [ubuntu18azureml@service.microsoft.com](mailto:ubuntu18azureml@service.microsoft.com) si vous avez d’autres questions ou préoccupations.
