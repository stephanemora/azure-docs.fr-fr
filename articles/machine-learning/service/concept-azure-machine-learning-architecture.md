---
title: Fonctionnement du service Azure Machine Learning
description: Découvrez l’architecture, la terminologie et les concepts relatifs au service Azure Machine Learning. Nous aborderons également le flux de travail général du service, ainsi que les services Azure qui sont utilisés par le service Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: haining
author: hning86
ms.reviewer: larryfr
ms.date: 10/24/2018
ms.openlocfilehash: 95f74b23b9d0c89966347f066041b23f64f3b82c
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2018
ms.locfileid: "50210684"
---
# <a name="how-the-azure-machine-learning-service-works-architecture-and-concepts"></a>Fonctionnement du service Azure Machine Learning : Architecture et concepts

Ce document décrit l’architecture et les concepts du service Azure Machine Learning. Le diagramme suivant montre les principaux composants du service et illustre le flux de travail général lors de l’utilisation du service : 

[![Architecture et workflow du service Azure Machine Learning](./media/concept-azure-machine-learning-architecture/workflow.png)](./media/concept-azure-machine-learning-architecture/workflow.png#lightbox)

Le flux de travail suit généralement ces étapes :

1. Développez les scripts d’entraînement de machine learning avec __Python__.
1. Créez et configurez une __cible de calcul__.
1. __Envoyez les scripts__ à la cible de calcul configurée en vue de leur exécution dans cet environnement. Pendant l’entraînement, la cible de calcul stocke les enregistrements d’exécution dans un __magasin de données__. Les enregistrements y sont enregistrés dans une __expérience__.
1. __Interrogez cette expérience__ pour obtenir les métriques journalisées relatives aux exécutions actuelles et passées. Si les métriques ne montrent pas le résultat souhaité, retournez à l’étape 1, puis itérez vos scripts.
1. Une fois qu’une exécution satisfaisante est trouvée, inscrivez le modèle persistant dans le __registre de modèles__.
1. Développez un script de scoring.
1. __Créez une image__ et inscrivez-la dans le __registre d’images__. 
1. __Déployez l’image__ comme un __service web__ dans Azure.


[!INCLUDE [aml-preview-note](../../../includes/aml-preview-note.md)]

> [!NOTE]
> Ce document définit les termes et les concepts utilisés par Azure Machine Learning, mais il ne définit pas ceux relatifs à la plateforme Azure. Pour plus d’informations sur la terminologie relative à la plateforme Azure, consultez le [glossaire Microsoft Azure](https://docs.microsoft.com/azure/azure-glossary-cloud-terminology).

## <a name="workspace"></a>Espace de travail

L’espace de travail est la ressource de niveau supérieur du service Azure Machine Learning. Il fournit un emplacement centralisé dans lequel utiliser tous les artefacts que vous créez lorsque vous faites appel au service Azure Machine Learning.

L’espace de travail conserve une liste de cibles de calcul pouvant être utilisées pour entraîner votre modèle. Il conserve également un historique des exécutions d’entraînement, y compris les journaux, métriques, sorties et instantanés de vos scripts. Ces informations permettent de déterminer quelle exécution d’entraînement produit le meilleur modèle.

Les modèles sont inscrits dans l’espace de travail. Un modèle inscrit et des scripts de scoring sont utilisés pour créer une image. L’image peut ensuite être déployée dans Azure Container Instances, Azure Kubernetes Service, ou dans un tableau FPGA, comme point de terminaison HTTP basé sur REST. Elle peut également être déployée en tant que module sur un appareil Azure IoT Edge.

Vous pouvez créer plusieurs espaces de travail, et chacun d’eux peut être partagé par plusieurs personnes. Lorsque vous partagez un espace de travail, contrôlez l’accès à l’espace de travail en attribuant les rôles suivants aux utilisateurs :

* Propriétaire
* Contributeur
* Lecteur

Lorsque vous créez un nouvel espace de travail, celui-ci crée automatiquement plusieurs ressources Azure qui sont utilisées par l’espace de travail :

* [Azure Container Registry](https://azure.microsoft.com/services/container-registry/) : inscrit les conteneurs Docker qui sont utilisés pendant l’entraînement et lors du déploiement d’un modèle.
* [Stockage Azure](https://azure.microsoft.com/services/storage/) : utilisé comme magasin de données par défaut pour l’espace de travail.
* [Azure Application Insights](https://azure.microsoft.com/services/application-insights/) : stocke les informations de supervision concernant les modèles.
* [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) : stocke les secrets utilisés par les cibles de calcul, ainsi que d’autres informations sensibles dont a besoin l’espace de travail.

> [!NOTE]
> Au lieu de créer de nouvelles versions, vous pouvez utiliser les services Azure existants. 

Le diagramme suivant est une taxonomie de l’espace de travail :

[![Taxonomie de l’espace de travail](./media/concept-azure-machine-learning-architecture/taxonomy.png)](./media/concept-azure-machine-learning-architecture/taxonomy.png#lightbox)

## <a name="model"></a>Modèle

Le modèle le plus simple est un morceau de code qui accepte une entrée et produit une sortie. La création d’un modèle Machine Learning implique la sélection d’un algorithme auquel vous devez fournir des données, ainsi que l’optimisation des hyperparamètres. L’entraînement est un processus itératif qui génère un modèle entraîné, lequel encapsule ce qu’il a appris au cours du processus d’entraînement.

Un modèle est généré par une exécution effectuée dans Azure Machine Learning. Vous pouvez également utiliser un modèle entraîné en dehors d’Azure Machine Learning. Un modèle peut être inscrit dans un espace de travail du service Azure Machine Learning.

Le service Azure Machine Learning est indépendant de l’architecture. Vous pouvez utiliser n’importe quel framework de machine learning lorsque vous créez un modèle, comme scikit-learn, xgboost, PyTorch, TensorFlow, Chainer ou CNTK.

Pour obtenir un exemple d’apprentissage du modèle, consultez le document [Démarrage rapide : Créer un espace de travail correspondant au service Machine Learning](quickstart-get-started.md).

### <a name="model-registry"></a>Registre de modèles

Le registre de modèles garde une trace de tous les modèles de l’espace de travail correspondant au service Azure Machine Learning. 

Les modèles sont identifiés par leur nom et par leur version. Chaque fois que vous inscrivez un modèle portant le même nom qu’un modèle existant, le registre suppose qu’il s’agit d’une nouvelle version. La version est incrémentée et le nouveau modèle est inscrit sous ce nom.

Vous pouvez fournir des étiquettes de métadonnées supplémentaires lorsque vous inscrivez le modèle, puis utiliser ces étiquettes pour rechercher des modèles.

Vous ne pouvez pas supprimer les modèles qui sont utilisés par une image.

Pour obtenir un exemple d’inscription de modèle, consultez le document [Entraîner un modèle de classification d’images avec Azure Machine Learning](tutorial-train-models-with-aml.md).

## <a name="image"></a>Image

Les images constituent un moyen fiable de déployer un modèle et tous les composants nécessaires à son utilisation. Une image contient les éléments suivants :

* Un modèle
* Un script ou une application de scoring. Ce script est utilisé pour passer l’entrée au modèle et retourner la sortie du modèle.
* Les dépendances dont ont besoin le modèle ou application/script de scoring.  Par exemple, vous pouvez inclure un fichier d’environnement Conda qui répertorie les dépendances du package Python.

Deux types d’images peuvent être créés par Azure Machine Learning :

* Image FPGA : utilisée lors du déploiement d’un tableau FPGA dans le cloud Azure.
* Image Docker : utilisée lors de déploiements sur des cibles de calcul autres que les tableaux FPGA. Par exemple, Azure Container Instances et Azure Kubernetes Service.

Pour obtenir un exemple de création d’image, consultez le document [Déployer un modèle de classification d’images dans Azure Container Instances](tutorial-deploy-models-with-aml.md).

### <a name="image-registry"></a>Registre d’images

Le registre d’images effectue le suivi des images créées à partir de vos modèles. Vous pouvez fournir des étiquettes de métadonnées supplémentaires lorsque vous créez des images. Les étiquettes de métadonnées sont stockées par le registre d’images et peuvent être interrogées pour trouver votre image.

## <a name="deployment"></a>Déploiement

Un déploiement est une instanciation de votre image soit dans un service web pouvant être hébergé dans le cloud, soit dans un module IoT pour les déploiements d’appareils intégrés. 

### <a name="web-service"></a>Service Web

Un service web déployé peut utiliser Azure Container Instances, Azure Kubernetes Service ou des tableaux FPGA.
Le service est créé à partir d’une image qui encapsule votre modèle, votre script et les fichiers associés. L’image a un point de terminaison HTTP à charge équilibrée qui reçoit les requêtes de scoring envoyées au service web.

Azure vous permet de superviser le déploiement de votre service web en collectant les données de télémétrie Application Insights et/ou les données de télémétrie des modèles, si vous avez choisi d’activer cette fonctionnalité. Vous seul pouvez accéder aux données de télémétrie qui sont stockées dans votre instance Application Insights et votre instance de compte de stockage.

Si vous avez activé la mise à l’échelle automatique, Azure met automatiquement à l’échelle votre déploiement.

Pour obtenir un exemple de déploiement de modèle en tant que service, consultez le document [Déployer un modèle de classification d’images dans Azure Container Instances](tutorial-deploy-models-with-aml.md).

### <a name="iot-module"></a>Module IoT

Un module IoT déployé est un conteneur Docker qui inclut votre modèle, ainsi que le script ou l’application associés et toutes les dépendances supplémentaires. Ces modules sont déployés à l’aide d’Azure IoT Edge sur les appareils de périmètre. 

Si vous avez activé la supervision, Azure collecte les données de télémétrie à partir du modèle qui se trouve dans le module Azure IoT Edge. Vous seul pouvez accéder aux données de télémétrie qui sont stockées dans votre instance de compte de stockage.

Azure IoT Edge garantit l’exécution de votre module et supervise l’appareil qui l’héberge.

## <a name="datastore"></a>Magasin de données

Un magasin de données est une abstraction de stockage d’un compte de stockage Azure. Le magasin de données peut utiliser un conteneur d’objets blob Azure ou un partage de fichiers Azure en tant que stockage backend. Chaque espace de travail comprend un magasin de données par défaut, et peut inscrire des magasins de données supplémentaires. 

Utilisez l’API du SDK Python ou l’interface CLI Azure Machine Learning pour stocker et récupérer des fichiers à partir du magasin de données. 

## <a name="run"></a>Exécuter

Une exécution est un enregistrement qui contient les informations suivantes :

* Les métadonnées relatives à l’exécution (horodatage, durée, etc.)
* Les métriques journalisées par votre script
* Les fichiers de sortie collectés automatiquement par l’expérience ou chargés explicitement par l’utilisateur
* Un instantané du répertoire qui contient vos scripts, avant l’exécution

Une exécution est déclenchée lorsque vous envoyez un script pour entraîner un modèle. Une exécution peut avoir zéro, une ou plusieurs exécutions enfants. De fait, l’exécution de niveau supérieur peut avoir deux exécutions enfants, et chacune d’elles peut avoir ses propres exécutions enfants.

Pour obtenir un exemple où sont affichées les exécutions déclenchées par l’entraînement d’un modèle, consultez le document [Démarrage rapide : bien démarrer avec le service Azure Machine Learning](quickstart-get-started.md).

## <a name="experiment"></a>Expérience

Une expérience est un regroupement d’exécutions provenant d’un script donné. Elle appartient toujours à un espace de travail. Lorsque vous envoyez une exécution, vous fournissez un nom pour l’expérience. Les informations concernant l’exécution sont stockées sous cette expérience. Si vous envoyez une exécution et spécifiez un nom d’expérience qui n’existe pas, une nouvelle expérience portant ce nom est automatiquement créée.

Pour obtenir un exemple d’utilisation d’une expérience, consultez le document [Démarrage rapide : bien démarrer avec le service Azure Machine Learning](quickstart-get-started.md).

## <a name="pipelines"></a>Pipelines

Les pipelines permettent de créer et gérer des flux de travail qui combinent les phases de Machine Learning. Par exemple, un pipeline peut inclure les phases de préparation des données, de formation du modèle, de déploiement du modèle et d’inférence. Chaque phase peut englober plusieurs étapes, chacune d’elles pouvant s’exécuter sans assistance dans différentes cibles de calcul.

Pour plus d’informations sur les pipelines Machine Learning disposant de ce service, consultez l’article [Pipelines et Azure Machine Learning](concept-ml-pipelines.md).

## <a name="compute-target"></a>Cible de calcul

Une cible de calcul est une ressource de calcul utilisée pour exécuter votre script de formation ou pour héberger votre déploiement de service. Les cibles de calcul prises en charge sont les suivantes : 

| Cible de calcul | Formation | Déploiement |
| ---- |:----:|:----:|
| Votre ordinateur local | ✓ | &nbsp; |
| Une machine virtuelle Linux dans Azure</br>(par exemple Data Science Virtual Machine) | ✓ | &nbsp; |
| Un cluster Azure Batch AI | ✓ | &nbsp; |
| Azure Databricks | ✓ | &nbsp; | &nbsp; |
| Service Analytique Azure Data Lake | ✓ | &nbsp; |
| Apache Spark pour HDInsight | ✓ | &nbsp; |
| Azure Container Instance | ✓ | ✓ |
| Azure Kubernetes Service | &nbsp; | ✓ |
| Azure IoT Edge | &nbsp; | ✓ |
| Projet Brainwave</br>(Field programmable gate array) | &nbsp; | ✓ |

Les cibles de calcul sont attachées à un espace de travail. Les cibles de calcul autres que l’ordinateur local sont partagées par les utilisateurs de l’espace de travail.

La plupart des cibles de calcul peuvent être créées directement par le biais de l’espace de travail à l’aide du portail Azure, du SDK Azure Machine Learning ou d’Azure CLI. Si vous disposez de cibles de calcul qui ont été créées par un autre processus (par exemple, via le portail Azure ou Azure CLI), vous pouvez les ajouter (attacher) à votre espace de travail. Certaines cibles de calcul doivent être créées en dehors de l’espace de travail, puis attachées.

Pour plus d’informations sur la sélection d’une cible de calcul pour l’entraînement, consultez le document [Sélectionner et utiliser une cible de calcul pour entraîner votre modèle](how-to-set-up-training-targets.md).

Pour plus d’informations sur la sélection d’une cible de calcul pour le déploiement, consultez le document [Déployer des modèles avec le service Azure Machine Learning](how-to-deploy-and-where.md).

## <a name="run-configuration"></a>Configuration de série de tests

Une configuration d’exécution est un ensemble d’instructions qui définit la façon dont un script doit être exécuté dans une cible de calcul donnée. Il comprend un vaste ensemble de définitions de comportement, par exemple, s’il faut utiliser un environnement Python existant ou utiliser un environnement Conda créé à partir des spécifications.

Une configuration d’exécution peut être rendue persistante dans un fichier du répertoire qui contient votre script d’entraînement, ou construite comme un objet en mémoire et utilisée pour envoyer une exécution.

Pour obtenir des exemples de configurations d’exécutions, consultez le document [Sélectionner et utiliser une cible de calcul pour entraîner votre modèle](how-to-set-up-training-targets.md).

## <a name="training-script"></a>Script d’entraînement

Pour entraîner un modèle, vous devez spécifier le répertoire qui contient le script d’entraînement et les fichiers associés. Vous pouvez également spécifier un nom pour l’expérience, qui est utilisée pour stocker les informations collectées au cours de l’entraînement. Pendant l’entraînement, l’intégralité du répertoire est copiée dans l’environnement d’entraînement (la cible de calcul) et le script spécifié par la configuration d’exécution est démarré. Un instantané du répertoire est également stocké sous l’expérience, dans l’espace de travail.

Pour en voir un exemple, consultez [Créer un espace de travail avec Python](quickstart-get-started.md).

## <a name="logging"></a>Journalisation

Lorsque vous développez votre solution, utilisez le SDK Python Azure Machine Learning dans votre script Python pour journaliser les métriques arbitraires. Après l’exécution, interrogez les métriques pour vérifier si celle-ci a produit le modèle que vous souhaitez déployer. 

## <a name="snapshot"></a>Instantané

Lorsque vous envoyez une exécution, Azure Machine Learning compresse le répertoire qui contient le script dans un fichier zip, puis l’envoie à la cible de calcul. Le fichier zip est ensuite développé et le script y est exécuté. Azure Machine Learning stocke également le fichier zip en tant qu’instantané dans l’enregistrement d’exécution. Toute personne ayant accès à l’espace de travail peut parcourir un enregistrement d’exécution et télécharger l’instantané.

## <a name="activity"></a>Activité

Une activité représente une opération de longue durée. Les opérations suivantes sont des exemples d’activités :

* Création ou suppression d’une cible de calcul
* Exécution d’un script sur une cible de calcul

Les activités peuvent envoyer des notifications via le SDK ou l’interface utilisateur web, ce qui vous permet de superviser facilement la progression de ces opérations.

## <a name="next-steps"></a>Étapes suivantes

Cliquez sur les liens suivants pour bien démarrer avec Azure Machine Learning :

* [Qu’est-ce que le service Azure Machine Learning ?](overview-what-is-azure-ml.md)
* [Démarrage rapide : créer un espace de travail avec Python](quickstart-get-started.md)
* [Tutoriel : entraîner un modèle](tutorial-train-models-with-aml.md)
