---
title: 'MLOps : Gestion des modèles ML'
titleSuffix: Azure Machine Learning
description: 'Apprenez-en plus sur la gestion des modèles avec Azure Machine Learning (MLOps). Déployez, gérez et surveillez vos modèles afin de les améliorer en permanence. '
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: jpe316
ms.author: jordane
ms.date: 11/22/2019
ms.custom: seodec18
ms.openlocfilehash: e53db645875646b1e021cc0d3d760677e1128c0c
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/20/2020
ms.locfileid: "77486374"
---
# <a name="mlops-model-management-deployment-and-monitoring-with-azure-machine-learning"></a>MLOps : Gestion des modèles, déploiement et surveillance avec Azure Machine Learning

Dans cet article, découvrez comment utiliser Azure Machine Learning pour gérer le cycle de vie de vos modèles. Azure Machine Learning utilise une approche d’Opérations de Machine Learning (MLOps). MLOps améliore la qualité et la cohérence de vos solutions de Machine Learning. 

Azure Machine Learning propose les fonctionnalités MLOps suivantes :

- **Créer des pipelines ML reproductibles**. Les pipelines vous permettent de définir des étapes reproductibles et réutilisables pour vos processus de préparation, d’entraînement et de scoring de données.
- **Inscrire, empaqueter et déployer des modèles à partir de n’importe où**, et suivre les métadonnées associées requises pour utiliser le modèle.
- **Capturer les données de gouvernance requises pour la capture de bout en bout du cycle de vie ML**, notamment qui publie des modèles, pourquoi des modifications sont apportées et quand les modèles ont été déployés ou utilisés en production.
- **Notifier et alerter sur les événements du cycle de vie ML** tels que l’accomplissement d’expérimentation, l’inscription de modèle, le déploiement de modèle et la détection de dérive des données.
- **Superviser les applications ML pour détecter des problèmes de fonctionnement et liés à ML**. Comparez les entrées de modèle entre l’entraînement et l’inférence, explorez les métriques spécifiques du modèle et fournissez des alertes et une supervision pour votre infrastructure ML.
- **Automatiser le cycle de vie ML de bout en bout avec Azure Machine Learning et Azure DevOps** pour mettre fréquemment à jour les modèles, tester de nouveaux modèles et déployer en permanence de nouveaux modèles ML en même temps que vos autres applications et services.

## <a name="create-reproducible-ml-pipelines"></a>Créer des pipelines ML reproductibles

Utilisez des pipelines ML d’Azure Machine Learning pour réunir toutes les étapes impliquées dans votre processus d’entraînement de modèle.

Un pipeline ML peut contenir des étapes allant de la préparation des données à l’extraction de fonctionnalité, pour le réglage d’hyperparamètre sur l’évaluation du modèle. Pour plus d’informations, consultez [Pipelines ML](concept-ml-pipelines.md).

Si vous utilisez le [Concepteur](concept-designer.md) pour créer vos pipelines ML, vous pouvez à tout moment cliquer sur **« ... »** en haut à droite de la page du Concepteur, puis sélectionner **Cloner**. Le clonage de votre pipeline vous permet d’itérer votre conception de pipeline sans perdre vos anciennes versions.  

## <a name="register-package-and-deploy-models-from-anywhere"></a>Inscrire, empaqueter et déployer des modèles à partir de n’importe quel emplacement

### <a name="register-and-track-ml-models"></a>Inscrire et suivre des modèles ML

L’inscription de modèle vous permet de stocker vos modèles dans le cloud Azure, au sein de votre espace de travail, et d’en gérer les versions. Le registre de modèle facilite l’organisation et le suivi de vos modèles entraînés.

> [!TIP]
> Un modèle inscrit est un conteneur logique pour un ou plusieurs fichiers qui composent votre modèle. Par exemple, si vous avez un modèle qui est stocké dans plusieurs fichiers, vous pouvez inscrire ces derniers en tant que modèle unique dans votre espace de travail Azure Machine Learning. Après l’inscription, vous pouvez ensuite télécharger ou déployer le modèle inscrit et recevoir tous les fichiers qui ont été inscrits.

Les modèles inscrits sont identifiés par leur nom et par leur version. Chaque fois que vous inscrivez un modèle portant le même nom qu’un modèle existant, le registre incrémente la version. Des balises de métadonnées supplémentaires peuvent être fournies lors de l’inscription. Ces balises sont ensuite utilisées lors de la recherche d’un modèle. Azure Machine Learning prend en charge tous les modèles pouvant être chargés avec Python 3.5.2 ou version ultérieure.

> [!TIP]
> Vous pouvez également inscrire des modèles formés en dehors d'Azure Machine Learning.

