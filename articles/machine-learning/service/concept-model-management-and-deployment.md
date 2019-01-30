---
title: Gérer, inscrire, déployer et surveiller des modèles ML
titleSuffix: Azure Machine Learning service
description: Découvrez comment utiliser le service Azure Machine Learning pour déployer, gérer et surveiller vos modèles afin de les améliorer en permanence. Vous pouvez déployer les modèles que vous avez entraînés avec le service Azure Machine Learning, sur votre ordinateur local ou depuis d’autres sources.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
author: chris-lauren
ms.author: clauren
ms.date: 09/24/2018
ms.custom: seodec18
ms.openlocfilehash: 44f61d7b90018b76b1903a04d219dcf0226f95e0
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2019
ms.locfileid: "54852318"
---
# <a name="manage-deploy-and-monitor-models-with-azure-machine-learning-service"></a>Déployer, gérer et surveiller des modèles avec le service Azure Machine Learning

Dans cet article, vous pouvez découvrir comment utiliser le service Azure Machine Learning pour déployer, gérer et surveiller vos modèles afin de les améliorer en permanence. Vous pouvez déployer les modèles que vous avez entraînés avec Azure Machine Learning, sur votre machine locale ou depuis d’autres sources. 

Le diagramme suivant illustre le flux de travail de déploiement complet : [ ![Flux de travail de déploiement Azure Machine Learning](media/concept-model-management-and-deployment/deployment-pipeline.png) ](media/concept-model-management-and-deployment/deployment-pipeline.png#lightbox)

Le workflow de déploiement comprend les étapes suivantes :
1. **Inscrire le modèle** dans un registre hébergé dans votre espace de travail du service Azure Machine Learning
1. **Inscrire une image** qui associe un modèle avec un script de scoring et des dépendances dans un conteneur portable 
1. **Déployer** l’image comme service web dans le cloud ou sur des périphériques de périmètre
1. **Surveiller et collecter des données**
1. **Mettre à jour** un déploiement pour utiliser une nouvelle image

Chaque étape peut être effectuée indépendamment ou dans le cadre d’une même commande de déploiement. En outre, vous pouvez intégrer le déploiement dans un **flux de travail CI/CD**, comme illustré dans ce graphique.

[ ![« Cycle d’intégration continue/déploiement continu (CI/CD) d’Azure Machine Learning »](media/concept-model-management-and-deployment/model-ci-cd.png) ](media/concept-model-management-and-deployment/model-ci-cd.png#lightbox)

## <a name="step-1-register-model"></a>Étape 1 : Inscrire le modèle

L’inscription de modèle vous permet de stocker vos modèles dans le cloud Azure, au sein de votre espace de travail, et d’en gérer les versions. Le registre de modèle facilite l’organisation et le suivi de vos modèles entraînés.
 
Les modèles inscrits sont identifiés par leur nom et par leur version. Chaque fois que vous inscrivez un modèle portant le même nom qu’un modèle existant, le registre incrémente la version. Vous pouvez également fournir des étiquettes de métadonnées supplémentaires lors de l’inscription, qui peuvent être utilisées lors de la recherche de modèles. Azure Machine Learning service prend en charge tous les modèles pouvant être chargés avec Python 3. 

Vous ne pouvez pas supprimer les modèles qui sont utilisés par une image.

Pour plus d’informations, consultez la section consacrée à l’inscription d’un modèle dans l’article [Déployer des modèles](how-to-deploy-and-where.md#registermodel).

Pour obtenir un exemple d’inscription d’un modèle stocké au format pickle, consultez [Tutoriel : Entraîner un modèle de classification d’images](tutorial-deploy-models-with-aml.md).

Pour obtenir des informations sur l’utilisation des modèles ONNX, consultez le document [ONNX et Azure Machine Learning](how-to-build-deploy-onnx.md).

## <a name="step-2-register-image"></a>Étape 2 : Inscrire une image

Les images permettent un déploiement de modèles fiable, ainsi que le déploiement de tous les composants nécessaires pour utiliser le modèle. Une image contient les éléments suivants :

* Le modèle
* Le moteur de scoring
* Le fichier ou l’application de scoring
* Toutes les dépendances nécessaires pour scorer le modèle

L’image peut également inclure des composants du SDK pour la journalisation et la surveillance. Les SDK journalise des données qui peuvent être utilisées pour optimiser ou réentraîner votre modèle, notamment les entrées et les sorties du modèle.

Azure Machine Learning prend en charge les infrastructures les plus courantes, mais d’une façon générale, les infrastructures pouvant être installées avec pip peuvent fonctionner.

Lors de la création de votre espace de travail, par conséquent, plusieurs autres ressources Azure utilisées par cet espace de travail ont également été créées.
Tous les objets utilisés pour créer l’image sont stockés dans le compte de stockage Azure de votre espace de travail. Vous pouvez fournir des étiquettes de métadonnées supplémentaires lorsque vous créez des images. Les étiquettes de métadonnées sont également stockées par le registre d’images et permettent de rechercher une image.

Pour plus d’informations, consultez la section consacrée à la configuration et à l’inscription d’une image dans l’article [Déployer des modèles](how-to-deploy-and-where.md#configureimage).

## <a name="step-3-deploy-image"></a>Étape 3 : Déployer une image

Vous pouvez déployer des images inscrites dans le cloud ou sur des périphériques de périmètre. Le processus de déploiement crée toutes les ressources nécessaires pour effectuer le monitoring, l’équilibrage de charge et la mise à l’échelle automatique du modèle. L’accès aux services déployés est sécurisable avec l’authentification par certificat en fournissant les ressources de sécurité lors du déploiement. Vous pouvez également mettre à niveau un déploiement existant pour utiliser une image plus récente.

Les déploiements de service web peuvent également faire l’objet de recherches. Par exemple, vous pouvez rechercher tous les déploiements d’un modèle ou d’une image spécifique.

[ ![Inférence des cibles](media/concept-model-management-and-deployment/inferencing-targets.png) ](media/concept-model-management-and-deployment/inferencing-targets.png#lightbox)

Vous pouvez déployer vos images sur les cibles de déploiement suivantes dans le cloud :

* Azure Container Instance
* Azure Kubernetes Service
* Machines FPGA Azure
* Appareil Azure IoT Edge

Une fois votre service déployé, la charge des demandes d’inférence est automatiquement équilibrée et le cluster est mis à l’échelle pour satisfaire les pics de demandes. Des [données de télémétrie sur votre service peuvent être capturées](how-to-enable-app-insights.md) dans le service Azure Application Insights associé à votre espace de travail.

Pour plus d’informations, consultez la section consacrée au déploiement dans l’article [Déployer des modèles](how-to-deploy-and-where.md#deploy).

## <a name="step-4-monitor-models-and-collect-data"></a>Étape 4 : Surveiller des modèles et collecter des données

Un SDK pour la journalisation des modèles et la capture de données est disponible pour vous permettre de surveiller les entrées, les sorties et d’autres données pertinentes de votre modèle. Les données sont stockées comme objet blob dans le compte de stockage Azure pour votre espace de travail.

Pour utiliser le SDK avec votre modèle, vous l’importez dans votre script ou votre application de scoring. Vous pouvez ensuite utiliser le SDK pour journaliser des données comme les paramètres, les résultats ou les détails des entrées.

Si vous décidez d’activer la collecte de données du modèle chaque fois que vous déployez l’image, les informations nécessaires pour capturer les données, comme les informations d’identification de votre magasin d’objets blob personnel, sont automatiquement provisionnées.

> [!Important]
> Microsoft ne voit pas les données collectées à partir de votre modèle. Les données sont envoyées directement dans le compte de stockage Azure pour votre espace de travail.

Pour plus d’informations, consultez [Guide pratique pour activer la collecte des données de modèle](how-to-enable-data-collection.md).

## <a name="step-5-update-the-deployment"></a>Étape 5 : Mettre à jour le déploiement

Les mises à jour de votre modèle ne sont pas inscrites automatiquement. De même, l’inscription d’une nouvelle image ne met pas automatiquement à jour les déploiements qui ont été créés à partir d’une version précédente de l’image. Au lieu de cela, vous devez manuellement inscrire le modèle, inscrire l’image, puis mettre à jour le modèle. Pour plus d’informations, consultez la section consacrée à la mise à jour dans l’article [Déployer des modèles](how-to-deploy-and-where.md#update).

## <a name="next-steps"></a>Étapes suivantes

Découvrez plus d’informations sur [comment et où vous pouvez déployer des modèles](how-to-deploy-and-where.md) avec le service Azure Machine Learning.

Découvrez comment créer des applications et services clients qui [utilisent un modèle déployé en tant que service web](how-to-consume-web-service.md).
