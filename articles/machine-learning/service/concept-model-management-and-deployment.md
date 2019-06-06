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
ms.openlocfilehash: 0eaf48f57c3011222b71a63d703e1ccec7aca001
ms.sourcegitcommit: 18a0d58358ec860c87961a45d10403079113164d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/05/2019
ms.locfileid: "66692824"
---
# <a name="mlops-manage-deploy-and-monitor-models-with-azure-machine-learning-service"></a>MLOps : Déployer, gérer et surveiller des modèles avec le service Azure Machine Learning

Dans cet article, découvrez comment utiliser le service Azure Machine Learning pour gérer le cycle de vie de vos modèles. Azure Machine Learning utilise une approche de Machine Learning Operations (MLOps), ce qui améliore la qualité et la cohérence de vos solutions d’apprentissage. Service Azure Machine Learning fournit les fonctionnalités MLOps suivantes :

* Intégration avec les Pipelines Azure. Définir des workflows d’intégration et déploiement continus pour vos modèles.
* Un Registre de modèle qui gère plusieurs versions de vos modèles formés.
* Validation du modèle. Valider vos modèles formés et sélectionnez la configuration optimale pour les déployer en production automatiquement.
* Déployez vos modèles en tant qu’un service web dans le cloud, localement ou sur des appareils IoT Edge.
* Surveiller les performances de votre modèle déployé, donc vous pouvez optimiser les améliorations dans la prochaine version du modèle.

Pour savoir plus sur les concepts qui sous-tendent MLOps et comment elles s’appliquent au service Azure Machine Learning, regardez la vidéo suivante.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2X1GX]

## <a name="integration-with-azure-pipelines"></a>Intégration avec les Pipelines Azure

Vous pouvez utiliser des Pipelines d’Azure pour créer un processus d’intégration continue qui effectue l’apprentissage d’un modèle. Dans un scénario classique, quand un scientifique des données vérifie une modification dans le référentiel Git pour un projet, le Pipeline Azure démarre une exécution de la formation. Les résultats de l’exécution peuvent ensuite être analysés pour afficher les caractéristiques de performances du modèle formé. Vous pouvez également créer un pipeline qui déploie le modèle comme un service web.

