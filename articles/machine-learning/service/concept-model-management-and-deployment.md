---
title: 'MLOps : Gérer, déployer et superviser des modèles ML'
titleSuffix: Azure Machine Learning service
description: 'Découvrez comment utiliser le service Azure Machine Learning pour les opérations Machine Learning : déployer, gérer et superviser vos modèles afin de les améliorer en permanence. Vous pouvez déployer les modèles que vous avez entraînés avec le service Azure Machine Learning, sur votre ordinateur local ou depuis d’autres sources.'
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: jpe316
ms.author: jordane
ms.date: 06/24/2019
ms.custom: seodec18
ms.openlocfilehash: 270ebb6247e812d42ac84698d0073b41c639cb9a
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/23/2019
ms.locfileid: "70011589"
---
# <a name="mlops-manage-deploy-and-monitor-models-with-azure-machine-learning-service"></a>MLOps : Déployer, gérer et surveiller des modèles avec le service Azure Machine Learning

Dans cet article, découvrez comment utiliser le service Azure Machine Learning pour gérer le cycle de vie de vos modèles. Azure Machine Learning utilise une approche des opérations Machine Learning (MLOps), ce qui améliore la qualité et la cohérence de vos solutions de machine learning. 

Le service Azure Machine Learning propose les fonctionnalités MLOps suivantes :

- **Déployer des projets ML depuis n’importe où**
- **Superviser les applications ML pour détecter des problèmes de fonctionnement et liés à ML** : comparez les entrées de modèle entre l’entraînement et l’inférence, explorez les métriques spécifiques au modèle et fournissez des alertes et la supervision pour votre infrastructure ML.
- **Capturer les données requises pour l’établissement d’une piste d’audit de bout en bout du cycle de vie ML**, notamment qui publie des modèles, pourquoi des modifications sont apportées et quand les modèles ont été déployés ou utilisés en production.
- **Automatiser le cycle de vie ML de bout en bout avec Azure Machine Learning et Azure DevOps** pour mettre fréquemment à jour les modèles, tester de nouveaux modèles et déployer en permanence de nouveaux modèles ML en même temps que vos autres applications et services.

Pour en savoir plus sur les concepts qui sous-tendent MLOps et comment ils s’appliquent au service Azure Machine Learning, regardez la vidéo suivante.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2X1GX]

## <a name="deploy-ml-projects-from-anywhere"></a>Déployer des projets ML depuis n’importe où

### <a name="turn-your-training-process-into-a-reproducible-pipeline"></a>Transformer votre processus d’entraînement en un pipeline reproductible
Utilisez des pipelines ML à partir d’Azure Machine Learning pour combiner toutes les étapes impliquées dans votre processus d’entraînement de modèle : préparation des données, extraction des caractéristiques, optimisation des hyperparamètres et évaluation du modèle.

Pour plus d’informations, consultez [Pipelines ML](concept-ml-pipelines.md).

### <a name="register-and-track-ml-models"></a>Inscrire et suivre des modèles ML

L’inscription de modèle vous permet de stocker vos modèles dans le cloud Azure, au sein de votre espace de travail, et d’en gérer les versions. Le registre de modèle facilite l’organisation et le suivi de vos modèles entraînés.

> [!TIP]
> Un modèle inscrit est un conteneur logique pour un ou plusieurs fichiers qui composent votre modèle. Par exemple, si vous avez un modèle qui est stocké dans plusieurs fichiers, vous pouvez inscrire ces derniers en tant que modèle unique dans votre espace de travail Azure Machine Learning. Après l’inscription, vous pouvez ensuite télécharger ou déployer le modèle inscrit et recevoir tous les fichiers qui ont été inscrits.
 
Les modèles inscrits sont identifiés par leur nom et par leur version. Chaque fois que vous inscrivez un modèle portant le même nom qu’un modèle existant, le registre incrémente la version. Vous pouvez également fournir des étiquettes de métadonnées supplémentaires lors de l’inscription, qui peuvent être utilisées lors de la recherche de modèles. Le service Azure Machine Learning prend en charge tous les modèles pouvant être chargés avec Python 3.5.2 ou version ultérieure.