Vous ne pouvez pas supprimer un modèle inscrit qui est utilisé dans un déploiement actif.
Pour plus d’informations, consultez la section consacrée à l’inscription d’un modèle dans l’article [Déployer des modèles](how-to-deploy-and-where.md#registermodel).

### <a name="package-and-debug-models"></a>Empaqueter et déboguer des modèles

Avant de déployer un modèle en production, il est empaqueté dans une image Docker. Dans la plupart des cas, la création d’image a lieu automatiquement en arrière-plan pendant le déploiement. Vous pouvez spécifier l’image manuellement.

Si vous rencontrez des problèmes avec le déploiement, vous pouvez déployer sur votre environnement de développement local pour la résolution des problèmes et le débogage.

Pour plus d’informations, consultez [Déployer des modèles](how-to-deploy-and-where.md#registermodel) et [Résolution des problèmes liés aux déploiements](how-to-troubleshoot-deployment.md).

### <a name="validate-and-profile-models"></a>Valider et profiler des modèles

Azure Machine Learning peut utiliser le profilage pour déterminer les paramètres de processeur et de mémoire idéaux à utiliser lors du déploiement de votre modèle. La validation de modèle se produit dans le cadre de ce processus, à l’aide de données que vous fournissez pour le processus de profilage.

### <a name="convert-and-optimize-models"></a>Convertir et optimiser les modèles

La conversion de votre modèle en ONNX ([Open Neural Network Exchange](https://onnx.ai)) peut améliorer les performances. En moyenne, la conversion en ONNX peut doubler les performances.

Pour plus d’informations sur ONNX avec Azure Machine Learning, consultez l’article [Créer et accélérer des modèles ML](concept-onnx.md).

### <a name="use-models"></a>Utiliser des modèles

Les modèles Machine Learning entraînés sont déployés en tant que services web dans le cloud ou localement. Vous pouvez également déployer les modèles sur des appareils Azure IoT Edge. Les déploiements utilisent un processeur, un processeur graphique ou des tableaux FPGA (Field-Programmable Gate Array) pour l’inférence. Vous pouvez également utiliser des modèles de Power BI.

Lorsque vous utilisez un modèle comme un service web ou un appareil IoT Edge, vous fournissez les éléments suivants :

* Modèles utilisés pour noter les données envoyées vers le service/l’appareil.
* Script d’entrée. Ce script accepte les demandes, utilise les modèles pour noter les données et renvoie une réponse.
* Fichier d’environnement conda qui décrit les dépendances requises par les modèles et le script d’entrée.
* Autres ressources, telles que le texte ou les données, entre autres, qui sont requises par les modèles et le script d’entrée.

Vous fournissez également la configuration de la plateforme de déploiement cible, par exemple le type de famille de machines virtuelles, la mémoire disponible et nombre de cœurs lors du déploiement sur Azure Kubernetes Service.

Lorsque l’image est créée, les composants requis par Azure Machine Learning sont également ajoutés, par exemple les ressources nécessaires pour exécuter le service web et interagir avec IoT Edge.

#### <a name="batch-scoring"></a>Scoring par lot
La notation par lots est prise en charge via des pipelines ML. Pour plus d’informations, consultez [Prédictions par lots sur le Big Data](how-to-use-parallel-run-step.md).

#### <a name="real-time-web-services"></a>Services web en temps réel

Vous pouvez utiliser vos modèles dans les **services web** avec les cibles de calcul suivantes :

* Azure Container Instance
* Azure Kubernetes Service
* Environnement de développement local

Pour déployer le modèle comme un service web, vous devez fournir les éléments suivants :

* Modèle ou ensemble de modèles.
* Dépendances requises pour utiliser le modèle, par exemple un script qui accepte les demandes et appelle le modèle, les dépendances conda, etc.
* Configuration de déploiement qui décrit comment et où déployer le modèle.

Pour plus d’informations, consultez [Déployer des modèles](how-to-deploy-and-where.md).

#### <a name="iot-edge-devices"></a>Appareils IoT Edge

Vous pouvez utiliser des modèles avec les appareils IoT via des **modules Azure IoT Edge**. Les modules IoT Edge sont déployés sur un périphérique matériel, ce qui permet l’inférence, ou le scoring de modèles, sur l’appareil.

Pour plus d’informations, consultez [Déployer des modèles](how-to-deploy-and-where.md).

### <a name="analytics"></a>Analytics

Microsoft Power BI prend en charge l’utilisation de modèles Machine Learning pour l’analytique données. Pour plus d’informations, consultez [Intégration d’Azure Machine Learning dans Power BI (préversion)](https://docs.microsoft.com/power-bi/service-machine-learning-integration).

## <a name="capture-the-governance-data-required-for-capturing-the-end-to-end-ml-lifecycle"></a>Capturer les données de gouvernance nécessaires pour la capture du cycle de vie ML de bout en bout

Azure ML vous donne la possibilité d’effectuer le suivi de la piste d’audit de bout en bout de toutes vos ressources ML. Plus précisément :

- Azure ML [s’intègre à Git](how-to-set-up-training-targets.md#gitintegration) pour le suivi des informations sur le dépôt / la branche / la validation d’où provient votre code.
- Les [jeux de données Azure ML](how-to-create-register-datasets.md) vous aident à suivre, à profiler et à gérer la version des données. 
- L’historique des exécutions Azure ML stocke une capture instantanée du code, des données et du calcul utilisés pour entraîner un modèle.
- Le registre de modèles Azure ML capture toutes les métadonnées associées à votre modèle (l’expérience qui l’a entraîné, où il est déployé, si ses déploiements sont sains).

## <a name="notify-automate-and-alert-on-events-in-the-ml-lifecycle"></a>Notifier, automatiser et alerter sur des événements du cycle de vie ML
Azure ML publie des événements clés dans Azure EventGrid, qui peuvent être utilisés pour notifier et automatiser des événements du cycle de vie ML. Pour plus d’informations, consultez [ce document](how-to-use-event-grid.md).


## <a name="monitor-for-operational--ml-issues"></a>Superviser les problèmes opérationnels et Machine Learning

La supervision vous permet de comprendre les données envoyées à votre modèle et les prédictions qu’il retourne.

Ces informations vous aident à comprendre l’utilisation de votre modèle. Les données d’entrée collectées peuvent également être utiles dans l’entraînement des futures versions du modèle.

Pour plus d’informations, consultez [Guide pratique pour activer la collecte des données de modèle](how-to-enable-data-collection.md).

## <a name="retrain-your-model-on-new-data"></a>Réentraîner votre modèle sur de nouvelles données

Vous souhaiterez souvent mettre à jour votre modèle, ou même le réentraîner depuis le début, à mesure que vous recevrez de nouvelles informations. Parfois, la réception de nouvelles données est une partie attendue du domaine. Dans d’autres cas, comme indiqué dans [Détecter une dérive de données (préversion) sur des jeux de données](how-to-monitor-datasets.md), les performances du modèle peuvent se dégrader en fonction de modifications apportées à un capteur particulier, de modifications de données naturelles telles que l’impact des saisons, ou de fonctionnalités qui évoluent dans leur relation avec d’autres fonctionnalités. 

Il n’y a aucune réponse universelle à la question « Comment savoir si je dois réentraîner ? » mais les outils de supervision et d’événements Azure ML étudiés précédemment constituent de bons points de départ pour l’automatisation. Une fois que vous avez décidé de réentraîner, vous devez : 

- Prétraiter vos données à l’aide d’un processus reproductible et automatisé
- Entraîner votre nouveau modèle
- Comparer les sorties de votre nouveau modèle à celles de votre ancien modèle
- Utiliser des critères prédéfinis pour choisir de remplacer ou non votre ancien modèle 

Un thème des étapes ci-dessus est que votre nouvel entraînement doit être automatisé et non ad hoc. Les [pipelines Azure Machine Learning](concept-ml-pipelines.md) sont une bonne réponse pour créer des workflows relatifs à la préparation des données, à l’entraînement, à la validation et au déploiement. Lisez [Réentraîner des modèles à l’aide du concepteur Azure Machine Learning (préversion)](how-to-retrain-designer.md) pour voir comment les pipelines et le concepteur Azure Machine Learning s’intègrent dans un scénario de nouvel entraînement. 

## <a name="automate-the-ml-lifecycle"></a>Automatiser le cycle de vie ML 

Vous pouvez utiliser GitHub et Azure Pipelines pour créer un processus d’intégration continue qui entraîne un modèle. Dans un scénario classique, quand un scientifique des données vérifie une modification dans le dépôt Git pour un projet, Azure Pipelines démarre une exécution d’entraînement. Les résultats de l’exécution peuvent ensuite être examinés pour afficher les caractéristiques de performances du modèle entraîné. Vous pouvez également créer un pipeline qui déploie le modèle comme un service web.

L’[extension Azure Machine Learning](https://marketplace.visualstudio.com/items?itemName=ms-air-aiagility.vss-services-azureml) facilite l’utilisation d’Azure Pipelines. Elle offre les améliorations suivantes à Azure Pipelines :

* Permet la sélection de l’espace de travail lors de la définition d’une connexion de service.
* Permet le déclenchement des pipelines de mise en production par les modèles entraînés créés dans un pipeline d’entraînement.

Pour plus d’informations sur l’utilisation d’Azure Pipelines avec Azure Machine Learning, consultez l’article [Intégration et déploiement continus de modèles ML avec Azure Pipelines](/azure/devops/pipelines/targets/azure-machine-learning) et le dépôt [MLOps Azure Machine Learning](https://aka.ms/mlops).

## <a name="next-steps"></a>Étapes suivantes

Découvrez plus d’informations en lisant et en explorant les ressources suivantes :

+ [Comment et où déployer des modèles](how-to-deploy-and-where.md) avec Azure Machine Learning

+ [Tutoriel : Déployer un modèle de classification d’images dans ACI](tutorial-deploy-models-with-aml.md)

+ [Dépôt d’exemples MLOps de bout en bout](https://github.com/microsoft/MLOps)

+ [CI/CD de modèles ML avec Azure Pipelines](/azure/devops/pipelines/targets/azure-machine-learning)

+ Créer des clients qui [consomment un modèle déployé](how-to-consume-web-service.md)

+ [Machine Learning à l’échelle](/azure/architecture/data-guide/big-data/machine-learning-at-scale)

+ [Dépôt des architectures de référence et des bonnes pratiques d’Azure AI](https://github.com/microsoft/AI)
