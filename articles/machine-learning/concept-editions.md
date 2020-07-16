---
title: Azure Machine Learning Éditions Entreprise et De base
description: En savoir plus sur les différences entre les éditions d’Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: j-martens
ms.author: jmartens
ms.date: 06/11/2020
ms.openlocfilehash: 173ff8b638c40773ca2c4bdac5021f2ea4b84549
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85555527"
---
# <a name="enterprise-and-basic-editions-of-azure-machine-learning"></a>Éditions Entreprise et De base d’Azure Machine Learning 

Azure Machine Learning propose deux éditions adaptées à vos besoins en Machine Learning. Ces éditions déterminent les outils de Machine Learning disponibles pour les développeurs et les scientifiques de données depuis leur espace de travail.

## <a name="choose-an-edition"></a>Choisir une édition

Vous attribuez l’édition chaque fois que vous créez un espace de travail. Les clients sont responsables des coûts liés au calcul et aux autres ressources Azure pendant cette période. Apprenez comment [gérer les coûts d’Azure Machine Learning](concept-plan-manage-cost.md).

Découvrez comment [mettre à niveau un espace de travail De base vers l’édition Entreprise](how-to-manage-workspace.md#upgrade). 

## <a name="whats-in-each-edition"></a>Que contient chaque édition

### <a name="data-for-machine-learning-capabilities"></a>Données pour les fonctionnalités Azure Machine Learning  

| Fonctionnalités                     | Édition                 |
|------------------------------------------------------------------------------------|:-----------:|
| Étiquetage : [Créer et gérer des projets d’étiquetage](tutorial-labeling.md) dans studio (Web)                                                | Tous                     |
| Étiquetage : Labeler dans studio (Web)                                    | Tous                     |
| Étiquetage : Utiliser le personnel privé                               | Tous                     |
| Étiquetage : [Classification et détection d’objet assistées par ML](how-to-label-images.md)                  | Edition Entreprise uniquement |
| Jeux de données + magasins de données : créer et gérer dans Python                       | Tous                     |
| Jeux de données + magasins de données : créer et gérer dans studio (Web)                         | Tous                     |
| Drift : Afficher et gérer des analyses de jeux de données dans Python                           | Tous                     |
| Drift : Afficher et gérer des analyses de jeux de données dans studio (Web)                            | Edition Entreprise uniquement |


<br/>
<br/>

### <a name="automated-training-capabilities-automl"></a>Fonctionnalités de formation automatisée (AutoML)

| Fonctionnalités    | Édition                 |
|------------------------------------------------------------------------------------|:-----------:|
| Créer et exécuter des [expériences AutoML dans des notebooks](how-to-configure-auto-train.md)               | Tous                     |
| Créer et exécuter des [expériences AutoML dans studio (web)](how-to-use-automated-ml-for-ml-models.md)   | Edition Entreprise uniquement |
| Meilleures fonctionnalités de prévision d’AutoML du secteur             | Edition Entreprise uniquement |
| Prise en charge de l’apprentissage profond et d’autres apprenants avancés | Edition Entreprise uniquement |
| Les données volumineuses prennent en charge les tâches de classification et de régression (jusqu’à 100 Go)                     | Edition Entreprise uniquement |


<br/>
<br/>

### <a name="responsible-machine-learning"></a>Machine Learning responsable

| Fonctionnalités    | Édition                 |
|------------------------------------------------------------------------------------|:-----------:|
| [Explicabilité du modèle](how-to-machine-learning-interpretability-automl.md)                                              | Tous                     |
| [Confidentialité différentielle](how-to-differential-privacy.md)                          | Tous                     |
| Balises personnalisées pour implémenter des feuilles de données    | Tous                     |
| Intégration d’impartialité AzureML                                      | Tous                     |

<br/>
<br/>


### <a name="build-and-train-capabilities"></a>Fonctionnalités de génération et de formation

| Fonctionnalités    | Édition                 |
|------------------------------------------------------------------------------------|:-----------:|
| Intégration de Visual Studio Code                                                     | Tous                     |
| Apprentissage par renforcement                                                             | Tous                     |
| IU d’expérimentation                                                                 | Tous                     |
| Intégration de Jupyter et JupyterLab                                                    | Tous                     |
| Prise en charge du Kit de développement logiciel (SDK) Python                                                                 | Tous                     |
| Prise en charge du Kit de développement logiciel (SDK) R                                                                      | Tous                     |
| Pipelines ML : Créer, exécuter et publier dans Python                           | Tous                     |
| Pipelines ML : Créer, modifier et supprimer des séries planifiées de pipelines dans Python| Tous                     |
| Pipelines ML : Créer des points de terminaison de pipeline dans le Kit de développement logiciel (SDK) Python                                   | Tous                     |
| Pipelines ML : Afficher les détails de l’exécution dans studio (Web)                                              | Tous                     |
| Pipelines ML : Créer, exécuter, visualiser et publier dans le concepteur                  | Edition Entreprise uniquement |
| Pipelines ML : Créer des points de terminaison de pipeline dans le concepteur | Edition Entreprise uniquement |
| Instances de calcul gérées pour les notebooks intégrés                                 | Tous                     |


<br/>
<br/>

### <a name="deployment-and-model-management-capabilities"></a>Fonctionnalités de gestion de déploiement et de modèle

| Fonctionnalités                            | Édition                 |
|------------------------------------------------------------------------------------|:-----------:|
| L’extension Azure DevOps pour Machine Learning et Azure ML CLI                 | Tous                     |
| [Intégration à Event Grid](how-to-use-event-grid.md)                                                             | Tous                     |
| Intégration d’Azure Stream Analytics avec Azure Machine Learning                       | Tous                     |
| Créer des pipelines ML dans le Kit de développement logiciel (SDK)                                                         | Tous                     |
| Inférence par lot                                                                  | Tous                     |
| Modèles accélérés matériellement basés sur les FPGA                                             | Tous                     |
| Profilage des modèles                                                                    | Tous                     |

<br/>
<br/>

### <a name="security-governance-and-control-capabilities"></a>Fonctionnalités de sécurité, de gouvernance et de contrôle

| Fonctionnalités     | Édition                 |
|------------------------------------------------------------------------------------|:-----------:|
| Prise en charge du[contrôle d’accès en fonction du rôle ](how-to-assign-roles.md) (RBAC)                                           | Tous                     |
| Prise en charge du [réseau virtuel (Vnet)](how-to-enable-virtual-network.md) pour calcul                                         | Tous                     |
| Authentification du point de terminaison scoring                                                    | Tous                     |
| [Espace de travail avec liaison privée](how-to-configure-private-link.md)                                                            | Tous                     |
| [Gestion de quota](how-to-manage-quotas.md) entre les espaces de travail                                                 | Edition Entreprise uniquement |

## <a name="next-steps"></a>Étapes suivantes

Vous trouverez plus d’informations sur les éléments disponibles dans la [vue d’ensemble et la page de tarification de l’édition](https://azure.microsoft.com/pricing/details/machine-learning/) d’Azure Machine Learning. 

Découvrez comment [mettre à niveau un espace de travail De base vers l’édition Entreprise](how-to-manage-workspace.md#upgrade). 