> [!TIP]
> Vous pouvez également inscrire des modèles entraînés en dehors du service Azure Machine Learning.

Vous ne pouvez pas supprimer un modèle inscrit qui est utilisé dans un déploiement actif.
Pour plus d’informations, consultez la section consacrée à l’inscription d’un modèle dans l’article [Déployer des modèles](how-to-deploy-and-where.md#registermodel).

### <a name="package-and-debug-models"></a>Empaqueter et déboguer des modèles

Avant de déployer un modèle en production, il est empaqueté dans une image Docker. Dans la plupart des cas, la création d’image a lieu automatiquement en arrière-plan pendant le déploiement. Pour les scénarios avancés, vous pouvez spécifier manuellement l’image.

Si vous rencontrez des problèmes avec le déploiement, vous pouvez déployer sur votre environnement de développement local pour la résolution des problèmes et le débogage.

Pour plus d’informations, consultez [Déployer des modèles](how-to-deploy-and-where.md#registermodel) et [Résolution des problèmes liés aux déploiements](how-to-troubleshoot-deployment.md).

### <a name="validate-and-profile-models"></a>Valider et profiler des modèles

Le service Azure Machine Learning peut utiliser le profilage pour déterminer les paramètres de processeur et de mémoire idéaux à utiliser lors du déploiement de votre modèle. La validation de modèle se produit dans le cadre de ce processus, à l’aide de données que vous fournissez pour le processus de profilage.

### <a name="convert-and-optimize-models"></a>Convertir et optimiser les modèles

La conversion de votre modèle en ONNX ([Open Neural Network Exchange](https://onnx.ai)) peut améliorer les performances. En moyenne, la conversion en ONNX peut doubler les performances.

Pour plus d’informations sur ONNX avec le service Azure Machine Learning, consultez l’article [Créer et accélérer des modèles ML](concept-onnx.md).

### <a name="use-models"></a>Utiliser des modèles

Les modèles Machine Learning entraînés peuvent être déployés en tant que services web dans le cloud ou localement sur votre environnement de développement. Vous pouvez également déployer les modèles sur des appareils Azure IoT Edge. Les déploiements peuvent utiliser un processeur, un processeur graphique ou des tableaux FPGA (Field-Programmable Gate Array) pour l’inférence. Vous pouvez également utiliser des modèles de Power BI.

Lorsque vous utilisez un modèle comme un service web ou un appareil IoT Edge, vous fournissez les éléments suivants :

* Modèles utilisés pour noter les données envoyées vers le service/l’appareil.
* Script d’entrée. Ce script accepte les demandes, utilise les modèles pour noter les données et renvoie une réponse.
* Fichier d’environnement conda qui décrit les dépendances requises par les modèles et le script d’entrée.
* Autres ressources, telles que le texte ou les données, entre autres, qui sont requises par les modèles et le script d’entrée.

Ces ressources sont empaquetées dans une image Docker et déployées en tant que service web ou module IoT Edge.

Si vous le souhaitez, vous pouvez utiliser les paramètres suivants pour affiner le déploiement :

* Activer le GPU : permet d’activer la prise en charge du processeur graphique dans l’image Docker. L’image doit être utilisée sur les services Microsoft Azure comme Azure Container Instances, Azure Kubernetes Service, Capacité de calcul Azure Machine Learning ou les machines virtuelles Azure.
* Étapes supplémentaires du fichier Docker : fichier qui contient des étapes Docker supplémentaires à exécuter lors de la création de l’image Docker.
* Image de base : image personnalisée à utiliser comme image de base. Si vous n’utilisez pas une image personnalisée, l’image de base est fournie par le service Azure Machine Learning.

Vous fournissez également la configuration de la plateforme de déploiement cible, par exemple le type de famille de machines virtuelles, la mémoire disponible et nombre de cœurs lors du déploiement sur Azure Kubernetes Service.

Lorsque l’image est créée, les composants requis par le service Azure Machine Learning sont également ajoutés, par exemple les ressources nécessaires pour exécuter le service web et interagir avec IoT Edge.

> [!NOTE]
> Vous ne pouvez pas modifier ni changer le serveur web ou les composants IoT Edge utilisés dans l’image Docker. Le service Azure Machine Learning utilise une configuration de serveur web et des composants IoT Edge qui sont testés et pris en charge par Microsoft.

#### <a name="web-service"></a>Service Web

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


## <a name="monitor-ml-applications-for-operational-and-ml-related-issues"></a>Superviser les applications ML pour détecter des problèmes de fonctionnement et liés à ML

La supervision vous permet de comprendre les données envoyées à votre modèle et les prédictions qu’il retourne.

Ces informations vous aident à comprendre l’utilisation de votre modèle. Les données d’entrée collectées peuvent également être utiles dans l’entraînement des futures versions du modèle.

Pour plus d’informations, consultez [Guide pratique pour activer la collecte des données de modèle](how-to-enable-data-collection.md).


## <a name="capture-an-end-to-end-audit-trail-of-the-ml-lifecycle"></a>Capturer une piste d’audit de bout en bout du cycle de vie ML

Azure ML vous donne la possibilité d’effectuer le suivi de la piste d’audit de bout en bout de toutes vos ressources ML. Plus précisément :

- Azure ML [s’intègre à Git](how-to-set-up-training-targets.md#gitintegration) pour le suivi des informations du dépôt / de la branche / de la validation d’où provient votre code.
- Les [jeux de données Azure ML](how-to-create-register-datasets.md) vous permettent de suivre et de gérer la version des données.
- L’historique des exécutions Azure ML stocke une capture instantanée du code, des données et du calcul utilisés pour entraîner un modèle.
- Le registre de modèles Azure ML capture toutes les métadonnées associées à votre modèle (l’expérience qui l’a entraîné, où il est déployé, si ses déploiements sont sains).

## <a name="automate-the-end-to-end-ml-lifecycle"></a>Automatiser le cycle de vie ML de bout en bout 

Vous pouvez utiliser GitHub et Azure Pipelines pour créer un processus d’intégration continue qui entraîne un modèle. Dans un scénario classique, quand un scientifique des données vérifie une modification dans le dépôt Git pour un projet, Azure Pipelines démarre une exécution d’entraînement. Les résultats de l’exécution peuvent ensuite être examinés pour afficher les caractéristiques de performances du modèle entraîné. Vous pouvez également créer un pipeline qui déploie le modèle comme un service web.

L’[extension Azure Machine Learning](https://marketplace.visualstudio.com/items?itemName=ms-air-aiagility.vss-services-azureml) facilite l’utilisation d’Azure Pipelines. Elle offre les améliorations suivantes à Azure Pipelines :

* Permet la sélection de l’espace de travail lors de la définition d’une connexion de service.
* Permet le déclenchement des pipelines de mise en production par les modèles entraînés créés dans un pipeline d’entraînement.

Pour plus d’informations sur l’utilisation d’Azure Pipelines avec Azure Machine Learning, consultez l’article [Intégration et déploiement continus de modèles ML avec Azure Pipelines](/azure/devops/pipelines/targets/azure-machine-learning) et le dépôt [MLOps du service Azure Machine Learning](https://aka.ms/mlops).

## <a name="next-steps"></a>Étapes suivantes

Découvrez plus d’informations sur [comment et où vous pouvez déployer des modèles](how-to-deploy-and-where.md) avec le service Azure Machine Learning. Pour obtenir un exemple de déploiement, consultez [Tutoriel : Déployer un modèle de classification d’images dans Azure Container Instances](tutorial-deploy-models-with-aml.md).

Découvrez comment créer [une intégration et un déploiement continus de modèles ML avec Azure Pipelines](/azure/devops/pipelines/targets/azure-machine-learning). 

Découvrez comment créer des applications et services clients qui [utilisent un modèle déployé en tant que service web](how-to-consume-web-service.md).
