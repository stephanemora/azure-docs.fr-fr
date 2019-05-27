---
title: 'MLOps : Gérer, déployer et surveiller des modèles ML'
titleSuffix: Azure Machine Learning service
description: 'Découvrez comment utiliser le Service Azure Machine Learning pour MLOps : déployer, gérer et surveiller vos modèles afin de les améliorer en permanence. Vous pouvez déployer les modèles que vous avez entraînés avec le service Azure Machine Learning, sur votre ordinateur local ou depuis d’autres sources.'
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: chris-lauren
ms.author: clauren
ms.date: 05/02/2019
ms.custom: seodec18
ms.openlocfilehash: 416bebc070cfcad52c6180e65f0066c46c826cbe
ms.sourcegitcommit: 16cb78a0766f9b3efbaf12426519ddab2774b815
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/17/2019
ms.locfileid: "65849646"
---
# <a name="mlops-manage-deploy-and-monitor-models-with-azure-machine-learning-service"></a>MLOps : Déployer, gérer et surveiller des modèles avec le service Azure Machine Learning

Dans cet article, vous pouvez découvrir comment utiliser le service Azure Machine Learning pour déployer, gérer et surveiller vos modèles afin de les améliorer en permanence. Vous pouvez déployer les modèles que vous avez entraînés avec Azure Machine Learning, sur votre machine locale ou depuis d’autres sources. 

Le diagramme suivant illustre le flux de travail de déploiement complet : [![Flux de travail de déploiement pour Azure Machine Learning](media/concept-model-management-and-deployment/deployment-pipeline.png)](media/concept-model-management-and-deployment/deployment-pipeline.png#lightbox)

Le MLOps / flux de travail de déploiement comprend les étapes suivantes :
1. **Inscrire le modèle** dans un registre hébergé dans votre espace de travail du service Azure Machine Learning
1. **Utilisez** le modèle dans un service web dans le cloud, sur un appareil IoT ou pour l’analytique avec Power BI.
1. **Surveiller et collecter des données**
1. **Mettre à jour** un déploiement pour utiliser une nouvelle image

Chaque étape peut être effectuée indépendamment ou en tant que partie d’une seule commande. En outre, vous pouvez créer un **flux de travail CI/CD** comme illustré dans ce graphique.

[![« Cycle de déploiement (CI/CD) une intégration continus azure Machine Learning »](media/concept-model-management-and-deployment/model-ci-cd.png)](media/concept-model-management-and-deployment/model-ci-cd.png#lightbox)

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2X1GX]

## <a name="step-1-register-model"></a>Étape 1 : Inscrire le modèle

L’inscription de modèle vous permet de stocker vos modèles dans le cloud Azure, au sein de votre espace de travail, et d’en gérer les versions. Le registre de modèle facilite l’organisation et le suivi de vos modèles entraînés.

> [!TIP]
> Vous pouvez également enregistrer des modèles formés en dehors du service Azure Machine Learning.
 
Les modèles inscrits sont identifiés par leur nom et par leur version. Chaque fois que vous inscrivez un modèle portant le même nom qu’un modèle existant, le registre incrémente la version. Vous pouvez également fournir des étiquettes de métadonnées supplémentaires lors de l’inscription, qui peuvent être utilisées lors de la recherche de modèles. Le service Azure Machine Learning prend en charge n’importe quel modèle qui peut être chargé à l’aide de Python 3.5.2 ou une version ultérieure.

Vous ne pouvez pas supprimer les modèles qui sont utilisés dans un déploiement actif.

Pour plus d’informations, consultez la section consacrée à l’inscription d’un modèle dans l’article [Déployer des modèles](how-to-deploy-and-where.md#registermodel).

Pour obtenir un exemple d’inscription d’un modèle stocké au format pickle, consultez [Tutoriel : Entraîner un modèle de classification d’images](tutorial-deploy-models-with-aml.md).

## <a name="step-2-use-the-model"></a>Étape 2 : Utiliser le modèle

Modèles d’apprentissage automatique peuvent être utilisés comme un service web, sur des appareils IoT Edge, ou pour l’analytique à partir des services tels que Power BI.

### <a name="web-service"></a>Service Web

Vous pouvez utiliser vos modèles dans **services web** cibles de calcul avec les éléments suivants :

* Azure Container Instances
* Azure Kubernetes Service

Pour déployer le modèle comme un service web, vous devez fournir les éléments suivants :

* Le modèle ou un ensemble de modèles.
* Dépendances requises pour utiliser le modèle. Par exemple, un script qui accepte les requêtes et appelle le modèle, les dépendances conda, etc.
* Configuration de déploiement qui décrit comment et où déployer le modèle.

Pour plus d’informations, consultez [déployer des modèles](how-to-deploy-and-where.md).

### <a name="iot-edge-devices"></a>Appareils IoT Edge

Vous pouvez utiliser des modèles avec les appareils IoT via **modules Azure IoT Edge**. Modules IoT Edge sont déployés sur les périphériques matériels, ce qui permet l’inférence, ou un modèle de notation, sur l’appareil.

Pour plus d’informations, consultez [déployer des modèles](how-to-deploy-and-where.md).

### <a name="analytics"></a>Analyse

Microsoft Power BI prend en charge à l’aide de modèles d’apprentissage automatique pour l’analytique des données. Pour plus d’informations, consultez [intégration d’Azure Machine Learning dans Power BI (version préliminaire)](https://docs.microsoft.com/power-bi/service-machine-learning-integration).

## <a name="step-3-monitor-models-and-collect-data"></a>Étape 3 : Surveiller des modèles et collecter des données

Surveillance vous permet de comprendre les données envoyées à votre modèle et les prédictions qu’elle retourne.

Ces informations vous aident à comprendre l’utilisation de votre modèle. Les données d’entrée collectées peuvent également être utiles dans les futures versions de formation du modèle.

Pour plus d’informations, consultez [Guide pratique pour activer la collecte des données de modèle](how-to-enable-data-collection.md).

## <a name="step-4-update-the-deployment"></a>Étape 4 : Mettre à jour le déploiement

Déploiements doivent être mis à jour explicitement. Pour plus d’informations, consultez la section consacrée à la mise à jour dans l’article [Déployer des modèles](how-to-deploy-and-where.md#update).

## <a name="next-steps"></a>Étapes suivantes

Découvrez plus d’informations sur [comment et où vous pouvez déployer des modèles](how-to-deploy-and-where.md) avec le service Azure Machine Learning. Pour obtenir un exemple de déploiement, consultez [didacticiel : Déployer un modèle de classification d’image dans Azure Container Instances](tutorial-deploy-models-with-aml.md).

Découvrez comment créer [intégration continue et déploiement de modèles ML avec les Pipelines Azure](/azure/devops/pipelines/targets/azure-machine-learning). 

Découvrez comment créer des applications et services clients qui [utilisent un modèle déployé en tant que service web](how-to-consume-web-service.md).
