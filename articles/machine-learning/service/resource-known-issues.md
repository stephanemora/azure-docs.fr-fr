---
title: Problèmes connus et résolutions
titleSuffix: Azure Machine Learning service
description: Liste des problèmes connus, des solutions de contournement et des résolutions pour Azure Machine Learning service.
services: machine-learning
author: j-martens
ms.author: jmartens
ms.reviewer: mldocs
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 08/09/2019
ms.custom: seodec18
ms.openlocfilehash: 96af61089f2b7b85d58a8a2ab61936459cef158b
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/10/2019
ms.locfileid: "70858693"
---
# <a name="known-issues-and-troubleshooting-azure-machine-learning-service"></a>Problèmes connus et dépannage du service Azure Machine Learning

Cet article vous permet de rechercher et de corriger les erreurs ou les défaillances rencontrées lors de l’utilisation du service Azure Machine Learning.

## <a name="visual-interface-issues"></a>Problèmes concernant l’interface visuelle

Interface visuelle pour les problèmes de service d’apprentissage automatique.

### <a name="long-compute-preparation-time"></a>Calcul de préparation de longue durée

La création d’un calcul ou l’évocation à la fin d’un calcul prend du temps, quelques minutes ou plus. L’équipe travaille sur l’optimisation.


### <a name="cannot-run-an-experiment-only-contains-dataset"></a>Impossible d’exécuter une expérience contenant uniquement des jeux de données 

Vous souhaiterez peut-être exécuter une expérience contenant uniquement des jeux de données pour visualiser le jeu de données. Toutefois, il n’est pas autorisé à exécuter une expérience contenant uniquement des jeux de données aujourd’hui. Nous nous efforçons activement de résoudre ce problème.
 
Avant le correctif, vous pouviez connecter le jeu de données à n’importe quel module de transformation de données (sélectionner des colonnes dans le jeu de données, modifier les métadonnées, fractionner des données, etc.) et exécuter l’expérience. Vous pouvez ensuite visualiser le jeu de données. 

L’image ci-dessous affiche comment : ![visulize-data](./media/resource-known-issues/aml-visualize-data.png)

## <a name="sdk-installation-issues"></a>Problèmes d’installation de Kit de développement logiciel (SDK)

**Message d’erreur : Impossible de désinstaller « PyYAML »**

Kit SDK Azure Machine Learning pour Python : PyYAML est un projet installé distutils. Par conséquent, nous ne pouvons pas déterminer avec précision les fichiers qui lui appartiennent en cas de désinstallation partielle. Pour poursuivre l’installation du kit de développement logiciel (SDK) en ignorant cette erreur, utilisez :

```Python
pip install --upgrade azureml-sdk[notebooks,automl] --ignore-installed PyYAML
```

**Message d’erreur : `ERROR: No matching distribution found for azureml-dataprep-native`**