Le [extension d’Azure Machine Learning](https://marketplace.visualstudio.com/items?itemName=ms-air-aiagility.vss-services-azureml) rend plus facile de travailler avec les Pipelines d’Azure. Il offre les améliorations suivantes aux Pipelines d’Azure :

* Autorise la sélection de l’espace de travail lors de la définition d’une connexion de service.
* Permet de libérer des pipelines d’être déclenchés par les modèles formés créés dans un pipeline d’apprentissage.

Pour plus d’informations sur l’utilisation des Pipelines d’Azure avec Azure Machine Learning, consultez le [intégration continue et déploiement de modèles ML avec les Pipelines Azure](/azure/devops/pipelines/targets/azure-machine-learning) article et le [MLOps de Service Azure Machine Learning](https://aka.ms/mlops) référentiel.

## <a name="convert-and-optimize-models"></a>Convertir et optimiser les modèles

Conversion de votre modèle à [ouvrir Exchange de réseau neuronal](https://onnx.ai) (ONNX) peut améliorer les performances. En moyenne, convertir ONNX peut produire un 2 x augmentation des performances.

Pour plus d’informations sur ONNX avec le service Azure Machine Learning, consultez le [créer et d’accélérer les modèles ML](concept-onnx.md) article.

## <a name="register-models"></a>Inscrire les modèles

L’inscription de modèle vous permet de stocker vos modèles dans le cloud Azure, au sein de votre espace de travail, et d’en gérer les versions. Le registre de modèle facilite l’organisation et le suivi de vos modèles entraînés.

> [!TIP]
> Vous pouvez également enregistrer des modèles formés en dehors du service Azure Machine Learning.
 
Les modèles inscrits sont identifiés par leur nom et par leur version. Chaque fois que vous inscrivez un modèle portant le même nom qu’un modèle existant, le registre incrémente la version. Vous pouvez également fournir des étiquettes de métadonnées supplémentaires lors de l’inscription, qui peuvent être utilisées lors de la recherche de modèles. Le service Azure Machine Learning prend en charge n’importe quel modèle qui peut être chargé à l’aide de Python 3.5.2 ou une version ultérieure.

Vous ne pouvez pas supprimer les modèles qui sont utilisés dans un déploiement actif.

Pour plus d’informations, consultez la section consacrée à l’inscription d’un modèle dans l’article [Déployer des modèles](how-to-deploy-and-where.md#registermodel).

Pour obtenir un exemple d’inscription d’un modèle stocké au format pickle, consultez [Tutoriel : Entraîner un modèle de classification d’images](tutorial-deploy-models-with-aml.md).

## <a name="package-and-debug-models"></a>Modèles de package et de débogage

Avant de déployer un modèle en production, il est empaqueté dans une image Docker. Dans la plupart des cas, la création d’images a lieu automatiquement en arrière-plan pendant le déploiement. Pour les scénarios avancés, vous pouvez spécifier manuellement l’image.

Si vous rencontrez des problèmes avec le déploiement, vous pouvez déployer sur votre environnement de développement local pour le dépannage et débogage.

Pour plus d’informations, consultez [déployer des modèles](how-to-deploy-and-where.md#registermodel) et [dépannage des déploiements](how-to-troubleshoot-deployment.md).

## <a name="validate-and-profile-models"></a>Valider et de modèles de profil

Service Azure Machine Learning peut utiliser le profilage pour déterminer les paramètres de processeur et mémoire idéale à utiliser lors du déploiement de votre modèle. Validation de modèle se produit dans le cadre de ce processus, à l’aide de données que vous fournissez pour le processus de profilage.

## <a name="use-models"></a>Utiliser des modèles

Modèles d’apprentissage formé peuvent être déployé en tant que services web dans le cloud ou localement sur votre environnement de développement. Vous pouvez également déployer des modèles sur des appareils Azure IoT Edge. Déploiements peuvent utiliser les UC, GPU ou des tableaux de portes à champ programmable (FPGA) pour l’inférence. Vous pouvez également utiliser des modèles à partir de Power BI.

Lorsque vous utilisez un modèle comme un service web ou d’un appareil IoT Edge, vous fournissez les éléments suivants :

* L’ou les modèles qui sont utilisé pour évaluer les données envoyées vers le service/le périphérique.
* Un script d’entrée. Ce script accepte les requêtes, l’ou les modèles pour noter les données et renvoie une réponse.
* Un fichier d’environnement conda qui décrit les dépendances requises par le script ou les modèles et d’entrée.
* Les ressources supplémentaires telles que le texte, les données, etc. qui sont requis par le script ou les modèles et d’entrée.

Ces ressources sont empaquetés dans une image Docker et déployés en tant que service web ou module IoT Edge.

Si vous le souhaitez, vous pouvez utiliser les paramètres suivants pour affiner le déploiement :

* Activer les GPU : Permet d’activer la prise en charge GPU dans l’image Docker. L’image doit être utilisée sur les Services Microsoft Azure tels que Azure Container Instances, Azure Kubernetes Service, Azure Machine Learning Compute ou les Machines virtuelles.
* Étapes du fichier docker supplémentaire : Un fichier qui contient des étapes supplémentaires de Docker à exécuter lors de la création de l’image Docker.
* Image de base : Une image personnalisée à utiliser comme image de base. Si vous n’utilisez pas une image personnalisée, l’image de base est fournie par le service Azure Machine Learning.

Vous fournissez également la configuration de la plateforme de déploiement cible. Par exemple, la machine virtuelle type de famille, la mémoire disponible et nombre de cœurs lors du déploiement sur Azure Kubernetes Service.

Lorsque l’image est créée, les composants requis par le Service Azure Machine Learning sont également ajoutés. Par exemple, les ressources nécessaires pour exécuter le service web et d’interagir avec IoT Edge.

> [!NOTE]
> Impossible de modifier ou de modifier le serveur web ou les composants d’IoT Edge utilisés dans l’image Docker. Service Azure Machine Learning utilise une configuration de serveur web et les composants d’IoT Edge qui sont testés et pris en charge par Microsoft.

### <a name="web-service"></a>Service Web

Vous pouvez utiliser vos modèles dans **services web** cibles de calcul avec les éléments suivants :

* Azure Container Instance
* Azure Kubernetes Service
* Environnement de développement local

Pour déployer le modèle comme un service web, vous devez fournir les éléments suivants :

* Le modèle ou un ensemble de modèles.
* Dépendances requises pour utiliser le modèle. Par exemple, un script qui accepte les requêtes et appelle le modèle, les dépendances conda, etc.
* Configuration de déploiement qui décrit comment et où déployer le modèle.

Pour plus d’informations, consultez [déployer des modèles](how-to-deploy-and-where.md).

### <a name="iot-edge-devices"></a>Appareils IoT Edge


Vous pouvez utiliser des modèles avec les appareils IoT via **modules Azure IoT Edge**. Modules IoT Edge sont déployés sur un périphérique matériel, ce qui permet l’inférence, ou un modèle de notation, sur l’appareil.

Pour plus d’informations, consultez [déployer des modèles](how-to-deploy-and-where.md).

### <a name="analytics"></a>Analytics

Microsoft Power BI prend en charge à l’aide de modèles d’apprentissage automatique pour l’analytique des données. Pour plus d’informations, consultez [intégration d’Azure Machine Learning dans Power BI (version préliminaire)](https://docs.microsoft.com/power-bi/service-machine-learning-integration).

## <a name="monitor-and-collect-data"></a>Surveiller et collecter des données

Surveillance vous permet de comprendre les données envoyées à votre modèle et les prédictions qu’elle retourne.

Ces informations vous aident à comprendre l’utilisation de votre modèle. Les données d’entrée collectées peuvent également être utiles dans les futures versions de formation du modèle.

Pour plus d’informations, consultez [Guide pratique pour activer la collecte des données de modèle](how-to-enable-data-collection.md).

## <a name="next-steps"></a>Étapes suivantes

Découvrez plus d’informations sur [comment et où vous pouvez déployer des modèles](how-to-deploy-and-where.md) avec le service Azure Machine Learning. Pour obtenir un exemple de déploiement, consultez [didacticiel : Déployer un modèle de classification d’image dans Azure Container Instances](tutorial-deploy-models-with-aml.md).

Découvrez comment créer [intégration continue et déploiement de modèles ML avec les Pipelines Azure](/azure/devops/pipelines/targets/azure-machine-learning). 

Découvrez comment créer des applications et services clients qui [utilisent un modèle déployé en tant que service web](how-to-consume-web-service.md).
