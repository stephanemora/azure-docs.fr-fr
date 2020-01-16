---
title: Présentation des environnements ML
titleSuffix: Azure Machine Learning
description: Dans cet article, découvrez les avantages des environnements Machine Learning, qui permettent des définitions de dépendance d'apprentissage automatique reproductibles, auditables et portables sur différentes cibles de calcul.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: trbye
author: trevorbye
ms.date: 01/06/2020
ms.openlocfilehash: ad520c7e6503f28de0bd5538662c223575a078fa
ms.sourcegitcommit: 2f8ff235b1456ccfd527e07d55149e0c0f0647cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/07/2020
ms.locfileid: "75692625"
---
# <a name="what-are-azure-machine-learning-environments"></a>Présentation des environnements Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Les environnements spécifient les packages, variables d’environnement et paramètres logiciels Python autour de vos scripts d’entraînement et de scoring, ainsi que les temps d’exécution (Python, Spark ou Docker). Il s’agit d’entités gérées et avec version dans votre espace de travail Azure Machine Learning qui permettent des workflows Machine Learning reproductibles, pouvant être audités et portables sur différentes cibles de calcul.

Vous pouvez utiliser un objet d’environnement sur votre calcul local pour développer votre script d’entraînement, réutiliser le même environnement sur la Capacité de calcul Azure Machine Learning pour l’entraînement de modèle à grande échelle, et même déployer votre modèle dans ce même environnement.

Le diagramme suivant montre qu’un même objet d’environnement peut être utilisé à la fois dans votre configuration d’exécution pour l’entraînement et dans votre configuration d’inférence et de déploiement pour les déploiements de services web.

![Diagramme d’environnement dans un workflow Machine Learning](./media/concept-environments/ml-environment.png)

## <a name="types-of-environments"></a>Types d’environnements

Les environnements se répartissent globalement en trois catégories : **organisés**, **gérés par l’utilisateur** et **gérés par le système**.

Les environnements organisés sont fournis par Azure Machine Learning et sont disponibles dans votre espace de travail par défaut. Ils contiennent des collections de packages et paramètres Python destinés à vous aider à prendre en main différentes infrastructures de Machine Learning. 

Pour un environnement géré par l'utilisateur, vous êtes responsable de la configuration de votre environnement et de l'installation de chaque package requis par votre script de formation sur la cible de calcul. Conda ne vérifie pas votre environnement et n’installe rien à votre place. Notez que si vous définissez votre propre environnement, vous devez répertorier `azureml-defaults` avec la version `>= 1.0.45` comme dépendance PIP. Ce package contient les fonctionnalités nécessaires pour héberger le modèle en tant que service web.

Les environnements gérés par le système sont utilisés quand vous souhaitez que [Conda](https://conda.io/docs/) gère automatiquement l’environnement Python et les dépendances de script. Le service adopte ce type d’environnement par défaut, en raison de son utilité sur les cibles de calcul distantes qui ne sont pas configurables manuellement.

## <a name="creating-and-managing-environments"></a>Création et gestion d’environnements

Les environnements peuvent être créés en :

* définissant de nouveaux objets `Environment`, soit à l’aide d’un environnement organisé, soit en définissant vos propres dépendances
* utilisant des objets `Environment` existants à partir de votre espace de travail. Cela permet de garantir la cohérence et la reproductibilité de vos dépendances
* Importation à partir d’une définition d’environnement Anaconda existante.

Pour obtenir des exemples de code spécifiques, consultez les [guides](how-to-use-environments.md#create-an-environment). Les environnements sont également faciles à gérer via votre espace de travail et incluent les fonctionnalités suivantes :

* Les environnements sont automatiquement inscrits à votre espace de travail lorsque vous soumettez une expérience. Ils peuvent également être inscrits manuellement
* Récupérer des environnements à partir de votre espace de travail et les utiliser pour l’apprentissage, le déploiement ou apporter des modifications à la définition de l’environnement
* Le contrôle de version vous permet de voir les modifications apportées à vos environnements dans le temps et garantit la reproductibilité
* Créez automatiquement des images Docker à partir de vos environnements

Consultez la section [Gérer des environnements](how-to-use-environments.md#manage-environments) du guide pratique pour obtenir des exemples de code.

## <a name="next-steps"></a>Étapes suivantes

* Découvrez comment [Créer et utiliser des environnements](how-to-use-environments.md) dans Azure Machine Learning
* Consultez la documentation de référence du kit de développement logiciel (SDK) Python pour la [classe d’environnement](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py).
* Consultez la documentation de référence du kit de développement logiciel R pour [environnements](https://azure.github.io/azureml-sdk-for-r/reference/index.html#section-environments).