La distribution Python 3.7.4 d’Anaconda comprend un bogue qui interrompt l’installation d’azureml-SDK. Ce problème est abordé dans ce [problème GitHub](https://github.com/ContinuumIO/anaconda-issues/issues/11195) qui peut être contourné en créant un nouvel environnement Conda à l’aide de cette commande :
```bash
conda create -n <env-name> python=3.7.3
```
Ce qui crée un environnement Conda utilisant Python 3.7.3, qui ne pose pas le problème d’installation présent dans 3.7.4.

## <a name="trouble-creating-azure-machine-learning-compute"></a>Problèmes à la création de la Capacité de calcul Azure Machine Learning

Il peut arriver, dans de rares cas, que des utilisateurs ayant créé leur espace de travail Azure Machine Learning sur le Portail Azure avant la disponibilité générale ne puissent pas créer de Capacité de calcul Azure Machine Learning dans cet espace de travail. Vous pouvez soulever une demande de support auprès du service ou créer un espace de travail sur le Portail ou avec le kit SDK pour vous débloquer sans délai.

## <a name="image-building-failure"></a>Échec de génération d’image

Échec de génération d’image lors du déploiement de service web. La solution de contournement consiste à ajouter « pynacl==1.2.1 » en tant que dépendance pip au fichier Conda pour la configuration d’image.

## <a name="deployment-failure"></a>Échec du déploiement

Si vous observez `['DaskOnBatch:context_managers.DaskOnBatch', 'setup.py']' died with <Signals.SIGKILL: 9>`, remplacez la référence SKU des machines virtuelles utilisées dans votre déploiement par une référence SKU disposant de plus de mémoire.

## <a name="fpgas"></a>FPGA

Vous ne serez pas en mesure de déployer des modèles sur des FPGA tant que vous n’aurez pas demandé un quota FPGA et qu’il n’aura pas été approuvé. Pour demander un accès, remplissez le formulaire de demande de quota : https://aka.ms/aml-real-time-ai

## <a name="automated-machine-learning"></a>Machine learning automatisé

Le machine learning automatisé des flux de tenseur ne prend actuellement pas en charge la version 1.13 des flux de tenseur. Si vous installez cette version, les dépendances de package cesseront de fonctionner. Nous nous efforçons de résoudre ce problème dans une version ultérieure. 

### <a name="experiment-charts"></a>Graphiques d’expérience

Les graphiques de classification binaire (rappel de précision, ROC, obtenir la courbe, etc.) indiqués dans les itérations d’expériences ML automatisées ne sont pas correctement rendus dans l’interface utilisateur depuis le 12/04. Les tracés de graphique affichent actuellement les résultats inverses, ainsi les modèles plus performants sont affichés avec les résultats les plus bas. Une résolution est en cours d’investigation.

## <a name="databricks"></a>Databricks

Problèmes Databricks et Azure Machine Learning

### <a name="failure-when-installing-packages"></a>Échec lors de l’installation des packages

L’installation du Kit de développement logiciel (SDK) Azure Machine Learning échoue sur Azure Databricks lorsque plusieurs packages sont installés. Certains packages, comme `psutil`, peuvent provoquer des conflits. Pour éviter les erreurs d’installation, installez les packages en bloquant la version des bibliothèques. Ce problème est lié à Azure Databricks et non au Kit de développement logiciel (SDK) Azure Machine Learning service. Vous pouvez également rencontrer ce problème avec d’autres bibliothèques. Exemple :

```python
psutil cryptography==1.5 pyopenssl==16.0.0 ipython==2.2.0
```

Vous pouvez également utiliser des scripts init si les problèmes d’installation persistent avec les bibliothèques Python. Cette approche n’est pas officiellement prise en charge. Pour plus d’informations, consultez [Scripts init à étendue au réseau en cluster](https://docs.azuredatabricks.net/user-guide/clusters/init-scripts.html#cluster-scoped-init-scripts).

### <a name="cancel-an-automated-machine-learning-run"></a>Annuler l’exécution d’un machine learning automatisé

Si vous utilisez les fonctionnalités de machine learning automatisé sur Azure Databricks et souhaitez annuler une exécution pour en démarrer une nouvelle à des fins d’expérimentation, redémarrez votre cluster Azure Databricks.

### <a name="10-iterations-for-automated-machine-learning"></a>>10 itérations d’apprentissage automatique automatisé

Dans les paramètres de machine learning automatisé, si vous avez plus de 10 itérations, définissez `show_output` sur `False` lorsque vous soumettez l’exécution.

### <a name="widget-for-the-azure-machine-learning-sdkautomated-machine-learning"></a>Widget pour le Kit de développement logiciel (SDK) Azure Machine Learning/apprentissage automatique automatisé

Le widget du Kit de développement logiciel (SDK) Azure Machine Learning n’est pas pris en charge dans un notebook Azure Databricks, car les notebooks ne peuvent pas analyser les widgets HTML. Vous pouvez afficher le widget dans le portail à l’aide de ce code Python dans la cellule du notebook Azure Databricks :

```
displayHTML("<a href={} target='_blank'>Azure Portal: {}</a>".format(local_run.get_portal_url(), local_run.id))
```

### <a name="import-error-no-module-named-pandascoreindexes"></a>Erreur d’importation : Aucun module nommé « pandas.core.indexes »

Si cette erreur s’affiche lorsque vous utilisez le machine learning automatisé :

1. Exécutez cette commande pour installer deux packages dans votre cluster Azure Databricks : 

   ```
   scikit-learn==0.19.1
   pandas==0.22.0
   ```

1. Détachez, puis réattachez le cluster à votre notebook. 

Si ces étapes ne résolvent pas le problème, essayez de redémarrer le cluster.

### <a name="failtosendfeather"></a>FailToSendFeather

Si vous voyez une erreur `FailToSendFeather` lors de la lecture de données sur le cluster Azure Databricks, reportez-vous aux solutions suivantes :

* Effectuez une mise à niveau du package `azureml-sdk[automl_databricks]` vers la dernière version.
* Ajoutez la version 1.1.8 ou une version supérieure de `azure-dataprep`.
* Ajoutez la version 0.11 ou une version supérieure de `pyarrow`.

## <a name="azure-portal"></a>Portail Azure

Si vous accédez directement à votre espace de travail à partir d’un lien de partage provenant du kit SDK ou du portail, vous ne pourrez pas afficher la page Vue d’ensemble normale comportant des informations sur l’abonnement dans l’extension. Vous ne pourrez pas non plus basculer sur un autre espace de travail. Si vous souhaitez afficher un autre espace de travail, la solution de contournement consiste à accéder directement au [portail Azure](https://portal.azure.com) et à rechercher le nom de l’espace de travail.

## <a name="diagnostic-logs"></a>Journaux de diagnostic

Parfois, fournir des informations de diagnostic quand vous demandez de l’aide peut se révéler utile. Pour afficher certains journaux d’activité, visitez le [Portail Microsoft Azure](https://portal.azure.com) et accédez à votre espace de travail, puis sélectionnez **Espace de travail > Expérience > Exécuter > Journaux d’activité**.  Vous pouvez également trouver ces informations dans la section **Expériences** de la [page d’arrivée de votre espace de travail (préversion)](https://ml.azure.com).

> [!NOTE]
> Azure Machine Learning service consigne des informations de diverses sources pendant la formation, telles que AutoML ou le conteneur Docker qui exécute le travail de formation. La plupart de ces journaux ne sont pas documentés. Si vous rencontrez des problèmes et que vous contactez le support Microsoft, il pourra peut-être utiliser ces journaux pendant la résolution des problèmes.

## <a name="activity-logs"></a>Journaux d’activité

Certaines actions dans l’espace de travail Azure Machine Learning ne consignent pas d’informations dans le __journal d’activité__. Par exemple, le démarrage d’une exécution de formation ou l’inscription d’un modèle.

Certaines de ces actions apparaissent dans la zone __Activités__ de votre espace de travail, mais elles n’indiquent pas qui a initié l’activité.

## <a name="resource-quotas"></a>Quotas de ressources

Découvrez plus d’informations sur les [quotas des ressources](how-to-manage-quotas.md) que vous pouvez rencontrer quand vous utilisez Azure Machine Learning.

## <a name="authentication-errors"></a>Erreurs d’authentification

Si vous effectuez une opération de gestion sur une cible de calcul à partir d'un travail distant, vous recevrez l'une des erreurs suivantes :

```json
{"code":"Unauthorized","statusCode":401,"message":"Unauthorized","details":[{"code":"InvalidOrExpiredToken","message":"The request token was either invalid or expired. Please try again with a valid token."}]}
```

```json
{"error":{"code":"AuthenticationFailed","message":"Authentication failed."}}
```

Par exemple, vous recevrez une erreur si vous essayez de créer ou de joindre une cible de calcul à partir d'un pipeline Machine Learning soumis en vue d'une exécution à distance.

## <a name="overloaded-azurefile-storage"></a>Stockage Fichier Azure surchargé

Si vous recevez une erreur `Unable to upload project files to working directory in AzureFile because the storage is overloaded`, appliquez les solutions de contournement suivantes.

Si vous utilisez le partage de fichiers pour d’autres charges de travail, telles que le transfert de données, il est recommandé d’utiliser des objets blob afin de permettre l’utilisation du partage de fichiers pour l’envoi des exécutions. Vous pouvez également répartir la charge de travail entre deux espaces de travail.
